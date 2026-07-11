# Lab 4 — GitHub Actions & Automation Pipelines

**Course:** IA 462 — Advanced Operating Systems Security & Administration  
**Points:** 75 points  
**Submission:** Upload your screen-recording video (.wmv), GitHub repository link, and required files via Canvas

> **Objectives & Outcomes:** Refer to syllabus for course objectives and grading criteria.

---

## 📋 Prerequisites

Before starting this lab, ensure you have:

- [ ] Labs 1, 2, and 3 completed
- [ ] A GitHub.com account configured (Lab 1)
- [ ] Docker installed on your system (Lab 3)
- [ ] Access to the student code upload repository (invited via EMU email)
- [ ] Screen recording software ready

---

## 🗂️ Lab Repository Structure

Your lab repository should have the following structure when complete:

```
lab4-cicd-security/
├── .github/
│   └── workflows/
│       └── security-pipeline.yml
├── Dockerfile
├── requirements.txt
├── app/
│   └── app.py
├── .trivyignore            (optional)
└── README.md
```

---

## 📝 Lab Instructions

### Part 1 — Set Up Your Lab Repository

#### 1a — Create the Repository

1. Go to your GitHub account and create a **new public repository** named `ia462-lab4-cicd`
2. Clone it to your local machine:
   ```bash
   git clone https://github.com/<your-username>/ia462-lab4-cicd.git
   cd ia462-lab4-cicd
   ```

#### 1b — Create the Application Files

Create a simple Python Flask app (`app/app.py`):

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return "IA 462 Lab 4 — Secure CI/CD Pipeline"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

Create `requirements.txt` with **pinned versions**:
```
flask==3.0.3
werkzeug==3.0.3
```

> **⚠️ Dependency pinning is a supply chain security best practice.** Using unpinned versions (e.g., `flask>=1.0`) allows unexpected updates that could introduce vulnerabilities.

Create a secure `Dockerfile`:
```dockerfile
FROM python:3.11-slim

LABEL maintainer="student@emich.edu"
LABEL description="IA 462 Lab 4 — Secure CI/CD App"

RUN groupadd -r appgroup && useradd -r -g appgroup appuser

WORKDIR /app

COPY --chown=appuser:appgroup requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY --chown=appuser:appgroup app/ ./app/

USER appuser

EXPOSE 5000

CMD ["python3", "app/app.py"]
```

**Screenshot:** Show your local directory structure.

---

### Part 2 — Configure the GitHub Actions Security Pipeline

Create `.github/workflows/security-pipeline.yml`:

```yaml
name: Security CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  # ── Job 1: Build Docker Image ──────────────────────────────────────────────
  build:
    name: Build Container Image
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build Docker Image
        run: |
          docker build -t ia462-lab4:${{ github.sha }} .
          docker save ia462-lab4:${{ github.sha }} -o image.tar

      - name: Upload image artifact
        uses: actions/upload-artifact@v4
        with:
          name: docker-image
          path: image.tar

  # ── Job 2: Vulnerability Scan with Trivy ──────────────────────────────────
  vulnerability-scan:
    name: Vulnerability Scan (Trivy)
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Download image artifact
        uses: actions/download-artifact@v4
        with:
          name: docker-image

      - name: Load Docker image
        run: docker load -i image.tar

      - name: Run Trivy Vulnerability Scanner
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: ia462-lab4:${{ github.sha }}
          format: 'table'
          exit-code: '1'           # Fail pipeline on CRITICAL vulnerabilities
          ignore-unfixed: true
          vuln-type: 'os,library'
          severity: 'CRITICAL,HIGH'
          output: trivy-results.txt

      - name: Upload Trivy Scan Results
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: trivy-results
          path: trivy-results.txt

  # ── Job 3: Generate SBOM ──────────────────────────────────────────────────
  sbom:
    name: Generate SBOM
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Download image artifact
        uses: actions/download-artifact@v4
        with:
          name: docker-image

      - name: Load Docker image
        run: docker load -i image.tar

      - name: Install Syft
        run: curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b /usr/local/bin

      - name: Generate SBOM
        run: |
          syft ia462-lab4:${{ github.sha }} -o spdx-json > sbom.spdx.json
          syft ia462-lab4:${{ github.sha }} -o table

      - name: Upload SBOM Artifact
        uses: actions/upload-artifact@v4
        with:
          name: sbom
          path: sbom.spdx.json

  # ── Job 4: Dependency Audit ───────────────────────────────────────────────
  dependency-audit:
    name: Python Dependency Audit
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install pip-audit
        run: pip install pip-audit

      - name: Run pip-audit
        run: pip-audit -r requirements.txt --output audit-results.txt || true

      - name: Upload audit results
        uses: actions/upload-artifact@v4
        with:
          name: pip-audit-results
          path: audit-results.txt
```

