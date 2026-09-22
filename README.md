# 🔎 Phishing Email Investigation

> **Simulated SOC Analyst Portfolio Project**

A hands-on investigation of a suspicious email using email header analysis, authentication checks, IOC extraction, and open-source threat intelligence.

---

## 🎯 Objective

Investigate a suspicious password-expiration email and determine whether its characteristics are consistent with phishing activity.

---

## 🛠️ Tools Used

* MXToolbox Email Header Analyzer
* VirusTotal
* URLScan
* Markdown / GitHub

---

## 🔍 Investigation Workflow

```text
Suspicious Email
       ↓
Email Header Analysis
       ↓
SPF / DKIM / DMARC Analysis
       ↓
IOC Extraction
       ↓
URL & Domain Investigation
       ↓
Findings & Classification
       ↓
Incident Response Recommendations
```

---

## 🚨 Key Findings

| Finding            | Result                              |
| ------------------ | ----------------------------------- |
| SPF                | ❌ Failed                            |
| DKIM               | ❌ Failed                            |
| DMARC              | ⚠️ No record found                  |
| From / Return-Path | ⚠️ Different domains                |
| URL reputation     | 0/90 detections                     |
| URLScan            | DNS resolution failed               |
| Classification     | **Suspicious / Suspected Phishing** |

---

## 📸 Evidence

### Email Header Analysis

![Header Analysis](images/header-analysis.png)

### VirusTotal Analysis

![VirusTotal Results](images/virustotal-results.png)

### URLScan Analysis

![URLScan Results](images/urlscan-preview.png)

---

## 📄 Full Incident Report

[View the complete Incident Report](INCIDENT-REPORT.md)

---

## 🧠 Investigation Summary

The email used an urgent password-expiration message to encourage the recipient to access an unfamiliar authentication URL.

Email authentication checks identified several anomalies:

* SPF authentication failed.
* DKIM authentication failed.
* No DMARC record was found for the visible sender domain.
* The visible `From` address and `Return-Path` used different domains.
* The embedded authentication URL used an unfamiliar domain.

VirusTotal did not identify the URL as malicious at the time of analysis, while URLScan was unable to resolve the domain.

These results do not independently confirm that the URL was malicious. However, when considered together with the email's social-engineering characteristics and authentication anomalies, the message was classified as **Suspicious / Suspected Phishing**.

---

## 🛡️ Recommended Response Actions

Based on this simulated investigation, the following actions would be recommended in an enterprise environment:

1. Quarantine the suspicious email.
2. Search for other messages containing the same sender, domain, or URL.
3. Determine whether the targeted user clicked the link.
4. If the link was accessed, review relevant authentication, browser, network, and endpoint logs.
5. If credentials were submitted, reset the affected account credentials and review authentication activity.
6. Revoke active sessions where appropriate if account compromise is suspected.
7. Validate indicators before adding them to security blocklists.
8. Remind users to access Microsoft 365 and other services through known, trusted portals rather than links in unexpected emails.

---

## Investigation Limitations

This investigation uses simulated training data.

The organization, email addresses, domains, and other indicators in the scenario are fictional. The IP address `192.0.2.45` belongs to a documentation/example IP range and should not be interpreted as a real attacker IP address.

Threat-intelligence results can also change over time. A clean result from VirusTotal does not establish that a URL is safe.

---

## Disclaimer

This project is a simulated cybersecurity investigation created for educational and portfolio purposes. It does not represent a real security incident or investigation of a real organization.
