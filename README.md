# InternSpark-Internship-Task-1
Website Security Assessment Using Nikto and Nmap


## Table of Contents

1. Overview
2. Tools Used
3. Scope of Assessment
4. Nikto Scan Findings
5. Nmap Scan Findings
6. Risk Summary
7. Recommendations
8. Conclusion


## Overview

This project demonstrates a foundational cybersecurity assessment conducted on a web application and its host system using two widely used open source security scanning tools, Nikto and Nmap. The goal was to uncover common security flaws, misconfigured services, unintended data exposure, and potential entry points for attackers.

Key focus areas included web server configuration weaknesses, unintended information leakage, publicly reachable hidden directories, HTTP level security gaps, exposed network ports and running services, and network level risk exposure.


## Tools Used

| Tool | Purpose |
|------|---------|
| Nikto | Web server vulnerability scanning |
| Nmap | Network and port scanning |
| Browser Testing | Manual verification of findings |


## Scope of Assessment

The assessment examined the following areas:

Web application availability on Port 3000, HTTP response header behavior, accessible and hidden directory paths, permitted HTTP request methods, open TCP ports on the host machine, and system level services running on the host.


## Nikto Scan Findings


### Internal Data Leakage via ETag Headers

Risk Level: Low

The web server inadvertently discloses internal inode data through ETag response headers, potentially giving attackers insight into the file system structure and server configuration.

Evidence: Server leaking inode data via ETags

Remediation: Disable ETags where not needed, configure them securely if required, and avoid exposing unnecessary metadata in server responses.


### Overly Permissive CORS Policy

Risk Level: Medium

The server is configured to accept cross origin requests from any domain using a wildcard policy. This may allow malicious websites to access protected application data without restriction.

Remediation: Limit allowed origins to verified and trusted domains only. Never use wildcard CORS policies in production environments.


### Publicly Accessible Hidden Directories

Risk Level: Medium

The robots.txt file inadvertently revealed internal application paths, both of which were found to be openly accessible. The path /ftp/ returned HTTP 200 and the path /public/ also returned HTTP 200. These directories could expose internal files, backup data, application resources, or sensitive content.

Remediation: Restrict public access to sensitive paths, disable directory listing on the web server, and avoid listing internal paths within robots.txt.


### Unnecessary Informational HTTP Headers

Risk Level: Low

The application exposes additional HTTP headers that reveal internal application details, including x-recruiting and feature-policy headers.

Remediation: Strip out non-essential HTTP headers and keep responses free of internal metadata.


### Missing or Unclear Server Banner

Risk Level: Low

No clear server identity banner was detected during the scan. While this generally reduces fingerprinting risk, inconsistencies in server configuration may still exist.

Remediation: Maintain uniform and secure server configuration and minimize unnecessary service exposure.


### Excessive HTTP Methods Permitted

Risk Level: Medium

The server accepts a broad range of HTTP methods including GET, POST, PUT, DELETE, and PATCH. Permitting methods that are not functionally required unnecessarily widens the potential attack surface.

Remediation: Limit HTTP methods to only those actively in use and disable any HTTP verb that serves no functional purpose.


## Nmap Scan Findings


### Web Application Accessible on Port 3000

Risk Level: Medium

The web application was running and responding on Port 3000, returning a live HTTP 200 OK response. Any publicly exposed application port increases the risk of unauthorized access attempts, automated scanning and probing, and targeted exploitation.

Remediation: Apply firewall rules to restrict port access, route external traffic through a reverse proxy, and limit visibility to trusted networks only.


### Unidentified Service on Port 3000

Risk Level: Low

The scanning tool could not fully identify the service running on Port 3000, suggesting a non standard or custom deployment configuration.

Remediation: Ensure all services are properly identified and documented, set up continuous monitoring and logging, and verify that service configurations meet security standards.


### SMB Service Exposed on Port 445

Risk Level: High

Port 445 was found open on the host system, exposing the SMB protocol. SMB is a frequently targeted service in attacks involving unauthorized file system access, lateral movement across internal networks, self propagating worm infections, and ransomware deployment.

Note: This service is part of the host operating system and not the web application itself.

Remediation: Disable SMB if it is not actively required, enforce strict firewall rules on Port 445, and permit access only from trusted internal systems.


### RPC Service Exposed on Port 135

Risk Level: Medium

Port 135 was open for Remote Procedure Call communications. Exposed RPC services can be probed or exploited to access system level interfaces if not properly hardened.

Note: Like SMB, this is a host OS level service and is not part of the web application.

Remediation: Restrict RPC access to trusted networks, block external access to Port 135, and apply hardening measures to RPC related components.


## Risk Summary

| Finding | Risk Level |
|---------|-----------|
| SMB Service Exposed (Port 445) | High |
| Web Application Port Open (Port 3000) | Medium |
| Excessive HTTP Methods Allowed | Medium |
| Accessible Hidden Directories | Medium |
| Permissive CORS Policy | Medium |
| RPC Service Exposed (Port 135) | Medium |
| ETag Information Disclosure | Low |
| Unnecessary HTTP Header Exposure | Low |
| Unidentified Service on Port 3000 | Low |


## Recommendations


### Web Application Security

Disable HTTP methods that are not functionally required. Enforce strict and origin specific CORS policies. Remove informational and non essential HTTP response headers. Prevent exposure of internal directory paths. Turn off directory listing globally on the web server.


### Network Security

Limit which ports are accessible from external networks. Shut down unused or unnecessary running services. Strengthen protections around SMB on Port 445 and RPC on Port 135. Implement and enforce robust firewall filtering rules.


### Ongoing Monitoring and Hardening

Schedule regular vulnerability assessments. Conduct periodic network and port scans. Enable comprehensive logging and alerting systems. Follow and apply server hardening best practices consistently.


## Conclusion

This security assessment successfully identified a range of vulnerabilities across both the web application and the underlying host system using Nikto and Nmap. Key issues uncovered include unintended information disclosure, excessive service and HTTP method permissions, open network ports exposing critical system services, and publicly reachable internal directories.

While no critical compromise of the web application itself was detected, the presence of exposed services such as SMB combined with overly permissive configurations significantly expands the potential attack surface. Addressing these findings is essential to improving the overall security posture of the system.

This assessment underscores the ongoing importance of routine security testing, proactive service hardening, and continuous vulnerability management.


## Disclaimer

This assessment was performed in a controlled lab environment for educational purposes only. Never run vulnerability scans against systems you do not own or have explicit written permission to test. Unauthorized scanning is illegal and unethical.
