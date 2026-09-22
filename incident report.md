# Phishing Email Investigation — Incident Report

## 1. Executive Summary

This report documents the investigation of a suspicious password-expiration email reported by an employee at Enterprise Corp.

The investigation focused on email header analysis, authentication checks, indicator extraction, and analysis of the URL embedded in the email.

The investigation identified several suspicious characteristics, including failed SPF and DKIM authentication, the absence of a DMARC record, a mismatch between the visible sender and Return-Path domains, and an unfamiliar authentication URL.

Based on the available evidence, the email was classified as **Suspicious / Suspected Phishing**.

---

## 2. Project Scenario

An employee at Enterprise Corp reported receiving an email claiming that their Microsoft 365 password would expire within two hours.

The email instructed the recipient to click an embedded link to keep their password active.

### Email Details

**From:** `support@account-update-notice.com`
**To:** `j.doe@enterprise-corp.com`
**Subject:** `URGENT: Your Microsoft 365 Password Expires Today`

### Email Body

> Dear Valued Employee, your corporate network password is set to expire in 2 hours. Click the button below to keep your active password:

**Embedded URL:**

`http://login-verify-portal-check.com/auth`

---

## 3. Investigation Objectives

The investigation aimed to:

* Analyze the raw email headers.
* Review SPF, DKIM, and DMARC authentication results.
* Identify potentially suspicious indicators of compromise.
* Investigate the embedded URL and associated domain.
* Use threat intelligence sources to assess the URL.
* Determine an appropriate incident classification.
* Formulate recommended containment and response actions.

---

## 4. Evidence Reviewed

The investigation used the following evidence:

* Raw email headers.
* Email sender and Return-Path information.
* SPF, DKIM, and DMARC authentication results.
* Embedded URL.
* VirusTotal URL reputation results.
* URLScan.io domain and webpage analysis.

---

# 5. Email Header Analysis

## 5.1 Header Information

The following raw email header information was analyzed:

```text
Received: from mail-out.unauthorized-relay.net (mail-out.unauthorized-relay.net [192.0.2.45])
    by mx.enterprise-corp.com (Postfix) with ESMTPS id 4SyT8k2zZ1z
    for <j.doe@enterprise-corp.com>; Mon, 21 Sep 2026 09:14:22 +0000 (UTC)
Authentication-Results: mx.enterprise-corp.com;
    spf=fail (sender IP 192.0.2.45 is not authorized) smtp.mailfrom=bounce@unauthorized-relay.net;
    dkim=fail header.d=account-update-notice.com;
Return-Path: <bounce@unauthorized-relay.net>
From: "IT Support Desk" <support@account-update-notice.com>
To: "Jane Doe" <j.doe@enterprise-corp.com>
Subject: URGENT: Your Microsoft 365 Password Expires Today
Date: Mon, 21 Sep 2026 09:14:18 +0000
Message-ID: <20260921091418.9812A4F@account-update-notice.com>
MIME-Version: 1.0
Content-Type: text/html; charset="UTF-8"
```

The headers show that the message was received from `mail-out.unauthorized-relay.net` and that the sending IP address was `192.0.2.45`.

The visible `From` address uses the domain `account-update-notice.com`, while the Return-Path uses `unauthorized-relay.net`.

The authentication results also indicate SPF and DKIM failures.

### Header Analysis Evidence

The headers were analyzed using MXToolbox:

![MXToolbox Header Analysis](images/header%20analysis.png)

---

## 5.2 SPF Analysis

**SPF (Sender Policy Framework)** is an email authentication mechanism used to verify whether a sending server or IP address is authorized to send email on behalf of a domain.

The email failed SPF authentication. The sending IP address `192.0.2.45` was not authorized for the relevant envelope sender domain.

This is a suspicious characteristic because the sending infrastructure did not pass the domain's SPF authentication check.

---

## 5.3 DKIM Analysis

