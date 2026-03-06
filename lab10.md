# Git Stash Lab

---

## What is git stash?
`git stash` temporarily saves your uncommitted changes (staged and unstaged) so you can switch context — like changing branches — without committing incomplete work.

---

## How git stash Works

```
┌──────────────────────────────────────────────────────────────┐
│                    git stash flow                            │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Working Dir / Staging                    Stash Stack        │
│  ┌─────────────────────┐                ┌──────────────┐    │
│  │ uncommitted changes │── git stash ──▶│  stash@{0}   │    │
│  └─────────────────────┘                ├──────────────┤    │
│           clean ✅                       │  stash@{1}   │    │
│                                         └──────────────┘    │
│  git stash pop / apply → restores changes back              │
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

## 1. Basic git stash

```bash
# Make some changes
echo "Work in progress" >> README.md

# Check status
git status

# Stash the changes
git stash

# Verify working directory is clean
git status

# View stash list
git stash list
```

```
┌──────────────────────────────────────────────────────────────┐
│                     git stash                                │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Before stash:                                               │
│  modified: README.md   ← uncommitted change                 │
│                                                              │
│  After stash:                                                │
│  nothing to commit, working tree clean ✅                    │
│                                                              │
│  git stash list:                                             │
│  stash@{0}: WIP on main: a00df0d Update README              │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 2. git stash with Message

```bash
# Make changes
echo "Feature in progress" >> README.md

# Stash with a descriptive message
git stash push -m "WIP: feature update on README"

# View stash list
git stash list
```

```
┌──────────────────────────────────────────────────────────────┐
│              git stash push -m "message"                     │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git stash list:                                             │
│  stash@{0}: On main: WIP: feature update on README          │
│  stash@{1}: WIP on main: a00df0d Update README              │
│                                                              │
│  Named stashes are easier to identify later.                │
└──────────────────────────────────────────────────────────────┘
```

---

## 3. git stash pop

Restores the latest stash and removes it from the stash list.

```bash
# View stash list
git stash list

# Pop the latest stash (restore + remove from list)
git stash pop

# Verify changes are back
git status
cat README.md
```

```
┌──────────────────────────────────────────────────────────────┐
│                    git stash pop                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Stash Stack Before:          Stash Stack After:            │
│  stash@{0} ← restored  →     (removed from list)           │
│  stash@{1}             →     stash@{0}                      │
│                                                              │
│  Changes restored to working directory ✅                    │
└──────────────────────────────────────────────────────────────┘
```

---

## 4. git stash apply

Restores the stash but **keeps it in the stash list**.

```bash
# Stash current changes
echo "Another change" >> README.md
git stash push -m "WIP: another change"

# Apply stash without removing from list
git stash apply

# Stash is still in list
git stash list

# Apply a specific stash by index
git stash apply stash@{1}
```

```
┌──────────────────────────────────────────────────────────────┐
│               git stash apply vs git stash pop               │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git stash pop   →  restore + DELETE from stash list        │
│  git stash apply →  restore + KEEP in stash list            │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 5. git stash list

```bash
# View all stashes
git stash list
```

```
┌──────────────────────────────────────────────────────────────┐
│                   git stash list                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  stash@{0}: On main: WIP: another change                    │
│  stash@{1}: On main: WIP: feature update on README          │
│  stash@{2}: WIP on main: a00df0d Update README              │
│                                                              │
│  stash@{0} = most recent                                     │
│  stash@{2} = oldest                                          │
└──────────────────────────────────────────────────────────────┘
```

---

## 6. git stash show

```bash
# Show summary of latest stash
git stash show

# Show full diff of latest stash
git stash show -p

# Show specific stash
git stash show stash@{1}
git stash show -p stash@{1}
```

```
┌──────────────────────────────────────────────────────────────┐
│                   git stash show                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git stash show:                                             │
│   README.md | 1 +                                           │
│   1 file changed, 1 insertion(+)                            │
│                                                              │
│  git stash show -p:                                          │
│  +++ b/README.md                                             │
│  +Another change                                            │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 7. git stash drop

Delete a specific stash from the list.

```bash
# View stash list
git stash list

# Drop the latest stash
git stash drop

# Drop a specific stash by index
git stash drop stash@{1}

# Verify
git stash list
```

