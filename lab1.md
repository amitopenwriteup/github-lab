# Git Setup - Step by Step Guide

---

## 1. Install Git

```bash
apt-get install -y git
```

---

## 2. Create Directory

```bash
mkdir myproject
cd myproject
```

---

## 3. Initialize Git

```bash
git init
ls .git
```

---

## 4. Configure Global User & Email

```bash
git config --global user.name "Your Name"
git config --global user.email you@example.com
git config --global --list
git config --global core.editor "vi"
```

---

## 5. Git Status

```bash
git status
```