**DKIM (DomainKeys Identified Mail)** uses a cryptographic signature to allow a receiving mail server to verify that a message was authorized by the signing domain and that relevant parts of the message were not modified.

The email failed DKIM authentication.

The analysis also indicated that no valid DKIM-Signature header was present. MXToolbox reported a DKIM signature error and indicated that an aligned DKIM signature was required for the message to be considered aligned.

This provided another authentication failure associated with the email.

---

## 5.4 DMARC Analysis

**DMARC (Domain-based Message Authentication, Reporting & Conformance)** builds on SPF and DKIM and uses domain alignment to help receiving mail systems determine how messages that fail authentication should be handled.

No DMARC record was found for `account-update-notice.com`.

The analysis also identified problems with:

* DMARC compliance.
* SPF alignment.
* SPF authentication.
* DKIM alignment.
* DKIM authentication.

The absence of a DMARC record meant that there was no published DMARC policy for the investigated domain.

---

# 6. IOC Extraction

The following indicators were identified during the investigation:

| IOC Type      | Indicator                                   | Observation                                 |
| ------------- | ------------------------------------------- | ------------------------------------------- |
| From Address  | `support@account-update-notice.com`         | Visible sender                              |
| Return-Path   | `bounce@unauthorized-relay.net`             | Different envelope-sender domain            |
| Mail Relay    | `mail-out.unauthorized-relay.net`           | Sending relay identified in Received header |
| IP Address    | `192.0.2.45`                                | Originating relay IP                        |
| Phishing URL  | `http://login-verify-portal-check.com/auth` | Embedded authentication link                |
| Target Domain | `login-verify-portal-check.com`             | Domain used by embedded URL                 |
| URL Path      | `/auth`                                     | Authentication-related URL path             |

**Note:** The IP address `192.0.2.45` is within a documentation/example address range and is therefore treated as synthetic training data rather than a real attacker infrastructure indicator.

---

# 7. URL and Domain Investigation

## 7.1 VirusTotal Analysis

The embedded URL was submitted to VirusTotal for reputation analysis:

`http://login-verify-portal-check.com/auth`

![VirusTotal Analysis](images/virustotal%20results.png)

The analysis returned **0/90 detections**.

The URL was not flagged as malicious by the security vendors included in the VirusTotal analysis at the time of investigation.

However, an unflagged result does **not** establish that a URL is safe. Newly created, inactive, unavailable, or previously unseen infrastructure may not yet have reputation data or detections.

The VirusTotal result was therefore treated as an additional data point rather than definitive evidence that the URL was legitimate.

---

## 7.2 URLScan Analysis

The domain `login-verify-portal-check.com` was investigated using URLScan.io:

![URLScan Analysis](images/urlscan%20preview.png)

The analysis returned an **HTTP 400 error** and a **DNS resolution error**. The domain did not resolve to a valid IPv4 or IPv6 address at the time of analysis, and the webpage could not be loaded.

The inability to resolve the domain prevented further webpage and network analysis.

This result does not by itself prove that the domain was malicious. In this investigation, it is consistent with the domain being unavailable or synthetic training data.

---

# 8. Analysis and Findings

## Finding 1: Urgency and Social Engineering

The email claimed that the recipient's corporate password would expire within two hours.

This creates urgency and encourages the recipient to act quickly rather than independently verifying the request.

The use of an urgent password-expiration message is consistent with a common phishing technique in which attackers attempt to create pressure around account access.

---

## Finding 2: Failed Email Authentication

The message failed multiple email authentication checks:

* SPF: Failed.
* DKIM: Failed.
* DMARC: No record found.
* SPF alignment: Problem.
* DKIM alignment: Problem.

The combination of these results increased the level of suspicion surrounding the message.

---

## Finding 3: Sender Domain Anomaly

The visible sender was:

`support@account-update-notice.com`

The Return-Path was:

`bounce@unauthorized-relay.net`

