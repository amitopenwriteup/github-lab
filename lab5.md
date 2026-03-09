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



