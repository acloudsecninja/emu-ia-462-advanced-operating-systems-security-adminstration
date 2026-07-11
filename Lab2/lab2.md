# Lab 2 — Linux Administration & Configuration

**Course:** IA 462 — Advanced Operating Systems Security & Administration  
**Points:** 75 points  
**Submission:** Upload your screen-recording video (.wmv) and required files via Canvas

> **Objectives & Outcomes:** Refer to syllabus for course objectives and grading criteria.

---

## 📋 Prerequisites

Before starting this lab, ensure you have:

- [ ] Lab 1 completed
- [ ] A running Ubuntu Linux VM (20.04 LTS or 22.04 LTS recommended)
- [ ] Root or sudo access to the VM
- [ ] Git configured from Lab 1
- [ ] Screen recording software ready

---

## 🛠️ Lab Environment

This lab is performed inside your **Ubuntu Linux Virtual Machine**.

To start your VM:
1. Open VirtualBox
2. Start your Ubuntu VM
3. Open a terminal

---

## 📝 Lab Instructions

### Part 1 — User & Group Management (15 points)

#### 1a — Audit Existing Users

View all user accounts on the system:
```bash
cat /etc/passwd
```

View all groups:
```bash
cat /etc/group
```

Identify accounts with UID 0 (root-level access):
```bash
awk -F: '$3 == 0 {print $1}' /etc/passwd
```

**Screenshot:** Take a screenshot showing only the UID-0 accounts.

#### 1b — Create a Secure User Account

Create a new user for lab purposes:
```bash
sudo useradd -m -s /bin/bash labuser
sudo passwd labuser
```

Add the user to a specific group (do NOT add to sudo):
```bash
sudo groupadd labgroup
sudo usermod -aG labgroup labuser
```

Verify membership:
```bash
groups labuser
```

**Screenshot:** Take a screenshot showing the successful user creation and group assignment.

#### 1c — Lock an Unused Account

Identify any accounts that should not have login access and lock them:
```bash
sudo passwd -l <username>
```

Verify the lock:
```bash
sudo passwd -S <username>
```

**Screenshot:** Capture the status output showing the account is locked.

---

### Part 2 — File Permissions & Principle of Least Privilege (15 points)

#### 2a — Understanding Permissions

Create a test directory and file:
```bash
mkdir ~/lab2-test
echo "sensitive data" > ~/lab2-test/secret.txt
ls -la ~/lab2-test/
```

#### 2b — Apply Restrictive Permissions

Set the file to be readable only by the owner:
```bash
chmod 600 ~/lab2-test/secret.txt
ls -la ~/lab2-test/secret.txt
```

Set the directory to be accessible only by the owner:
```bash
chmod 700 ~/lab2-test/
ls -la ~ | grep lab2-test
```

#### 2c — Find World-Writable Files (Security Risk)

Search for world-writable files on the system:
```bash
sudo find / -xdev -type f -perm -0002 2>/dev/null | head -20
```

**Screenshot:** Take a screenshot of the world-writable file list.

#### 2d — Find SUID/SGID Binaries

Identify binaries with SUID bit set:
```bash
sudo find / -xdev -perm /4000 2>/dev/null
```

**Screenshot:** Capture the SUID binary list.

---

### Part 3 — Service Hardening (Reduce Attack Surface) (15 points)

#### 3a — List Running Services

```bash
systemctl list-units --type=service --state=running
```

#### 3b — Identify Unnecessary Services

Review the running services and identify at least **two** that are not required for a secure minimal system.

Common candidates for disabling in a hardened environment:
- `cups` (printing — usually not needed on servers)
- `avahi-daemon` (network discovery — not needed on isolated VMs)
- `bluetooth` (if no Bluetooth hardware)

#### 3c — Disable an Unnecessary Service

```bash
sudo systemctl stop <service-name>
sudo systemctl disable <service-name>
sudo systemctl status <service-name>
```

**Screenshot:** Take a screenshot showing the service is now `inactive (dead)`.

---

### Part 4 — SSH Hardening (15 points)

#### 4a — Review the SSH Configuration

```bash
sudo nano /etc/ssh/sshd_config
```

Locate and review the following settings:

#### 4b — Apply Hardening Settings

Make the following changes to `/etc/ssh/sshd_config`:

```
# Disable root login
PermitRootLogin no

# Use SSH Protocol 2 only
Protocol 2

# Disable password authentication (use keys instead)
PasswordAuthentication no

# Limit login attempts
MaxAuthTries 3

# Set login grace time
LoginGraceTime 30

# Disable empty passwords
PermitEmptyPasswords no
```

> **⚠️ Note:** Do NOT save `PasswordAuthentication no` unless you have SSH key authentication set up, or you will lock yourself out. For this lab, you may comment it out after reviewing.

Restart SSH to apply changes:
```bash
sudo systemctl restart sshd
sudo systemctl status sshd
```

**Screenshot:** Take a screenshot of the relevant `sshd_config` settings and the service status.

---

### Part 5 — Basic Security Audit with Lynis (15 points)

Lynis is an open-source security auditing tool for Linux.

#### 5a — Install Lynis

```bash
sudo apt update
sudo apt install lynis -y
```

#### 5b — Run a Basic Audit

```bash
sudo lynis audit system
```

> This will take a few minutes to complete.

#### 5c — Review the Output

After the scan completes, review:
- The **Hardening Index** score
- **Warnings** listed
- **Suggestions** listed

Save the results:
```bash
sudo lynis audit system 2>&1 | tee ~/lab2-lynis-report.txt
```

**Screenshot:** Take a screenshot of the Lynis summary showing the Hardening Index and at least 3 suggestions.

---

### Part 6 — Video Walkthrough (included in points above)

Using your screen recording software, record a video demonstrating:

1. User creation and group assignment
2. File permission changes
3. A disabled service showing `inactive` status
4. The SSH config changes
5. Lynis scan results with your Hardening Index score
6. A brief verbal explanation of **one hardening technique** you found most impactful

> **⚠️ Critical:** Export your video in `.wmv` format before uploading to Canvas.

---

## 📤 Submission Requirements

| Item | Format | Required |
|------|--------|----------|
| Video walkthrough | `.wmv` | ✅ Yes |
| `lab2-lynis-report.txt` | Plain text | ✅ Yes |
| Screenshots (within a `lab2-screenshots/` folder or embedded) | `.png` or `.jpg` | ✅ Yes |

---

## 📊 Grading Rubric

| Section | Points |
|---------|--------|
| User & group management (screenshots + explanation) | 15 |
| File permissions applied correctly | 15 |
| Service hardening demonstrated | 15 |
| SSH hardening configuration shown | 15 |
| Lynis audit completed, report submitted | 15 |
| **Total** | **75** |

---

## 💡 Tips & Resources

- **Linux Permissions Guide:** https://www.guru99.com/file-permissions.html
- **Lynis Documentation:** https://cisofy.com/lynis/
- **CIS Ubuntu Benchmarks:** https://www.cisecurity.org/benchmark/ubuntu_linux
- **SSH Hardening Guide:** https://www.ssh.com/academy/ssh/config
- If you get stuck, post in the course Slack channel.

---

> **📌 Reminder:** All work must be your own. Refer to syllabus for full course policies.