Different domains between the visible From address and Return-Path can occur legitimately, particularly when organizations use third-party email delivery services.

However, in this case, the domain difference occurred alongside failed SPF and DKIM authentication and other suspicious characteristics.

It was therefore treated as an anomaly requiring further investigation.

---

## Finding 4: Suspicious Authentication URL

The email directed the recipient to:

`http://login-verify-portal-check.com/auth`

The domain was unfamiliar and used an authentication-related path.

The URL also did not correspond to the organization's known Microsoft 365 authentication infrastructure.

Combined with the urgent password-expiration message, the URL represented a significant phishing indicator.

---

## Finding 5: Threat Intelligence Did Not Confirm Malicious Activity

VirusTotal returned **0/90 detections**.

URLScan.io returned an **HTTP 400 error and DNS resolution error**.

The available threat intelligence therefore did not independently identify the URL as malicious at the time of analysis.

However, the absence of detections should not be interpreted as proof that the URL was safe.

---

# 9. Incident Classification

Based on the available evidence, the simulated email was classified as:

## **Suspicious / Suspected Phishing**

The classification was based on the combination of:

* Urgent password-expiration messaging.
* Failed SPF authentication.
* Failed DKIM authentication.
* Missing DMARC record.
* Sender and Return-Path domain mismatch.
* Suspicious authentication URL.
* Unresolved URL domain.

The evidence did not establish that the URL successfully delivered malware or that credentials were actually compromised.

---

# 10. Recommended Incident Response Actions

Based on this simulated investigation, the following actions would be recommended in an enterprise environment.

### 1. Quarantine the Email

Remove or quarantine the suspicious email from the targeted user's mailbox and prevent further interaction with the embedded URL.

### 2. Search for Related Messages

Search the email environment for:

* The sender address.
* The sender domain.
* The Return-Path domain.
* The embedded URL.
* The URL domain.
* Related subject lines or message identifiers.

This can help determine whether other users received the same message.

### 3. Determine Whether the User Clicked the Link

Review available browser, proxy, DNS, firewall, or endpoint telemetry to determine whether the targeted user accessed the URL.

### 4. Investigate Potential Credential Exposure

If the user clicked the link and entered credentials, the organization should consider:

* Resetting the affected password.
* Reviewing authentication activity.
* Revoking active sessions where appropriate.
* Investigating suspicious sign-in activity.
* Escalating the incident if evidence of account compromise is identified.

### 5. Block Confirmed Malicious Indicators

If further investigation confirms that the domain or URL is malicious, appropriate security controls can be updated to block the identified indicators.

### 6. User Awareness

Users should be encouraged to access corporate services through known bookmarks or official portals rather than following unexpected authentication links received by email.

---

# 11. Investigation Limitations

Several limitations should be considered when interpreting the results.

* The investigation was performed using simulated email and domain data.
* The IP address `192.0.2.45` is from a documentation/example address range.
* The investigated URL could not be resolved during the URLScan analysis.
* VirusTotal did not identify the URL as malicious at the time of analysis.
* No endpoint, proxy, DNS, firewall, or authentication logs were available to determine whether the recipient interacted with the URL.
* No evidence was available to confirm actual credential compromise or malware execution.

These limitations mean that the investigation can establish suspicious characteristics but cannot independently confirm a successful phishing compromise.

---

# 12. Conclusion

The investigated email demonstrated multiple characteristics associated with a potential phishing attempt, including urgency-based messaging, failed email authentication, sender-domain anomalies, and a suspicious authentication URL.

Although the available threat intelligence did not identify the URL as malicious and the domain could not be resolved during analysis, these results do not establish that the email was legitimate.

Based on the combined evidence, the email was classified as **Suspicious / Suspected Phishing**.

The investigation demonstrates a practical workflow for analyzing suspicious email messages using header analysis, authentication results, IOC extraction, and external threat intelligence.
