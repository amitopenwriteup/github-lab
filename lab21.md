# Docker CI/CD Lab Guide - javadocker

---

## Pipeline Overview

```
┌──────────────────────────────────────────────────────────────┐
│                  3-Stage Pipeline                            │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Stage 1: build                                              │
│    → triggered on every push / pull_request                 │
│    → docker build + smoke test                              │
│                                                              │
│  Stage 2: publish  (workflow_dispatch only)                  │
│    → login to Docker Hub                                    │
│    → docker push image                                      │
│                                                              │
│  Stage 3: deploy-kubernetes  (workflow_dispatch only)        │
│    → load image into KIND cluster                           │
│    → kubectl apply deployment                               │
│    → verify pods running                                    │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 1: Fork the Repository

```
1. Go to https://github.com/amitopenwriteup/javadocker
2. Click 'Fork' button at the top right
3. Select your GitHub account
4. Click 'Create fork'
```

```
┌──────────────────────────────────────────────────────────────┐
│                    Fork the Repo                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Original:  github.com/amitopenwriteup/javadocker           │
│                          │                                   │
│                       Fork ↓                                │
│                          │                                   │
│  Your Fork: github.com/<your-username>/javadocker           │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 2: Clone Your Forked Repo

```bash
cd ~
git clone https://github.com/<your-username>/javadocker.git
cd javadocker

# Verify files
ls -la
cat Dockerfile
```

---

## Part 3: Add GitHub Secrets

Secrets are used to store Docker Hub credentials securely.

```
1. Go to your forked repo on GitHub
2. Click 'Settings' tab
3. Left sidebar → 'Secrets and variables' → 'Actions'
4. Click 'New repository secret'
5. Add the following two secrets:
```

```
┌──────────────────────────────────────────────────────────────┐
│         Settings → Secrets and variables → Actions          │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Secret 1:                                                   │
│  Name  : DOCKER_USERNAME                                     │
│  Value : <your Docker Hub username>                         │
│                                                              │
│  Secret 2:                                                   │
│  Name  : DOCKER_PASSWORD                                     │
│  Value : <your Docker Hub password or access token>         │
│                                                              │
│  Click 'Add secret' after each one ✅                        │
└──────────────────────────────────────────────────────────────┘
```

```
┌──────────────────────────────────────────────────────────────┐
│              Navigate to Secrets                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Settings                                                    │
│       └── Secrets and variables                             │
│                  └── Actions                                │
│                         └── New repository secret          │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 4: Create the Workflow File

```bash
mkdir -p .github/workflows
vi .github/workflows/docker-ci.yml
```

Add the following content:

```yaml
name: Docker CI
on:
  push:
    branches: ["**"]
  pull_request:
    branches: ["**"]
  workflow_dispatch:
env:
  IMAGE_NAME: "my-docker-app"
  IMAGE_VERSION: "1.0.0"
  DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
  KIND_CLUSTER_NAME: "kind"
  KUBE_NAMESPACE: "default"
