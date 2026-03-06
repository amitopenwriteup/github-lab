# GitHub Actions Lab - Self-Hosted Runner with Dockerfile

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

## Part 2: Install Docker on Your Linux Machine

```bash
# Update package list
sudo apt-get update

# Install Docker
sudo apt-get install -y docker.io

# Start Docker service
sudo systemctl start docker
sudo systemctl enable docker

# Add current user to docker group (avoid sudo for docker)
sudo usermod -aG docker $USER

# Apply group change (or logout and login again)
newgrp docker

# Verify Docker is installed
docker --version
docker ps
```

```
┌──────────────────────────────────────────────────────────────┐
│              Verify Docker Installation                      │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  docker --version:                                           │
│  Docker version 24.x.x, build xxxxx                         │
│                                                              │
│  docker ps:                                                  │
│  CONTAINER ID   IMAGE   COMMAND   STATUS   PORTS            │
│  (empty — no containers running yet)                        │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 3: Add Self-Hosted Runner on GitHub

```
1. Go to your forked repo:
   https://github.com/<your-username>/javadocker
2. Click 'Settings' tab
3. Left sidebar → Click 'Actions' → 'Runners'
4. Click 'New self-hosted runner'
5. Select:
   - Operating System : Linux
   - Architecture     : x64
6. Follow the commands shown on the page
```

```
┌──────────────────────────────────────────────────────────────┐
│         GitHub → Settings → Actions → Runners               │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Settings                                                    │
│       └── Actions                                           │
│              └── Runners                                    │
│                     └── New self-hosted runner              │
│                                                              │
│  OS: (•) Linux   ( ) Windows   ( ) macOS                    │
│  Architecture: (•) x64                                      │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 4: Setup Runner on Your Linux Machine

### Step A — Create Runner Directory

```bash
cd ~
mkdir actions-runner
cd actions-runner
```

### Step B — Download Runner Package

```bash
# Download latest runner (copy exact command from GitHub UI)
curl -o actions-runner-linux-x64-2.317.0.tar.gz -L \
  https://github.com/actions/runner/releases/download/v2.317.0/actions-runner-linux-x64-2.317.0.tar.gz

# Extract
tar xzf actions-runner-linux-x64-2.317.0.tar.gz

# List files
ls
```

### Step C — Configure Runner

```bash
# Copy the config command from GitHub UI
# Replace URL and TOKEN with values shown on GitHub page
./config.sh \
  --url https://github.com/<your-username>/javadocker \
  --token <YOUR_RUNNER_TOKEN>
```

```
┌──────────────────────────────────────────────────────────────┐
│              config.sh prompts                               │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Enter the name of runner: [hostname]   ← press Enter       │
│  Enter any additional labels:           ← press Enter       │
│  Enter name of work folder: [_work]     ← press Enter       │
│                                                              │
│  ✅ Runner successfully added                                │
└──────────────────────────────────────────────────────────────┘
```

### Step D — Install Runner as Systemd Service

```bash
# Install as service
sudo ./svc.sh install

# Start the service
sudo ./svc.sh start

# Check status
sudo ./svc.sh status
```

```
┌──────────────────────────────────────────────────────────────┐
│              Runner Service Status                           │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Active: active (running) ✅                                 │
│  Listening for Jobs...                                       │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 5: Verify Runner on GitHub

```
1. Go to Settings → Actions → Runners
2. Runner should show as 'Idle'
```

```
┌──────────────────────────────────────────────────────────────┐
│              GitHub Runners Page                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Self-hosted runners                                         │
│  ┌──────────────────────────────────────────────────┐       │
│  │  ● my-linux-runner    Idle   Linux  X64          │       │
│  └──────────────────────────────────────────────────┘       │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 6: Clone Forked Repo on Your Linux Machine

```bash
cd ~
git clone https://github.com/<your-username>/javadocker.git
cd javadocker

# Verify Dockerfile is present
ls -la
cat Dockerfile
```

---

## Part 7: Create GitHub Actions Workflow

```bash
# Create workflows directory
mkdir -p .github/workflows

# Create the workflow file
vi .github/workflows/self-hosted-docker.yml
```

Add the following content:

```yaml
name: Docker Build and Run - Self-Hosted

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]

jobs:
  build:
    runs-on: self-hosted

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Verify Docker version
        run: docker --version

      - name: Build Docker image using Dockerfile
        run: docker build -t javadocker:latest .

      - name: List Docker images
        run: docker images

      - name: Run Docker container
        run: docker run --rm javadocker:latest

      - name: Show runner info
        run: |
          echo "Hostname : $(hostname)"
          echo "User     : $(whoami)"
          echo "Branch   : $GITHUB_REF_NAME"
          echo "Actor    : $GITHUB_ACTOR"
```

---

## Part 8: Commit and Push

```bash
git add .github/workflows/self-hosted-docker.yml
git commit -m "Add self-hosted Docker build and run workflow"
git push origin main
```

---

## Part 9: Verify Workflow on GitHub

```
1. Go to https://github.com/<your-username>/javadocker
2. Click 'Actions' tab
3. Click 'Docker Build and Run - Self-Hosted'
4. Expand each step to see output from YOUR machine
```

```
┌──────────────────────────────────────────────────────────────┐
│              GitHub Actions UI - Self-Hosted Run             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Docker Build and Run - Self-Hosted                         │
│  ✅  Add self-hosted Docker build and run workflow           │
│       └── build (self-hosted)                               │
│             ├── ✅ Checkout code                             │
│             ├── ✅ Verify Docker version                     │
│             │       Docker version 24.x.x                   │
│             ├── ✅ Build Docker image using Dockerfile       │
│             │       Successfully built xxxxxxx              │
│             │       Successfully tagged javadocker:latest   │
│             ├── ✅ List Docker images                        │
│             │       javadocker   latest   xxxxxxx           │
│             ├── ✅ Run Docker container                      │
│             └── ✅ Show runner info                          │
│                     Hostname: my-linux-runner               │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Full Flow Summary

```
┌──────────────────────────────────────────────────────────────┐
│                  Complete Flow                               │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1.  Fork amitopenwriteup/javadocker                        │
│  2.  Install Docker on your Linux machine                   │
│  3.  Settings → Actions → Runners → New self-hosted runner  │
│  4.  Download + configure + start runner as service         │
│  5.  Runner shows Idle on GitHub ✅                          │
│  6.  Clone forked repo on Linux machine                     │
│  7.  Create .github/workflows/self-hosted-docker.yml        │
│  8.  git add → commit → push                                │
│  9.  GitHub triggers workflow → runs on YOUR Linux machine  │
│  10. docker build → docker run on your machine ✅            │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---|---|
| `sudo apt-get install -y docker.io` | Install Docker |
| `sudo systemctl start docker` | Start Docker service |
| `sudo usermod -aG docker $USER` | Add user to docker group |
| `docker --version` | Verify Docker |
| `./config.sh --url <url> --token <token>` | Configure runner |
| `sudo ./svc.sh install` | Install runner as service |
| `sudo ./svc.sh start` | Start runner service |
| `sudo ./svc.sh status` | Check runner status |
| `docker build -t javadocker:latest .` | Build Docker image |
| `docker run --rm javadocker:latest` | Run and remove container |
| `docker images` | List all images |

---

> **Note:** Unlike GitHub managed runners, Docker is NOT pre-installed on self-hosted runners. You must install it manually before the runner can execute docker commands.
