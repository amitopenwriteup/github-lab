#  GitHub Lab: Code Review Process & Issue/Project Management

**Duration:** 60–90 minutes  
**Level:** Beginner to Intermediate  
**Prerequisites:** GitHub account, Git installed locally, basic command line familiarity

---

##  Overview

In this lab you will practice the full GitHub collaboration workflow — from opening issues and creating branches, to submitting pull requests, conducting code reviews, and managing work with GitHub Projects.

By the end of this lab you will be able to:

- Create and label GitHub Issues
- Link issues to a GitHub Project board
- Create branches tied to issues
- Open and review Pull Requests with inline comments
- Approve, request changes, and merge PRs
- Close issues automatically via commit messages

---

##  Part 1 — Setting Up Your Lab Repository

### Step 1: Fork or Create a Repository

**Option A — Fork a shared class repo (if provided by instructor):**

1. Navigate to the provided repository URL
2. Click **Fork** (top right) → choose your personal account
3. Click **Create fork**

**Option B — Create your own practice repository:**

1. Go to [https://github.com/new](https://github.com/new)
2. Fill in the following:
   - **Repository name:** `github-lab-practice`
   - **Description:** `Lab practice for code review and project management`
   - **Visibility:** Public
   - ✅ Check **Add a README file**
   - ✅ Check **Add .gitignore** → choose `Node` or `Python`
3. Click **Create repository**

### Step 2: Clone the Repository Locally

```bash
git clone https://github.com/YOUR-USERNAME/github-lab-practice.git
cd github-lab-practice
```

> ✅ **Checkpoint:** Run `git status` — you should see `On branch main, nothing to commit`.

---

##  Part 2 — Creating and Managing Issues

Issues are the foundation of project tracking on GitHub. They represent bugs, features, tasks, or questions.

### Step 3: Create Your First Issue

1. In your repository, click the **Issues** tab
2. Click **New issue**
3. Fill in the form:

**Title:**
```
Add a greeting function to index.js
```

**Body (use this template):**
```markdown
## Description
We need a `greet()` function that accepts a name parameter and returns a greeting string.

## Acceptance Criteria
- [ ] Function is named `greet`
- [ ] Accepts one parameter: `name`
- [ ] Returns the string `"Hello, <name>!"`
- [ ] Function is exported for use in other modules

## Notes
Keep it simple — this is a foundational utility function.
```

4. On the right sidebar:
   - **Labels** → Click **Labels** → select `enhancement`
   - **Assignees** → assign yourself
5. Click **Submit new issue**
6. Note the issue number (e.g., `#1`) — you'll use it later

### Step 4: Create a Second Issue (Bug)

1. Click **New issue** again
2. Fill in:

**Title:**
```
README missing setup instructions
```

**Body:**
```markdown
## Bug Description
The README.md file does not contain any setup or installation instructions.

## Expected Behavior
README should include:
- Prerequisites
- Installation steps
- How to run the project

## Steps to Reproduce
1. Visit the repository
2. Open README.md
3. Observe missing content
```

3. Assign label: `documentation`
4. Assign yourself
5. Click **Submit new issue**

### Step 5: Create Custom Labels

1. Go to **Issues** → **Labels** → **New label**
2. Create the following labels:

| Label Name | Color | Description |
|---|---|---|
| `in-progress` | `#0075ca` | Work has started |
| `needs-review` | `#e4e669` | Ready for code review |
| `blocked` | `#d93f0b` | Blocked by another task |

---

##  Part 3 — GitHub Projects (Kanban Board)

### Step 6: Create a Project Board

1. Click the **Projects** tab in your repository
2. Click **Link a project** → **New project**
3. Choose **Board** template
4. Name it: `Lab Sprint Board`
5. Click **Create project**

### Step 7: Configure Board Columns

Your board should have these columns (add/rename as needed):

| Column | Purpose |
|---|---|
|  **Backlog** | All new issues start here |
|  **In Progress** | Actively being worked on |
| 👀 **In Review** | PR is open, awaiting review |
| ✅ **Done** | Merged and closed |

### Step 8: Add Issues to the Board

1. In the **Backlog** column, click **+** → **Add item**
2. Type `#` to search and add both issues you created
3. Move Issue #1 (`greet()` function) to **In Progress**

---

##  Part 4 — Branching and Making Changes

### Step 9: Create a Feature Branch

Best practice: one branch per issue, named descriptively.

```bash
# Make sure you're on main and up to date
git checkout main
git pull origin main

# Create and switch to a new branch
# Format: issue-NUMBER-short-description
git checkout -b issue-1-add-greet-function
```

### Step 10: Write the Code

Create a new file called `index.js`:

```bash
touch index.js
```

Open `index.js` in your editor and add:

```javascript
/**
 * Returns a greeting string for the given name.
 * @param {string} name - The name to greet
 * @returns {string} A greeting message
 */
function greet(name) {
  return `Hello, ${name}!`;
}

module.exports = { greet };
```

### Step 11: Commit and Push

```bash
# Stage your changes
git add index.js

# Commit with a message that references the issue
# Using "Closes #1" will auto-close the issue when the PR merges
git commit -m "feat: add greet() function

Implements a greet function that accepts a name and returns
a formatted greeting string.

Closes #1"

# Push the branch to GitHub
git push origin issue-1-add-greet-function
```

> ✅ **Checkpoint:** You should see your branch listed on GitHub under **Code** → **Branches**.

---

##  Part 5 — Pull Requests and Code Review

### Step 12: Open a Pull Request

1. Go to your repository on GitHub
2. You'll see a banner: **"issue-1-add-greet-function had recent pushes"** → click **Compare & pull request**
3. Fill in the PR form:

**Title:**
```
feat: add greet() utility function
```

**Body:**
```markdown
## Summary
Adds a `greet(name)` function to `index.js` that returns a formatted greeting string.

## Changes
- Created `index.js` with exported `greet()` function
- Added JSDoc comment for documentation

## Related Issue
Closes #1

## Testing
To verify manually:
```js
const { greet } = require('./index.js');
console.log(greet('GitHub')); // "Hello, GitHub!"
```

## Checklist
- [x] Code is readable and commented
- [x] Follows project naming conventions
- [x] Issue is referenced in commit message
```

4. Set **Reviewers** → assign a classmate or yourself (for solo practice)
5. Set **Labels** → `needs-review`
6. Link to **Project** in the right sidebar → select `Lab Sprint Board`
7. Click **Create Pull Request**

### Step 13: Move Card on the Project Board

1. Go to your **Lab Sprint Board**
2. Drag the Issue #1 card from **In Progress** → **In Review**

---

### Step 14: Conduct a Code Review

Now switch roles — either have a partner review your PR, or review it yourself as a practice exercise.

**To start a review:**

1. Open the Pull Request
2. Click the **Files changed** tab
3. Hover over a line of code → click the **blue `+`** icon to add a comment

**Practice adding these review comments:**

**Comment on line with `return`:**
> 💬 *"Looks good! One thought — should we handle the case where `name` is `null` or `undefined`? Could return `'Hello, Guest!'` as a fallback."*

**Comment on the JSDoc block:**
> 💬 *"Great that you included JSDoc — this will help with auto-generated docs. Consider adding an `@example` tag."*

**To submit your review:**

1. Click **Review changes** (top right of Files changed tab)
2. Write a summary comment:
```
Overall this looks clean and well-documented. Left a couple of suggestions — 
feel free to address or discuss. Happy to approve once you've had a look!
```
3. Choose one of:
   - ✅ **Approve** — code is ready to merge
   - 💬 **Comment** — general feedback, no decision
   - ❌ **Request changes** — must be addressed before merging
4. Click **Submit review**

---

### Step 15: Respond to Review Feedback

1. Go back to the PR **Conversation** tab
2. Reply to the inline comment:
```
Good point! I'll add a null check in a follow-up issue to keep this PR focused.
Happy to add the @example tag now though.
```
3. Click **Resolve conversation** on any addressed comments

### Step 16: Approve and Merge

Once the review is approved:

1. Click **Merge pull request**
2. Choose **Squash and merge** (keeps history clean)
3. Confirm the commit message, then click **Confirm squash and merge**
4. Click **Delete branch** to clean up

>  **Checkpoint:** Issue #1 should now be automatically **closed** (because you used `Closes #1` in your commit).

---

## Part 6 — Fix the Documentation Issue

Now practice the same workflow for Issue #2.

### Step 17: Create a Branch for the README Fix

```bash
git checkout main
git pull origin main
git checkout -b issue-2-update-readme
```

### Step 18: Update the README

Open `README.md` and replace its contents:

```markdown
# GitHub Lab Practice

A practice repository for learning GitHub workflows including issues, pull requests, and code reviews.

## Prerequisites

- [Node.js](https://nodejs.org/) v18 or higher
- Git installed locally
- A GitHub account

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/YOUR-USERNAME/github-lab-practice.git
   cd github-lab-practice
   ```

2. Install dependencies (when applicable):
   ```bash
   npm install
   ```

## Usage

```javascript
const { greet } = require('./index.js');
console.log(greet('World')); // Hello, World!
```

## Contributing

Please read our contributing guidelines and open an issue before submitting a pull request.
```

### Step 19: Commit, Push, and Open a PR

```bash
git add README.md
git commit -m "docs: add setup instructions to README

Adds prerequisites, installation steps, and usage example.

Closes #2"

git push origin issue-2-update-readme
```

Open a Pull Request on GitHub following the same steps as Part 5.

---

## 🏁 Part 7 — Final Review and Wrap-Up

### Step 20: Review Your Project Board

Go to **Lab Sprint Board** and verify:

- Both issues are in the **Done** column
- All cards show as closed
- The board accurately reflects your completed work

### Step 21: Review Your Closed Issues

1. Click the **Issues** tab
2. Click **Closed**
3. Confirm both issues appear as closed with the merge reference

---

## 🎯 Lab Challenges (Bonus)

Complete these if you finish early:

1. **Milestones** — Create a milestone called `v1.0` and assign both issues to it. Mark it as complete.

2. **Issue Templates** — Go to **Settings** → **Features** → **Issues** → **Set up templates**. Create a Bug Report and Feature Request template.

3. **Branch Protection** — Go to **Settings** → **Branches** → **Add rule**. Require at least 1 approving review before merging to `main`.

4. **Draft PR** — Create a new branch, make a small change, and open a **Draft Pull Request**. Practice converting it to a ready PR.

5. **Stacked Review** — Leave a multi-line comment block on a PR using the click-and-drag method on the Files Changed tab.

---

## ✅ Lab Completion Checklist

Before submitting, confirm you have completed each item:

- [ ] Created 2 GitHub Issues with appropriate labels
- [ ] Added issues to a GitHub Project board
- [ ] Created feature branches using the `issue-NUMBER-description` naming convention
- [ ] Wrote meaningful commit messages referencing issue numbers
- [ ] Opened Pull Requests with descriptive bodies and checklists
- [ ] Left at least 2 inline code review comments
- [ ] Submitted a formal review (Approve or Request Changes)
- [ ] Merged both PRs using Squash and Merge
- [ ] Confirmed both issues are automatically closed
- [ ] Project board shows all items in Done

---

## 📚 Key Concepts Reference

| Concept | Description |
|---|---|
| **Issue** | A trackable unit of work: bug, feature, task, or question |
| **Label** | Color-coded tag to categorize and filter issues |
| **Milestone** | A target grouping issues toward a release or date |
| **Branch** | An isolated line of development tied to a feature or fix |
| **Pull Request (PR)** | A request to merge one branch into another |
| **Code Review** | Structured feedback on proposed changes before merging |
| **Inline Comment** | Feedback attached to a specific line of code in a PR |
| **Squash Merge** | Combine all commits in a PR into one before merging |
| **Closes #N** | Magic keyword that auto-closes issue N when PR merges |
| **Project Board** | Kanban-style view for tracking issues across workflows |
| **Draft PR** | A PR marked as not ready for review — work in progress |
| **Branch Protection** | Rules that restrict who can push or merge to a branch |

---

*Lab created for GitHub fundamentals training. Estimated time: 60–90 minutes.*
