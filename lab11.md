# Git Cherry-Pick Lab

---

## What is git cherry-pick?
`git cherry-pick` copies a specific commit from one branch and applies it to another branch — without merging the entire branch.

---

## How git cherry-pick Works

```
┌──────────────────────────────────────────────────────────────┐
│                  git cherry-pick flow                        │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  feature  ●────A────B────C────D                              │
│                                                              │
│  main     ●────X────Y                                        │
│                                                              │
│  git cherry-pick C  (on main branch)                        │
│                                                              │
│  main     ●────X────Y────C'  ← C copied onto main           │
│                                                              │
│  Only commit C is copied — not A, B, or D                   │
└──────────────────────────────────────────────────────────────┘
```

---

## Setup

```bash
cd ~
cd myproject

git config --global user.name "John Doe"
git config --global user.email "johndoe@example.com"

git checkout main
git log --oneline
```

---

## 1. Basic Cherry-Pick

### Create commits on a feature branch

```bash
# Create and switch to feature branch
git checkout -b feature-cp

echo "Fix A" > fix-a.txt
git add . && git commit -m "Fix A: critical fix"

echo "Fix B" > fix-b.txt
git add . && git commit -m "Fix B: minor fix"

echo "Fix C" > fix-c.txt
git add . && git commit -m "Fix C: important fix"

# View commits
git log --oneline
```

### Cherry-pick a specific commit to main

```bash
# Switch to main
git checkout main

# Get the commit hash of "Fix C"
git log --oneline feature-cp

# Cherry-pick that commit (replace hash with yours)
git cherry-pick <HASH>

# Example
git cherry-pick a1b2c3d

# Verify
git log --oneline
ls
```

```
┌──────────────────────────────────────────────────────────────┐
│                   Basic Cherry-Pick                          │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  feature-cp  ●── Fix A ── Fix B ── Fix C                    │
│                                                              │
│  main  ●── (existing commits)                               │
│                                                              │
│  git cherry-pick <Fix C hash>                               │
│                                                              │
│  main  ●── (existing commits) ── Fix C'  ← copied           │
│                                                              │
│  fix-c.txt now exists on main ✅                             │
└──────────────────────────────────────────────────────────────┘
```

---

## 2. Cherry-Pick Multiple Commits

```bash
# Get hashes of Fix A and Fix B
git log --oneline feature-cp

# Cherry-pick multiple commits (space separated)
git cherry-pick <HASH-A> <HASH-B>

# Example
git cherry-pick a1b2c3d d4e5f6g

# Verify
git log --oneline
```

```
┌──────────────────────────────────────────────────────────────┐
│            Cherry-Pick Multiple Commits                      │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git cherry-pick <hash1> <hash2>                            │
│                                                              │
│  main  ●──X──Y──Fix A'──Fix B'  ← both commits copied       │
│                                                              │
│  Each commit creates a separate new commit on main.         │
└──────────────────────────────────────────────────────────────┘
```

---

## 3. Cherry-Pick a Range of Commits

```bash
# Cherry-pick a range (exclusive of first hash)
git cherry-pick <HASH-START>..<HASH-END>

# Example — pick Fix B through Fix C
git cherry-pick a1b2c3d..g7h8i9j

# To include the start commit use ^
git cherry-pick a1b2c3d^..g7h8i9j
```

```
┌──────────────────────────────────────────────────────────────┐
│              Cherry-Pick a Range                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  A..C  →  picks B and C  (A excluded)                       │
│  A^..C →  picks A, B and C  (A included)                    │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 4. Cherry-Pick with --no-commit

Applies the changes but does NOT create a commit automatically.
Lets you review or combine before committing.

```bash
# Cherry-pick without auto-commit
git cherry-pick --no-commit <HASH>

# Review staged changes
git status
git diff --staged

# Commit manually
git commit -m "Cherry-picked fix with custom message"
```

```
┌──────────────────────────────────────────────────────────────┐
│            git cherry-pick --no-commit                       │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Changes applied to staging area only.                      │
│  No commit created yet.                                      │
│                                                              │
│  Useful for:                                                 │
│  - Reviewing changes before committing                      │
│  - Combining multiple cherry-picks into one commit          │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 5. Cherry-Pick with Custom Commit Message

