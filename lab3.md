# GitHub SSH Setup & Push to Remote

---

## Part 1: Create a Repository on GitHub

```
1. Go to https://github.com
2. Click the '+' icon at the top right → 'New repository'
3. Fill in the details:
   - Name: repo-lab
   - Visibility: Public
   - Do NOT initialize with a README
4. Click the green 'Create Repository' button
```

```
┌─────────────────────────────────────────────────┐
│              Create a New Repository            │
├─────────────────────────────────────────────────┤
│  Repository name:  [ repo-lab                 ] │
│                                                 │
│  Visibility:                                    │
│    ( ) Private                                  │
│    (•) Public                                   │
│                                                 │
│  Initialize with README:  [ ] (leave unchecked) │
│                                                 │
│         [ Create Repository ]                   │
└─────────────────────────────────────────────────┘
```

---

## Part 2: Copy the SSH URL

```
1. After creating the repo, you will see the 'Quick Setup' page
2. Click 'SSH' tab
3. Copy the SSH URL shown
```

```
┌─────────────────────────────────────────────────────────────┐
│                      Quick Setup                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   [ HTTPS ]  [ SSH ]  ← click SSH                          │
│                                                             │
│   git@github.com:amitopenwriteup/gittraining.git  [copy]   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 3: Initialize and Link Remote Repository

Run the following commands inside your project directory:

```bash
git branch -M main
git remote add origin git@github.com:<your-username>/gittraining.git
```

**Example:**
```bash
git branch -M main
git remote add origin git@github.com:amitopenwriteup/gittraining.git
```

> Replace `amitopenwriteup` with your own GitHub username.

---

## Part 4: Configure SSH Connection to GitHub

### Step A — Generate SSH Key Pair

```bash
ssh-keygen -t rsa -b 4096
# Press Enter for default file location
# Press Enter twice for no passphrase
```

**Output:**
```
Your identification has been saved in /root/.ssh/id_rsa        [Private Key]
Your public key has been saved in /root/.ssh/id_rsa.pub        [Public Key]
```

```
┌─────────────────────────────────────────┐
│         SSH Key Pair Generated          │
├─────────────────────────────────────────┤
│                                         │
│  /root/.ssh/id_rsa        ← Private Key │
│  /root/.ssh/id_rsa.pub    ← Public Key  │
│                                         │
└─────────────────────────────────────────┘
```

---

### Step B — View and Copy the Public Key

```bash
cat /root/.ssh/id_rsa.pub
```

```
┌──────────────────────────────────────────────────────────────┐
│                     Public Key Output                        │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDo3xample...         │
│  ...xampleKeyContent...                                      │
│  johndoe@example.com                                         │
│                                                              │
│  ← Copy the entire output                                    │
└──────────────────────────────────────────────────────────────┘
```

---

### Step C — Add Public Key to GitHub

```
1. Go to https://github.com
2. Click your Profile Picture → Settings
3. In the left sidebar → Click 'SSH and GPG keys'
4. Click 'New SSH key'
5. Fill in:
   - Title: Public repo-key
   - Key: Paste the copied public key
6. Click 'Add SSH key'
```

```
┌─────────────────────────────────────────────────┐
│         GitHub → Settings → SSH Keys           │
├─────────────────────────────────────────────────┤
│                                                 │
│  Profile Picture                                │
│       └── Settings                             │
│              └── SSH and GPG keys              │
│                     └── New SSH key            │
│                                                 │
│  Title : [ Public repo-key                  ]  │
│  Key   : [ ssh-rsa AAAA...paste here        ]  │
│                                                 │
│              [ Add SSH key ]                    │
└─────────────────────────────────────────────────┘
```

---

### Step D — How SSH Authentication Works

```
┌──────────────────────────────────────────────────────────────┐
│                  SSH Authentication Flow                     │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│   Your Machine                        GitHub                 │
│  ┌──────────────┐                ┌──────────────┐           │
│  │ Private Key  │── git push ───▶│  Public Key  │           │
│  │ (id_rsa)     │                │  (uploaded)  │           │
│  └──────────────┘                └──────────────┘           │
│                                         │                    │
│                                  Decrypts & Verifies         │
│                                         │                    │
│                                  ✅ Authenticated            │
└──────────────────────────────────────────────────────────────┘
```

---

## Part 5: Push to GitHub

```bash
# Confirm you are in the correct directory
pwd

# Move into your project folder
cd myproject

# Push to remote and set upstream
git push -u origin main
```

```
┌──────────────────────────────────────────────────────────┐
│                   git push flow                          │
├──────────────────────────────────────────────────────────┤
│                                                          │
│   Local Repo (main)  ──── git push ────▶  GitHub Repo   │
│                                                          │
│   Counting objects: 5, done.                             │
│   Writing objects: 100%                                  │
│   Branch 'main' set up to track 'origin/main'           │
└──────────────────────────────────────────────────────────┘
```

---

## Part 6: Verify on GitHub

```
1. Go to https://github.com/<your-username>/gittraining
2. You should see your files listed in the repository
```

```
┌──────────────────────────────────────────────────────┐
│       github.com/amitopenwriteup/gittraining         │
├──────────────────────────────────────────────────────┤
│                                                      │
│  📁 myproject                                        │
│  ├── 📄 README.md                                    │
│  ├── 📄 app.py                                       │
│  └── 📄 requirements.txt                            │
│                                                      │
│  Latest commit: "Initial commit"  ✅                 │
└──────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---|---|
| `ssh-keygen -t rsa -b 4096` | Generate SSH key pair |
| `cat /root/.ssh/id_rsa.pub` | View public key |
| `git branch -M main` | Rename branch to main |
| `git remote add origin <url>` | Link local repo to remote |
| `git push -u origin main` | Push and set upstream |

---

> **Note:** The `-u` flag in `git push -u origin main` sets the upstream tracking.
> After this, you can simply run `git push` for future pushes.
