# Web Application Security Home Lab
### DVWA + Docker + SafeLine WAF + Kali Linux


# DVWA + SafeLine WAF Home Lab Tutorial

## Introduction

This project demonstrates how to build a complete web application security lab using Docker, DVWA (Damn Vulnerable Web Application), SafeLine WAF, and Kali Linux.

The purpose of this lab is to understand both offensive and defensive web security by deploying a vulnerable web application, protecting it with a Web Application Firewall (WAF), and simulating attacks from an attacker machine.

By the end of this tutorial, you will have:

* A vulnerable web application running in Docker
* SafeLine WAF protecting the application
* A Kali Linux machine for attack simulation
* A realistic environment for learning web security concepts

---

# Lab Architecture

```text
                     +------------------+
                     |   Kali Linux     |
                     |   Attacker VM    |
                     +---------+--------+
                               |
                               |
                               v
                     +------------------+
                     |   SafeLine WAF   |
                     | Reverse Proxy    |
                     +---------+--------+
                               |
                               |
                               v
                     +------------------+
                     |       DVWA       |
                     | Docker Container |
                     | Ubuntu Server    |
                     +------------------+
```

---

# Prerequisites

## Hardware Requirements

Minimum recommended specifications:

* 8 GB RAM
* 50 GB Free Storage
* Dual-Core Processor

---

## Software Requirements

### Host Machine

* VirtualBox or VMware Workstation

### Virtual Machines

* Ubuntu Server 22.04 LTS
* Kali Linux Latest Version

---

# Step 1: Create the Virtual Machines

## Ubuntu Server VM

Recommended settings:

| Setting | Value           |
| ------- | --------------- |
| RAM     | 4 GB            |
| CPU     | 2 Cores         |
| Disk    | 25 GB           |
| Network | Bridged Adapter |

Install Ubuntu Server and update the system:

```bash
sudo apt update
sudo apt upgrade -y
```

---

## Kali Linux VM

Recommended settings:

| Setting | Value           |
| ------- | --------------- |
| RAM     | 4 GB            |
| CPU     | 2 Cores         |
| Disk    | 25 GB           |
| Network | Bridged Adapter |

After installation verify connectivity:

```bash
ping <Ubuntu-IP>
```

Both machines should be able to communicate.

---

# Step 2: Install Docker on Ubuntu

Install Docker using the official installation script:

```bash
curl -fsSL https://get.docker.com | sudo bash
```

Verify installation:

```bash
docker --version
```

Enable Docker:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

---

# Step 3: Deploy DVWA

Pull the DVWA image:

```bash
docker pull vulnerables/web-dvwa
```

Create the container:

```bash
docker run -d \
--name dvwa \
-p 8080:80 \
vulnerables/web-dvwa
```

Verify:

```bash
docker ps
```

Expected output should show the DVWA container running.

Access DVWA:

```text
http://<Ubuntu-IP>:8080
```

---

# Step 4: Install SafeLine WAF

Install SafeLine using the official installation script:

```bash
bash -c "$(curl -fsSLk https://waf.chaitin.com/release/latest/setup.sh)"
```

The installation process may take several minutes.

Verify the containers:

```bash
docker ps
```

Several SafeLine containers should appear.

---

# Step 5: Access the SafeLine Dashboard

Open a browser and navigate to:

```text
http://<Ubuntu-IP>:9443
```

Create an administrator account and log in.

After login you will be presented with the SafeLine management dashboard.

---

# Step 6: Configure DVWA Behind SafeLine

Currently:

```text
Client --> DVWA
```

After configuration:

```text
Client --> SafeLine --> DVWA
```

Inside SafeLine:

1. Navigate to Applications
2. Click Add Application
3. Configure the following:

### Application Name

```text
DVWA
```

### Backend Server

```text
<Ubuntu-IP>
```

### Backend Port

```text
8080
```

### Protocol

```text
HTTP
```

Save the configuration.

SafeLine will now inspect all traffic before forwarding requests to DVWA.

---

# Step 7: Configure DVWA

Login using:

```text
Username: admin
Password: password
```

Navigate to:

```text
DVWA Security
```

Set the security level to:

```text
Low
```

Save changes.

This enables vulnerable functionality for testing.

---

# Step 8: Reconnaissance Testing

From Kali Linux perform a basic service scan:

```bash
nmap -sV <Ubuntu-IP>
```

This identifies:

* Open ports
* Running services
* Service versions

---

# Step 9: SQL Injection Testing

Navigate to:

```text
DVWA → SQL Injection
```

Test the following payload:

```sql
1' OR '1'='1
```

Observe:

* Application behaviour
* SafeLine alerts
* Security logs

The WAF should identify suspicious requests.

---

# Step 10: SQLMap Testing

Automated SQL injection testing can be performed using SQLMap.

Example:

```bash
sqlmap -u "http://<target>/vulnerabilities/sqli/?id=1&Submit=Submit" --batch
```

Monitor SafeLine logs during execution.

You should observe:

* Attack detection
* Signature matching
* Request blocking

---

# Step 11: Cross-Site Scripting (XSS)

Navigate to:

```text
DVWA → XSS Reflected
```

Test:

```html
<script>alert('XSS')</script>
```

Observe how SafeLine reacts to the malicious payload.

---

# Step 12: Directory Enumeration

Run Gobuster from Kali:

```bash
gobuster dir \
-u http://<target> \
-w /usr/share/wordlists/dirb/common.txt
```

This generates high-volume requests and helps evaluate WAF monitoring capabilities.

---

# Step 13: Brute Force Testing

Navigate to:

```text
DVWA → Brute Force
```

Use Hydra:

```bash
hydra -l admin -P rockyou.txt http-post-form
```

Monitor SafeLine for:

* Rate limiting
* Suspicious activity alerts
* Request blocking

---

# Step 14: Monitoring and Analysis

Open the SafeLine dashboard and review:

## Attack Logs

* SQL Injection attempts
* XSS attacks
* Automated scans

## Security Events

* Source IP addresses
* Attack signatures
* Timestamps
* Block actions

## Statistics

* Requests per second
* Total requests
* Blocked requests
* Allowed requests

This visibility demonstrates how WAFs help security teams identify threats.

---

# Expected Results

After completing the lab you should be able to:

* Deploy vulnerable applications using Docker
* Configure a reverse proxy WAF
* Perform web application attacks
* Analyse attack telemetry
* Understand defensive security controls
* Investigate security events

---

# Skills Learned

This project provides hands-on experience with:

* Linux Administration
* Docker
* Web Application Security
* SQL Injection
* Cross-Site Scripting
* Vulnerability Assessment
* Web Application Firewalls
* Security Monitoring
* Network Troubleshooting
* Cybersecurity Lab Design

---

# Future Enhancements

To expand the lab consider adding:

* OWASP Juice Shop
* Security Onion
* Suricata IDS/IPS
* Splunk SIEM
* Elastic Stack
* HTTPS Certificates
* Reverse Proxy Load Balancing

---

# Conclusion

This lab provides a practical environment for learning both offensive and defensive cybersecurity techniques. By combining DVWA, SafeLine WAF, Docker, Ubuntu Server, and Kali Linux, learners can safely explore web application attacks while understanding how modern security controls detect and mitigate threats.

This project is ideal for students, aspiring penetration testers, SOC analysts, blue teamers, and cybersecurity enthusiasts looking to gain hands-on experience in web application security.