Commit and push:
```bash
git add .
git commit -m "feat: Add secure Dockerfile, app, and CI/CD security pipeline"
git push origin main
```

**Screenshot:** Take a screenshot of the GitHub Actions pipeline running in your repository.

---

### Part 3 — Review Pipeline Results

#### 3a — Verify Pipeline Execution

1. Navigate to your GitHub repository → **Actions** tab
2. Click on the most recent workflow run
3. Review each job's status and output

**Screenshot:** Capture the completed pipeline showing all jobs (✅ passed or ❌ failed with reason).

#### 3b — Review Vulnerability Scan Output

1. Download the `trivy-results` artifact from the pipeline run
2. Open the file and review the findings

In `lab4-analysis.txt`, answer:
1. Did any CRITICAL or HIGH vulnerabilities block the pipeline? Which ones?
2. What package/library is affected and what is the CVE?
3. How would you remediate the most critical finding?

#### 3c — Review the SBOM

1. Download the `sbom` artifact
2. Open `sbom.spdx.json` and review the package inventory

In `lab4-analysis.txt`, answer:
1. How many packages are listed in the SBOM?
2. What SPDX version is used?
3. How does an SBOM help with supply chain security in a CI/CD pipeline?

---

### Part 4 — Introduce and Fix a Vulnerability

#### 4a — Introduce a Known Vulnerable Dependency

Modify `requirements.txt` to introduce an older, vulnerable version:
```
flask==2.0.0
werkzeug==2.0.0
```

Commit and push:
```bash
git add requirements.txt
git commit -m "test: introduce older dependency version for vulnerability testing"
git push origin main
```

**Screenshot:** Capture the pipeline failing on the vulnerability scan.

#### 4b — Fix the Vulnerability

Restore the pinned secure versions:
```
flask==3.0.3
werkzeug==3.0.3
```

Commit and push:
```bash
git add requirements.txt
git commit -m "fix: pin dependencies to secure versions"
git push origin main
```

**Screenshot:** Capture the pipeline passing after the fix.

---

### Part 5 — Video Walkthrough

Record a video demonstrating:

1. Your GitHub repository structure
2. The GitHub Actions pipeline running (all 4 jobs visible)
3. The Trivy vulnerability scan results
4. The SBOM artifact download and preview
5. The "introduce vulnerability → pipeline fails → fix → pipeline passes" cycle
6. A brief verbal explanation of **why automated security gates in CI/CD are important**

> **⚠️ Critical:** Export your video in `.wmv` format before uploading to Canvas.

---

## 📤 Submission Requirements

| Item | Format | Required |
|------|--------|----------|
| Video walkthrough | `.wmv` | ✅ Yes |
| GitHub repository URL | URL (in Canvas submission comment) | ✅ Yes |
| `lab4-analysis.txt` | Plain text | ✅ Yes |
| Screenshots | `.png` or `.jpg` | ✅ Yes |

---

## 💡 Tips & Resources

- **GitHub Actions Documentation:** https://docs.github.com/en/actions
- **Trivy Scanner:** https://github.com/aquasecurity/trivy
- **Syft:** https://github.com/anchore/syft
- **pip-audit:** https://github.com/pypa/pip-audit
- If you get stuck, post in the course Slack channel.

---

> **📌 Reminder:** All work must be your own. Refer to syllabus for full course policies.
