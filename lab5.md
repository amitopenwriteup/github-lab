# Lab - Git Restore

---

## Setup

```bash
cd ~
mkdir restore-lab
cd restore-lab
git init

echo "Line 1" > file.txt
git add .
git commit -m "Initial commit"
```

```
┌──────────────────────────────────────────────────┐
│                 Git Three Areas                  │
├──────────────────────────────────────────────────┤
│                                                  │
│  Working Directory → Staging Area → Repository  │
│       (edit)            (add)        (commit)    │
│                                                  │
│  git restore          →  undoes working dir      │
│  git restore --staged →  undoes staging area     │
│                                                  │
└──────────────────────────────────────────────────┘
```

---

## 1. Restore Unstaged Changes (Working Directory)

```bash
# Make a change
echo "Unwanted change" >> file.txt

# Check status
git status

# Restore file to last commit state
git restore file.txt

# Verify change is gone
cat file.txt
```

```
┌──────────────────────────────────────────────────┐
│           git restore file.txt                   │
├──────────────────────────────────────────────────┤
│                                                  │
│  Working Dir       Staging        Repository     │
│  ┌─────────┐      ┌────────┐     ┌───────────┐  │
│  │ changed │  ◀── │        │     │  HEAD     │  │
│  │ file    │      │        │     │  commit   │  │
│  └─────────┘      └────────┘     └───────────┘  │
│       ▲                                │         │
│       └──────── restored from ─────────┘         │
│                                                  │
│  ⚠️  Discards changes. Cannot be undone.          │
└──────────────────────────────────────────────────┘
```

---

## 2. Restore All Unstaged Changes

```bash
echo "Change 1" >> file.txt
echo "New file" > newfile.txt

# Restore all modified tracked files
git restore .
```

```
┌──────────────────────────────────────────────────┐
│               git restore .                      │
├──────────────────────────────────────────────────┤
│                                                  │
│  file.txt    → tracked   → ✅ restored           │
│  newfile.txt → untracked → ❌ NOT affected        │
│                                                  │
│  Only restores tracked files.                    │
│  Untracked files are not affected.               │
└──────────────────────────────────────────────────┘
```

---

## 3. Restore Staged File (Unstage)

```bash
echo "Staged change" >> file.txt
git add file.txt

# Check it is staged
git status

# Unstage the file (move back to working directory)
git restore --staged file.txt

# Verify it is unstaged
git status
```

```
┌──────────────────────────────────────────────────┐
│         git restore --staged file.txt            │
├──────────────────────────────────────────────────┤
│                                                  │
│  Working Dir       Staging        Repository     │
│  ┌─────────┐      ┌────────┐     ┌───────────┐  │
│  │ change  │ ◀─── │ change │     │  HEAD     │  │
│  │ kept ✅ │      │removed │     │  commit   │  │
│  └─────────┘      └────────┘     └───────────┘  │
│                                                  │
│  Moves file out of staging area.                 │
│  Change is kept in working directory.            │
└──────────────────────────────────────────────────┘
```

---

## 4. Restore Staged + Working Directory

```bash
echo "Another change" >> file.txt
git add file.txt

# Unstage AND discard working directory change
git restore --staged --worktree file.txt

# Verify completely clean
git status
cat file.txt
```

```
┌──────────────────────────────────────────────────┐
│    git restore --staged --worktree file.txt      │
├──────────────────────────────────────────────────┤
│                                                  │
│  Working Dir       Staging        Repository     │
│  ┌─────────┐      ┌────────┐     ┌───────────┐  │
│  │ change  │      │ change │     │  HEAD     │  │
│  │ removed │      │removed │ ◀── │  commit   │  │
│  └─────────┘      └────────┘     └───────────┘  │
│       ❌                ❌                        │
│                                                  │
│  Both staging and working dir are cleared.       │
└──────────────────────────────────────────────────┘
```

---

## 5. Restore File from Specific Commit

```bash
echo "Version 2" >> file.txt
git add .
git commit -m "Second commit"

echo "Version 3" >> file.txt
git add .
git commit -m "Third commit"

# View commits
git log --oneline

# Restore file.txt as it was 2 commits ago
git restore --source=HEAD~2 file.txt

# Verify content
cat file.txt
```

