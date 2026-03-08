# GitHub Actions — GCP Service Account Integration

## Step 1: Add SA Key to GitHub Secrets

1. Open your SA JSON file and **copy the entire contents**

2. Go to your GitHub repository:
   ```
   Settings → Secrets and variables → Actions
   ```

3. Click **"New repository secret"**

4. Fill in the fields:
   | Field | Value |
   |-------|-------|
   | **Name** | `GCP_SA_KEY` |
   | **Secret** | *(paste your full JSON content here)* |

5. Click **"Add secret"**

> ✅ Your SA key is now securely stored and will **never be exposed** in logs or workflow output.

---

## Step 2: Use SA Key in Your Workflow

Create a file at `.github/workflows/deploy.yml` in your repository:

```yaml
name: GCP Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Authenticate to GCP
        uses: google-github-actions/auth@v2
        with:
          credentials_json: '${{ secrets.GCP_SA_KEY }}'

      - name: Set up Cloud SDK
        uses: google-github-actions/setup-gcloud@v2

      - name: Verify authentication
        run: gcloud auth list
```

> 💡 After the `auth` step, all `gcloud`, `gsutil`, and `bq` commands are automatically authenticated.

---

## Quick Reference

| Item | Value |
|------|-------|
| Secret name | `GCP_SA_KEY` |
| Auth action | `google-github-actions/auth@v2` |
| SDK action | `google-github-actions/setup-gcloud@v2` |
| Verify command | `gcloud auth list` |
