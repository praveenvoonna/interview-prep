# 05 — Storage: S3, EBS & EFS

## 🧠 The One Idea

**There are three shapes of storage, and the trick is matching the shape to the job: object
(S3) for files/blobs, block (EBS) for a single server's disk, file (EFS) for a shared folder
many servers mount.** Object = a giant warehouse you reach by name over HTTP; block = the hard
drive bolted to one EC2; file = a network drive everyone shares.

The common one-liner: **"S3 = object, EBS = block, EFS = file — pick by access pattern."**

---

## 1. S3 — object storage

- Stores **objects** (a blob + metadata) in **buckets**, addressed by **key** over HTTP(S) APIs.
  No file system, no server to attach — just `PUT`/`GET`.
- **Massively durable: 11 nines (99.999999999%)** — objects are replicated across AZs in a Region.
- **Use for:** backups, images/video, static websites, data lakes, logs, big-data input/output.
- **Not for:** an OS disk or low-latency random read/write of a database (that's EBS).

### S3 storage classes (cost vs access)
- **Standard** — frequent access.
- **Intelligent-Tiering** — auto-moves objects between tiers by usage (good default when unsure).
- **Standard-IA / One Zone-IA** — infrequent access, cheaper storage, retrieval fee.
- **Glacier / Glacier Deep Archive** — archival, very cheap, retrieval takes minutes–hours.
- **Lifecycle rules** auto-transition/expire objects (e.g. Standard → IA → Glacier after N days).

### S3 must-mention features
- **Versioning** (recover overwrites/deletes), **encryption** (SSE-S3/SSE-KMS, on by default),
  **Block Public Access** (stop the classic open-bucket leak), and **bucket policies** for access.

---

## 2. EBS — block storage for EC2

- **Elastic Block Store** = a virtual hard drive **attached to one EC2 instance** in the **same
  AZ**. Persists independently of the instance (survives stop/start).
- **Volume types:** **gp3/gp2** general SSD, **io1/io2** high-IOPS SSD (databases), **st1/sc1**
  throughput/cold HDD.
- **Snapshots** back up a volume to **S3** (incremental); restore or copy across AZs/Regions.
- **One writer:** classically attached to a single instance (single-AZ). Think "the disk in this
  server."

---

## 3. EFS (& FSx) — shared file storage

- **EFS** = a managed **NFS** file system **many EC2 instances mount at once**, across AZs.
  Elastic — grows/shrinks automatically. Linux workloads needing a shared POSIX folder.
- **FSx** = managed file systems for specific needs (FSx for Windows, FSx for Lustre HPC).
- **Use EFS when** multiple instances need the **same files** simultaneously (EBS can't do that).

---

## 4. Choosing (decision table)

| Need | Use |
|---|---|
| Store blobs/files accessed by name over HTTP, cheap & durable | **S3** |
| A disk for one EC2 (DB, OS volume) | **EBS** |
| A shared folder mounted by many instances | **EFS** |
| Archive rarely-read data cheaply | **S3 Glacier** |

---

## 🎤 Say this in the interview

- *"Three shapes: **S3** object (11 nines, HTTP, lifecycle to Glacier), **EBS** block (one EC2's
  disk, snapshots to S3), **EFS** file (shared NFS across many instances/AZs)."*
- *"S3 is durable across AZs and has classes from Standard to Glacier; I use **lifecycle rules**,
  **versioning**, **Block Public Access**, and **SSE-KMS** encryption."*
- *"EBS is single-AZ, attached to one instance; when many instances need the same files I reach
  for **EFS**."*

➡️ **Next:** [06 — Databases: RDS, DynamoDB & friends](./06_Databases.md)