```
┌──────────────────────────────────────────────────────────────┐
│                   git stash drop                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Before:                  After drop stash@{1}:             │
│  stash@{0}                stash@{0}                         │
│  stash@{1}  ← deleted     stash@{1} (was stash@{2})         │
│  stash@{2}                                                   │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 8. git stash clear

Delete ALL stashes at once.

```bash
# Clear all stashes
git stash clear

# Verify list is empty
git stash list
```

```
┌──────────────────────────────────────────────────────────────┐
│                  git stash clear                             │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Before:              After:                                 │
│  stash@{0}            (empty — all stashes deleted)  ✅      │
│  stash@{1}                                                   │
│  stash@{2}                                                   │
│                                                              │
│  ⚠️  Cannot be undone. All stashed work is lost.             │
└──────────────────────────────────────────────────────────────┘
```

---

## 9. Stash Untracked Files

By default `git stash` only stashes tracked files.

```bash
# Create a new untracked file
echo "New file" > newfile.txt

# Regular stash (newfile.txt NOT stashed)
git stash
git status   # newfile.txt still shows

# Stash including untracked files
git stash push -u -m "WIP: include untracked"

# Verify
git status   # newfile.txt is now stashed
```

```
┌──────────────────────────────────────────────────────────────┐
│              git stash push -u (untracked)                   │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git stash          →  stashes tracked files only           │
│  git stash push -u  →  stashes tracked + untracked files    │
│  git stash push -a  →  stashes tracked + untracked + ignored│
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 10. Stash and Switch Branch

Common use case — stash work, switch branch, come back.

```bash
# Working on main — make changes
echo "Half done work" >> README.md

# Urgent fix needed on another branch — stash first
git stash push -m "WIP: half done work"

# Switch to hotfix branch
git checkout -b hotfix

# Do urgent work
echo "Urgent fix" > fix.txt
git add .
git commit -m "Hotfix: urgent fix"

# Switch back to main
git checkout main

# Restore stashed work
git stash pop

# Continue where you left off
cat README.md
```

```
┌──────────────────────────────────────────────────────────────┐
│              Stash → Switch → Restore Flow                   │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  main (WIP changes)                                          │
│       │                                                      │
│       ├── git stash         → save WIP                      │
│       ├── git checkout hotfix → switch branch               │
│       ├── fix + commit      → do urgent work                │
│       ├── git checkout main → switch back                   │
│       └── git stash pop     → restore WIP ✅                │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 11. Create Branch from Stash

```bash
# Create a new branch from stash and apply it
git stash branch feature-from-stash stash@{0}

# This creates branch, checks it out, applies stash and drops it
git log --oneline
git status
```

```
┌──────────────────────────────────────────────────────────────┐
│              git stash branch                                │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  git stash branch <branchname> stash@{0}                    │
│                                                              │
│  1. Creates new branch                                       │
│  2. Checks out the branch                                    │
│  3. Applies the stash                                        │
│  4. Drops the stash from list                               │
│                                                              │
│  Useful when stash causes conflicts on current branch.      │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---|---|
| `git stash` | Stash tracked changes |
| `git stash push -m "message"` | Stash with a label |
| `git stash push -u` | Stash including untracked files |
| `git stash push -a` | Stash all including ignored files |
| `git stash list` | List all stashes |
| `git stash show` | Summary of latest stash |
| `git stash show -p` | Full diff of latest stash |
| `git stash pop` | Restore latest stash and remove |
| `git stash apply` | Restore latest stash and keep |
| `git stash apply stash@{1}` | Restore specific stash |
| `git stash drop` | Delete latest stash |
| `git stash drop stash@{1}` | Delete specific stash |
| `git stash clear` | Delete all stashes |
| `git stash branch <name>` | Create branch from stash |

---

## git stash vs git commit vs git reset

| Action | Command | Use Case |
|---|---|---|
| Save temporarily | `git stash` | Switch context, come back later |
| Save permanently | `git commit` | Work is complete or at a milestone |
| Discard changes | `git restore` | Throw away unwanted changes |
| Undo last commit | `git reset` | Rewrite local history |

---

> **Best Practice:** Always use `git stash push -m "message"` instead of plain `git stash` so you can easily identify stashes when you have multiple saved.
