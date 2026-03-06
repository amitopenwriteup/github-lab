# Git Rebase Lab

---

## What is git rebase?
`git rebase` moves or replays commits from one branch onto another.
It rewrites commit history to create a **cleaner, linear history** — unlike `git merge` which creates a merge commit.

---

## git merge vs git rebase

```
┌──────────────────────────────────────────────────────────────┐
│                  merge vs rebase                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git merge:                                                  │
│  main   ●────●────────────────● ← merge commit              │
│               └── feature ●──●┘                             │
│                                                              │
│  git rebase:                                                 │
│  main   ●────●────●────●  ← linear, no merge commit         │
│               feature commits replayed on top of main       │
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

# Make sure we are on main and up to date
git checkout main
git log --oneline
```

---

## 1. Basic Rebase

### Create a feature branch and add commits

```bash
# Create and switch to feature branch
git checkout -b feature-rebase

# Add a new file and commit
echo "Feature line 1" > feature.txt
git add .
git commit -m "Feature: add feature.txt"

echo "Feature line 2" >> feature.txt
git add .
git commit -m "Feature: update feature.txt"

# View branch log
git log --oneline
```

### Add a new commit on main

```bash
# Switch to main
git checkout main

# Add a commit on main
echo "Main update" >> README.md
git add .
git commit -m "Main: update README"

# View main log
git log --oneline
```

### Rebase feature branch onto main

```bash
# Switch to feature branch
git checkout feature-rebase

# Rebase onto main
git rebase main

# View log after rebase
git log --oneline
```

```
┌──────────────────────────────────────────────────────────────┐
│                    Basic Rebase Flow                         │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Before rebase:                                              │
│  main     ●────A────B                                        │
│                └── feature  ●────C────D                      │
│                                                              │
│  After git rebase main (on feature branch):                  │
│  main     ●────A────B                                        │
│                          └── feature  ●────C'────D'          │
│                                                              │
│  C and D are replayed as C' and D' on top of B              │
└──────────────────────────────────────────────────────────────┘
```

---

## 2. Rebase and Merge (Fast-Forward)

```bash
# Switch to main
git checkout main

# Merge feature branch (fast-forward after rebase)
git merge feature-rebase

# View clean linear log
git log --oneline

# Delete feature branch
git branch -d feature-rebase
```

```
┌──────────────────────────────────────────────────────────────┐
│              Fast-Forward Merge After Rebase                 │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  After rebase + merge:                                       │
│  main  ●────A────B────C'────D'  ← (HEAD)                    │
│                                                              │
│  Clean linear history — no merge commit created.            │
└──────────────────────────────────────────────────────────────┘
```

---

## 3. Interactive Rebase

Interactive rebase lets you edit, squash, reorder, or drop commits.

```bash
# Create a new branch
git checkout -b interactive-rebase

echo "Line A" > notes.txt
git add . && git commit -m "Add line A"

echo "Line B" >> notes.txt
git add . && git commit -m "Add line B"

echo "Line C" >> notes.txt
git add . && git commit -m "Add line C"

# View commits
git log --oneline
```

```bash
# Start interactive rebase for last 3 commits
git rebase -i HEAD~3
```

```
┌──────────────────────────────────────────────────────────────┐
│               Interactive Rebase Editor                      │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  pick a1b2c3 Add line A                                      │
│  pick d4e5f6 Add line B                                      │
│  pick g7h8i9 Add line C                                      │
│                                                              │
│  Commands:                                                   │
│  pick   → keep commit as is                                  │
│  reword → keep commit, edit message                          │
│  squash → combine with previous commit                       │
│  fixup  → squash but discard commit message                  │
│  drop   → remove commit entirely                             │
│  edit   → pause to amend commit                              │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 4. Squash Commits with Interactive Rebase

```bash
# Squash last 3 commits into one
git rebase -i HEAD~3

# In editor change to:
# pick   a1b2c3 Add line A
# squash d4e5f6 Add line B
# squash g7h8i9 Add line C

# Save and close editor
# Enter new combined commit message when prompted

# View result — 3 commits become 1
git log --oneline
```

```
┌──────────────────────────────────────────────────────────────┐
│                    Squash Commits                            │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Before squash:                                              │
│  ●── Add line A ──● Add line B ──● Add line C               │
│                                                              │
│  After squash:                                               │
│  ●── Add lines A, B and C  (single commit)                  │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 5. Rebase --onto (Advanced)

Move a branch onto a completely different base.

```bash
# Create branch from feature
git checkout -b hotfix main

echo "Hotfix change" > hotfix.txt
git add . && git commit -m "Hotfix: add hotfix.txt"

# Rebase hotfix onto main skipping feature commits
git rebase --onto main feature-rebase hotfix

# View log
git log --oneline
```

```
┌──────────────────────────────────────────────────────────────┐
│                  git rebase --onto                           │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Before:                                                     │
│  main  ●────A                                                │
│              └── feature  ●────B────C                        │
│                                    └── hotfix  ●────D        │
│                                                              │
│  git rebase --onto main feature hotfix                       │
│                                                              │
│  After:                                                      │
│  main  ●────A────D'  ← hotfix replayed directly on main     │
│              └── feature  ●────B────C                        │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 6. Abort a Rebase

```bash
# If conflicts occur during rebase
git rebase main

# To stop and go back to original state
git rebase --abort

# Verify status
git status
```

---

## 7. Continue Rebase After Resolving Conflicts

```bash
# If rebase hits a conflict
git rebase main

# Edit conflicted file to resolve
vi file.txt

# Stage the resolved file
git add file.txt

# Continue the rebase
git rebase --continue

# View final log
git log --oneline
```

```
┌──────────────────────────────────────────────────────────────┐
│              Conflict Resolution During Rebase               │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1. git rebase main       → conflict occurs                  │
│  2. vi file.txt           → manually resolve conflict        │
│  3. git add file.txt      → mark as resolved                 │
│  4. git rebase --continue → resume rebase                    │
│                                                              │
│  OR                                                          │
│  4. git rebase --abort    → cancel and go back               │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 8. Push Rebased Branch to Remote

```bash
# After rebase, history is rewritten
# Force push is required for remote branches

git push origin feature-rebase --force
```

```
┌──────────────────────────────────────────────────────────────┐
│                  ⚠️  Force Push Warning                      │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git push --force rewrites remote history.                  │
│                                                              │
│  ✅  Safe   → force push on your own feature branch         │
│  ❌  Unsafe → force push on shared/main branch              │
│                                                              │
│  Use --force-with-lease for safer force push:               │
│  git push origin feature-rebase --force-with-lease          │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---|---|
| `git rebase main` | Rebase current branch onto main |
| `git rebase -i HEAD~3` | Interactive rebase last 3 commits |
| `git rebase --onto main feature hotfix` | Rebase onto specific base |
| `git rebase --abort` | Abort rebase on conflict |
| `git rebase --continue` | Continue after resolving conflict |
| `git push origin branch --force` | Force push after rebase |
| `git push origin branch --force-with-lease` | Safer force push |

---

## git rebase vs git merge vs git reset

| Command | Rewrites History | Creates Merge Commit | Use Case |
|---|---|---|---|
| `git rebase` | Yes | No | Clean linear history |
| `git merge` | No | Yes | Preserve full history |
| `git reset` | Yes | No | Undo local commits |

---

> **Best Practice:** Never rebase commits that have already been pushed to a shared or main branch. Use rebase only on your local or personal feature branches to keep history clean before merging.
