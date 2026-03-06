# GitHub Actions - Self-Hosted Runner Lab

---

## What is a Self-Hosted Runner?
A self-hosted runner is your own machine (Linux/Windows/Mac) that runs GitHub Actions workflows instead of GitHub's cloud runners.

```
┌──────────────────────────────────────────────────────────────┐
│         GitHub-Hosted vs Self-Hosted Runner                  │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  GitHub-Hosted:                                              │
│  GitHub ──── runs-on: ubuntu-latest ────▶ GitHub Cloud VM   │
│              (managed by GitHub, fresh every run)           │
│                                                              │
│  Self-Hosted:                                                │
│  GitHub ──── runs-on: self-hosted ──────▶ Your Machine      │
│              (managed by you, persistent environment)       │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 1: Create a New Repository on GitHub

```
1. Go to https://github.com
2. Click '+' → New repository
3. Fill in:
   - Name       : myshellrepo
   - Visibility : Public
   - Do NOT initialize with a README
4. Click 'Create repository'
```

```
┌──────────────────────────────────────────────────────────────┐
│              Create a New Repository                         │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Repository name : [ myshellrepo                          ] │
│  Visibility      :  (•) Public  ( ) Private                 │
│  Initialize README: [ ] leave unchecked                     │
│                                                              │
│              [ Create repository ]                          │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 2: Add Self-Hosted Runner on GitHub

```
1. Go to your repo → https://github.com/<username>/myshellrepo
2. Click 'Settings' tab
3. In left sidebar → Click 'Actions' → 'Runners'
4. Click 'New self-hosted runner'
5. Select:
   - Operating System : Linux
   - Architecture     : x64
6. Follow the commands shown on screen (copied below)
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

## Part 3: Setup Runner on Your Linux Machine

### Step A — Create Runner Directory

```bash
cd ~
mkdir actions-runner
cd actions-runner
```

### Step B — Download the Runner Package

```bash
# Download latest runner (check GitHub UI for latest version)
curl -o actions-runner-linux-x64-2.317.0.tar.gz -L \
  https://github.com/actions/runner/releases/download/v2.317.0/actions-runner-linux-x64-2.317.0.tar.gz

# Extract the package
tar xzf actions-runner-linux-x64-2.317.0.tar.gz

# List extracted files
ls
```

```
┌──────────────────────────────────────────────────────────────┐
│              actions-runner directory                        │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  actions-runner/                                             │
│  ├── config.sh       ← configure runner                     │
│  ├── run.sh          ← start runner manually                │
│  ├── svc.sh          ← install as service                   │
│  └── bin/                                                    │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### Step C — Configure the Runner

```bash
# Copy the config command from GitHub UI
# It looks like this (replace TOKEN with your token from GitHub UI):
./config.sh \
  --url https://github.com/<your-username>/myshellrepo \
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

### Step D — Start the Runner

```bash
# Run manually (foreground — good for testing)
./run.sh
```

```
┌──────────────────────────────────────────────────────────────┐
│              Runner Started Output                           │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  √ Connected to GitHub                                       │
│  Current runner version: 2.317.0                            │
│  Listening for Jobs                                          │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 4: Install Runner as a Service (Persistent)

So the runner starts automatically on reboot.

```bash
# Install as a systemd service
sudo ./svc.sh install

# Start the service
sudo ./svc.sh start

# Check service status
sudo ./svc.sh status

# Stop the service
sudo ./svc.sh stop

# Uninstall the service
sudo ./svc.sh uninstall
```

```
┌──────────────────────────────────────────────────────────────┐
│              Runner as Systemd Service                       │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  sudo ./svc.sh install  → registers as systemd service      │
│  sudo ./svc.sh start    → starts the runner service         │
│  sudo ./svc.sh status   → shows running/stopped             │
│                                                              │
│  Runner now starts automatically on machine reboot ✅        │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 5: Verify Runner on GitHub

```
1. Go to https://github.com/<username>/myshellrepo
2. Click Settings → Actions → Runners
3. You should see your runner listed as 'Idle'
```

```
┌──────────────────────────────────────────────────────────────┐
│              GitHub Runners Page                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Self-hosted runners                                         │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  ● my-linux-runner    Idle   Linux  X64             │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                              │
│  Status:                                                     │
│  Idle    → runner is ready and waiting for jobs             │
│  Active  → runner is currently executing a job              │
│  Offline → runner is not connected                          │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 6: Create Repo and Workflow File

