# Lab 3 — Docker Containers & Configuration Management

**Course:** IA 462 — Advanced Operating Systems Security & Administration  
**Points:** 75 points  
**Submission:** Upload your screen-recording video (.wmv) and required files via Canvas

> **Objectives & Outcomes:** Refer to syllabus for course objectives and grading criteria.

---

## 📋 Prerequisites

Before starting this lab, ensure you have:

- [ ] Labs 1 and 2 completed
- [ ] Docker Desktop (Windows/macOS) or Docker Engine (Linux) installed
- [ ] A running Ubuntu Linux VM or Docker on your host machine
- [ ] Git configured from Lab 1
- [ ] Screen recording software ready

---

## 🛠️ Lab Environment Setup

### Install Docker (Ubuntu VM)

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
sudo usermod -aG docker $USER
```

> Log out and back in (or run `newgrp docker`) for the group change to take effect.

Verify Docker:
```bash
docker --version
docker run hello-world
```

**Screenshot:** Capture the `hello-world` successful output.

---

## 📝 Lab Instructions

### Part 1 — Build a Secure Dockerfile

#### 1a — Create an Insecure Dockerfile (Baseline)

Create a project directory:
```bash
mkdir ~/lab3-docker && cd ~/lab3-docker
```

Create an intentionally insecure Dockerfile to start with:
```dockerfile
# Insecure Dockerfile — DO NOT use in production
FROM ubuntu:latest

RUN apt-get update && apt-get install -y \
    curl \
    wget \
    python3 \
    python3-pip

COPY . /app
WORKDIR /app

# Running as root — INSECURE
CMD ["python3", "-m", "http.server", "8080"]
```

Save this as `Dockerfile.insecure` and review it:
```bash
nano Dockerfile.insecure
```

In your video, verbally identify at least **3 security issues** with this Dockerfile as you review it on screen.

#### 1b — Build a Hardened Dockerfile

Create a secure version named `Dockerfile`:

```dockerfile
# Hardened Dockerfile — IA 462 Lab 3
# Pin to a specific digest to prevent unexpected changes (supply chain security)
FROM python:3.11-slim@sha256:<replace-with-actual-digest>

# Metadata labels
LABEL maintainer="student@emich.edu"
LABEL version="1.0"
LABEL description="IA 462 Hardened Container"

# Create a non-root user
RUN groupadd -r appgroup && useradd -r -g appgroup appuser

# Install only required dependencies — pin versions
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl=7.88.1-10+deb12u5 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Copy application files
COPY --chown=appuser:appgroup . /app

# Switch to non-root user
USER appuser

# Use a non-privileged port
EXPOSE 8080

# Healthcheck
HEALTHCHECK --interval=30s --timeout=5s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:8080 || exit 1

CMD ["python3", "-m", "http.server", "8080"]
```

> **⚠️ Note on digest pinning:** To get the actual digest, run:
> ```bash
> docker pull python:3.11-slim
> docker inspect python:3.11-slim | grep -i digest
> ```
> Replace `<replace-with-actual-digest>` with the actual SHA256 value.

Build the hardened image:
```bash
docker build -t lab3-secure:v1 .
```

**Screenshot:** Capture the successful build output.

#### 1c — Walk Through the Hardening Decisions

In your video, narrate:
1. What was insecure in `Dockerfile.insecure`
2. Walk through each line of the hardened Dockerfile explaining **why** each change improves security

---

### Part 2 — Run & Verify the Container

Run the container:
```bash
docker run -d -p 8080:8080 --name lab3-container lab3-secure:v1
```

Verify it is running:
```bash
docker ps
docker logs lab3-container
```

Inspect the running user inside the container:
```bash
docker exec lab3-container whoami
docker exec lab3-container id
```

**Screenshot:** Capture the output showing the container runs as the non-root user.

Stop and remove:
```bash
docker stop lab3-container
docker rm lab3-container
```

---

### Part 3 — Generate a Software Bill of Materials (SBOM)

A Software Bill of Materials (SBOM) is an inventory of all components and dependencies in a software artifact.

#### 3a — Install Syft (SBOM Generator)

```bash
curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b /usr/local/bin
syft version
```

#### 3b — Generate an SBOM for Your Image

```bash
syft lab3-secure:v1 -o json > lab3-sbom.json
syft lab3-secure:v1 -o table
```

**Screenshot:** Capture the SBOM table output showing the package inventory.

#### 3c — Review the SBOM

Open the JSON file and review:
```bash
cat lab3-sbom.json | python3 -m json.tool | head -60
```

Take screenshots showing:
1. The JSON output header and first few packages
2. The total package count (use `cat lab3-sbom.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(len(d.get('artifacts',d.get('packages',[]))))"`)
3. Narrate in your video what the SBOM contains

---

### Part 4 — Vulnerability Scanning with Grype

#### 4a — Install Grype

```bash
curl -sSfL https://raw.githubusercontent.com/anchore/grype/main/install.sh | sh -s -- -b /usr/local/bin
grype version
```

#### 4b — Scan Your Container Image

```bash
grype lab3-secure:v1 --output table 2>&1 | tee lab3-vuln-scan.txt
```

**Screenshot:** Capture the vulnerability scan output.

#### 4c — Review and Remediate

Review the scan results in your terminal:
- Note the severity breakdown: **Critical**, **High**, **Medium**, **Low**
- Pick one CVE from the output and look it up on https://nvd.nist.gov in your browser

Take screenshots showing:
1. The full Grype scan output with severity counts
2. The NVD page for the CVE you looked up

---

### Part 5 — Video Walkthrough

Record a video demonstrating:

1. Building both the insecure and secure Dockerfiles
2. The container running as a non-root user
3. SBOM generation output
4. Vulnerability scan results
5. Looking up a CVE on the NVD website

> **⚠️ Critical:** Export your video in `.wmv` format before uploading to Canvas.

---

## 📤 Submission Requirements

| Item | Format | Required |
|------|--------|----------|
| Video walkthrough | `.wmv` | ✅ Yes |
| `Dockerfile` (hardened) | Text file | ✅ Yes |
| `Dockerfile.insecure` (baseline) | Text file | ✅ Yes |
| `lab3-sbom.json` | JSON | ✅ Yes |
| `lab3-vuln-scan.txt` | Plain text | ✅ Yes |
| Screenshots (all parts) | `.png` or `.jpg` | ✅ Yes |

---

## 💡 Tips & Resources

- **Docker Docs:** https://docs.docker.com/
- **Syft:** https://github.com/anchore/syft
- **Grype:** https://github.com/anchore/grype
- **NVD CVE Database:** https://nvd.nist.gov
- If you get stuck, post in the course Slack channel.

---

> **📌 Reminder:** All work must be your own. Refer to syllabus for full course policies.
