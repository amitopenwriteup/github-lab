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

## 5. Revert Multiple Commits (Range)

```bash
# Revert a range of commits (oldest..newest)
git revert HEAD~3..HEAD --no-edit

# This reverts HEAD, HEAD~1, HEAD~2 — each as separate revert commits
git log --oneline
```

---

## 6. Revert Multiple Commits as a Single Commit

```bash
# Use -n (--no-commit) to stage all reverts without committing
git revert -n HEAD~3..HEAD

# Check staged changes
git status

# Commit all reverts as one single commit
git commit -m "Revert last 3 commits"

# Verify
git log --oneline
```

---

## 7. Revert a Merge Commit

```bash
# Merges have two parents — must specify mainline parent with -m
# -m 1 = keep the first parent (the branch you merged into)
# -m 2 = keep the second parent (the branch you merged from)

git revert -m 1 <MERGE_COMMIT_HASH> --no-edit

# Verify
git log --oneline
```

> Always use `-m 1` when reverting a merge commit to preserve the base branch.

---

## 8. Revert and Push to Remote (Safe Workflow)

```bash
# Revert the last commit
git revert HEAD --no-edit

# Push the revert commit to remote
git push origin main

# Remote history is preserved — no force push needed
```

---

## 9. Abort a Revert (if conflicts occur)

```bash
# If revert causes conflicts
git revert HEAD

# To abort and go back to previous state
git revert --abort

# Verify status is clean
git status
```

---

## 10. Continue a Revert After Resolving Conflicts

```bash
# If revert causes conflicts
git revert HEAD

# Resolve conflicts manually in the file
# Then stage resolved files
git add file.txt

# Continue the revert
git revert --continue

# Verify
git log --oneline
```

---

## 11. Revert Without Staging (--no-commit)

```bash
# Stage the revert changes without creating a commit
git revert -n HEAD

# Review staged changes
git diff --staged

# Commit manually
git commit -m "Manual revert commit"

# Or discard the revert
git reset --hard HEAD
```

---

## 12. View What a Revert Will Do (Dry Run)

```bash
# Check what changes will be reverted without applying
git diff HEAD~1..HEAD

# This shows what the revert will undo
```

---

## Quick Reference

| Command | Description |
|---|---|
| `git revert HEAD` | Revert latest commit |
| `git revert HEAD --no-edit` | Revert without opening editor |
| `git revert <hash> --no-edit` | Revert specific commit by hash |
| `git revert HEAD~1 --no-edit` | Revert one commit before HEAD |
| `git revert HEAD~3..HEAD --no-edit` | Revert a range of commits |
| `git revert -n HEAD~3..HEAD` | Stage range revert without committing |
| `git revert -m 1 <hash>` | Revert a merge commit |
| `git revert --abort` | Abort revert on conflict |
| `git revert --continue` | Continue revert after resolving conflict |

---

## Common Revert Workflow

```bash
# 1. Identify the bad commit
git log --oneline

# 2. Revert it
git revert <hash> --no-edit

# 3. Verify the revert commit
git log --oneline
git show HEAD

# 4. Push safely to remote
git push origin main
```

---

## git revert vs git reset vs git restore

| Command | Rewrites History | Safe for Remote | Creates New Commit | Use Case |
|---|---|---|---|---|
| `git revert` | No | Yes | Yes | Undo pushed commits safely |
| `git reset` | Yes | No | No | Undo local commits |
| `git restore` | No | Yes | No | Discard working dir changes |

---

> **Best Practice:** Always use `git revert` when undoing commits that have already been pushed to a remote or shared branch. Use `git reset` only for local unpushed commits.
