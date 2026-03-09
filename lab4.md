# Lab 4 - GitHub Personal Access Token (PAT)

---

## 1. Create a Personal Access Token (PAT) on GitHub

```
1. Login to GitHub → https://github.com
2. Click your Profile Picture (top right) → Settings
3. Scroll down → Click "Developer settings" (left sidebar)
4. Click "Personal access tokens" → "Tokens (classic)"
5. Click "Generate new token" → "Generate new token (classic)"
6. Fill in:
   - Note       : my-lab-token
   - Expiration : 30 days
   - Scopes     : check "repo" (full control of private repos)
7. Click "Generate token"
8. COPY the token immediately — it won't show again!
```

```
┌──────────────────────────────────────────────────────┐
│         GitHub → Settings → Developer Settings      │
├──────────────────────────────────────────────────────┤
│                                                      │
│  Profile Picture                                     │
│       └── Settings                                  │
│              └── Developer settings (left sidebar)  │
│                     └── Personal access tokens      │
│                            └── Tokens (classic)     │
│                                   └── Generate new token (classic)
│                                                      │
└──────────────────────────────────────────────────────┘
```

```
┌──────────────────────────────────────────────────────┐
│              Generate New Token Form                 │
├──────────────────────────────────────────────────────┤
│                                                      │
│  Note       : [ my-lab-token                      ] │
│  Expiration : [ 30 days                           ] │
│                                                      │
│  Scopes:                                             │
│  [✓] repo  ← Full control of private repositories  │
│      ├── repo:status                                │
│      ├── repo_deployment                            │
│      ├── public_repo                                │
│      └── repo:invite                                │
│                                                      │
│              [ Generate token ]                     │
└──────────────────────────────────────────────────────┘
```

```
┌──────────────────────────────────────────────────────┐
│                  ⚠️  Copy Your Token                 │
├──────────────────────────────────────────────────────┤
│                                                      │
│  ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx            │
│                                           [ Copy ]  │
│                                                      │
│  ⚠️  This token will NOT be shown again!             │
│     Copy and store it in a safe place now.          │
│                                                      │
└──────────────────────────────────────────────────────┘
```

---

## 2. Clone the Repo Using PAT

```bash
git clone https://<YOUR_TOKEN>@github.com/<USERNAME>/<REPO_NAME>.git
```

**Example:**
```bash
cd ~
git clone https://ghp_xxxxxxxxxxxxxxxxxxxx@github.com/johndoe/myproject.git
cd myproject
```

```
┌──────────────────────────────────────────────────────────────┐
│                    PAT Clone Flow                            │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│   https://<TOKEN>@github.com/<USERNAME>/<REPO>.git          │
│          │                                                   │
│          └──── Token embedded in URL for authentication     │
│                                                              │
│   Local Machine  ────── git clone ──────▶  GitHub Repo      │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 3. Configure User (if not already set)

```bash
git config --global user.name "John Doe"
git config --global user.email "johndoe@example.com"
```

---

## 4. Git Add

```bash
# Create or edit a file
echo "Hello GitHub" > hello.txt

# Stage single file
git add hello.txt

# Or stage all files
git add .
```

---

## 5. Git Commit

```bash
git commit -m "Add hello.txt"
```

---

## 6. Git Push

```bash
# Push to default branch
git push origin main

# Or if branch is master
git push origin master
```

**Push with PAT inline (alternative):**
```bash
git push https://ghp_xxxxxxxxxxxxxxxxxxxx@github.com/johndoe/myproject.git main
```

```
┌──────────────────────────────────────────────────────────────┐
│                      git push flow                           │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│   Local Repo                           GitHub Repo           │
│  ┌───────────┐                        ┌───────────┐         │
│  │ hello.txt │──── git push origin ──▶│ hello.txt │         │
│  │ committed │         main           │ visible   │         │
│  └───────────┘                        └───────────┘         │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 7. Store PAT to Avoid Typing Every Time

```bash
# Cache credentials temporarily (15 min default)
git config --global credential.helper cache

# Store permanently
git config --global credential.helper store
```

> After running `store`, the next push will ask for username + PAT once, then save it automatically.

```
┌──────────────────────────────────────────────────────┐
│             Credential Helper Options                │
├──────────────────────────────────────────────────────┤
│                                                      │
│  cache  →  Stores in memory for 15 mins (default)   │
│  store  →  Saves permanently to ~/.git-credentials  │
│                                                      │
│  First push after 'store':                          │
│    Username: johndoe                                 │
│    Password: ghp_xxxxxxxxxxxx  ← enter PAT once     │
│                                                      │
│  All future pushes → automatic ✅                    │
└──────────────────────────────────────────────────────┘
```

---

## 8. Verify Push on GitHub

```
1. Go to https://github.com/<USERNAME>/<REPO_NAME>
2. You should see your new commit listed
```

```
┌──────────────────────────────────────────────────────┐
│         github.com/johndoe/myproject                 │
├──────────────────────────────────────────────────────┤
│                                                      │
│  📁 myproject                                        │
│  ├── 📄 README.md                                    │
│  └── 📄 hello.txt   ← new file visible here         │
│                                                      │
│  Latest commit: "Add hello.txt"  ✅                  │
│  Committed by:  johndoe                              │
└──────────────────────────────────────────────────────┘
```

---

## ⚠️  Important: PAT Security

```
┌──────────────────────────────────────────────────────────────┐
│                     PAT Security Rules                       │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ✅  Treat your PAT like a password                          │
│  ❌  Never share it with anyone                              │
│  ❌  Never commit it into your repository                    │
│                                                              │
│  If your PAT is exposed:                                     │
│    GitHub → Settings                                         │
│          → Developer settings                               │
│          → Personal access tokens                           │
│          → Delete the exposed token immediately             │
│          → Generate a new token                             │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Quick Reference

| Command | Description |
|---|---|
| `git clone https://<TOKEN>@github.com/...` | Clone using PAT |
| `git add hello.txt` | Stage single file |
| `git add .` | Stage all files |
| `git commit -m "message"` | Commit changes |
| `git push origin main` | Push to main branch |
| `git config --global credential.helper cache` | Cache PAT temporarily |
| `git config --global credential.helper store` | Store PAT permanently |
