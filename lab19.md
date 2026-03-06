# Kind - Kubernetes in Docker Setup Lab

---

## Login


---

## Delete Existing Cluster (if any)

```bash
kind delete cluster
```

---

## Part 1: Install Kind

```bash
#!/bin/bash
# For AMD64 / x86_64
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x ./kind
sudo cp ./kind /usr/local/bin/kind
rm -rf kind
```

### Verify Installation

```bash
kind --version
```

```
┌──────────────────────────────────────────────────────────────┐
│              kind --version output                           │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  [amit@test-kubernetes]# kind --version                      │
│  kind version 0.20.0                                         │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 2: Bring Up a Multi-Node Cluster

### Create config.yaml

```bash
vi config.yaml
```

Add the following content:

```yaml
# 4 node (3 workers) cluster config
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  image: kindest/node:v1.28.0
- role: worker
  image: kindest/node:v1.28.0
- role: worker
  image: kindest/node:v1.28.0
- role: worker
  image: kindest/node:v1.28.0
```

```
┌──────────────────────────────────────────────────────────────┐
│              Cluster Layout                                  │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌─────────────────────┐                                     │
│  │   control-plane     │  ← manages the cluster             │
│  └─────────────────────┘                                     │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                     │
│  │ worker 1 │ │ worker 2 │ │ worker 3 │  ← run workloads    │
│  └──────────┘ └──────────┘ └──────────┘                     │
│                                                              │
│  All nodes: kindest/node:v1.28.0                            │
└──────────────────────────────────────────────────────────────┘
```

### Start the Cluster

```bash
kind create cluster --config=config.yaml
```

```
┌──────────────────────────────────────────────────────────────┐
│              kind create cluster output                      │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Creating cluster "kind" ...                                 │
│   ✓ Ensuring node image (kindest/node:v1.28.0) 🖼            │
│   ✓ Preparing nodes 📦 📦 📦                                  │
│   ✓ Writing configuration 📜                                 │
│   ✓ Starting control-plane 🕹️                                │
│   ✓ Installing CNI 🔌                                        │
│   ✓ Installing StorageClass 💾                               │
│   ✓ Joining worker nodes 🚜                                  │
│                                                              │
│  Set kubectl context to "kind-kind"                         │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 3: Check Cluster Status

### Using Kind

```bash
kind get clusters
```

```
[amit@test-kubernetes]# kind get clusters
kind
```

### Using kubectl

```bash
kubectl cluster-info
```

```
[amit@test-kubernetes]# kubectl cluster-info
Kubernetes control plane is running at https://127.0.0.1:41273
CoreDNS is running at https://127.0.0.1:41273/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

---

## Part 4: Check Node Status

```bash
kubectl get nodes
```

```
┌──────────────────────────────────────────────────────────────┐
│              kubectl get nodes output                        │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  NAME                 STATUS   ROLES           AGE   VERSION │
│  kind-control-plane   Ready    control-plane   22m   v1.28.0 │
│  kind-worker          Ready    <none>          21m   v1.28.0 │
│  kind-worker2         Ready    <none>          21m   v1.28.0 │
│  kind-worker3         Ready    <none>          21m   v1.28.0 │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 5: Check Kubernetes Version

```bash
kubectl version
```

```
[amit@test-kubernetes]# kubectl version
Client Version: v1.28.3
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: v1.28.0
```

```
┌──────────────────────────────────────────────────────────────┐
│              Version Info                                    │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Client Version : v1.28.3  ← kubectl on your machine        │
│  Server Version : v1.28.0  ← cluster running inside Kind    │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 6: Install Helm

```bash
wget https://get.helm.sh/helm-v3.10.2-linux-amd64.tar.gz
tar -zxvf helm-v3.10.2-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
```

### Verify Helm Installation

```bash
helm version
```

```
┌──────────────────────────────────────────────────────────────┐
│              helm version output                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  version.BuildInfo{                                          │
│    Version:"v3.10.2",                                        │
│    GitCommit:"...",                                          │
│    GoVersion:"go1.18.8"                                      │
│  }                                                           │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---|---|
| `kind --version` | Check Kind version |
| `kind create cluster --config=config.yaml` | Create cluster from config |
| `kind get clusters` | List all Kind clusters |
| `kind delete cluster` | Delete the cluster |
| `kubectl cluster-info` | Show cluster endpoint info |
| `kubectl get nodes` | List all nodes and status |
| `kubectl version` | Show client and server versions |
| `helm version` | Check Helm version |