```
┌──────────────────────────────────────────────────┐
│        git restore --source=HEAD~2               │
├──────────────────────────────────────────────────┤
│                                                  │
│  HEAD~2        HEAD~1          HEAD              │
│  ┌──────┐     ┌──────┐       ┌──────┐           │
│  │ v1   │────▶│ v2   │──────▶│ v3   │           │
│  └──────┘     └──────┘       └──────┘           │
│     │                                            │
│     └──── file.txt restored to this version      │
│                                                  │
│  Does not affect commit history.                 │
│  Brings back file content only.                  │
└──────────────────────────────────────────────────┘
```

---

## 6. Restore File from Specific Hash

```bash
# Get commit hash
git log --oneline

# Restore using hash
git restore --source=<HASH> file.txt

# Example
git restore --source=12e6d42 file.txt
```

```
┌──────────────────────────────────────────────────┐
│       git restore --source=<HASH>                │
├──────────────────────────────────────────────────┤
│                                                  │
│  git log --oneline                               │
│  a00df0d  Third commit                           │
│  27f9939  Second commit                          │
│  12e6d42  Initial commit  ← restore from here   │
│                                                  │
│  git restore --source=12e6d42 file.txt           │
└──────────────────────────────────────────────────┘
```

---

## 7. Restore a Deleted File

```bash
# Delete the file
rm file.txt

# Check status
git status

# Restore deleted file
git restore file.txt

# Verify file is back
ls
cat file.txt
```

```
┌──────────────────────────────────────────────────┐
│           Restore a Deleted File                 │
├──────────────────────────────────────────────────┤
│                                                  │
│  rm file.txt          → file deleted locally     │
│  git status           → shows "deleted: file.txt"│
│  git restore file.txt → file recovered from HEAD │
│                                                  │
│  Before:  file.txt ❌                            │
│  After:   file.txt ✅                            │
└──────────────────────────────────────────────────┘
```

---

## 8. Restore Specific Lines (Patch Mode)

```bash
echo "Good line" >> file.txt
echo "Bad line" >> file.txt
git add file.txt

# Interactively choose which chunks to unstage
git restore --staged --patch file.txt
```

```
┌──────────────────────────────────────────────────┐
│         git restore --staged --patch             │
├──────────────────────────────────────────────────┤
│                                                  │
│  Options shown per chunk:                        │
│                                                  │
│  y  →  yes, unstage this chunk                   │
│  n  →  no, keep staged                           │
│  s  →  split into smaller chunks                 │
│  q  →  quit                                      │
│                                                  │
│  Allows selective unstaging line by line.        │
└──────────────────────────────────────────────────┘
```

---

## 9. Restore File from Another Branch

```bash
# Restore file.txt from main branch without switching
git restore --source=main file.txt

# Restore from another branch
git restore --source=feature-branch file.txt
```

```
┌──────────────────────────────────────────────────┐
│       git restore --source=<branch>              │
├──────────────────────────────────────────────────┤
│                                                  │
│  Current Branch: feature-branch                  │
│                                                  │
│  main           feature-branch                   │
│  ┌──────┐       ┌──────┐                         │
│  │ v1   │──────▶│ v2   │  ← you are here         │
│  └──────┘       └──────┘                         │
│     │                                            │
│     └──── file.txt pulled from main              │
│           without switching branch               │
└──────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---|---|
| `git restore file.txt` | Discard working dir changes |
| `git restore .` | Discard all working dir changes |
| `git restore --staged file.txt` | Unstage a file |
| `git restore --staged --worktree file.txt` | Unstage + discard changes |
| `git restore --source=HEAD~1 file.txt` | Restore from previous commit |
| `git restore --source=<hash> file.txt` | Restore from specific commit |
| `git restore --staged --patch file.txt` | Interactive patch restore |
| `git restore --source=main file.txt` | Restore from another branch |

---

> **git restore vs git checkout**
> - `git checkout -- file.txt` = same as `git restore file.txt` (old way)
> - `git restore` is the modern preferred command from Git 2.23+
