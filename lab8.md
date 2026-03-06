# Git Branch Lab

---

## Setup: Create Remote Repository on GitHub

```
1. Go to https://github.com
2. Click '+' → New repository
3. Fill in:
   - Name       : myproject
   - Visibility : Public
   - Do NOT initialize with a README
4. Click 'Create repository'
5. Copy the SSH or HTTPS URL
```

```
┌──────────────────────────────────────────────────────┐
│              Create a New Repository                 │
├──────────────────────────────────────────────────────┤
│                                                      │
│  Repository name : [ myproject                    ] │
│  Visibility      :  (•) Public  ( ) Private         │
│  Initialize README: [ ] leave unchecked             │
│                                                      │
│              [ Create repository ]                  │
└──────────────────────────────────────────────────────┘
```

---

## Map Local Repo to Remote

```bash
# Navigate into existing myproject directory
cd ~
cd myproject

# Add remote origin (replace with your URL)
git remote add origin git@github.com:<your-username>/myproject.git

# Verify remote is set
git remote -v

# Push to remote
git push -u origin main
```

```
┌──────────────────────────────────────────────────────────────┐
│               Local → Remote Mapping                         │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│   Local Repo                          GitHub Repo           │
│  ┌──────────────┐                   ┌──────────────┐        │
│  │  myproject   │── git remote ────▶│  myproject   │        │
│  │  (main)      │   add origin      │  (main)      │        │
│  └──────────────┘                   └──────────────┘        │
│                                                              │
│  git remote -v  →  shows fetch/push URLs                    │
└──────────────────────────────────────────────────────────────┘
```

---

## 1. Create a New Branch

```bash
git branch test
```

```
┌──────────────────────────────────────────────────┐
│              git branch test                     │
├──────────────────────────────────────────────────┤
│                                                  │
│  Before:                                         │
│  main  ●──────────────── (HEAD)                  │
│                                                  │
│  After:                                          │
│  main  ●──────────────── (HEAD)                  │
│             └── test  ●                          │
│                                                  │
│  Branch created but not switched yet.            │
└──────────────────────────────────────────────────┘
```

---

## 2. List Branches

```bash
git branch
```

```
┌──────────────────────────────────────────────────┐
│                 git branch                       │
├──────────────────────────────────────────────────┤
│                                                  │
│  * main    ← current branch (asterisk)           │
│    test                                          │
│                                                  │
└──────────────────────────────────────────────────┘
```

---

## 3. Checkout (Switch to) the Branch

```bash
git checkout test
```

```
┌──────────────────────────────────────────────────┐
│             git checkout test                    │
├──────────────────────────────────────────────────┤
│                                                  │
│  Before:  HEAD → main                            │
│  After:   HEAD → test                            │
│                                                  │
│  main  ●──────────────────                       │
│             └── test  ● ← (HEAD now here)        │
│                                                  │
└──────────────────────────────────────────────────┘
```

---

## 4. List Branch, Add File and Commit

```bash
# Confirm you are on test branch
git branch

# Create a new file
touch mycode

# Stage the file
git add mycode

# List files
ls

# Commit
git commit -m "adding to test branch"
```

```
┌──────────────────────────────────────────────────┐
│           Commit on test branch                  │
├──────────────────────────────────────────────────┤
│                                                  │
│  main  ●────────────────────                     │
│             └── test  ●────● ← new commit        │
│                          mycode added            │
│                                                  │
└──────────────────────────────────────────────────┘
```

---

## 5. Push Branch to Remote

```bash
git push origin test
```

```
┌──────────────────────────────────────────────────────────────┐
│                  git push origin test                        │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│   Local                               GitHub                 │
│  ┌────────────┐                      ┌────────────┐         │
│  │ test branch│── git push origin ──▶│ test branch│         │
│  │  mycode    │        test          │  mycode    │         │
│  └────────────┘                      └────────────┘         │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 6. Merge Branch into Main

```bash
# Switch back to main
git checkout main

# Merge test branch into main
git merge test
```

```
┌──────────────────────────────────────────────────┐
│              git merge test                      │
├──────────────────────────────────────────────────┤
│                                                  │
│  Before merge:                                   │
│  main  ●────────────────                         │
│             └── test  ●────●                     │
│                                                  │
│  After merge:                                    │
│  main  ●──────────────────────● ← (HEAD)         │
│             └── test  ●────●──┘                  │
│                                                  │
│  mycode is now part of main branch.              │
└──────────────────────────────────────────────────┘
```

---

## 7. Delete the Branch

```bash
# Delete branch locally
git branch -d test

# Delete branch on remote
git push origin --delete test

# Push updated main to remote
git push origin main
```

```
┌──────────────────────────────────────────────────────────────┐
│                    Branch Cleanup                            │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git branch -d test          → deletes local test branch    │
│  git push origin --delete test → deletes remote test branch │
│  git push origin main        → pushes merged main to remote │
│                                                              │
│  Before:  main ●───● , test ●───●                           │
│  After:   main ●───────────●   test ❌ (deleted)            │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Full Flow Summary

```
┌──────────────────────────────────────────────────────────────┐
│                  Complete Branch Workflow                    │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  1. git branch test          → create branch                │
│  2. git branch               → list branches                │
│  3. git checkout test        → switch to branch             │
│  4. touch mycode             → create file                  │
│     git add mycode           → stage file                   │
│     git commit -m "..."      → commit to branch             │
│  5. git push origin test     → push branch to remote        │
│  6. git checkout main        → switch to main               │
│     git merge test           → merge branch into main       │
│  7. git branch -d test       → delete local branch          │
│     git push origin --delete test → delete remote branch    │
│     git push origin main     → push final main to remote    │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---|---|
| `git branch test` | Create new branch |
| `git branch` | List all branches |
| `git checkout test` | Switch to branch |
| `git checkout -b test` | Create and switch in one step |
| `git push origin test` | Push branch to remote |
| `git checkout main` | Switch back to main |
| `git merge test` | Merge branch into main |
| `git branch -d test` | Delete local branch |
| `git push origin --delete test` | Delete remote branch |
| `git push origin main` | Push main to remote |
| `git remote add origin <url>` | Map local to remote repo |
| `git remote -v` | View remote URLs |

---

> **Tip:** Use `git checkout -b test` to create and switch to a new branch in a single command instead of running `git branch test` and `git checkout test` separately.
