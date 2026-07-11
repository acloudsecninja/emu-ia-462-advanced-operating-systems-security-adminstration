# Midterm Exam — IA 462 Advanced Operating Systems Security & Administration

**Course:** IA 462 — Advanced Operating Systems Security & Administration  
**Points:** 200 points  
**Format:** Take-home practical exam + written response  
**Submission:** Submit all files via Google Drive share link (see syllabus for submission instructions)  
**Due Date:** See Canvas for exact due date

> **Objectives & Outcomes:** Refer to syllabus for course objectives and grading criteria.

---

## 📌 Overview

The midterm exam covers all material from **Weeks 1 through 8** of the course. It consists of two components:

- **Part A — Written Knowledge Assessment**
- **Part B — Practical Hands-On Demonstration**

> **⚠️ Academic Integrity:** This is an individual exam. You may reference course materials, notes, and publicly available documentation. You may NOT collaborate with other students or use AI tools to generate any portion of your answers. Violations will result in a zero and may be referred for academic misconduct review.

---

## 📚 Topics Covered (Weeks 1–8)

- Course orientation and GitHub.com setup
- Supply Chain Security fundamentals (concepts, attack vectors, mitigations)
- Linux security fundamentals — user management, permissions, services
- Linux system hardening techniques (Lynis, CIS benchmarks)
- Windows security policy and hardening basics
- Docker container architecture and security
- Docker container hardening (non-root user, image pinning, minimal base images)
- Software Bill of Materials (SBOM) concepts and generation
- Dependency pinning and version control as a security control
- CI/CD security pipelines — concepts and GitHub Actions introduction

---

## ✍️ Part A — Written Knowledge Assessment

Submit your answers in a Word document (`.docx`) named `midterm-partA-<yourname>.docx`.  
**APA format, double-spaced, minimum length noted per question.**

---

### Section 1 — Supply Chain Security

**Question 1:**

Write a detailed essay (minimum 1.5 pages) responding to the following:

> *"Supply chain attacks have become one of the most significant threats to enterprise organizations. Discuss the anatomy of a supply chain attack — how it begins, how it propagates, and why it is so difficult to detect. Use at least one real-world example (e.g., SolarWinds, XZ Utils, Log4Shell supply chain context) to support your answer. What technical controls covered in this course could have mitigated or detected the attack?"*

Your response must include:
- Definition and explanation of a software supply chain attack
- Step-by-step breakdown of how the attack you chose unfolded
- At least two technical controls from this course that apply
- Your personal assessment of the biggest challenge in defending the supply chain

**Question 2:**

Answer the following short-answer questions:

**2a.** What is a Software Bill of Materials (SBOM)? Explain its purpose and why CISA and the U.S. Federal Government have made SBOM a priority in recent years.

**2b.** Explain dependency pinning. Provide a concrete example showing the difference between a pinned and unpinned dependency in a `requirements.txt` or `Dockerfile`. Why does unpinned = security risk?

**2c.** What is the difference between a vulnerability scanner that scans OS packages vs. one that scans application dependencies? Provide one tool example for each category.

---

### Section 2 — Linux Security

**Question 3:**

Write a step-by-step hardening guide (minimum 1 page) for a freshly installed Ubuntu 22.04 LTS server. Your guide must cover at least **six** of the following areas:

- User account management and password policies
- SSH daemon configuration
- Disabling unnecessary services
- File permission hardening (SUID/SGID/world-writable files)
- Firewall configuration (UFW)
- Kernel parameter hardening (`/etc/sysctl.conf`)
- Automated security auditing (Lynis)
- Log monitoring and auditd

For each area, provide:
1. The specific hardening action
2. The Linux command(s) to implement it
3. Why this action improves security

**Question 4:**

Answer the following short-answer questions:

**4a.** Explain the Linux permission model. What do the read (r), write (w), and execute (x) bits mean for files vs. directories? What is the risk of a world-writable directory?

**4b.** What is the difference between `chmod 700`, `chmod 755`, and `chmod 777`? In what scenarios would you use each, and which would you never use in a hardened environment and why?

**4c.** Describe three steps you would take to harden SSH on a Linux server to reduce the risk of unauthorized access.

---

### Section 3 — Docker Container Security

**Question 5:**

You are given the following Dockerfile:

```dockerfile
FROM ubuntu

RUN apt-get update && apt-get install -y python3 python3-pip curl wget vim

COPY . /app
WORKDIR /app

RUN pip3 install -r requirements.txt

CMD ["python3", "app.py"]
```

Write a **detailed security analysis** (minimum 1.5 pages) that:
1. Identifies every security problem in this Dockerfile (aim for at least 6 issues)
2. Explains why each issue is a security concern
3. Provides the corrected/hardened version of the Dockerfile with inline comments explaining each change
4. Explains the concept of "minimal attack surface" in the context of container security

**Question 6:**

Write a mini-essay (minimum 1 page) responding to:

