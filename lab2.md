# GIT FULL LAB - ALL COMMANDS

---

## SETUP: Init, Config, Files & Commits

```bash
git init
```

---

## 1. GIT ADD

```bash
# Stage a single file
touch README.md
git add README.md
```
> Stages only README.md

```bash
# Stage all files
git add .
```
> Stages all untracked/modified files

```bash
# Interactive/patch mode
sed -i '1i echo' README.md
git add -p README.md
# press y to stage the chunk
```
> Lets you selectively stage chunks of a file

```bash
# Stage all including deletions
git add -A
```
> Stages all changes including deleted files

---

## 2. GIT COMMIT

```bash
# Commit with inline message
git commit -m "Initial commit: add README"
```

```bash
# Add + commit tracked files in one step
sed -i '1i print' README.md
git commit -am "Quick update"
git log --oneline
```

```bash
# Amend the last commit message
git commit --amend -m "Corrected commit message"
git log --oneline
```

```bash
# Full log
git log
```

---

## Exercise

```
1. Add requirements.txt
2. Perform add and commit
3. Check log
```

```bash
touch requirements.txt
git add requirements.txt
git commit -m "Add requirements.txt"
git log
```

---

## 3. GIT LOG

```bash
# Full log
git log

# Limit to last 3 entries
git log -n 3

# Filter by author
git log --author="amit"

# Filter by date
git log --since="1 day ago"
```

---

## 4. GIT LOG --ONELINE

```bash
git log --oneline
```

---

## 5. GIT REFLOG

```bash
# Full reflog
git reflog

# Reflog oneline
git reflog --oneline
```

---

## 6. GIT SHOW

```bash
# Show a specific commit
git show <commit_id>

# Show one commit before HEAD
git show HEAD~1

# Show files changed only
git show --stat README.md
```

---

## 7. GIT DIFF

```bash
# Unstaged changes
sed -i '1i echo' README.md
git diff

# Staged changes
git add .
git diff --staged
```

---

## 8. GIT CLONE

### Part 1: Create a GitHub Account

```
1. Go to https://github.com
2. If you have an existing account, log in — or click 'Sign up' to create a new one
3. Create a username (you may need a personal email address)
4. Check your email and verify your email address
```

### Part 2: Create a Repository on GitHub

```
1. After login, click the '+' icon at the top right → 'New repository'
2. Fill in the details:
   - Name: repo-lab
   - Visibility: Public
   - Check: Initialize with a README
3. Click the green 'Create Repository' button
```

### Part 3: Copy the Repository URL

```
1. Open your repository on GitHub
2. Click the green 'Code' button
3. Select HTTPS
4. Copy the URL shown
```

### Part 4: Clone the Repository

```bash
git clone <https-url>
```

**Example:**
```bash
git clone https://github.com/yourusername/repo-lab.git
cd repo-lab
```
