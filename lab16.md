# GitHub Actions - Self-Hosted Runner Lab (Java Maven Project)

---

## What We Will Do

```
┌──────────────────────────────────────────────────────────────┐
│              Self-Hosted Runner Flow                         │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1. Fork javadocker repo                                     │
│  2. Setup self-hosted runner on your Linux machine          │
│  3. Install Java and Maven on your Linux machine            │
│  4. Create workflow with runs-on: self-hosted               │
│  5. Push code → workflow runs on YOUR machine               │
│                                                              │
│  GitHub Cloud          Your Linux Machine                    │
│  ┌──────────┐         ┌──────────────────┐                  │
│  │  GitHub  │─────────│  Self-Hosted     │                  │
│  │  Actions │ trigger │  Runner          │                  │
│  │          │────────▶│  mvn build runs  │                  │
│  └──────────┘         └──────────────────┘                  │
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

## Part 2: Install Java and Maven on Your Linux Machine

These must be installed on YOUR machine since it will run the jobs.

```bash
# Update package list
sudo apt-get update

# Install Java 17
sudo apt-get install -y openjdk-17-jdk

# Install Maven
sudo apt-get install -y maven

# Verify installations
java -version
mvn -version
```

```
┌──────────────────────────────────────────────────────────────┐
│              Verify Output                                   │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  java -version:                                              │
│  openjdk version "17.x.x"                                   │
│                                                              │
│  mvn -version:                                               │
│  Apache Maven 3.x.x                                         │
│  Java version: 17.x.x                                       │
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

```
┌──────────────────────────────────────────────────────────────┐
│              actions-runner directory contents               │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  actions-runner/                                             │
│  ├── config.sh    ← configure runner with GitHub repo       │
│  ├── run.sh       ← start runner manually                   │
│  ├── svc.sh       ← install as systemd service              │
│  └── bin/                                                    │
│                                                              │
└──────────────────────────────────────────────────────────────┘
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
# Install as service (auto-start on reboot)
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
│  sudo ./svc.sh status output:                               │
│                                                              │
│  ● actions.runner.<username>.javadocker.service             │
│     Loaded: loaded                                           │
│     Active: active (running) ✅                              │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 5: Verify Runner on GitHub

```
1. Go to your forked repo on GitHub
2. Click Settings → Actions → Runners
3. Runner should show as 'Idle'
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
│  Idle    → ready and waiting for jobs                       │
│  Active  → currently running a job                          │
│  Offline → not connected to GitHub                          │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 6: Clone Forked Repo on Linux Machine

```bash
cd ~

# Clone forked repo
git clone https://github.com/<your-username>/javadocker.git

cd javadocker

# Add upstream
git remote add upstream https://github.com/amitopenwriteup/javadocker.git

# Verify
git remote -v

# Check project files
ls -la
cat pom.xml
```

---

## Part 7: Test Maven Build Locally First

Always verify the build works on your machine before adding to workflow.

```bash
cd ~/javadocker

# Clean and compile
mvn clean compile

# Run tests
mvn test

# Package into jar
mvn clean package

# Verify jar created
ls -lh target/
```

```
┌──────────────────────────────────────────────────────────────┐
│              Maven Build Output                              │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  [INFO] BUILD SUCCESS                                        │
│  [INFO] Total time: XX s                                     │
│  [INFO] Finished at: 2026-03-06T...                         │
│                                                              │
│  target/                                                     │
│  └── javadocker-1.0.jar   ← built jar file ✅               │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 8: Create GitHub Actions Workflow for Self-Hosted

```bash
# Create workflows directory
mkdir -p .github/workflows

# Create the workflow file
vi .github/workflows/self-hosted-maven.yml
```

Add the following content:

```yaml
name: Java Maven CI - Self-Hosted

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

      - name: Verify Java and Maven version
        run: |
          java -version
          mvn -version

      - name: Maven Clean Compile
        run: mvn clean compile

      - name: Maven Run Tests
        run: mvn test

      - name: Maven Package (Create JAR)
        run: mvn clean package -DskipTests

      - name: Show build output
        run: |
          echo "Build completed on self-hosted runner"
          echo "Hostname: $(hostname)"
          echo "User: $(whoami)"
          ls -lh target/
