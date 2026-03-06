# Assignment - Fork cicdtest & Run GitHub Actions with Dockerfile

---

## Objective

```
┌──────────────────────────────────────────────────────────────┐
│                    Assignment Goal                           │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1. Fork the cicdtest repo                                   │
│  2. Clone it locally                                         │
│  3. Create a GitHub Actions workflow                        │
│  4. Workflow builds Docker image using the Dockerfile        │
│  5. Runs the container                                       │
│  6. Push and verify in Actions tab                          │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Step 1: Fork the Repository

```
1. Go to https://github.com/amitopenwriteup/cicdtest
2. Click 'Fork' button at the top right
3. Select your GitHub account
4. Click 'Create fork'
```

```
┌──────────────────────────────────────────────────────────────┐
│                    Fork the Repo                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Original:  github.com/amitopenwriteup/cicdtest             │
│                          │                                   │
│                       Fork ↓                                │
│                          │                                   │
│  Your Fork: github.com/<your-username>/cicdtest             │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Step 2: Clone Your Forked Repo

```bash
cd ~
git clone https://github.com/<your-username>/cicdtest.git
cd cicdtest

# Verify Dockerfile is present
ls -la
cat Dockerfile
```

---

## Step 3: Create GitHub Actions Workflow

```bash
# Create workflows directory
mkdir -p .github/workflows

# Create the workflow file
vi .github/workflows/docker-ci.yml
```

Add the following content:

```yaml
name: CICD Test - Docker Build and Run

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Verify Docker version
        run: docker --version

      - name: Build Docker image using Dockerfile
        run: docker build -t cicdtest:latest .

      - name: List Docker images
        run: docker images

      - name: Run Docker container
        run: docker run --rm cicdtest:latest
```

---

## Step 4: Commit and Push

```bash
git add .github/workflows/docker-ci.yml
git commit -m "Add GitHub Actions workflow for Docker build and run"
git push origin main
```

---

## Step 5: Verify on GitHub

```
1. Go to https://github.com/<your-username>/cicdtest
2. Click 'Actions' tab
3. Click 'CICD Test - Docker Build and Run'
4. Expand each step to verify all pass ✅
```

```
┌──────────────────────────────────────────────────────────────┐
│              GitHub Actions UI                               │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  CICD Test - Docker Build and Run                           │
│  ✅  Add GitHub Actions workflow                             │
│       └── build (ubuntu-latest)                             │
│             ├── ✅ Checkout code                             │
│             ├── ✅ Verify Docker version                     │
│             ├── ✅ Build Docker image using Dockerfile       │
│             ├── ✅ List Docker images                        │
│             └── ✅ Run Docker container                      │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Full Flow Summary

```
┌──────────────────────────────────────────────────────────────┐
│                  Assignment Flow                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1. Fork amitopenwriteup/cicdtest                           │
│  2. Clone forked repo locally                               │
│  3. Verify Dockerfile is in the repo                        │
│  4. Create .github/workflows/docker-ci.yml                  │
│  5. git add → commit → push                                 │
│  6. GitHub triggers workflow on ubuntu-latest runner        │
│  7. docker build -t cicdtest:latest .                       │
│  8. docker run --rm cicdtest:latest                         │
│  9. All steps pass in Actions tab ✅                         │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

> **Note:** GitHub managed runners (ubuntu-latest) have Docker pre-installed. The Dockerfile already exists in the repo — just fork, add the workflow, and push.
