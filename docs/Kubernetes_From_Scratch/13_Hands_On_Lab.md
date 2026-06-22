# 13 — Hands-On Lab (make it muscle memory)

## 🧠 The One Idea

**Reading about Kubernetes makes you recognize it; *doing* it makes you fluent.** This lab
spins up a real cluster on your laptop and walks you through deploying, scaling, breaking, and
healing an app — so the concepts from lessons 00–10 become things you've *seen happen*. Budget
~45–60 minutes. **Type the commands, watch the output, then explain it out loud.**

---

## 0. Setup (one-time)

```bash
# Install (macOS, Homebrew). Linux: see kind/kubectl docs.
brew install kind kubectl

# Create a 3-node cluster (1 control-plane + 2 workers)
cat > kind-cluster.yaml <<'EOF'
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
EOF
kind create cluster --name lab --config kind-cluster.yaml

# Verify you can talk to it
kubectl get nodes          # should list 3 nodes, Ready
kubectl cluster-info
```

> No Docker? Use `minikube start --nodes 3` instead — the `kubectl` steps are identical.

---

## 1. Your first Deployment (Lessons 02–03)

```bash
kubectl create deployment web --image=nginx:1.27 --replicas=3
kubectl get deploy,rs,pods -o wide        # see the Deployment→ReplicaSet→Pods chain
kubectl get pods -l app=web -w            # -w = watch live (Ctrl-C to stop)
```
**Observe:** 3 Pods spread across the worker nodes (the *scheduler* placed them).

### Watch self-healing (the thermostat in action)
```bash
kubectl delete pod <one-pod-name>         # kill a Pod
kubectl get pods -l app=web -w            # a NEW Pod appears within seconds
```
**Explain out loud:** *"The ReplicaSet saw actual=2, desired=3, and created a replacement —
level-triggered reconciliation."*

### Scale
```bash
kubectl scale deployment web --replicas=5
kubectl get pods -l app=web               # now 5
```

---

## 2. Expose it with a Service + DNS (Lesson 05)

```bash
kubectl expose deployment web --port=80 --name=web   # creates a ClusterIP Service
kubectl get svc web
kubectl get endpointslices -l kubernetes.io/service-name=web   # the backing Pod IPs

# Prove DNS + load-balancing from inside the cluster:
kubectl run tester --rm -it --image=busybox:1.36 --restart=Never -- \
  sh -c "wget -qO- http://web && echo OK"
```
**Explain:** *"I reached the app by the name `web` (CoreDNS), and kube-proxy load-balanced to
one of the Ready Pods behind the Service."*

---

## 3. A rolling update + rollback (Lesson 03)

```bash
kubectl set image deployment/web nginx=nginx:1.27.1
kubectl rollout status deployment/web      # watch it shift Pods gradually
kubectl get rs -l app=web                  # old RS scaled to 0, new RS at 5
kubectl rollout undo deployment/web        # instant rollback to previous RS
kubectl rollout history deployment/web
```

---

## 4. Config & a probe (Lessons 04, 08)

```bash
kubectl create configmap app-config --from-literal=LOG_LEVEL=debug
kubectl set env deployment/web --from=configmap/app-config   # triggers a rollout
kubectl describe pod -l app=web | grep -A2 "Environment"
```
Add a readiness probe and watch it gate traffic:
```bash
kubectl patch deployment web --type=json -p='[{"op":"add",
 "path":"/spec/template/spec/containers/0/readinessProbe",
 "value":{"httpGet":{"path":"/","port":80},"initialDelaySeconds":2}}]'
kubectl get pods -l app=web -w     # Pods show READY 0/1 → 1/1 as the probe passes
```

---

## 5. Namespaces & RBAC (Lesson 09)

```bash
kubectl create namespace team-a
kubectl -n team-a create serviceaccount my-app
# Can that SA list pods? (expect: no)
kubectl auth can-i list pods \
  --as=system:serviceaccount:team-a:my-app -n team-a
```
Now grant it and re-check:
```bash
kubectl -n team-a create role pod-reader --verb=get,list,watch --resource=pods
kubectl -n team-a create rolebinding read-pods \
  --role=pod-reader --serviceaccount=team-a:my-app
kubectl auth can-i list pods \
  --as=system:serviceaccount:team-a:my-app -n team-a   # now: yes
```

---

## 6. Extend the API with a CRD (Lesson 10)

```bash
cat > crd.yaml <<'EOF'
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata: { name: widgets.acme.example.com }
spec:
  group: acme.example.com
  scope: Namespaced
  names: { plural: widgets, singular: widget, kind: Widget }
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties: { size: { type: integer } }
EOF
kubectl apply -f crd.yaml
kubectl apply -f - <<'EOF'
apiVersion: acme.example.com/v1
kind: Widget
metadata: { name: w1 }
spec: { size: 3 }
EOF
kubectl get widgets               # your own new resource type, served like a native one!
```
**Explain:** *"The CRD taught the API a new noun. With a controller watching `widgets`, this
would be an Operator."*

---

## 7. Debugging drills (Lessons 02, 08, + day-2)

```bash
kubectl get pods -A                       # cluster-wide view
kubectl describe pod <pod>                # events at the bottom = why it's stuck
kubectl logs <pod>                        # app logs
kubectl logs <pod> --previous            # logs from the PREVIOUS crash (CrashLoopBackOff!)
kubectl get events --sort-by=.lastTimestamp
kubectl top pods                          # live CPU/mem (needs metrics-server)
```

---

## 8. Clean up

```bash
kind delete cluster --name lab
```

---

## ✅ What to be able to explain after this lab
- The **Deployment → ReplicaSet → Pod** chain, and why deleting a Pod brings a new one back.
- How a **Service + DNS** let you reach Pods by name with load-balancing.
- What a **rolling update** does and how **rollback** is instant.
- How **RBAC** grants the minimum, verified with `kubectl auth can-i`.
- What a **CRD** adds and how it becomes an **Operator** with a controller.

If you can narrate each of those while running the commands, your fundamentals are solid.

➡️ **Next:** [14 — Interview Q&A flashcards](./14_Interview_QA_Flashcards.md)
