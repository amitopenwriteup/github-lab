# GitHub Actions - Shell Script Trigger Lab

---

## What is GitHub Actions?
GitHub Actions automates workflows triggered by events (push, pull_request, etc.).
As shown in the diagram — when an event fires, the workflow starts, a job executes, steps run, and results are reported.

```
┌──────────────────────────────────────────────────────────────┐
│              GitHub Actions Workflow Structure               │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1  Event Triggered    ← push / pull_request / schedule     │
│          │                                                   │
│  2  Workflow Starts    ← .github/workflows/ci.yml           │
│          │                                                   │
│  3  Job Executes       ← runs-on: ubuntu-latest             │
│          │                                                   │
│  4  Steps Run          ← checkout, setup, run script        │
│          │                                                   │
│  5  Results Reported   ← pass / fail shown in GitHub UI     │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Setup: Navigate to myshellrepo

```bash
cd ~
cd myshellrepo
```

---

## Part 1: Create the Shell Script

```bash
# Create scripts directory
mkdir -p scripts

# Create the shell script
vi scripts/hello.sh
```

Add the following content:

```bash
#!/bin/bash

echo "==============================="
echo "  GitHub Actions Shell Script  "
echo "==============================="
echo "Running on: $(uname -a)"
echo "Current directory: $(pwd)"
echo "Date: $(date)"
echo "Script executed successfully!"
```

```bash
# Make the script executable
chmod +x scripts/hello.sh

# Test the script locally
bash scripts/hello.sh
```

```
┌──────────────────────────────────────────────────────────────┐
│              scripts/hello.sh output                         │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ===============================                             │
│    GitHub Actions Shell Script                               │
│  ===============================                             │
│  Running on: Linux ubuntu-latest ...                         │
│  Current directory: /home/runner/work/myshellrepo             │
│  Date: Fri Mar  6 10:00:00 UTC 2026                         │
│  Script executed successfully!                               │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 2: Create the GitHub Actions Workflow

```bash
# Create the workflows directory
mkdir -p .github/workflows

# Create the workflow file
vi .github/workflows/run-script.yml
```

Add the following content:

```yaml
name: CI Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Show repo contents
        run: ls -la

      - name: Run shell script
        run: bash scripts/hello.sh

      - name: Print environment info
        run: |
          echo "Runner OS: $RUNNER_OS"
          echo "GitHub Actor: $GITHUB_ACTOR"
          echo "GitHub Repo: $GITHUB_REPOSITORY"
          echo "Branch: $GITHUB_REF_NAME"
```

```
┌──────────────────────────────────────────────────────────────┐
│              Workflow File Structure                         │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  myshellrepo/                                                  │
│  ├── .github/                                                │
│  │   └── workflows/                                          │
│  │       └── run-script.yml   ← workflow file               │
│  ├── scripts/                                                │
│  │   └── hello.sh             ← shell script                │
│  └── README.md                                               │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 3: Commit and Push to GitHub

```bash
# Stage all new files
git add .

# Check what is staged
git status

# Commit
git commit -m "Add GitHub Actions workflow and shell script"

# Push to remote
git push origin main
```

---

## Part 4: Verify on GitHub

```
1. Go to https://github.com/<your-username>/myshellrepo
2. Click the 'Actions' tab
3. You will see 'CI Pipeline' workflow listed
4. Click on the latest run to see details
5. Expand each step to see output
```

```
┌──────────────────────────────────────────────────────────────┐
│              GitHub Actions UI                               │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Actions Tab:                                                │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  CI Pipeline                                        │    │
│  │  ✅  Add GitHub Actions workflow and shell script   │    │
│  │       └── build (ubuntu-latest)                    │    │
│  │             ├── ✅ Checkout code                   │    │
│  │             ├── ✅ Show repo contents               │    │
│  │             ├── ✅ Run shell script                 │    │
│  │             └── ✅ Print environment info           │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 5: Trigger Workflow on Every Push

Any push to `main` or `develop` will now automatically trigger the workflow.

```bash
# Make a change and push to trigger workflow again
echo "Trigger workflow" >> README.md
git add .
git commit -m "Trigger GitHub Actions workflow"
git push origin main
```

```
┌──────────────────────────────────────────────────────────────┐
│                  Trigger Flow                                │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git push origin main                                        │
│          │                                                   │
│          ▼                                                   │
│  GitHub detects push event                                   │
│          │                                                   │
│          ▼                                                   │
│  Workflow: CI Pipeline starts                                │
│          │                                                   │
│          ▼                                                   │
│  Job: build (ubuntu-latest runner)                          │
│          │                                                   │
│          ▼                                                   │
│  Steps run → hello.sh executes                              │
│          │                                                   │
│          ▼                                                   │
│  Results reported in Actions tab ✅                          │
└──────────────────────────────────────────────────────────────┘
```

---

## Workflow Keywords Explained

```
┌──────────────────────────────────────────────────────────────┐
│              Workflow YAML Keywords                          │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  name        → workflow display name in GitHub UI           │
│  on          → events that trigger the workflow             │
│  push        → triggered on git push                        │
│  pull_request→ triggered on PR creation                     │
│  branches    → which branches trigger the workflow          │
│  jobs        → collection of jobs to run                    │
│  build       → job name (can be any name)                   │
│  runs-on     → type of runner (ubuntu, windows, macos)      │
│  steps       → list of tasks in a job                       │
│  name        → step display name                            │
│  uses        → run a pre-built GitHub Action                │
│  run         → run a shell command or script                │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Keyword | Description |
|---|---|
| `on: push` | Trigger on push event |
| `on: pull_request` | Trigger on pull request |
| `on: schedule` | Trigger on cron schedule |
| `runs-on: ubuntu-latest` | Use Ubuntu runner |
| `uses: actions/checkout@v4` | Checkout repo code |
| `run: bash scripts/hello.sh` | Run shell script |
| `$GITHUB_ACTOR` | GitHub username who triggered |
| `$GITHUB_REPOSITORY` | Repo name |
| `$GITHUB_REF_NAME` | Branch name |
| `$RUNNER_OS` | Runner operating system |

---

## Common Triggers

| Trigger | Description |
|---|---|
| `push` | On every push to branch |
| `pull_request` | On PR open/update |
| `workflow_dispatch` | Manual trigger from UI |
| `schedule: cron` | Run on schedule |
| `release` | On new release created |

---

> **Tip:** The workflow file must be placed exactly at `.github/workflows/<filename>.yml` inside your repository. GitHub automatically detects and runs any `.yml` files in that directory.
