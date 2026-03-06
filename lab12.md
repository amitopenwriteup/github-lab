# Git Pull & Fetch Lab

---

## What is git fetch and git pull?

```
┌──────────────────────────────────────────────────────────────┐
│               git fetch vs git pull                          │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git fetch:                                                  │
│  Remote ──── downloads changes ────▶ Remote Tracking Branch │
│              (does NOT touch working directory)             │
│                                                              │
│  git pull = git fetch + git merge:                          │
│  Remote ──── downloads + merges ────▶ Working Directory     │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Setup

```bash
cd ~
cd myproject

git config --global user.name "John Doe"
git config --global user.email "johndoe@example.com"

# Verify remote is set
git remote -v

# Check current branch
git branch
git log --oneline
```

---

## 1. git fetch

Downloads changes from remote but does NOT merge into local branch.

```bash
# Fetch all changes from remote
git fetch origin

# View what was fetched
git log --oneline origin/main

# Compare local vs remote
git diff main origin/main

# View branches including remote tracking
git branch -a
```

```
┌──────────────────────────────────────────────────────────────┐
│                     git fetch origin                         │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Before fetch:                                               │
│  Local main:          ●────A────B                           │
│  Remote origin/main:  ●────A────B────C────D  (ahead)        │
│                                                              │
│  After git fetch:                                            │
│  Local main:          ●────A────B            (unchanged)    │
│  origin/main:         ●────A────B────C────D  (updated)      │
│                                                              │
│  C and D are downloaded but NOT merged yet.                 │
└──────────────────────────────────────────────────────────────┘
```

---

## 2. git fetch a Specific Branch

```bash
# Fetch only a specific branch
git fetch origin main

# Fetch a feature branch
git fetch origin feature-branch

# View fetched branch
git log --oneline origin/feature-branch
```

---

## 3. git fetch All Remotes

```bash
# Fetch from all configured remotes
git fetch --all

# View all remote branches
git branch -r

# View all branches (local + remote)
git branch -a
```

```
┌──────────────────────────────────────────────────────────────┐
│                   git branch -a output                       │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  * main                       ← local current branch        │
│    feature-cp                 ← local branch                 │
│    remotes/origin/main        ← remote tracking branch      │
│    remotes/origin/feature-cp  ← remote tracking branch      │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 4. git fetch and Merge Manually

```bash
# Step 1 — fetch changes
git fetch origin

# Step 2 — review what is coming
git log --oneline main..origin/main

# Step 3 — merge fetched changes into local branch
git merge origin/main

# Verify
git log --oneline
```

```
┌──────────────────────────────────────────────────────────────┐
│              fetch + merge (manual pull)                     │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git fetch origin     → download C, D to origin/main        │
│  git merge origin/main → merge C, D into local main         │
│                                                              │
│  Local main:  ●────A────B────C────D  ✅                      │
│                                                              │
│  This is exactly what git pull does automatically.          │
└──────────────────────────────────────────────────────────────┘
```

---

## 5. git pull

Downloads and merges remote changes into local branch in one step.

```bash
# Pull latest changes from remote
git pull

# Pull from specific remote and branch
git pull origin main

# Verify
git log --oneline
```

```
┌──────────────────────────────────────────────────────────────┐
│                     git pull origin main                     │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Remote origin/main:  ●────A────B────C────D                 │
│                                                              │
│  git pull  =  git fetch  +  git merge                       │
│                                                              │
│  Local main after pull:  ●────A────B────C────D  ✅           │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 6. git pull --rebase

Instead of merge, replays local commits on top of fetched changes.
Creates a cleaner linear history.

```bash
# Pull with rebase instead of merge
git pull --rebase origin main

# Verify clean history
git log --oneline
```

```
┌──────────────────────────────────────────────────────────────┐
│              git pull vs git pull --rebase                   │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git pull (merge):                                           │
│  ●────A────B────C────D────M  ← M = merge commit             │
│                   └── local commits ──┘                     │
│                                                              │
│  git pull --rebase:                                          │
│  ●────A────B────C────D────local commits'  ← linear          │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 7. git pull --no-commit