### Initialize local repo

```bash
cd ~
mkdir myshellrepo
cd myshellrepo
git init
git config --global user.name "John Doe"
git config --global user.email "johndoe@example.com"

# Create shell script
mkdir -p scripts
cat > scripts/hello.sh << 'EOF'
#!/bin/bash
echo "==============================="
echo "  Running on Self-Hosted Runner"
echo "==============================="
echo "Hostname: $(hostname)"
echo "User: $(whoami)"
echo "Date: $(date)"
echo "Script executed successfully!"
EOF

chmod +x scripts/hello.sh
```

### Create workflow using self-hosted runner

```bash
mkdir -p .github/workflows
vi .github/workflows/self-hosted.yml
```

Add the following content:

```yaml
name: Self-Hosted Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: self-hosted

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Show repo contents
        run: ls -la

      - name: Run shell script
        run: bash scripts/hello.sh

      - name: Print system info
        run: |
          echo "Hostname: $(hostname)"
          echo "User: $(whoami)"
          echo "OS: $(uname -a)"
          echo "GitHub Actor: $GITHUB_ACTOR"
          echo "Branch: $GITHUB_REF_NAME"
```

```
┌──────────────────────────────────────────────────────────────┐
│           runs-on: self-hosted vs ubuntu-latest              │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  runs-on: ubuntu-latest  → uses GitHub cloud runner         │
│  runs-on: self-hosted    → uses your own machine            │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 7: Push and Trigger Workflow

```bash
# Link to remote
git remote add origin git@github.com:<your-username>/myshellrepo.git
git branch -M main

# Stage and commit all files
git add .
git commit -m "Add self-hosted runner workflow and shell script"

# Push to trigger workflow
git push -u origin main
```

---

## Part 8: Verify Workflow Ran on Self-Hosted Runner

```
1. Go to https://github.com/<username>/myshellrepo
2. Click 'Actions' tab
3. Click the latest workflow run
4. Expand steps to see output from your machine
```

```
┌──────────────────────────────────────────────────────────────┐
│              Actions Tab — Workflow Run                      │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Self-Hosted Pipeline                                        │
│  ✅  Add self-hosted runner workflow                         │
│       └── build (self-hosted)                               │
│             ├── ✅ Checkout code                             │
│             ├── ✅ Show repo contents                        │
│             ├── ✅ Run shell script                          │
│             │       Hostname: my-linux-runner               │
│             │       User: ubuntu                            │
│             │       Script executed successfully!           │
│             └── ✅ Print system info                         │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Full Flow Summary

```
┌──────────────────────────────────────────────────────────────┐
│              Complete Self-Hosted Runner Flow                │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1. Create repo on GitHub (myshellrepo)                     │
│  2. Settings → Actions → Runners → New self-hosted runner   │
│  3. Download + extract runner on your Linux machine         │
│  4. ./config.sh --url ... --token ...                       │
│  5. sudo ./svc.sh install && sudo ./svc.sh start            │
│  6. Runner shows as 'Idle' on GitHub                        │
│  7. Create workflow with runs-on: self-hosted               │
│  8. git push → workflow triggers → runs on your machine     │
│  9. Results visible in GitHub Actions tab                   │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---|---|
| `./config.sh --url <url> --token <token>` | Configure runner |
| `./run.sh` | Start runner manually |
| `sudo ./svc.sh install` | Install runner as service |
| `sudo ./svc.sh start` | Start runner service |
| `sudo ./svc.sh stop` | Stop runner service |
| `sudo ./svc.sh status` | Check runner service status |
| `sudo ./svc.sh uninstall` | Remove runner service |
| `runs-on: self-hosted` | Use self-hosted runner in workflow |

---

> **Security Note:** Self-hosted runners on public repositories can be a security risk. Anyone can fork your repo and trigger workflows that run on your machine. Use self-hosted runners only with private repositories or add strict branch protections.