```

```
┌──────────────────────────────────────────────────────────────┐
│           runs-on: self-hosted vs ubuntu-latest              │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  runs-on: ubuntu-latest  → GitHub cloud runner              │
│                             (Java must be installed via step)│
│                                                              │
│  runs-on: self-hosted    → your own Linux machine           │
│                             (Java already installed on it)  │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 9: Commit and Push to Trigger Workflow

```bash
# Stage workflow file
git add .github/workflows/self-hosted-maven.yml

# Check status
git status

# Commit
git commit -m "Add self-hosted Maven CI workflow"

# Push to forked repo
git push origin main
```

---

## Part 10: Verify Workflow Ran on Your Machine

```
1. Go to https://github.com/<your-username>/javadocker
2. Click the 'Actions' tab
3. Click 'Java Maven CI - Self-Hosted' workflow
4. Click the latest run
5. Expand steps to see output from YOUR machine
```

```
┌──────────────────────────────────────────────────────────────┐
│              GitHub Actions UI - Self-Hosted Run             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Java Maven CI - Self-Hosted                                 │
│  ✅  Add self-hosted Maven CI workflow                       │
│       └── build (self-hosted)                               │
│             ├── ✅ Checkout code                             │
│             ├── ✅ Verify Java and Maven version             │
│             │       openjdk version "17.x.x"                │
│             │       Apache Maven 3.x.x                      │
│             ├── ✅ Maven Clean Compile                       │
│             │       [INFO] BUILD SUCCESS                     │
│             ├── ✅ Maven Run Tests                           │
│             ├── ✅ Maven Package (Create JAR)                │
│             └── ✅ Show build output                         │
│                     Hostname: my-linux-runner               │
│                     javadocker-1.0.jar                      │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Full Flow Summary

```
┌──────────────────────────────────────────────────────────────┐
│                  Complete Lab Flow                           │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1.  Fork amitopenwriteup/javadocker                        │
│  2.  Install Java 17 + Maven on Linux machine               │
│  3.  Settings → Actions → Runners → New self-hosted runner  │
│  4.  Download + extract runner on Linux machine             │
│  5.  ./config.sh --url ... --token ...                      │
│  6.  sudo ./svc.sh install && sudo ./svc.sh start           │
│  7.  Runner shows Idle on GitHub ✅                          │
│  8.  Clone forked repo on Linux machine                     │
│  9.  Test mvn build locally                                  │
│  10. Create .github/workflows/self-hosted-maven.yml         │
│  11. git add → git commit → git push                        │
│  12. GitHub triggers workflow → runs on YOUR Linux machine  │
│  13. mvn compile → mvn test → mvn package → jar created ✅  │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---|---|
| `sudo apt-get install -y openjdk-17-jdk` | Install Java 17 |
| `sudo apt-get install -y maven` | Install Maven |
| `java -version` | Verify Java |
| `mvn -version` | Verify Maven |
| `./config.sh --url <url> --token <token>` | Configure runner |
| `sudo ./svc.sh install` | Install runner as service |
| `sudo ./svc.sh start` | Start runner service |
| `sudo ./svc.sh status` | Check runner status |
| `sudo ./svc.sh stop` | Stop runner service |
| `mvn clean compile` | Compile Java source |
| `mvn test` | Run unit tests |
| `mvn clean package` | Build and create JAR |
| `mvn clean package -DskipTests` | Build JAR skip tests |
| `ls target/` | View generated JAR |

---

> **Security Note:** Self-hosted runners on public repositories can be a security risk. Anyone can fork your repo and trigger workflows that run on your machine. Use self-hosted runners with private repositories or add strict branch protection rules.
