# Final Exam & Project — IA 462 Advanced Operating Systems Security & Administration

**Course:** IA 462 — Advanced Operating Systems Security & Administration  
**Points:** 300 points  
**Format:** Comprehensive hands-on project + written exam + video demonstration  
**Submission:** Submit all files via Google Drive share link (see syllabus for submission instructions)  
**Due Date:** See Canvas for exact due date

> **Objectives & Outcomes:** Refer to syllabus for course objectives and grading criteria.

---

## 📌 Overview

The Final Exam covers all material from the **entire course (Weeks 1–16)** and builds upon your Midterm work. It is the capstone assessment for the course and consists of three components:

- **Part A — Written Final Exam**
- **Part B — GitHub Docker / Open-Source Security Project**
- **Part C — Comprehensive Video Demonstration**

> **⚠️ Academic Integrity:** This is an individual exam and project. You may reference course materials, notes, and public documentation. You may NOT collaborate with other students or use AI tools to generate any portion of your answers. Violations will result in a zero and may be referred for academic misconduct review.

---

## 📚 Topics Covered (Full Course — Weeks 1–16)

All Midterm topics, plus:

- Advanced CI/CD security pipeline configuration
- Third-party security scanner integration (Trivy, Grype, Snyk, etc.)
- SLSA framework and supply chain security maturity levels
- Image signing and verification (Cosign / Sigstore)
- Windows supply chain security techniques
- Continuous Vulnerability Management lifecycle
- CIS Benchmarks, NIST 800-53, DISA STIG overview
- Advanced Docker hardening (read-only filesystem, seccomp, AppArmor)
- Defense-in-depth for OS and container environments
- End-to-end secure software delivery pipeline

---

## ✍️ Part A — Written Final Exam

Submit your answers in a Word document (`.docx`) named `final-partA-<yourname>.docx`.  
**APA format, double-spaced, minimum length noted per question.**

---

### Section 1 — Advanced Supply Chain Security

**Question 1:**

Write a comprehensive essay (minimum 2 pages) responding to:

> *"You have been hired as a security consultant for a mid-size software company. They are moving their infrastructure to containers and cloud. Design a complete Supply Chain Security strategy for this organization. Your strategy must address: source code integrity, dependency management, container image security, CI/CD pipeline hardening, and runtime protection. Reference specific tools and frameworks covered in this course. How would you measure the maturity of their supply chain security posture over time?"*

Your response must include:
- At least one reference to the SLSA framework
- Specific tools (e.g., Syft, Grype/Trivy, Cosign, pip-audit, GitHub Actions)
- A tiered approach (what to do now vs. in 6 months vs. in a year)
- How SBOM fits into the overall strategy

**Question 2:**

Answer the following short-answer questions:

**2a.** What is image signing (e.g., Cosign/Sigstore), and how does it improve supply chain security for container images? Walk through the basic signing and verification flow.

**2b.** Compare and contrast the SLSA framework levels (SLSA 1 through 4). What does each level require, and which level would you recommend as a baseline for an enterprise organization?

---

### Section 2 — End-to-End OS Security

**Question 3:**

You have been given access to a production Linux server that has never been hardened. Write a **comprehensive hardening runbook** (minimum 2 pages) that a junior engineer could follow. Your runbook must cover:

- Initial system assessment (what to check first)
- User account audit and remediation
- Network and service hardening
- File system security (permissions, SUID audit, immutable flags)
- SSH hardening
- Kernel parameter tuning (`sysctl`)
- Firewall configuration (UFW or iptables)
- Log monitoring and audit setup
- Automated scanning (Lynis)
- Ongoing maintenance and re-assessment schedule

For each section, include the commands, expected output, and success criteria.

**Question 4:**

Answer the following short-answer questions:

**4a.** What is a CIS Benchmark? Choose one benchmark (Linux, Docker, or Windows) and describe 5 specific controls it recommends, including why each matters.

**4b.** What is defense-in-depth? Using the concept of OS and container security, illustrate how multiple security layers work together so that a single control failure does not result in a complete breach.

---

### Section 3 — Docker & Container Security Mastery

**Question 5:**

Write a comprehensive essay (minimum 1.5 pages) on:

> *"Advanced Docker container hardening goes beyond just running as a non-root user. Discuss the following advanced container security controls, explain what they protect against, and provide the Dockerfile or Docker run command syntax for each: read-only root filesystem, dropped Linux capabilities, seccomp profiles, and resource limits (CPU/memory). How do these controls align with the principle of least privilege?"*

**Question 6:**

Answer the following short-answer questions:

**6a.** What is the difference between an image vulnerability scanner (scanning a built image) and a Dockerfile linter (e.g., Hadolint)? Why are both valuable in a CI/CD pipeline?

**6b.** Explain what a "distroless" base image is. What are its security advantages and limitations compared to a slim base image?

---

### Section 4 — CI/CD Security Pipeline Mastery

**Question 7:**

Design a complete GitHub Actions CI/CD security pipeline for a containerized Python application. Your answer must include:

- The full YAML workflow file (annotated with explanations)
- Jobs for: build, SAST/dependency audit, container vulnerability scan, SBOM generation, and optional image signing
- Security gates that block deployment on critical findings
- Artifact management strategy
- An explanation of how this pipeline implements "shift-left security"

**Question 8:**

Answer the following short-answer questions:

**8a.** What is "shift-left security" and how does integrating security into CI/CD pipelines embody this concept?

**8b.** You discover that your production Trivy scan is generating 200+ vulnerability findings, making it hard to prioritize. Describe your triage strategy — how do you decide what to fix first and what to accept as risk?

---

## 🖥️ Part B — GitHub Docker / Open-Source Security Project

This is the major hands-on deliverable for the course. You will build, harden, and secure a containerized application from scratch and publish it to GitHub.

---

### Project Requirements

#### Repository Setup

Create a **public** GitHub repository named `ia462-final-project`. Your repository must contain:

```
ia462-final-project/
├── .github/
│   └── workflows/
│       └── security-pipeline.yml
├── app/
│   ├── app.py           (or equivalent application code)
│   └── requirements.txt (pinned versions only)
├── Dockerfile
├── .dockerignore
├── sbom/                (generated SBOM artifacts — committed for review)
├── scan-results/        (vulnerability scan results — committed for review)
└── README.md            (project documentation)
```

Your `README.md` must include:
- Project description
- Security controls implemented
- How to build and run the image locally
- Pipeline overview
- How to verify the SBOM

#### Application & Dockerfile

Build a containerized application (Python Flask, Node.js, or similar) with a hardened Dockerfile that demonstrates:

- [ ] Pinned base image with SHA256 digest
- [ ] Non-root user execution
- [ ] Minimal base image (slim or distroless)
- [ ] No sensitive data in image layers
- [ ] `.dockerignore` configured to exclude unnecessary files
- [ ] Pinned dependency versions in `requirements.txt` / `package.json`
- [ ] HEALTHCHECK instruction
- [ ] Proper `LABEL` metadata (maintainer, version, description)
- [ ] Read-only filesystem (`--read-only` in docker run, or documented)
- [ ] Dropped capabilities where applicable (documented)

#### CI/CD Security Pipeline

Your GitHub Actions pipeline must include all of the following jobs:

- Build Docker image (`docker build`)
- SBOM generation (Syft, SPDX JSON format)
- Container vulnerability scan with security gate (Trivy, fail on CRITICAL)
- Dependency audit (pip-audit or npm audit)
- Dockerfile linting (Hadolint)
- SBOM artifact upload (GitHub Actions artifact)

#### Security Documentation

Create `SECURITY.md` in the repository that documents:

1. **Threat Model:** What are the top 5 threats to your application, and how does your security implementation mitigate each?

2. **SBOM Review:** A summary of your SBOM findings — total packages, any concerning dependencies, and your assessment.

3. **Vulnerability Scan Review:** Summary of vulnerability scan results — total findings, severity breakdown, CVEs you identified and how you remediated or accepted them.

---

### Project Submission Checklist

- [ ] GitHub repository is **public**
- [ ] All code committed (no sensitive secrets in commits)
- [ ] Pipeline runs successfully (or failing items documented with explanation)
- [ ] `SECURITY.md` completed
- [ ] SBOM and scan result files committed to `sbom/` and `scan-results/` folders
- [ ] Repository URL submitted in Canvas

