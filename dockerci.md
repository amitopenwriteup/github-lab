#  Docker CI — GitHub Actions Workflow

The workflow requires two secrets to authenticate with Docker Hub. Follow the steps below to add them to your repository.

### Required Secrets

| Secret Name       | Description                        |
|-------------------|------------------------------------|
| `DOCKER_USERNAME` | Your Docker Hub username           |
| `DOCKER_PASSWORD` | Your Docker Hub password or token  |

---

### Step-by-Step: Adding Secrets to GitHub

#### Step 1 — Open Repository Settings
1. Navigate to your GitHub repository.
2. Click the **Settings** tab (top navigation bar).

#### Step 2 — Go to Secrets
1. In the left sidebar, expand **Security**.
2. Click **Secrets and variables** → **Actions**.

#### Step 3 — Add `DOCKER_USERNAME`
1. Click **New repository secret**.
2. Fill in:
   - **Name:** `DOCKER_USERNAME`
   - **Secret:** *(your Docker Hub username, e.g. `johndoe`)*
3. Click **Add secret**.

#### Step 4 — Add `DOCKER_PASSWORD`
1. Click **New repository secret** again.
2. Fill in:
   - **Name:** `DOCKER_PASSWORD`
   - **Secret:** *(your Docker Hub password **or** an access token — see below)*
3. Click **Add secret**.

---

### 🔑 Using a Docker Hub Access Token (Recommended)

Instead of using your real password, generate a dedicated **Access Token** on Docker Hub for better security:

1. Log in to [hub.docker.com](https://hub.docker.com).
2. Click your avatar (top right) → **Account Settings**.
3. Navigate to **Security** → **Access Tokens**.
4. Click **New Access Token**.
5. Enter a description (e.g. `github-actions`) and set permissions to **Read, Write, Delete**.
6. Click **Generate** and **copy the token immediately** (it won't be shown again).
7. Paste this token as the value for the `DOCKER_PASSWORD` secret in GitHub.

> ✅ Access tokens can be revoked at any time without changing your account password.

---


A GitHub Actions workflow to **build** a Docker image on every push/PR and **publish** it to Docker Hub only on manual trigger (`workflow_dispatch`).

---

##install docker
```
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
```

```
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

```

```
sudo usermod -a -G docker user
sudo chown user:docker /var/run/docker.sock
sudo chmod 660 /var/run/docker.sock
```

## 📁 Workflow File

**Path:** `.github/workflows/docker-ci.yml`

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

jobs:
  build:
    name: Build Docker Image
    runs-on: self-hosted
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

  publish:
    name: Publish to Docker Hub
    runs-on: self-hosted
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

      - name: Clean up local images
        if: always()
        run: |
          docker rmi ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:${{ env.IMAGE_VERSION }} || true
          docker rmi ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}:latest || true
          docker logout

      - name: Deployment summary
        run: |
          echo "=========================================="
          echo "Image   : ${{ env.DOCKER_USERNAME }}/${{ env.IMAGE_NAME }}"
          echo "Version : ${{ env.IMAGE_VERSION }}"
          echo "Status  : Published to Docker Hub"
          echo "=========================================="
```

---

## 🔐 Setting Up GitHub Secrets


## ⚙️ Workflow Overview

```
Push / Pull Request
        │
        ▼
  ┌───────────┐
  │   build   │  ← Runs on every push and PR to any branch
  │           │     Builds image with version + latest tags
  └───────────┘
        │
        │  (only if triggered via workflow_dispatch)
        ▼
  ┌───────────┐
  │  publish  │  ← Logs in to Docker Hub
  │           │     Pushes both tags
  │           │     Cleans up local images
  │           │     Prints deployment summary
  └───────────┘
```

### Trigger Conditions

| Event               | `build` job | `publish` job |
|---------------------|:-----------:|:-------------:|
| `push` (any branch) | ✅ Runs      | ❌ Skipped     |
| `pull_request`      | ✅ Runs      | ❌ Skipped     |
| `workflow_dispatch` | ✅ Runs      | ✅ Runs        |

---

## 🛠️ Customisation

To adapt this workflow to your project, update the `env` block at the top of the YAML file:

```yaml
env:
  IMAGE_NAME: "my-docker-app"    # ← Change to your image name
  IMAGE_VERSION: "1.0.0"         # ← Change to your desired version tag
```

---

## 📋 Prerequisites

- A **self-hosted runner** tagged with `linux` must be registered on your repository or organisation.
- A `Dockerfile` must exist at the **root** of the repository.
- The `DOCKER_USERNAME` and `DOCKER_PASSWORD` secrets must be configured as described above.
