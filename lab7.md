# Git Revert - Full Lab Guide

## What is git revert?
`git revert` creates a **new commit** that undoes the changes of a previous commit.
Unlike `git reset`, it does **not** rewrite history — making it safe for shared and remote branches.

---

## How git revert Works

```
Before revert:
A --- B --- C --- D  (HEAD)

After git revert C:
A --- B --- C --- D --- E  (HEAD)
                        E = new commit that undoes C
```

---

## git reset vs git revert

| Feature | git reset | git revert |
|---|---|---|
| Rewrites history | Yes | No |
| Safe for remote/shared branch | No | Yes |
| Creates new commit | No | Yes |
| Undoes pushed commits | Risky | Safe |
| Can undo multiple commits | Yes | Yes |

---

## Setup Lab

```bash
mkdir revert-lab
cd revert-lab
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

## 1. Revert the Last Commit

```bash
# Revert HEAD (latest commit)
git revert HEAD

# Git opens editor for commit message — save and close
# A new revert commit is created

# Verify
git log --oneline
cat file.txt
```

> Undoes the last commit by creating a new commit.

---

## 2. Revert Using --no-edit

Skips the editor and uses the default revert message.

```bash
git revert HEAD --no-edit

# Verify
git log --oneline
```

---

## 3. Revert a Specific Commit by Hash

```bash
# View log to get hash
git log --oneline

# Revert specific commit (replace with your hash)
git revert <COMMIT_HASH> --no-edit

# Example
git revert 12e6d42 --no-edit

# Verify
git log --oneline
cat file.txt
```

---

## 4. Revert Using HEAD References

```bash
# Revert 1 commit before HEAD
git revert HEAD~1 --no-edit

# Revert 2 commits before HEAD
git revert HEAD~2 --no-edit

# Verify
git log --oneline
```

---
mmits.
