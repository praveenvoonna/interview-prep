# 10 — Hands-On Lab

## 🧠 The One Idea

**You learn AWS by building one small, real thing end-to-end: a network, a server in it, and some
storage — wired together with least-privilege access.** This lab uses the **CLI** so the moving
parts are explicit. Twenty minutes of doing beats hours of reading.

> **Goal:** create a VPC + subnet → launch an EC2 instance → create an S3 bucket → let the
> instance access the bucket **via a role, not keys**. Then **delete everything** to avoid charges.

> ⚠️ **Cost & safety:** use a sandbox/free-tier account, a least-privilege IAM user for the CLI,
> and **tear it all down** at the end. Replace placeholder IDs with your real values.

---

## 0. Configure the CLI

```bash
aws --version                 # AWS CLI v2
aws configure                 # set access key, secret, default region, output
aws sts get-caller-identity   # confirms who you are
```

---

## 1. Create a VPC and a public subnet

```bash
VPC_ID=$(aws ec2 create-vpc --cidr-block 10.0.0.0/16 \
  --query Vpc.VpcId --output text)

SUBNET_ID=$(aws ec2 create-subnet --vpc-id $VPC_ID \
  --cidr-block 10.0.1.0/24 --query Subnet.SubnetId --output text)

IGW_ID=$(aws ec2 create-internet-gateway \
  --query InternetGateway.InternetGatewayId --output text)
aws ec2 attach-internet-gateway --vpc-id $VPC_ID --internet-gateway-id $IGW_ID
```

(For real internet access you'd also add a route table pointing `0.0.0.0/0` → IGW and enable
auto-assign public IP — see Lesson 03.)

---

## 2. A security group (SSH from your IP only)

```bash
SG_ID=$(aws ec2 create-security-group --group-name lab-sg \
  --description "lab" --vpc-id $VPC_ID --query GroupId --output text)

aws ec2 authorize-security-group-ingress --group-id $SG_ID \
  --protocol tcp --port 22 --cidr <YOUR.IP.ADDR.ESS>/32
```

Note it's **stateful** — you don't need a matching outbound rule for the SSH reply (Lesson 03).

---

## 3. Create an S3 bucket

```bash
BUCKET=lab-bucket-$RANDOM-$RANDOM
aws s3 mb s3://$BUCKET
echo "hello from the lab" > hello.txt
aws s3 cp hello.txt s3://$BUCKET/
aws s3 ls s3://$BUCKET/
```

The bucket is **private by default** — good. Don't open it.

---

## 4. Launch EC2 with a role (no keys in the instance)

Create an IAM role with an S3 read policy, wrap it in an instance profile, then launch:

```bash
# (create role 'lab-ec2-role' trusting ec2, attach AmazonS3ReadOnlyAccess,
#  create instance profile 'lab-ec2-profile' and add the role — via IAM console/CLI)

aws ec2 run-instances --image-id <AMI_ID> --instance-type t3.micro \
  --subnet-id $SUBNET_ID --security-group-ids $SG_ID \
  --iam-instance-profile Name=lab-ec2-profile \
  --count 1
```

SSH in and run `aws s3 ls s3://$BUCKET/` **with no credentials configured** — it works because
the instance uses its **role** (Lesson 02). That's the lesson: **roles, not access keys.**

---

## 5. Tear it ALL down (avoid charges)

```bash
aws ec2 terminate-instances --instance-ids <INSTANCE_ID>
aws s3 rb s3://$BUCKET --force
aws ec2 delete-security-group --group-id $SG_ID
aws ec2 detach-internet-gateway --vpc-id $VPC_ID --internet-gateway-id $IGW_ID
aws ec2 delete-internet-gateway --internet-gateway-id $IGW_ID
aws ec2 delete-subnet --subnet-id $SUBNET_ID
aws ec2 delete-vpc --vpc-id $VPC_ID
```

Confirm in the console that nothing is left running.

---

## ✅ What you should now be able to say

- "I built a VPC + subnet + IGW + security group, launched EC2, and created a private S3 bucket."
- "The instance read S3 **via an IAM role**, with **no access keys** on the box."
- "I tore everything down — in the cloud, leaving resources running means a bill."

> 💡 **Next step beyond the lab:** rebuild this in **Terraform/CloudFormation** so it's one
> `apply`/`destroy` — that's how real teams do it (Lesson 01).

➡️ **Next:** [11 — Interview Q&A flashcards](./11_Interview_QA_Flashcards.md)