```bash
# Cherry-pick and edit the commit message
git cherry-pick --edit <HASH>

# Editor opens — modify the message and save
```

---

## 6. Cherry-Pick and Keep Original Author

```bash
# Preserve the original author info
git cherry-pick -x <HASH>

# Adds reference to original commit in message:
# (cherry picked from commit a1b2c3d)
git log --oneline
git show HEAD
```

```
┌──────────────────────────────────────────────────────────────┐
│              git cherry-pick -x                              │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Commit message after -x:                                    │
│                                                              │
│  Fix C: important fix                                        │
│  (cherry picked from commit a1b2c3d)                        │
│                                                              │
│  Useful for traceability and audit history.                 │
└──────────────────────────────────────────────────────────────┘
```

---

## 7. Abort a Cherry-Pick

```bash
# If cherry-pick causes a conflict
git cherry-pick <HASH>

# Abort and restore original state
git cherry-pick --abort

# Verify status is clean
git status
```

---

## 8. Continue Cherry-Pick After Resolving Conflict

```bash
# Cherry-pick hits a conflict
git cherry-pick <HASH>

# Resolve conflict manually
vi file.txt

# Stage resolved file
git add file.txt

# Continue cherry-pick
git cherry-pick --continue

# Verify
git log --oneline
```

```
┌──────────────────────────────────────────────────────────────┐
│           Conflict Resolution During Cherry-Pick             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1. git cherry-pick <hash>    → conflict occurs             │
│  2. vi file.txt               → resolve conflict manually   │
│  3. git add file.txt          → mark as resolved            │
│  4. git cherry-pick --continue → finish cherry-pick         │
│                                                              │
│  OR                                                          │
│  4. git cherry-pick --abort   → cancel entirely             │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 9. Cherry-Pick from Another Remote Branch

```bash
# Fetch remote branches first
git fetch origin

# Cherry-pick a commit from a remote branch
git cherry-pick <HASH-FROM-REMOTE>

# Example — get hash from remote branch log
git log --oneline origin/feature-cp
git cherry-pick b3c4d5e
```

---

## 10. Real World Use Case

```bash
# Scenario:
# A bug fix was committed on feature branch.
# You need that fix on main immediately
# without merging the entire feature branch.

# Step 1 — identify the fix commit on feature branch
git log --oneline feature-cp

# Step 2 — switch to main
git checkout main

# Step 3 — cherry-pick only the fix commit
git cherry-pick <FIX-HASH> -x

# Step 4 — push to remote
git push origin main
```

```
┌──────────────────────────────────────────────────────────────┐
│                  Real World Use Case                         │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  feature  ●── WIP ── WIP ── BUG FIX ── WIP                  │
│                               │                             │
│                     cherry-pick only this                   │
│                               │                             │
│  main     ●── stable ─────────●── BUG FIX' ← pushed        │
│                                                              │
│  Feature branch is not merged — only the fix is taken.     │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---|---|
| `git cherry-pick <hash>` | Copy single commit to current branch |
| `git cherry-pick <hash1> <hash2>` | Copy multiple commits |
| `git cherry-pick <hash1>..<hash2>` | Copy a range (hash1 excluded) |
| `git cherry-pick <hash1>^..<hash2>` | Copy a range (hash1 included) |
| `git cherry-pick --no-commit <hash>` | Apply changes without committing |
| `git cherry-pick --edit <hash>` | Cherry-pick with custom message |
| `git cherry-pick -x <hash>` | Add original commit reference |
| `git cherry-pick --abort` | Abort on conflict |
| `git cherry-pick --continue` | Continue after resolving conflict |

---

## git cherry-pick vs git merge vs git rebase

| Command | Copies Specific Commits | Rewrites History | Use Case |
|---|---|---|---|
| `git cherry-pick` | Yes | No | Pick one or few commits across branches |
| `git merge` | No | No | Bring entire branch history together |
| `git rebase` | No | Yes | Replay commits for clean linear history |

---

> **Best Practice:** Use `git cherry-pick -x` when picking commits so the original commit hash is recorded in the message. This keeps your history traceable and makes it clear where the change came from.
