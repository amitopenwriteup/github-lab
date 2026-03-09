# Git Reset - Full Lab Guide

## What is git reset?
`git reset` is used to undo commits, unstage files, or move the HEAD pointer to a previous commit.
It operates on three levels: **commit history**, **staging area**, and **working directory**.

---

## The Three Trees of Git

| Tree | Description |
|---|---|
| HEAD | Last commit snapshot |
| Index (Staging) | Proposed next commit |
| Working Directory | Current file changes |

---

## Reset Modes Overview

| Mode | HEAD | Staging Area | Working Directory |
|---|---|---|---|
| `--soft` | Moves | Unchanged | Unchanged |
| `--mixed` (default) | Moves | Cleared | Unchanged |
| `--hard` | Moves | Cleared | Cleared |

---

## Setup Lab

```bash
mkdir reset-lab
cd reset-lab
git init

echo "Line 1" > file.txt
git add . && git commit -m "First commit"

echo "Line 2" >> file.txt
git add . && git commit -m "Second commit"

echo "Line 3" >> file.txt
git add . && git commit -m "Third commit"

echo "Line 4" >> file.txt
git add . && git commit -m "Fourth commit"

# View all commits
git log --oneline
```

---

## 1. git reset --soft

Moves HEAD to a previous commit.
Keeps all changes **staged** (ready to recommit).

```bash
# Check current log
git log --oneline

# Reset to 1 commit before HEAD
git reset --soft HEAD~1

# Changes are still staged
git status

# Recommit if needed
git commit -m "Recommitted after soft reset"
```

> Use when: You want to undo a commit but keep all changes staged.

---

## 2. git reset --mixed (default)

Moves HEAD and **clears the staging area**.
Changes remain in the working directory (unstaged).

```bash
# Reset to 1 commit before HEAD
git reset --mixed HEAD~1

# Or simply
git reset HEAD~1

# Changes are unstaged but still present
git status
cat file.txt

# Re-stage and commit
git add .
git commit -m "Recommitted after mixed reset"
```

> Use when: You want to undo a commit and re-stage files selectively.

---

## 3. git reset --hard

Moves HEAD, clears staging area, and **discards all working directory changes**.

```bash
# Reset to 1 commit before HEAD
git reset --hard HEAD~1

# Everything is gone — no changes in staging or working dir
git status
cat file.txt
```

> **Warning:** Changes are permanently lost. Cannot be undone easily.

---

