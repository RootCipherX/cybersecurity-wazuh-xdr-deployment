# 🛡️ Cybersecurity: Wazuh XDR & SIEM Deployment and Firewall Configuration

## 📖 Table of Contents
- [Introduction to Wazuh](#-introduction-to-wazuh-xdr--siem)
- [Project Overview](#-project-overview)
- [Objective](#-objective)
- [System Specifications](#️-system-specifications)
- [Deployment Methodology Workflow](#-deployment-methodology-workflow)
  - [Phase 1: Documentation & Requirements Verification](#phase-1-documentation--requirements-verification)
  - [Phase 2: Automated Installation](#phase-2-automated-installation)
  - [Phase 3: Service Validation](#phase-3-service-validation)
  - [Phase 4: Network Troubleshooting & Firewall Configuration](#phase-4-network-troubleshooting--firewall-configuration)
  - [Phase 5: Dashboard Initialization & Health Check](#phase-5-dashboard-initialization--health-check)
- [Security Relevance & Impact](#-security-relevance--impact)
- [Ethical Guidelines & Disclaimer](#️-ethical-guidelines--disclaimer)

---

## 🛑 Introduction to Wazuh (XDR & SIEM)
**Wazuh** is a free, open-source security platform that unifies Extended Detection and Response (XDR) and Security Information and Event Management (SIEM) capabilities. It protects endpoints and cloud workloads by providing log data analysis, intrusion detection, file integrity monitoring, and vulnerability detection. Deploying Wazuh allows security teams to centralize threat intelligence and actively monitor their infrastructure for malicious activity.

## 📌 Project Overview
This project documents the complete deployment of the Wazuh central components (Server, Indexer, and Dashboard) on a Linux environment. It details the process of executing the automated installation assistant, verifying backend services, and crucially, troubleshooting network connectivity issues by configuring the Uncomplicated Firewall (UFW) to permit secure web interface access.

## 🎯 Objective
To successfully provision a locally hosted Wazuh XDR platform, ensuring all core services are running correctly. A primary objective of this lab is to demonstrate practical Linux network troubleshooting by identifying firewall blocks and surgically opening the required listening ports to establish access to the SIEM dashboard.

## 🛠️ System Specifications
*   **Operating System:** Linux (Ubuntu/Debian architecture)
*   **Target Application:** Wazuh version 4.14
*   **Key Components:** Wazuh Manager, Wazuh Indexer, Wazuh Dashboard
*   **Networking Utilities:** `ufw` (Uncomplicated Firewall), `ss` (Socket Statistics), `ifconfig`

---

## 🚀 Deployment Methodology Workflow

### Phase 1: Documentation & Requirements Verification

The deployment began with researching the official Wazuh installation guidelines to ensure the correct architecture constraints were met.
<br>

![Google Search](images/01-open-google.png)

Located the official Wazuh documentation to avoid third-party, potentially insecure installation guides.
<br>

![Click Official Link](images/02-search-for-install-wazuh-click-link.png)

Navigated directly to the primary Documentation hub.
<br>

![Wazuh Documentation](images/03-click-on-documentation.png)

Selected the "Quickstart" guide, which provides the streamlined method for deploying all three central components on a single host.
<br>

![Quickstart Guide](images/04-click-on-quickstart.png)

Reviewed the initial Quickstart deployment architecture and open-source licensing terms.
<br>

![Review Quickstart](images/05-quickstart-opened.jpg)

Verified the hardware prerequisites, ensuring the Virtual Machine was allocated a minimum of 4 vCPUs and 8 GiB of RAM to handle the heavy indexing workload.
<br>

![Hardware Requirements](images/06-check-system-requirement.png)

---

### Phase 2: Automated Installation

Acquired the official `curl` command to execute the Wazuh automated installation assistant.
<br>

![Copy Install Command](images/07-copy-command-in-terminal.png)

Executed the installation command in the Linux terminal. 
*   **Command Breakdown:** `curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a`
    *   `curl -sO`: Silently downloads the script file and saves it with its original remote name.
    *   `&&`: Ensures the second command only runs if the download is successful.
    *   `sudo bash ./wazuh-install.sh -a`: Executes the bash script with root privileges using the `-a` (assistant/automated) flag to handle dependencies and component linking automatically.
<br>

![Run Installation](images/08-linuxmint-start-wazuh-installation.jpg)

The installation completed successfully. The assistant automatically generated and output the highly secure `admin` credentials required for web dashboard access.
<br>

![Installation Finished](images/09-installation-finished.jpg)

---

### Phase 3: Service Validation

Before attempting to access the dashboard, it is critical to verify that the underlying Linux services are running properly. I began by checking the primary manager.
*   **Command Breakdown:** `sudo systemctl status wazuh-manager` checks the current operational state of the manager daemon.
<br>

![Wazuh Manager Status](images/10-check-status-wazuh-manager.jpg)

Verified the Wazuh Indexer, the core search and analytics engine that stores the alerts.
*   **Command Breakdown:** `sudo systemctl status wazuh-indexer`
<br>

![Wazuh Indexer Status](images/11-check-status-wazuh-indexer.jpg)

Finally, verified the Wazuh Dashboard service, which powers the web-based user interface.
*   **Command Breakdown:** `sudo systemctl status wazuh-dashboard`
<br>

![Wazuh Dashboard Status](images/12-check-status-wazuh-dashboard.jpg)

---

### Phase 4: Network Troubleshooting & Firewall Configuration

To access the dashboard from the host machine, the Virtual Machine's IP address was required.
*   **Command Breakdown:** `ifconfig` lists the current network interfaces, revealing the local IP as `192.168.139.129`.
<br>

![Check IP Address](images/13-check-ip-for-host-system.png)

Attempted to browse to the IP address via HTTPS, but the connection timed out, indicating a network drop or firewall block.
<br>

![Connection Timeout](images/14-try-launching-wazuh.png)

Initiated troubleshooting by checking the state of the Uncomplicated Firewall (UFW).
*   **Command Breakdown:** `sudo systemctl status ufw` revealed the firewall service was actively running.
<br>

![UFW Service Status](images/15-check-firewall-status.png)

Queried the firewall's specific rule configuration.
*   **Command Breakdown:** `sudo ufw status verbose` showed that the default policy was set to `deny (incoming)`, meaning all web traffic to the dashboard was being blocked.
<br>

![UFW Verbose Status](images/16-check-firewall-verbose.png)

Verified which ports Wazuh was actively listening on to ensure the correct firewall rules would be applied.
*   **Command Breakdown:** `sudo ss -tuln` (Socket Statistics) displays all listening TCP (`-t`) and UDP (`-u`) ports numerically (`-n`). This confirmed active listeners on port 443 (HTTPS), 1514, and 1515.
<br>

![Check Listening Ports](images/17-check-firewall-rules.png)

Surgically modified the firewall rules to permit inbound web traffic.
*   **Command Breakdown:** 
    *   `sudo ufw allow 80`: Opens port 80 for standard HTTP.
    *   `sudo ufw allow 443`: Opens port 443 for secure HTTPS dashboard access.
<br>

![Allow UFW Ports](images/18-allow-port-80-443.png)

---

### Phase 5: Dashboard Initialization & Health Check

With the firewall properly configured, the browser successfully established an HTTPS connection, and the Wazuh platform began loading.
<br>

![Wazuh Loading](images/19-wazuh-loading.png)

Successfully accessed the authentication portal and logged in using the generated `admin` credentials.
<br>

![Wazuh Login](images/20-login-into-wazuh.png)

The system automatically initiated an API health check, successfully verifying the connection, versioning, and index patterns.
<br>

![Health Check](images/21-wazuh-health-check.png)

Reached the main Wazuh Dashboard, providing a comprehensive overview of security events, endpoint status, and threat intelligence metrics.
<br>

![Wazuh Dashboard](images/22-wazuh-dashboard.png)

Navigated to the Discover tab (Wazuh Logs) to verify that raw system logs, rule descriptions, and indexing data were flowing correctly into the analytics engine.
<br>

![Wazuh Logs](images/23-wazuh-logs.jpg)

---

## 🛡️ Security Relevance & Impact
Deploying an XDR/SIEM solution like Wazuh is critical for maintaining infrastructure visibility. However, deploying the software is only half the battle. The ability to utilize Linux command-line tools (`systemctl`, `ss`, `ufw`) to diagnose network timeouts, identify active listening sockets, and securely modify firewall rules demonstrates the practical systems engineering skills required to maintain an active Security Operations Center (SOC).

---

## ⚖️ Ethical Guidelines & Disclaimer
This deployment and network configuration lab was performed within a private, authorized Virtual Machine environment strictly for educational and defensive cybersecurity training purposes.