jobs:
  build:
    name: Build Docker Image
    runs-on: [self-hosted, linux]
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Build Docker image
        run: |
          echo "Building image: ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_VERSION }}"
          docker build \
            --tag ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_VERSION }} \
            --tag ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:latest \
            .

      - name: Smoke test
        run: |
          echo "Starting container for smoke test..."
          CONTAINER_ID=$(docker run -d ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_VERSION }})
          sleep 5
          docker ps | grep $CONTAINER_ID
          docker stop $CONTAINER_ID
          docker rm $CONTAINER_ID
          echo "Smoke test passed."

  publish:
    name: Publish to Docker Hub
    runs-on: [self-hosted, linux]
    needs: build
    if: github.event_name == 'workflow_dispatch'
    steps:
      - name: Log in to Docker Hub
        run: |
          echo "${{ secrets.DOCKER_PASSWORD }}" | docker login \
            -u ${{ secrets.DOCKER_USERNAME }} --password-stdin

      - name: Push Docker image
        run: |
          echo "Pushing ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_VERSION }}"
          docker push ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_VERSION }}
          docker push ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:latest

      - name: Docker logout
        if: always()
        run: docker logout

      - name: Deployment summary
        run: |
          echo "=========================================="
          echo "Image   : ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}"
          echo "Version : ${{ env.IMAGE_VERSION }}"
          echo "Status  : Published to Docker Hub"
          echo "=========================================="

  deploy-kubernetes:
    name: Deploy to KIND Kubernetes
    runs-on: [self-hosted, linux]
    needs: publish
    if: github.event_name == 'workflow_dispatch'
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Verify image exists locally
        run: |
          echo "Checking image is present locally..."
          docker images | grep ${{ env.IMAGE_NAME }}
          if ! docker image inspect ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_VERSION }} > /dev/null 2>&1; then
            echo "Image not found locally, pulling from Docker Hub..."
            echo "${{ secrets.DOCKER_PASSWORD }}" | docker login \
              -u ${{ secrets.DOCKER_USERNAME }} --password-stdin
            docker pull ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_VERSION }}
            docker logout
          else
            echo "Image found locally, skipping pull."
          fi

      - name: Check KIND cluster is running
        run: |
          echo "Checking KIND cluster..."
          kind get clusters
          kubectl cluster-info --context kind-${{ env.KIND_CLUSTER_NAME }}
          kubectl get nodes

      - name: Load image into KIND cluster
        run: |
          echo "Loading image into KIND cluster..."
          kind load docker-image \
            ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_VERSION }} \
            --name ${{ env.KIND_CLUSTER_NAME }}
          echo "Image loaded into KIND successfully."

      - name: Set image in deployment manifest
        run: |
          sed -i "s|DOCKER_USERNAME|${{ env.DOCKER_USERNAME }}|g" deployment.yml
          echo "Updated deployment.yml:"
          cat deployment.yml

      - name: Apply Kubernetes deployment
        run: |
          echo "Applying deployment to namespace: ${{ env.KUBE_NAMESPACE }}"
          kubectl apply -f deployment.yml --namespace=${{ env.KUBE_NAMESPACE }}

      - name: Wait for rollout
        run: |
          echo "Waiting for deployment rollout..."
          kubectl rollout status deployment/${{ env.IMAGE_NAME }} \
            --namespace=${{ env.KUBE_NAMESPACE }} \
            --timeout=120s

      - name: Verify pods are running
        run: |
          echo "Checking pod status..."
          kubectl get pods --namespace=${{ env.KUBE_NAMESPACE }} \
            -l app=${{ env.IMAGE_NAME }}
          kubectl get service --namespace=${{ env.KUBE_NAMESPACE }} \
            -l app=${{ env.IMAGE_NAME }}

      - name: Clean up local images
        if: always()
        run: |
          docker rmi ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_VERSION }} || true
          docker rmi ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:latest || true

      - name: Deployment summary
        run: |
          echo "=========================================="
          echo "Cluster   : ${{ env.KIND_CLUSTER_NAME }}"
          echo "Namespace : ${{ env.KUBE_NAMESPACE }}"
          echo "Image     : ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_VERSION }}"
          echo "Replicas  : 2"
          echo "Status    : Deployed to KIND successfully"
          echo "=========================================="
