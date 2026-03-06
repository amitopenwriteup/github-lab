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
git config --global user.name "John Doe"
git config --global user.email "johndoe@example.com"

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

## 4. Reset to a Specific Commit Hash

```bash
# View log to get hash
git log --oneline

# Reset to specific commit (replace hash with your own)
git reset --hard <COMMIT_HASH>

# Example
git reset --hard 12e6d42
```

---

## 5. Reset Using HEAD References

```bash
# Go back 1 commit
git reset --soft HEAD~1

# Go back 2 commits
git reset --soft HEAD~2

# Go back 3 commits
git reset --hard HEAD~3
```

---

## 6. Unstage a File (reset without commit)

```bash
echo "New change" >> file.txt
git add file.txt

# Unstage the file (keeps change in working dir)
git reset file.txt

# Verify
git status
```

> This is equivalent to `git restore --staged file.txt`

---

## 7. Unstage All Files

```bash
echo "Change A" >> file.txt
echo "New file" > newfile.txt
git add .

# Unstage everything
git reset

# Verify
git status
```

---

## 8. Reset to Remote Branch State

```bash
# Fetch latest from remote
git fetch origin

# Hard reset local branch to match remote
git reset --hard origin/main
```

> Use when: Your local branch has diverged and you want to match remote exactly.

---

## 9. Recover from a Hard Reset (using reflog)

```bash
# Even after hard reset, commits are recoverable via reflog
git reflog

# Find the commit hash you want to recover
# Example output:
# a00df0d HEAD@{0}: reset: moving to HEAD~1
# 27f9939 HEAD@{1}: commit: Fourth commit

# Reset back to recovered commit
git reset --hard 27f9939
```

> `git reflog` is your safety net after a hard reset.

---

## 10. Reset a Single File to a Specific Commit

```bash
# Reset only file.txt to its state at HEAD~2
git reset HEAD~2 -- file.txt

# File is now staged with old content
git status
cat file.txt

# Commit if needed
git commit -m "Reverted file.txt to older version"
```

---

## Quick Reference

| Command | Effect |
|---|---|
| `git reset --soft HEAD~1` | Undo commit, keep changes staged |
| `git reset --mixed HEAD~1` | Undo commit, keep changes unstaged |
| `git reset --hard HEAD~1` | Undo commit, discard all changes |
| `git reset --hard <hash>` | Reset to specific commit |
| `git reset HEAD~2` | Go back 2 commits (mixed) |
| `git reset file.txt` | Unstage a single file |
| `git reset` | Unstage all files |
| `git reset --hard origin/main` | Match remote branch exactly |
| `git reset HEAD~2 -- file.txt` | Reset single file to older commit |

---

## git reset vs git revert vs git restore

| Command | Rewrites History | Safe for Shared Branch | Use Case |
|---|---|---|---|
| `git reset` | Yes | No | Local undo of commits |
| `git revert` | No | Yes | Undo pushed commits safely |
| `git restore` | No | Yes | Discard working dir changes |

---

> **Important:** Never use `git reset --hard` on commits that have already been pushed to a shared/remote branch. Use `git revert` instead to safely undo pushed commits.
