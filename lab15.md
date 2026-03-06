# GitHub Actions Lab - Fork javadocker & Run Maven CI Pipeline

---

## Project Overview

```
┌──────────────────────────────────────────────────────────────┐
│              javadocker Project                              │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Source Repo : github.com/amitopenwriteup/javadocker        │
│  Type        : Java Application                             │
│  Build Tool  : Maven (mvn)                                  │
│                                                              │
│  What GitHub Actions will do:                               │
│  1. Checkout code                                            │
│  2. Setup Java on runner                                     │
│  3. mvn clean install                                        │
│  4. mvn test                                                 │
│  5. mvn package (create jar)                                │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 1: Fork the Repository

```
1. Go to https://github.com/amitopenwriteup/javadocker
2. Click the 'Fork' button at the top right
3. Select your GitHub account as the destination
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
│  You now have full control of your own copy.                │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 2: Clone Your Forked Repo

```bash
cd ~

# Clone your forked repo (replace with your username)
git clone https://github.com/<your-username>/javadocker.git

cd javadocker

# Verify remote
git remote -v

# Add upstream to track original repo
git remote add upstream https://github.com/amitopenwriteup/javadocker.git

# Verify both remotes
git remote -v
```

```
┌──────────────────────────────────────────────────────────────┐
│                  Remote Setup                                │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  origin    → your fork   (push/pull here)                   │
│  upstream  → original repo (fetch updates from here)        │
│                                                              │
│  git remote -v output:                                       │
│  origin    https://github.com/<you>/javadocker (fetch)      │
│  origin    https://github.com/<you>/javadocker (push)       │
│  upstream  https://github.com/amitopenwriteup/javadocker (fetch)
│  upstream  https://github.com/amitopenwriteup/javadocker (push)
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 3: Explore the Project Structure

```bash
# List project files
ls -la

# View Maven build file
cat pom.xml

# Check Java source files
find . -type f -name "*.java"

# View existing workflow files if any
find . -type f -name "*.yml"
```

```
┌──────────────────────────────────────────────────────────────┐
│              Expected Project Structure                      │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  javadocker/                                                 │
│  ├── src/                                                    │
│  │   └── main/java/...    ← Java source code                │
│  │   └── test/java/...    ← Java test code                  │
│  ├── pom.xml              ← Maven build config              │
│  └── README.md                                               │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 4: Install Maven on Linux (if not installed)

```bash
# Check if Maven is installed
mvn -version

# Install Maven if not present
sudo apt-get update
sudo apt-get install -y maven

# Verify installation
mvn -version
java -version
```

```
┌──────────────────────────────────────────────────────────────┐
│              mvn -version output                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Apache Maven 3.9.x                                          │
│  Java version: 17.x.x                                       │
│  OS name: Linux                                              │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 5: Test Maven Build Locally

```bash
cd ~/javadocker

# Clean and compile the project
mvn clean compile

# Run tests
mvn test

# Package into jar
mvn clean package

# Verify jar is created
ls target/
```

```
┌──────────────────────────────────────────────────────────────┐
│              Maven Commands Explained                        │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  mvn clean          → delete target/ folder                 │
│  mvn compile        → compile Java source code              │
│  mvn test           → run unit tests                        │
│  mvn package        → create .jar file in target/          │
│  mvn clean install  → clean + compile + test + package      │
│  mvn -DskipTests    → skip tests during build               │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 6: Create GitHub Actions Workflow

```bash
# Create workflows directory
mkdir -p .github/workflows

# Create the workflow file
vi .github/workflows/maven-ci.yml
```

Add the following content:

```yaml
name: Java Maven CI Pipeline

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

      - name: Setup Java 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

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
        run: ls -lh target/
```

---

## Part 7: Workflow Steps Explained