```

---

## Part 5: Workflow Explained

```
┌──────────────────────────────────────────────────────────────┐
│              Stage 1: build (every push)                     │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  docker build --tag <user>/my-docker-app:1.0.0 .            │
│       ↓                                                      │
│  docker run -d <image>   ← start container                  │
│  sleep 5                 ← wait                             │
│  docker ps | grep ID     ← verify running                   │
│  docker stop + rm        ← cleanup                          │
│  Smoke test passed ✅                                        │
│                                                              │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│     Stage 2: publish (workflow_dispatch only)                │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  docker login Docker Hub using DOCKER_PASSWORD secret        │
│       ↓                                                      │
│  docker push <user>/my-docker-app:1.0.0                     │
│  docker push <user>/my-docker-app:latest                    │
│       ↓                                                      │
│  docker logout                                               │
│  Image published to Docker Hub ✅                            │
│                                                              │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│  Stage 3: deploy-kubernetes (workflow_dispatch only)         │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  kind get clusters        ← verify KIND is running          │
│  kubectl get nodes        ← check nodes                     │
│       ↓                                                      │
│  kind load docker-image   ← load image into KIND            │
│       ↓                                                      │
│  kubectl apply -f deployment.yml                            │
│  kubectl rollout status   ← wait for pods                   │
│  kubectl get pods         ← verify running                  │
│  docker rmi               ← cleanup local images            │
│  Deployed to KIND ✅                                         │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 6: Commit and Push

```bash
git add .github/workflows/docker-ci.yml
git commit -m "Add Docker CI workflow with build, publish and deploy stages"
git push origin main
```

---

## Part 7: Trigger the Pipeline

### Auto trigger (build only)
```bash
# Any push triggers the build + smoke test stage automatically
git push origin main
```

### Manual trigger (build + publish + deploy)
```
1. Go to https://github.com/<your-username>/javadocker
2. Click 'Actions' tab
3. Click 'Docker CI' in left sidebar
4. Click 'Run workflow' button
5. Select branch: main
6. Click 'Run workflow'
```

```
┌──────────────────────────────────────────────────────────────┐
│              workflow_dispatch trigger                       │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Actions tab                                                 │
│       └── Docker CI                                         │
│                └── Run workflow ← click this                │
│                       └── branch: main                      │
│                              └── Run workflow               │
│                                                              │
│  This runs all 3 stages: build + publish + deploy ✅         │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 8: Verify on GitHub

```
1. Go to https://github.com/<your-username>/javadocker
2. Click 'Actions' tab
3. Click the latest workflow run
4. Expand each job and step to see output
```

```
┌──────────────────────────────────────────────────────────────┐
│              GitHub Actions UI                               │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Docker CI                                                   │
│  ✅  Add Docker CI workflow                                  │
│       ├── ✅ Build Docker Image                              │
│       │      ├── ✅ Checkout repository                      │
│       │      ├── ✅ Build Docker image                       │
│       │      └── ✅ Smoke test                               │
│       ├── ✅ Publish to Docker Hub                           │
│       │      ├── ✅ Log in to Docker Hub                     │
│       │      ├── ✅ Push Docker image                        │
│       │      ├── ✅ Docker logout                            │
│       │      └── ✅ Deployment summary                       │
│       └── ✅ Deploy to KIND Kubernetes                       │
│              ├── ✅ Checkout repository                      │
│              ├── ✅ Verify image exists locally              │
│              ├── ✅ Check KIND cluster is running            │
│              ├── ✅ Load image into KIND cluster             │
│              ├── ✅ Set image in deployment manifest         │
│              ├── ✅ Apply Kubernetes deployment              │
│              ├── ✅ Wait for rollout                         │
│              ├── ✅ Verify pods are running                  │
│              ├── ✅ Clean up local images                    │
│              └── ✅ Deployment summary                       │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Item | Value |
|---|---|
| `IMAGE_NAME` | `my-docker-app` |
| `IMAGE_VERSION` | `1.0.0` |
| `KIND_CLUSTER_NAME` | `kind` |
| `KUBE_NAMESPACE` | `default` |
| `DOCKER_USERNAME` secret | Your Docker Hub username |
| `DOCKER_PASSWORD` secret | Your Docker Hub password/token |
| Build trigger | Every push and pull_request |
| Publish + Deploy trigger | `workflow_dispatch` only |
| Runner | `self-hosted, linux` |

---

> **Note:** Stages 2 and 3 (publish + deploy) only run when triggered manually via `workflow_dispatch`. Every push to any branch automatically runs Stage 1 (build + smoke test) only.
