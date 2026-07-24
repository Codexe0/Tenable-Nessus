<div align="center">

<img src="screenshots/nessus-logo.png" alt="Tenable Nessus Logo" width="280"/>

# 🛡️ Tenable Nessus Vulnerability Assessment Lab
### Hands-On Vulnerability Scanning & Risk Assessment on Kali Linux

[![Nessus](https://img.shields.io/badge/Nessus-10.12.1-blue?style=for-the-badge&logo=tenable)](https://www.tenable.com/products/nessus)
[![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-557C94?style=for-the-badge&logo=kalilinux)](https://kali.org)
[![Docker](https://img.shields.io/badge/Target-DVWA%20(Docker)-2496ED?style=for-the-badge&logo=docker)](https://docker.com)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)]()

> A comprehensive cybersecurity practical lab demonstrating end-to-end **Vulnerability Assessment and Management** using **Tenable Nessus Essentials** on Kali Linux targeting a containerized **Damn Vulnerable Web Application (DVWA)** environment.

---

[📋 Overview](#-overview) • [🔍 Fundamentals](#-understanding-vulnerability-assessment) • [🏗️ Architecture](#-lab-architecture) • [⚙️ Setup & Deployment](#-part-1--installing--configuring-tenable-nessus) • [🧪 Scanning & Execution](#-part-3--configuring--executing-vulnerability-scan) • [📊 Analysis](#-part-4--vulnerability-analysis--dashboard-results) • [💡 Takeaways](#-key-takeaways)

</div>

---

## 📌 Overview

This repository provides a step-by-step practical implementation of an automated vulnerability assessment workflow. Using industry-grade tools, this project demonstrates how security professionals discover host exposure, scan open ports, identify misconfigurations and software vulnerabilities, and prioritize remediation based on threat severity.

### Key Objectives & Outcomes

| Step | Task | Target Outcome |
|:---:|:---|:---|
| 1️⃣ | **Install Tenable Nessus** | Deploy Nessus 10.12.1 engine & initialize plugin database on Kali Linux |
| 2️⃣ | **Enable Scanner Daemon** | Configure `nessusd` systemd service for persistent scanning capabilities |
| 3️⃣ | **Deploy Vulnerable Lab Target** | Spin up DVWA web & database application via Docker container |
| 4️⃣ | **Execute Basic Network Scan** | Conduct host discovery, port scanning, service enumeration, and CVE lookup |
| 5️⃣ | **Analyze Risk & Vulnerabilities** | Categorize findings by CVSS v3 score and outline actionable remediations |

### Technical Stack & Environment

| Component | Technology | Version / Specification | Role |
|:---|:---|:---|:---|
| **Host OS** | Kali Linux | 2024.x (Debian-based) | Security Operations Platform |
| **Vulnerability Scanner** | Tenable Nessus Essentials | v10.12.1 | Automated Scanner & Engine |
| **Container Engine** | Docker Engine (`docker.io`) | Latest | Target Environment Isolation |
| **Target Application** | DVWA (`vulnerables/web-dvwa`) | MariaDB + Apache + PHP | Vulnerable Target Workload |
| **Web Browser** | Mozilla Firefox | Default | Admin Interface for Nessus & Target |

---

## 🔍 Understanding Vulnerability Assessment

### What is a Vulnerability Assessment?

A **Vulnerability Assessment** is a systematic technical evaluation of security weaknesses within an information system. It evaluates whether systems are susceptible to known vulnerabilities, assigns severity levels to those vulnerabilities, and recommends remediation or mitigation.

```
┌─────────────────────────────────────────────────────────┐
│              VULNERABILITY ASSESSMENT LIFECYCLE         │
│                                                         │
│  1. SCOPING       →   Identify assets, targets, and    │
│                       scanning requirements             │
│                                                         │
│  2. ENUMERATION   →   Discover active hosts, open      │
│                       ports, and running services       │
│                                                         │
│  3. DETECTION     →   Match service fingerprints and    │
│                       banners against CVE plugins       │
│                                                         │
│  4. PRIORITIZATION→   Score risks using CVSS v3.0      │
│                       and severity metrics              │
│                                                         │
│  5. REMEDIATION   →   Apply patches, harden configs,    │
│                       and verify fix via rescan         │
└─────────────────────────────────────────────────────────┘
```

### Why Nessus Essentials?

Tenable Nessus is the industry standard for vulnerability scanning. It utilizes over 190,000+ constantly updated plugins to detect:
- Unpatched software vulnerabilities (CVEs)
- Default or weak credentials
- Web application flaws & misconfigurations
- Unnecessary running services & open ports
- Missing security headers and weak SSL/TLS protocol implementations

### Regulatory & Compliance Framework Alignment

```
PCI-DSS  → Requirement 11.2 — Run internal & external network vulnerability scans quarterly
NIST CSF → DE.CM-8 & PR.IP-1 — Vulnerability monitoring and management process implementation
ISO 27001→ A.12.6.1 — Management of technical vulnerabilities
HIPAA    → §164.308(a)(1)(ii)(A) — Security risk analysis and management
```

---

## 🏗️ Lab Architecture

```
╔══════════════════════════════════════════════════════════════════════════╗
║                          KALI LINUX HOST MACHINE                         ║
║                            IP: 192.168.10.131                            ║
║                                                                          ║
║  ┌──────────────────────────────┐        ┌────────────────────────────┐  ║
║  │   TENABLE NESSUS ESSENTIALS  │        │   DOCKER CONTAINER ENGINE  │  ║
║  │   Daemon: nessusd.service    │        │   Target: web-dvwa         │  ║
║  │                              │  Scan  │                            │  ║
║  │   • Port Scanner (SSH/SYN)   │───────►│   • Web: Apache (Port 80)  │  ║
║  │   • Plugin Database (190k+)  │ Loopback│   • DB: MariaDB Server     │  ║
║  │   • Web Admin: Port 8834     │127.0.0.1│   • PHP Runtime            │  ║
║  └──────────────┬───────────────┘        └────────────────────────────┘  ║
║                 │                                                        ║
╚═════════════════┼════════════════════════════════════════════════════════╝
                  ▼
   https://192.168.10.131:8834 (Browser Administration Interface)
```

### Port & Service Map

| Service | Port | Protocol | Direction | Description |
|:---|:---:|:---:|:---:|:---|
| **Nessus Web UI** | `8834` | HTTPS | Client ↔ Nessus | Web Dashboard & Scan Console |
| **DVWA HTTP** | `80` | HTTP | Scanner ↔ Target | Target Web Server Interface |
| **SSH** | `22` | TCP | Internal | Local scanner transport & port checking |

---

## ⚙️ Part 1 — Installing & Configuring Tenable Nessus

### Step 1: Download Nessus Package

Download the official Tenable Nessus installation package for Debian/Ubuntu Linux (`.deb` architecture).

- **Selected Package:** `Nessus - 10.12.1`
- **Platform:** `Linux - Ubuntu - amd64`

> 📸 **Screenshot — Tenable Downloads Portal:**

![Downloading Tenable Nessus 10.12.1 installer on Kali Linux](screenshots/Nessus%20Download%20Link.png)

```bash
# Navigate to downloads directory and verify file download
cd ~/Downloads
ls -la Nessus-10.12.1-ubuntu1604_amd64.deb
```

---

### Step 2: Install and Start Nessus Service

Install the `.deb` package using `dpkg` or `apt`, then manage the `nessusd` systemd service daemon:

> 📸 **Screenshot — Checking initial status and starting `nessusd` service:**

![Executing systemctl status and start commands for nessusd](screenshots/CMD%20to%20start%20and%20check%20status%20of%20Nessus.png)

```bash
# Install Nessus package
sudo dpkg -i Nessus-10.12.1-ubuntu1604_amd64.deb

# Check initial status (should be inactive)
sudo systemctl status nessusd

# Start the Nessus Vulnerability Scanner service
sudo systemctl start nessusd

# Verify service is running active (PID assigned)
sudo systemctl status nessusd
```

Now enable the daemon so that Nessus starts automatically upon system startup:

> 📸 **Screenshot — Enabling Nessus service for persistent auto-start:**

![Enabling nessusd service with systemctl enable](screenshots/Enabled%20the%20Nessus%20Services.png)

```bash
# Enable service on boot
sudo systemctl enable nessusd

# Confirm status is enabled and active
sudo systemctl status nessusd
```

---

### Step 3: Nessus Registration & Initialization

Access the web interface at `https://192.168.10.131:8834/#/` using your browser.

> 📸 **Screenshot — Nessus Web UI Setup Screen:**

![Welcome screen for Nessus Setup interface](screenshots/Register%20in%20the%20Nessus%20using%20ip.png)

1. Select **Nessus Essentials** (Free Tier).
2. Enter your activation key obtained from Tenable.
3. Create administrator credentials (`Codex0`).
4. Allow Nessus to download and compile the vulnerability plugin database.

> 📸 **Screenshot — Plugin Compilation & Database Download:**

![Nessus initializing progress bar downloading plugins](screenshots/Downloading%20Plugins.png)

---

## 🐳 Part 2 — Target Deployment (DVWA via Docker)

To simulate a real-world vulnerable asset safely, we install Docker and spin up **Damn Vulnerable Web Application (DVWA)** containing web vulnerabilities and open database endpoints.

### Step 1: Install Docker Engine

> 📸 **Screenshot — Installing `docker.io` package on Kali Linux:**

![Running apt install docker.io package command](screenshots/Docker%20install%20for%20testing.png)

```bash
# Update package repositories and install docker
sudo apt update && sudo apt install docker.io -y
```

---

### Step 2: Start and Verify Docker Daemon

> 📸 **Screenshot — Starting Docker Service:**

![Executing sudo systemctl start docker command](screenshots/Started%20the%20Docker.png)

> 📸 **Screenshot — Verifying Active Docker Service:**

![Checking docker.service status active and running](screenshots/Status%20of%20Docker.png)

```bash
# Start Docker service
sudo systemctl start docker

# Verify Docker daemon status
sudo systemctl status docker
```

---

### Step 3: Run DVWA Target Container

Pull and launch the containerized application mapped to local port 80:

> 📸 **Screenshot — Docker pulling and launching DVWA container:**

![Executing sudo docker run command for web-dvwa container](screenshots/Run%20the%20Docker.png)

```bash
# Run DVWA in interactive container mode mapping port 80
sudo docker run --rm -it -p 80:80 vulnerables/web-dvwa
```

*The container automatically starts MariaDB database server and Apache HTTP daemon (`apache2`).*

---

### Step 4: Verify DVWA Target Accessibility

Navigate to `http://127.0.0.1/` in Mozilla Firefox to confirm the vulnerable application is responsive:

> 📸 **Screenshot — DVWA Target Interface loaded in browser:**

![Damn Vulnerable Web Application homepage running on localhost](screenshots/Opens%20the%20DVWA.png)

---

## 🧪 Part 3 — Configuring & Executing Vulnerability Scan

### Step 1: Configure New Basic Network Scan

In the Nessus Console, click **New Scan** ➔ **Basic Network Scan**.

Set up the general target parameters:

> 📸 **Screenshot — Basic Network Scan Configuration:**

![Configuring scan parameters for DVWA target 127.0.0.1](screenshots/Network%20Scan.png)

#### Scan Settings Configuration

| Setting Category | Field | Configured Value | Description |
|:---|:---|:---|:---|
| **Basic** | Name | `DVWA` | Project identification tag |
| **Basic** | Description | `Test` | Audit scope note |
| **Basic** | Folder | `My Scans` | Destination workspace |
| **Targets** | Targets | `127.0.0.1` | Loopback address for target container |

---

### Step 2: Run Scan & Monitor Execution

Click **Save** and launch the scan manually. Nessus begins executing probes:
1. Host discovery ping probe
2. TCP/UDP port enumeration
3. OS & Service Version fingerprinting
4. Plugin vulnerability matching (CVE lookup)

---

## 📊 Part 4 — Vulnerability Analysis & Dashboard Results

### Host Summary Overview

Once scanning completes, view the high-level assessment overview under the **Hosts** tab:

> 📸 **Screenshot — Host Overview & Vulnerability Severity Distribution:**

![Host scan report showing 127.0.0.1 results, CVSS v3 base, and vulnerability distribution chart](screenshots/Host.png)

#### Scan Summary Details

| Parameter | Recorded Metric |
|:---|:---|
| **Target Host** | `127.0.0.1` |
| **Scan Policy** | Basic Network Scan |
| **Scan Status** | Completed |
| **Severity Base** | CVSS v3.0 |
| **Scanner Type** | Local Scanner |
| **Authentication Status** | Pass ✅ |
| **Total Vulnerabilities Identified** | **47 Findings** |

---

### Detailed Vulnerability Breakdown

Click into host details to inspect individual plugin outputs and vulnerability families:

> 📸 **Screenshot — Detailed Vulnerability Findings List:**

![Detailed table listing discovered vulnerabilities, severity levels, CVSS, and family categories](screenshots/Vuln%20found%20during%20scan.png)

#### Highlighted Finding Categories

| Severity | Count | Plugin Family | Vulnerability / Finding Name | CVSS v3 | Risk Summary |
|:---:|:---:|:---|:---|:---:|:---|
| 🔵 **Info** | 4 | Web Servers | `HTTP (Multiple Issues)` | N/A | Multiple web server header/configuration alerts |
| 🔵 **Info** | 3 | General | `SSL (Multiple Issues)` | N/A | SSL certificate/protocol information |
| 🔵 **Info** | 2 | General | `TLS (Multiple Issues)` | N/A | Supported TLS protocol suites detected |
| 🔵 **Info** | 2 | Service detection | `TLS (Multiple Issues)` | N/A | TLS service endpoints identified |
| 🔵 **Info** | 3 | Service detection | `Service Detection` | N/A | Enumerated active listening ports |
| 🔵 **Info** | 2 | Databases | `MariaDB Client/Server Installed` | N/A | Database engine version footprinting |
| 🔵 **Info** | 2 | Port scanners | `Netstat Portscanner (SSH)` | N/A | Local port auditing via credentials |
| 🔵 **Info** | 1 | Web Servers | `Apache HTTP Server Version` | N/A | Web server banner disclosure |

---

## 🛡️ Remediation Strategies & Cyber Security Best Practices

Based on findings provided by Nessus scanning, the following security hardening steps are recommended for system administration and DevOps teams:

### 1. Web Server Hardening (Apache HTTP)
- **Banner Suppression:** Modify `/etc/apache2/conf-enabled/security.conf`:
  ```apache
  ServerTokens Prod
  ServerSignature Off
  ```
- **Security Headers:** Enforce defensive headers (`HTTP Strict-Transport-Security`, `X-Frame-Options`, `X-Content-Type-Options`, `Content-Security-Policy`).

### 2. TLS/SSL Hardening
- Disable deprecated SSLv2, SSLv3, TLS 1.0, and TLS 1.1 protocols.
- Enforce **TLS 1.2** and **TLS 1.3** with strong cipher suites supporting Forward Secrecy (ECDHE).

### 3. Database Security (MariaDB)
- Restrict MariaDB binding interface to internal sockets or `127.0.0.1` only.
- Disable remote root login and default test databases via `mysql_secure_installation`.

### 4. Attack Surface Minimization
- Close unneeded exposed ports.
- Implement strict iptables / ufw firewall rules.

---

## 💡 Key Takeaways

```
✅  Vulnerability assessments provide continuous visibility into network exposure.

✅  Nessus Essentials enables automated vulnerability detection against 190,000+ plugins.

✅  Containerized workloads (Docker) present unique attack surfaces requiring isolated host scanning.

✅  Proactive remediation based on CVSS scoring reduces likelihood of successful exploits.

✅  Automated scanning aligns directly with compliance standards (PCI-DSS 11.2, ISO 27001, NIST CSF).
```

---

## 📁 Repository Structure

```
Tenable-Nessus/
├── README.md                                  ← Project documentation
└── screenshots/                               ← Execution evidence & UI captures
    ├── CMD to start and check status of Nessus.png
    ├── Docker install for testing.png
    ├── Downloading Plugins.png
    ├── Enabled the Nessus Services.png
    ├── Host.png
    ├── Nessus Download Link.png
    ├── Network Scan.png
    ├── Opens the DVWA.png
    ├── Register in the Nessus using ip.png
    ├── Run the Docker.png
    ├── Started the Docker.png
    ├── Status of Docker.png
    └── Vuln found during scan.png
```

---

## 📚 References & Further Reading

- 🌐 [Tenable Nessus Official Documentation](https://docs.tenable.com/nessus/)
- 🌐 [DVWA (Damn Vulnerable Web Application) Official Repo](https://github.com/digininja/DVWA)
- 🌐 [NIST National Vulnerability Database (NVD)](https://nvd.nist.gov/)
- 🌐 [OWASP Top 10 Web Application Security Risks](https://owasp.org/www-project-top-ten/)

---

<div align="center">

**⭐ If you found this security project helpful, please consider starring the repository!**

Created with 🔒 by **[Codex](https://github.com/Codexe0)** | Practical Cyber Security & Ethical Hacking Labs

</div>