```
┌──────────────────────────────────────────────────────────────┐
│              Workflow Steps Explained                        │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Step 1: Checkout code                                       │
│    → pulls repo code into the GitHub runner                 │
│                                                              │
│  Step 2: Setup Java 17                                       │
│    → installs Java 17 (Temurin) on ubuntu-latest runner     │
│                                                              │
│  Step 3: Verify Java and Maven version                       │
│    → confirms java and mvn are available                    │
│                                                              │
│  Step 4: Maven Clean Compile                                 │
│    → mvn clean compile builds the source code               │
│                                                              │
│  Step 5: Maven Run Tests                                     │
│    → mvn test runs all unit tests                           │
│                                                              │
│  Step 6: Maven Package                                       │
│    → mvn package creates the .jar in target/               │
│                                                              │
│  Step 7: Show build output                                   │
│    → ls -lh target/ confirms jar was created                │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 8: Commit and Push Workflow

```bash
# Stage the workflow file
git add .github/workflows/maven-ci.yml

# Check status
git status

# Commit
git commit -m "Add GitHub Actions Maven CI pipeline"

# Push to your forked repo
git push origin main
```

---

## Part 9: Verify Workflow on GitHub

```
1. Go to https://github.com/<your-username>/javadocker
2. Click the 'Actions' tab
3. You will see 'Java Maven CI Pipeline' running
4. Click on the workflow run to see each step output
```

```
┌──────────────────────────────────────────────────────────────┐
│              GitHub Actions UI - Workflow Run                │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Java Maven CI Pipeline                                      │
│  ✅  Add GitHub Actions Maven CI pipeline                    │
│       └── build (ubuntu-latest)                             │
│             ├── ✅ Checkout code                             │
│             ├── ✅ Setup Java 17                             │
│             ├── ✅ Verify Java and Maven version             │
│             ├── ✅ Maven Clean Compile                       │
│             ├── ✅ Maven Run Tests                           │
│             ├── ✅ Maven Package (Create JAR)                │
│             └── ✅ Show build output                         │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 10: Sync Fork with Original Repo

When the original repo gets updates, sync your fork:

```bash
# Fetch updates from original repo
git fetch upstream

# Merge upstream changes into your local main
git merge upstream/main

# Push synced changes to your fork
git push origin main
```

```
┌──────────────────────────────────────────────────────────────┐
│              Keeping Fork in Sync                            │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  upstream (amitopenwriteup/javadocker)                       │
│          │                                                   │
│     git fetch upstream                                       │
│          │                                                   │
│     git merge upstream/main                                  │
│          │                                                   │
│  your local repo (up to date)                               │
│          │                                                   │
│     git push origin main                                     │
│          │                                                   │
│  your fork (<you>/javadocker) ✅                             │
└──────────────────────────────────────────────────────────────┘
```

---

## Full Flow Summary

```
┌──────────────────────────────────────────────────────────────┐
│                  Complete Lab Flow                           │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1. Fork amitopenwriteup/javadocker → your GitHub account   │
│  2. Clone forked repo to local machine                      │
│  3. Add upstream remote                                      │
│  4. Install Maven on Linux (if needed)                      │
│  5. Test build locally with mvn commands                    │
│  6. Create .github/workflows/maven-ci.yml                   │
│  7. git add → git commit → git push                         │
│  8. GitHub Actions triggers on push                         │
│  9. Runner: compile → test → package → show jar ✅          │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---|---|
| `git clone <fork-url>` | Clone your forked repo |
| `git remote add upstream <url>` | Track original repo |
| `git fetch upstream` | Get updates from original |
| `git merge upstream/main` | Sync fork with original |
| `mvn clean compile` | Compile Java source |
| `mvn test` | Run unit tests |
| `mvn clean package` | Build and create JAR |
| `mvn clean install` | Full build cycle |
| `mvn -DskipTests` | Skip tests during build |
| `ls target/` | View generated JAR |

---

> **Tip:** If the original repo uses `master` instead of `main` as the default branch, update the `branches` field in the workflow to `[ master ]` and push to `master` instead.
