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

### Step 1 — Install Git for Windows

#### 1A — Download Git for Windows

1. Open your web browser and navigate to: https://git-scm.com/download/win
2. The download should start automatically for the latest 64-bit version. If not, click the **"64-bit Git for Windows Setup"** link.
3. Save the installer (e.g., `Git-2.x.x-64-bit.exe`) to your Downloads folder.

#### 1B — Run the Git for Windows Installer

1. Double-click the downloaded `.exe` file to launch the installer.
2. Walk through the installer with the following recommended settings:
   - **Select Components:** Leave defaults checked (Windows Explorer integration, Git Bash Here, Git LFS)
   - **Default Editor:** Select your preferred editor (Nano or Notepad++ recommended for beginners)
   - **Adjusting PATH:** Select **"Git from the command line and also from 3rd-party software"** (recommended)
   - **SSH executable:** Select **"Use bundled OpenSSH"**
   - **HTTPS transport backend:** Select **"Use the OpenSSL library"**
   - **Line ending conversions:** Select **"Checkout Windows-style, commit Unix-style line endings"**
   - **Terminal emulator:** Select **"Use MinTTY"** (the default terminal of MSYS2)
   - **Default branch name:** Select **"Override the default branch name"** and enter `main`
   - **Extra options:** Leave defaults (Enable file system caching, Enable Git Credential Manager)
3. Click **Install** and wait for the installation to complete.
4. Click **Finish** (optionally check "Launch Git Bash").

#### 1C — Validate Git Installation on Windows

Open **Git Bash** (right-click desktop → "Git Bash Here", or search "Git Bash" in Start Menu):

```bash
# Check that git is installed and display the version
git --version
```

Expected output (version may vary):
```
git version 2.45.0.windows.1
```

Also verify Git is accessible from **Windows PowerShell**:
```powershell
# Open PowerShell (Win + X → Windows PowerShell) and run:
git --version
```

And from **Windows Command Prompt (cmd.exe)**:
```cmd
# Open CMD (Win + R → type cmd → Enter) and run:
git --version
```

> **✅ Validation Check:** If all three shells (Git Bash, PowerShell, CMD) return a version number, Git for Windows is installed correctly.

If `git` is not recognized in PowerShell or CMD, the PATH was not set correctly during installation. Reinstall Git and select **"Git from the command line and also from 3rd-party software"** at the PATH step.

#### 1D — Validate Git Works with WSL (Windows Subsystem for Linux)

If you are using WSL (Windows Subsystem for Linux), Git must also be installed **inside** the WSL environment separately — the Windows Git installation does not carry over into WSL.

**Step 1 — Open your WSL terminal:**
```powershell
# From PowerShell or CMD, launch your WSL distro:
wsl
```
Or search for **"Ubuntu"** (or your installed distro) in the Start Menu.

**Step 2 — Install Git inside WSL:**
```bash
sudo apt update && sudo apt install git -y
```

**Step 3 — Verify Git version inside WSL:**
```bash
git --version
```

Expected output:
```
git version 2.43.0
```

**Step 4 — Configure Git identity inside WSL:**
```bash
git config --global user.name "Your Full Name"
git config --global user.email "yourname@emich.edu"
```

**Step 5 — Validate the full Git workflow inside WSL:**
```bash
# Create a test directory
mkdir ~/git-test && cd ~/git-test

# Initialize a new Git repository
git init

# Create a test file
echo "Hello from WSL" > test.txt

# Stage the file
git add test.txt

# Commit the file
git commit -m "Test commit from WSL"

# View the commit log
git log --oneline

# Clean up the test directory
cd ~ && rm -rf ~/git-test
```

> **✅ Validation Check:** If `git log --oneline` shows your test commit, Git is fully functional inside WSL.

**Step 6 — (Optional) Configure WSL to use Windows Git Credential Manager:**

This allows WSL to share credentials with Windows so you don't have to authenticate twice:
```bash
git config --global credential.helper "/mnt/c/Program\ Files/Git/mingw64/bin/git-credential-manager.exe"
```

> **⚠️ Note:** The path above assumes Git for Windows was installed to the default location (`C:\Program Files\Git`). Adjust if your installation path differs.

---

### Step 1 (Alternative) — Install Git on Linux or macOS

**Linux (Ubuntu — standalone, not WSL):**
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

### Part 6 — Push Screenshots to Your Repository

All screenshots taken during this lab must be committed and pushed to your repository. This validates that your Git workflow (add, commit, push) is functioning correctly.

**Step 1 — Create a screenshots folder in your repo:**
```bash
mkdir -p Lab1/screenshots
```

**Step 2 — Move or copy your screenshots into the folder:**
```bash
# Copy all your lab screenshots into the folder
# Adjust the source path to wherever you saved them
cp ~/Desktop/screenshot-*.png Lab1/screenshots/
```

Name your files descriptively so they are easy to identify:
- `01-github-profile.png`
- `02-git-config-list.png`
- `03-clone-directory-listing.png`
- `04-git-log.png`
- `05-security-tab.png`
- `06-security-md-pushed.png`

**Step 3 — Stage, commit, and push the screenshots:**
```bash
git add Lab1/screenshots/
git status
git commit -m "Add Lab1 screenshots"
git push origin main
```

**Step 4 — Verify the push on GitHub:**
1. Navigate to your repository on GitHub.com
2. Confirm the `Lab1/screenshots/` folder is visible and contains all your images
3. **Screenshot:** Take a final screenshot showing the screenshots folder on GitHub

> **✅ Validation Check:** If you can see your screenshot files in the `Lab1/screenshots/` folder on GitHub.com, your Git push workflow is confirmed working.

---

### Part 7 — Record Your Video Walkthrough

Using your screen recording software, record a video demonstrating:

1. Your GitHub profile page
2. Your local Git configuration (`git config --list`)
3. The cloned repository directory and file listing
4. A walkthrough of the repository structure
5. The Security tab and SECURITY.md file on GitHub
6. The `Lab1/screenshots/` folder pushed to GitHub with all images visible

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
