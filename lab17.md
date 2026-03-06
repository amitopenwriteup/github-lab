# GitHub Actions Lab - Fork javadocker & Build with Dockerfile

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

# Verify files including Dockerfile
ls -la
cat Dockerfile
```

---

## Part 3: Create GitHub Actions Workflow

```bash
# Create workflows directory
mkdir -p .github/workflows

# Create the workflow file
vi .github/workflows/docker-build-run.yml
```

Add the following content:

```yaml
name: Docker Build and Run

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
        run: docker build -t javadocker:latest .

      - name: List Docker images
        run: docker images

      - name: Run Docker container
        run: docker run --rm javadocker:latest
```

---

## Part 4: Commit and Push

```bash
git add .github/workflows/docker-build-run.yml
git commit -m "Add GitHub Actions workflow to build and run Dockerfile"
git push origin main
```

---

## Part 5: Verify on GitHub

```
1. Go to https://github.com/<your-username>/javadocker
2. Click 'Actions' tab
3. Click 'Docker Build and Run' workflow
4. Expand each step to see output
```

```
┌──────────────────────────────────────────────────────────────┐
│              GitHub Actions UI                               │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Docker Build and Run                                        │
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
│                  Complete Flow                               │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1. Fork amitopenwriteup/javadocker                         │
│  2. Clone forked repo locally                               │
│  3. Create .github/workflows/docker-build-run.yml           │
│  4. git add → commit → push                                 │
│  5. GitHub triggers workflow on ubuntu-latest runner        │
│  6. Runner checks out code (Dockerfile already in repo)     │
│  7. docker build -t javadocker:latest .                     │
│  8. docker run --rm javadocker:latest                       │
│  9. Results shown in Actions tab ✅                          │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

> **Note:** GitHub managed runners (ubuntu-latest) have Docker pre-installed. No additional setup needed — just checkout the code and run `docker build`.