> *"An organization has decided to move their entire application stack to Docker containers. They ask you to design a container security strategy for them from the ground up. What are the key components of your strategy? How do SBOM, vulnerability scanning, image signing, non-root execution, and CI/CD security gates all work together to form a defense-in-depth approach?"*

---

## 🖥️ Part B — Practical Hands-On Demonstration

Submit a **screen-recording video** (`.wmv` format) and any supporting files listed below.

---

### Practical Task 1 — Linux Hardening Demonstration

On your Ubuntu Linux VM, perform and record the following:

**Task 1a:** User & Service Hardening
- Create a user named `midterm-user` with no sudo access
- Lock the account for a user named `unused-svc` (create it first if needed)
- List all running services
- Identify and disable one unnecessary service
- Show the service is now inactive

**Task 1b:** File Permissions & SUID Audit
- Create a directory `~/midterm-secure/` and a file `secret-data.txt` inside it
- Set permissions so only the owner can read/write the file (`chmod 600`)
- Run the command to find all SUID binaries on the system
- Save the output to `suid-audit.txt`

**Task 1c:** Run Lynis and Review Results
- Run a full Lynis audit: `sudo lynis audit system`
- Save the report to `midterm-lynis.txt`
- In a separate file `midterm-lynis-review.txt`, summarize:
  - Your Hardening Index score
  - 3 warnings found
  - 5 suggestions and your plan to implement them

**Screenshot requirements:** Screenshots at each step showing the commands and outputs.

---

### Practical Task 2 — Docker Security Demonstration

**Task 2a:** Build a Hardened Container
- Create a directory `~/midterm-docker/`
- Write a hardened `Dockerfile` using:
  - A pinned base image (with digest)
  - A non-root user
  - Minimal dependencies
  - A HEALTHCHECK instruction
  - Proper `LABEL` metadata
- Build the image: `docker build -t midterm-secure:v1 .`
- Run it and confirm it runs as a non-root user (`docker exec <container> whoami`)

**Task 2b:** Generate SBOM
- Use Syft to generate an SBOM for your image:
  ```bash
  syft midterm-secure:v1 -o table
  syft midterm-secure:v1 -o json > midterm-sbom.json
  ```
- Narrate in your video: how many packages are in the SBOM and what it tells you

**Task 2c:** Vulnerability Scan
- Use Grype to scan your image:
  ```bash
  grype midterm-secure:v1 2>&1 | tee midterm-vuln-scan.txt
  ```
- Narrate in your video: total finding count, severity breakdown, and one CVE you would prioritize remediating

---

### Practical Task 3 — GitHub CI/CD Security Gate

**Task 3a:** Create a GitHub repository named `ia462-midterm-pipeline` with:
- A simple application `Dockerfile`
- A `requirements.txt` with pinned dependencies
- A GitHub Actions workflow (`.github/workflows/midterm-security.yml`) that:
  - Builds the Docker image
  - Runs Trivy for vulnerability scanning
  - Fails the pipeline on CRITICAL findings
  - Generates an SBOM as a pipeline artifact

**Task 3b:** Demonstrate the pipeline in action:
- Push code and show the pipeline running in GitHub Actions
- Show the pipeline passing (no critical findings) or explain the finding
- Download and show the SBOM artifact

**Screenshot requirements:** Screenshots of the GitHub Actions pipeline runs and artifact downloads.

---

## 📤 Submission Requirements

Submit the following via Google Drive share link (see syllabus for submission email) by the Canvas due date:

| Item | Format | Required |
|------|--------|----------|
| Part A written responses | `.docx` | ✅ Yes |
| Video walkthrough (Part B) | `.wmv` | ✅ Yes |
| `suid-audit.txt` | Plain text | ✅ Yes |
| `midterm-lynis.txt` | Plain text | ✅ Yes |
| `midterm-lynis-review.txt` | Plain text | ✅ Yes |
| `midterm-sbom.json` | JSON | ✅ Yes |
| `midterm-vuln-scan.txt` | Plain text | ✅ Yes |
| GitHub repository URL (in submission comment) | URL | ✅ Yes |
| Screenshots | `.png` or `.jpg` | ✅ Yes |

---

## 💡 Study Guide — Key Topics to Review

- **Supply Chain Security:** SolarWinds, XZ Utils, Log4Shell; SLSA framework levels
- **Linux:** `chmod`, `chown`, `passwd`, `usermod`, `systemctl`, SSH config, Lynis
- **Docker:** Dockerfile best practices, non-root containers, image digests, HEALTHCHECK
- **SBOM:** SPDX format, Syft tool, what's included in an SBOM
- **Vulnerability Scanning:** Grype, Trivy, CVE severity levels (CVSS)
- **CI/CD:** GitHub Actions syntax, jobs/steps/artifacts, security gates
- **Dependency Management:** Pinned vs. unpinned versions, pip-audit

---

## ⏰ Time Management Tips

- Start with Part A written questions while your VM is booting
- Budget ~45 minutes per Practical Task
- Record your video in one continuous session for each practical task when possible
- Label your screenshots clearly before submitting

---

> **📌 Reminder:** All work must be your own. Refer to syllabus for full course policies.