---

## 🎥 Part C — Comprehensive Video Demonstration

Record a **comprehensive video demonstration** (`.wmv` format) that walks through your entire final project and demonstrates mastery of course concepts.

---

### Video Section 1 — Linux Security Mastery

Demonstrate on your Ubuntu VM:

1. Complete user audit — show all users, identify any with UID 0, lock an unused account
2. Run a full Lynis audit and narrate your Hardening Index, top 3 warnings, and 5 remediation steps you would take
3. Demonstrate SSH hardening — show your `sshd_config` and explain each key setting
4. SUID/SGID audit — show the command, output, and explain which binaries concern you and why
5. Verbal explanation: What is the single most impactful Linux hardening step for reducing supply chain risk, and why?

---

### Video Section 2 — Docker Security Mastery

1. Build your final project Docker image and show it running as a non-root user
2. Walk through your Dockerfile line by line, explaining every security decision
3. Generate and walk through the SBOM — total packages, what information it provides
4. Run Grype or Trivy manually and walk through the findings
5. Verbal explanation: Why is image digest pinning a supply chain security control, and what attack does it prevent?

---

### Video Section 3 — GitHub CI/CD Pipeline Demonstration

1. Walk through your GitHub Actions pipeline YAML file — explain each job and why it exists
2. Show the pipeline running in GitHub Actions — all jobs completing (or explain failures)
3. Download and show the SBOM artifact from the pipeline run
4. Show the Trivy vulnerability scan results artifact

---

### Video Section 4 — Course Reflection

Deliver a 3–5 minute verbal reflection covering:

1. What was the most impactful concept you learned in this course and why?
2. How would you apply the security techniques from this course in a real-world job?
3. What is the biggest challenge organizations face in implementing supply chain security, in your opinion?
4. What would you do differently in your final project if you had more time?

> **⚠️ Critical:** Export your video in `.wmv` format. Incorrect formats cannot be graded.

---

## 📤 Submission Requirements

Submit all of the following via Google Drive share link (see syllabus for submission email) by the Canvas due date:

| Item | Format | Required |
|------|--------|----------|
| Part A written exam | `.docx` | ✅ Yes |
| Comprehensive video | `.wmv` | ✅ Yes |
| GitHub repository URL | URL (in Canvas submission comment) | ✅ Yes |
| `SECURITY.md` (also in GitHub, copy submitted separately) | Markdown | ✅ Yes |
| All screenshots referenced in video | `.png` or `.jpg` | ✅ Yes |

---

## 💡 Study Guide — Key Topics to Review (Full Course)

**Supply Chain:**
- SLSA Levels 1–4; SolarWinds, XZ Utils, Log4j; Cosign/Sigstore image signing
- SBOM formats (SPDX, CycloneDX); Syft tool; CISA SBOM mandate

**Linux:**
- `chmod`/`chown`/`umask`; `/etc/passwd`, `/etc/shadow`, `/etc/group`; systemctl
- SSH hardening; Lynis; UFW; `sysctl`; immutable files (`chattr +i`)
- CIS Ubuntu Benchmark

**Docker:**
- Non-root user; read-only filesystem; dropped capabilities; seccomp; distroless
- Image digest pinning; `HEALTHCHECK`; `.dockerignore`; Hadolint

**CI/CD:**
- GitHub Actions: jobs, steps, artifacts, secrets, `exit-code`
- Trivy / Grype; Syft; pip-audit; Hadolint
- Shift-left security; security gates; SBOM as pipeline artifact

**Frameworks:**
- SLSA; CIS Benchmarks; NIST 800-53 (basics); defense-in-depth

---

## ⏰ Time Management Recommendations

| Task | Suggested Time |
|------|---------------|
| Part A — Written exam | 4–6 hours |
| Part B — GitHub project (coding + pipeline) | 8–12 hours |
| Part C — Video recording | 1–2 hours |
| Review and polish | 2 hours |

> Start the project **at least one week before the due date**. CI/CD pipeline debugging takes time. Do not wait until the last day.

---

> **📌 Final Reminder:** All work must be your own. Refer to syllabus for full course policies. Good luck!