Fetches and merges but does not auto-commit the merge.

```bash
# Pull without auto-committing merge
git pull --no-commit origin main

# Review merged changes
git status
git diff --staged

# Commit manually
git commit -m "Merged remote changes from origin/main"
```

---

## 8. git pull --ff-only (Fast-Forward Only)

Only pulls if the merge can be done as a fast-forward.
Prevents accidental merge commits.

```bash
# Pull only if fast-forward is possible
git pull --ff-only origin main

# If not fast-forward, git will show error:
# fatal: Not possible to fast-forward, aborting.
```

```
┌──────────────────────────────────────────────────────────────┐
│              git pull --ff-only                              │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Fast-forward possible:                                      │
│  Local:  ●────A────B                                         │
│  Remote: ●────A────B────C────D  ✅ pull succeeds             │
│                                                              │
│  Fast-forward NOT possible (local has extra commits):        │
│  Local:  ●────A────B────X  ← X is local only               │
│  Remote: ●────A────B────C  ❌ pull --ff-only fails           │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 9. git fetch and Prune Deleted Branches

Remove references to branches deleted on remote.

```bash
# Fetch and prune stale remote tracking branches
git fetch --prune

# Or prune separately
git remote prune origin

# Verify remote branches
git branch -r
```

```
┌──────────────────────────────────────────────────────────────┐
│                  git fetch --prune                           │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Remote deleted: origin/old-feature                         │
│                                                              │
│  Before prune:                                               │
│  remotes/origin/main                                         │
│  remotes/origin/old-feature  ← stale reference              │
│                                                              │
│  After git fetch --prune:                                    │
│  remotes/origin/main         ← only active branches         │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 10. Check Difference Before Pulling

Always review what changes are incoming before pulling.

```bash
# Fetch first
git fetch origin

# See commits on remote not in local
git log --oneline main..origin/main

# See files that will change
git diff --stat main origin/main

# See full diff
git diff main origin/main

# Now pull
git pull origin main
```

```
┌──────────────────────────────────────────────────────────────┐
│              Safe Pull Workflow                              │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1. git fetch origin          → download changes            │
│  2. git log main..origin/main → review incoming commits     │
│  3. git diff main origin/main → review incoming changes     │
│  4. git pull origin main      → merge when ready            │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 11. Pull a Specific Remote Branch into Local

```bash
# Pull a remote branch into a new local branch
git fetch origin feature-branch
git checkout -b feature-branch origin/feature-branch

# Or in one step
git checkout --track origin/feature-branch

# Verify
git branch
git log --oneline
```

---

## Quick Reference

| Command | Description |
|---|---|
| `git fetch origin` | Download all changes from remote |
| `git fetch origin main` | Fetch specific branch |
| `git fetch --all` | Fetch from all remotes |
| `git fetch --prune` | Fetch and remove stale branches |
| `git pull` | Fetch + merge into current branch |
| `git pull origin main` | Pull specific remote branch |
| `git pull --rebase origin main` | Pull with rebase instead of merge |
| `git pull --no-commit` | Pull without auto-committing |
| `git pull --ff-only` | Pull only if fast-forward possible |
| `git log main..origin/main` | See incoming commits before pull |
| `git diff main origin/main` | See incoming changes before pull |
| `git branch -r` | List remote tracking branches |
| `git branch -a` | List all local and remote branches |
| `git remote prune origin` | Remove stale remote references |

---

## git fetch vs git pull vs git clone

| Command | Downloads | Merges | Creates Local Repo | Use Case |
|---|---|---|---|---|
| `git clone` | Yes | Yes | Yes | First time setup |
| `git fetch` | Yes | No | No | Review before merging |
| `git pull` | Yes | Yes | No | Update local branch |

---

> **Best Practice:** Use `git fetch` first to review incoming changes before merging. This avoids surprises especially on shared branches. Run `git log main..origin/main` after fetch to see exactly what commits are coming in.
