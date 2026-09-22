# Phishing Email Investigation & Incident Triage

A hands-on cybersecurity investigation of a suspicious password-expiration email using raw header analysis, OSINT threat intelligence, and email authentication checks.

## Objective

Investigate a reported password-expiration email targeting an employee at Enterprise Corp, analyze authentication headers, inspect embedded URL artifacts, and formulate containment recommendations.

## Tools Used

* **MXToolbox** — Header Analysis & Authentication Checks
* **VirusTotal** — URL Reputation Lookup
* **URLScan.io** — Webpage & DNS Analysis

## Key Findings

| Check / Artifact         | Finding / Result                    | Status               |
| ------------------------ | ----------------------------------- | -------------------- |
| Header Sender            | `support@account-update-notice.com` | ⚠️ Suspicious Domain |
| Return-Path              | `bounce@unauthorized-relay.net`     | ⚠️ Domain Mismatch   |
| SPF                      | `spf=fail`                          | ❌ Failed             |
| DKIM                     | `dkim=fail`                         | ❌ Failed             |
| DMARC                    | No record found                     | ⚠️ Missing Policy    |
| VirusTotal               | 0/90 detections                     | ℹ️ Unflagged         |
| URLScan.io               | HTTP 400 / DNS Resolution Error     | ⚠️ Unresolvable      |
| **Final Classification** | **Suspicious / Suspected Phishing** | 🚨                   |

## 📖 Full Incident Report

[View the complete Incident Response Report →](INCIDENT-REPORT.md)

> **Note:** This is a simulated cybersecurity investigation created. Enterprise Corp and the indicators used in the scenario are fictional/training data.
