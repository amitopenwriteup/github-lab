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

> **Screenshot: New Repository Form**
> ![Create Repo](screenshots/01_create_repo.png)

---

## Part 2: Copy the SSH URL

```
1. After creating the repo, you will see the 'Quick Setup' page
2. Click 'SSH' tab
3. Copy the SSH URL shown
```

> **Screenshot: Quick Setup SSH URL**
> ![SSH URL](screenshots/02_ssh_url.png)

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
```

```
# Press Enter for default file location
# Press Enter twice for no passphrase
```

**Output:**
```
Your identification has been saved in /root/.ssh/id_rsa        [Private Key]
Your public key has been saved in /root/.ssh/id_rsa.pub        [Public Key]
```

---

### Step B — View and Copy the Public Key

```bash
cat /root/.ssh/id_rsa.pub
```

> Copy the entire output — it starts with `ssh-rsa` and ends with your email.

> **Screenshot: Public Key Output**
> ![Public Key](screenshots/03_public_key.png)

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

> **Screenshot: SSH Keys Settings Page**
> ![SSH Settings](screenshots/04_ssh_settings.png)

> **Screenshot: Add New SSH Key**
> ![Add SSH Key](screenshots/05_add_ssh_key.png)

---

### Step D — How SSH Authentication Works

```
Private Key  →  Stored on your workstation (/root/.ssh/id_rsa)
Public Key   →  Uploaded to GitHub

When you push:
  GitHub uses your PUBLIC key to decrypt communication
  from your PRIVATE key, authenticating you as an authorized user.
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

> **Screenshot: Successful Push Output**
> ![Push Output](screenshots/06_push_output.png)

---

## Part 6: Verify on GitHub

```
1. Go to https://github.com/<your-username>/gittraining
2. You should see your files listed in the repository
```

> **Screenshot: GitHub Repo After Push**
> ![GitHub Repo](screenshots/07_github_repo.png)

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
