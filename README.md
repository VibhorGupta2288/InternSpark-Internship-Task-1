# InternSpark-Internship-Task-1
# Website Security Assessment — Nikto & Nmap

![Security](https://img.shields.io/badge/Security-Assessment-red?style=flat-square)
![Tools](https://img.shields.io/badge/Tools-Nikto%20%7C%20Nmap-blue?style=flat-square)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)

A foundational cybersecurity assessment conducted on a web application and its host system using **Nikto** and **Nmap** — two of the most widely used open-source security scanning tools.

---

## Table of Contents

- [Overview](#overview)
- [Tools Used](#tools-used)
- [Scope of Assessment](#scope-of-assessment)
- [Nikto Scan Findings](#nikto-scan-findings)
- [Nmap Scan Findings](#nmap-scan-findings)
- [Risk Summary](#risk-summary)
- [Recommendations](#recommendations)
- [Conclusion](#conclusion)

---

## Overview

The goal of this assessment was to identify common security vulnerabilities, misconfigured services, unintentional data exposure, and potential attack vectors within a web application environment.

**Key focus areas:**
- Web server configuration weaknesses
- Unintended information leakage
- Publicly reachable hidden directories
- HTTP-level security gaps
- Exposed network ports and running services
- Network-level risk exposure

---

## Tools Used

| Tool | Purpose |
|------|---------|
| **Nikto** | Web server vulnerability scanning |
| **Nmap** | Network and port scanning |
| **Browser Testing** | Manual verification of findings |

---

## Scope of Assessment

The assessment covered the following areas:

- Web application exposure on **Port 3000**
- HTTP response header analysis
- Hidden and publicly accessible directories
- Permitted HTTP request methods
- Open TCP ports on the host machine
- System-level services running on the host

---

## Nikto Scan Findings

### 1. Internal Data Leakage via ETag Headers
**Risk Level:** `Low`

The web server inadvertently discloses internal inode data through ETag response headers, potentially giving attackers insight into the file system structure and server configuration.

> **Evidence:** Server leaking inode data via ETags

**Remediation:**
- Disable ETags where not required
- Configure ETags securely if needed
- Avoid exposing unnecessary server metadata

---

### 2. Overly Permissive CORS Policy
**Risk Level:** `Medium`

The server accepts cross-origin requests from **any** domain using a wildcard policy:
```
Access-Control-Allow-Origin: *
```
This may allow malicious third-party websites to access protected application resources without restriction.

**Remediation:**
- Restrict allowed origins to verified, trusted domains only
- Never use wildcard CORS policies in production environments

---

### 3. Publicly Accessible Hidden Directories
**Risk Level:** `Medium`

The `robots.txt` file inadvertently revealed internal application paths — both of which were found to be openly accessible:

| Path | HTTP Status |
|------|------------|
| `/ftp/` | `200 OK` |
| `/public/` | `200 OK` |

These directories could expose internal files, backup data, application resources, or sensitive content.

**Remediation:**
- Restrict public access to sensitive paths
- Disable directory listing on the web server
- Avoid listing internal paths within `robots.txt`

---

### 4. Unnecessary Informational HTTP Headers
**Risk Level:** `Low`

The application exposes additional HTTP headers that reveal internal application details:

```
x-recruiting: /#/jobs
feature-policy
```

**Remediation:**
- Strip out non-essential HTTP headers
- Keep responses lean and free of internal metadata

---

### 5. Missing or Unclear Server Banner
**Risk Level:** `Low`

No clear server identity banner was detected during the scan. While this generally reduces fingerprinting risk, inconsistencies in server configuration may still exist beneath the surface.

**Remediation:**
- Maintain uniform, secure server configuration
- Minimize unnecessary service exposure

---

### 6. Excessive HTTP Methods Permitted
**Risk Level:** `Medium`

The server accepts a broad range of HTTP methods:
```
GET, POST, PUT, DELETE, PATCH
```
Permitting methods that are not functionally required unnecessarily widens the potential attack surface.

**Remediation:**
- Limit HTTP methods to only those actively in use
- Disable any HTTP verb that serves no functional purpose

---

## Nmap Scan Findings

### 1. Web Application Accessible on Port 3000
**Risk Level:** `Medium`

The web application was running and responding on Port 3000, returning a live `HTTP/1.1 200 OK` response. Any publicly exposed application port increases risk exposure to:
- Unauthorized access attempts
- Automated scanning and probing
- Targeted exploitation

**Remediation:**
- Apply firewall rules to restrict port access
- Route external traffic through a reverse proxy
- Limit visibility to trusted networks only

---

### 2. Unidentified Service on Port 3000
**Risk Level:** `Low`

The scanning tool could not fully identify the service running on Port 3000, suggesting a non-standard or custom deployment configuration.

**Remediation:**
- Ensure all services are properly identified and documented
- Set up continuous monitoring and logging
- Verify service configurations meet security standards

---

### 3. SMB Service Exposed on Port 445
**Risk Level:** `High`

Port 445 was found open on the host system, exposing the **SMB (Server Message Block)** protocol. SMB is a frequently targeted service in attacks including:

- Unauthorized file system access
- Lateral movement across internal networks
- Self-propagating worm infections
- Ransomware deployment

 **Note:** This service is part of the host operating system, not the web application itself.

**Remediation:**
- Disable SMB if it is not actively required
- Enforce strict firewall rules on Port 445
- Permit access only from trusted internal systems

---

### 4. RPC Service Exposed on Port 135
**Risk Level:** `Medium`

Port 135 was open for **Remote Procedure Call (RPC)** communications. Exposed RPC services can be probed or exploited to access system-level interfaces if not properly hardened.

 **Note:** Like SMB, this is a host OS-level service and is not part of the web application.

**Remediation:**
- Restrict RPC access to trusted networks
- Block external access to Port 135
- Apply hardening measures to RPC-related components

---

## Risk Summary

| Finding | Risk Level |
|---------|-----------|
| SMB Service Exposed (Port 445) | **High** |
| Web Application Port Open (3000) | **Medium** |
| Excessive HTTP Methods Allowed | **Medium** |
| Accessible Hidden Directories | **Medium** |
| Permissive CORS Policy | **Medium** |
| RPC Service Exposed (Port 135) | **Medium** |
| ETag Information Disclosure | **Low** |
| Unnecessary HTTP Header Exposure | **Low** |
| Unidentified Service on Port 3000 | **Low** |

---

## Recommendations

### Web Application Security
- Disable HTTP methods that are not functionally required
- Enforce strict, origin-specific CORS policies
- Remove informational and non-essential HTTP response headers
- Prevent exposure of internal directory paths
- Turn off directory listing globally on the web server

### Network Security
- Limit which ports are accessible from external networks
- Shut down unused or unnecessary running services
- Strengthen protections around SMB (Port 445) and RPC (Port 135)
- Implement and enforce robust firewall filtering rules

### Ongoing Monitoring & Hardening
- Schedule regular vulnerability assessments
- Conduct periodic network and port scans
- Enable comprehensive logging and alerting systems
- Follow and apply server hardening best practices consistently

---

## Conclusion

This security assessment successfully identified a range of vulnerabilities across both the web application and the underlying host system using **Nikto** and **Nmap**.

Key issues uncovered include:
- Unintended information disclosure
- Excessive service and HTTP method permissions
- Open network ports exposing critical system services
- Publicly reachable internal directories

While no critical compromise of the web application itself was detected, the presence of exposed services — particularly **SMB** — combined with overly permissive configurations, significantly expands the potential attack surface. Addressing these findings is essential to improving the overall security posture of the system.

> This assessment underscores the ongoing importance of **routine security testing**, **proactive service hardening**, and **continuous vulnerability management**.

---

## Disclaimer

This assessment was performed in a **controlled lab environment** for educational purposes only. Never run vulnerability scans against systems you do not own or have explicit permission to test. Unauthorized scanning is illegal and unethical.

---

*Generated as part of a cybersecurity learning project.*
