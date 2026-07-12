# Lab 1 — GitHub Setup & Configuration

**Course:** IA 462 — Advanced Operating Systems Security & Administration  
**Points:** 75 points  
**Submission:** Upload your screen-recording video (.wmv) and any required files via Canvas

> **Objectives & Outcomes:** Refer to syllabus for course objectives and grading criteria.

---

## 📋 Prerequisites

Before starting this lab, ensure you have:

- [ ] A working computer running Windows 10/11, Linux (Ubuntu), or macOS
- [ ] VirtualBox installed (or a VM environment ready)
- [ ] At least 8 GB RAM and 30 GB free disk space
- [ ] Screen recording software installed (e.g., https://www.freescreenrecording.com/)
- [ ] Your EMU email address accessible

---

## 🛠️ Lab Environment Setup

### Step 1 — Install Git

**Windows:**
1. Download Git for Windows from https://git-scm.com/download/win
2. Run the installer with default settings
3. Open **Git Bash** and verify the installation:
   ```bash
   git --version
   ```

**Linux (Ubuntu):**
```bash
sudo apt update && sudo apt install git -y
git --version
```

**macOS:**
```bash
xcode-select --install
git --version
```

---

## 📝 Lab Instructions

### Part 1 — Create a GitHub Account

1. Navigate to https://github.com
2. Click **Sign Up** and register using your **EMU email address** (`@emich.edu`)
3. Choose a professional username (e.g., `jsmith-emu` or similar)
4. Verify your email address
5. **Screenshot:** Take a screenshot of your GitHub profile page showing your username

### Part 2 — Configure Git Locally

Open a terminal (Git Bash on Windows, Terminal on Linux/macOS) and configure your identity:

```bash
git config --global user.name "Your Full Name"
git config --global user.email "yourname@emich.edu"
git config --global core.editor "nano"
```

Verify your configuration:
```bash
git config --list
```

**Screenshot:** Take a screenshot of the `git config --list` output.

### Part 3 — Clone the Course Repository

1. Navigate to the course GitHub organization (link provided in Canvas)
2. Find the course lab repository
3. Click the green **Code** button and copy the HTTPS clone URL
4. In your terminal, clone the repository:
   ```bash
   git clone <paste-repo-url-here>
   ```
5. Navigate into the cloned directory:
   ```bash
   cd <repo-name>
   ls -la
   ```

**Screenshot:** Take a screenshot showing the successful clone and directory listing.

### Part 4 — Explore the Repository Structure

1. Review the repository structure — identify the Lab folders, README files, and any configuration files
2. Open the `README.md` at the root of the repository and read through it
3. In your terminal, view the Git log to understand the commit history:
   ```bash
   git log --oneline -10
   ```

**Screenshot:** Take a screenshot of the `git log` output.

### Part 5 — Explore Repository Security Features

Perform the following hands-on tasks in your GitHub repository:

1. Navigate to the **Security** tab of your repository on GitHub.com — take a screenshot
2. Click on **Dependabot alerts** (if available) — screenshot the page
3. Go to **Settings → Code security and analysis** — screenshot the available options
4. Create a file named `SECURITY.md` in your repository with placeholder text:
   ```bash
   echo "# Security Policy" > SECURITY.md
   echo "## Reporting a Vulnerability" >> SECURITY.md
   echo "Please report security issues to the course instructor." >> SECURITY.md
   git add SECURITY.md
   git commit -m "Add security policy"
   git push origin main
   ```
5. Verify the `SECURITY.md` appears on your repository's **Security** tab on GitHub.com — screenshot

### Part 6 — Record Your Video Walkthrough

Using your screen recording software, record a video demonstrating:

1. Your GitHub profile page
2. Your local Git configuration (`git config --list`)
3. The cloned repository directory and file listing
4. A walkthrough of the repository structure
5. The Security tab and SECURITY.md file on GitHub

> **⚠️ Critical:** Export your video in `.wmv` format before uploading to Canvas.

---

## 📤 Submission Requirements

Submit the following to Canvas by the due date:

| Item | Format | Required |
|------|--------|----------|
| Video walkthrough | `.wmv` | ✅ Yes |
| Screenshots (all parts) | `.png` or `.jpg` | ✅ Yes |

---

## 💡 Tips & Resources

- **Git Cheat Sheet:** https://education.github.com/git-cheat-sheet-education.pdf
- **GitHub Docs:** https://docs.github.com
- If you get stuck, post in the course Slack channel.

---

> **📌 Reminder:** All work must be your own. Refer to syllabus for full course policies.
