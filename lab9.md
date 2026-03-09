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


