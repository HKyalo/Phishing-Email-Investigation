# Phishing Email Investigation — Incident Report

## 1. Executive Summary

This report documents a simulated investigation of a suspicious email claiming that a user's Microsoft 365 password would expire within two hours.

The investigation examined the email headers, authentication results, sender information, embedded URL, and available open-source threat intelligence.

Several suspicious characteristics were identified, including failed SPF authentication, failed DKIM authentication, the absence of a DMARC record, a mismatch between the visible sender and Return-Path domains, an unfamiliar authentication URL, and an urgent password-expiration message.

Based on the combined evidence, the email was classified as:

**Suspicious / Suspected Phishing**

The investigation did not independently confirm that the URL was malicious. VirusTotal returned 0/90 detections at the time of analysis, while URLScan was unable to resolve the domain.

This investigation uses simulated training data and is intended for educational and portfolio purposes.

---

## 2. Project Scenario

A simulated employee at **Enterprise Corp** received an email appearing to originate from an IT Support Desk.

The email claimed that the employee's corporate network password would expire within two hours and instructed the recipient to click an authentication link to keep the password active.

### Email Subject

`URGENT: Your Microsoft 365 Password Expires Today`

### Targeted User

`j.doe@enterprise-corp.com`

### Visible Sender

`support@account-update-notice.com`

### Embedded URL

`http://login-verify-portal-check.com/auth`

The purpose of the investigation was to determine whether the email displayed characteristics consistent with phishing activity.

---

## 3. Investigation Objectives

The investigation aimed to:

1. Analyze the email headers and message routing information.
2. Evaluate SPF, DKIM, and DMARC authentication results.
3. Identify potentially relevant indicators of compromise (IOCs).
4. Investigate the embedded URL and domain using open-source intelligence.
5. Assess the overall characteristics of the email.
6. Develop appropriate incident response recommendations.

---

## 4. Evidence Reviewed

The following evidence was available for analysis:

* Raw email headers
* Email body and embedded URL
* MXToolbox Email Header Analyzer results
* VirusTotal URL analysis
* URLScan domain investigation

---

# 5. Email Header Analysis

## 5.1 Header Information

The relevant headers were:

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

The message was received by the simulated enterprise mail server from:

`mail-out.unauthorized-relay.net`

The originating IP shown in the header was:

`192.0.2.45`

This IP address belongs to a documentation/example IP range and is therefore treated as **synthetic training data**, rather than a real attacker IP address.

---

## 5.2 SPF Analysis

**SPF result: FAIL**

The authentication results indicated:

`spf=fail`

The sending IP address was reported as not being authorized to send mail for the relevant envelope sender domain.

The SMTP envelope sender was:

`bounce@unauthorized-relay.net`

### What this indicates

Sender Policy Framework (SPF) is an email authentication mechanism used to identify which servers are authorized to send email on behalf of a domain.

In this investigation, the sending server failed the SPF check.

This is a significant anomaly because legitimate email infrastructure should normally be authorized by the relevant domain's SPF policy.

However, SPF failure alone does not prove that an email is phishing. It must be considered together with other evidence.

---

## 5.3 DKIM Analysis

**DKIM result: FAIL**

The authentication results indicated:

`dkim=fail`

The analysis also reported that no valid `DKIM-Signature` header was present.

### What this indicates

DomainKeys Identified Mail (DKIM) uses a cryptographic signature to allow receiving mail systems to verify that a message was signed by an authorized domain and that relevant message content has not been altered.

In this case, the message did not provide a valid DKIM signature.

This represents another authentication anomaly.

---

## 5.4 DMARC Analysis

**DMARC result: No DMARC record found**

The MXToolbox analysis reported:

* DMARC Compliant: No DMARC Record Found
* SPF Alignment: Problem
* SPF Authenticated: Problem
* DKIM Alignment: Problem
* DKIM Authenticated: Problem

### What this indicates

Domain-based Message Authentication, Reporting & Conformance (DMARC) builds on SPF and DKIM and checks whether the authenticated domain is appropriately aligned with the domain visible in the `From` address.

The analysis found no DMARC record for:

`account-update-notice.com`

This means the domain did not provide a DMARC policy that could be evaluated for the message.

The lack of a DMARC record does not by itself prove that the message is malicious. However, in combination with the failed SPF and DKIM results, it contributes to the overall suspicious nature of the message.

---

# 6. IOC Extraction

The following indicators were extracted from the email:

| IOC Type      | Indicator                                   | Observation                                 |
| ------------- | ------------------------------------------- | ------------------------------------------- |
| From address  | `support@account-update-notice.com`         | Visible sender                              |
| Return-Path   | `bounce@unauthorized-relay.net`             | Different envelope-sender domain            |
| Mail relay    | `mail-out.unauthorized-relay.net`           | Sending relay identified in Received header |
| IP address    | `192.0.2.45`                                | Synthetic documentation/example IP          |
| Phishing URL  | `http://login-verify-portal-check.com/auth` | Embedded authentication link                |
| Target domain | `login-verify-portal-check.com`             | Domain used by the embedded URL             |
| URL path      | `/auth`                                     | Authentication-related path                 |

### From vs Return-Path

The visible `From` address was:

`support@account-update-notice.com`

The Return-Path was:

`bounce@unauthorized-relay.net`

A difference between these addresses can occur legitimately when organizations use third-party email delivery services.

However, in this investigation the difference is notable because it occurs alongside SPF failure, DKIM failure, the absence of a DMARC record, and other suspicious characteristics.

---

# 7. URL and Domain Investigation

## 7.1 VirusTotal Analysis

The embedded URL was submitted to VirusTotal:

`http://login-verify-portal-check.com/auth`

### Result

**0 / 90 security vendors flagged the URL at the time of analysis.**

Analysis date:

`2026-09-22 13:27:27 UTC`

The available report did not show the URL as being identified as malicious by the listed security vendors.

### Interpretation

A 0/90 result should **not** be interpreted as proof that the URL is safe.

Threat intelligence databases may not contain newly created, inactive, synthetic, or previously unseen domains.

Therefore, the result is recorded as:

**Threat intelligence did not independently identify the URL as malicious at the time of analysis.**

---

## 7.2 URLScan Analysis

The domain was investigated using URLScan:

`login-verify-portal-check.com`

The result was:

**HTTP 400 Error**

with the following resolution result:

**DNS Error — Could not resolve domain**

URLScan reported that the domain could not be resolved to a valid IPv4 or IPv6 address and therefore could not be loaded.

### Interpretation

At the time of investigation, the domain did not resolve through the DNS infrastructure available to URLScan.

This means the domain could not be actively examined through the browser-based scan.

The result is consistent with the domain being inactive, nonexistent, synthetic, or otherwise unavailable at the time of investigation.

It does not independently establish that the domain was malicious.

---

# 8. Analysis and Findings

## Finding 1: Urgency and Social Engineering

The email used an urgent password-expiration message:

> "Your Microsoft 365 Password Expires Today"

The body stated that the password would expire within two hours and instructed the recipient to click a link to maintain access.

This creates pressure for the recipient to act quickly without independently verifying the request.

The use of urgency and fear of account loss is consistent with common phishing and social-engineering techniques.

---

## Finding 2: Failed Email Authentication

The message failed both SPF and DKIM authentication.

### Authentication results

| Authentication Mechanism | Result          |
| ------------------------ | --------------- |
| SPF                      | Fail            |
| DKIM                     | Fail            |
| DMARC                    | No record found |

These authentication anomalies reduce confidence that the message originated from an authorized mail system associated with the visible sender domain.

---

## Finding 3: Sender Domain Anomaly

The visible sender was:

`support@account-update-notice.com`

while the Return-Path was:

`bounce@unauthorized-relay.net`

The domains therefore differed.

Although sender and Return-Path differences can occur legitimately, the mismatch is noteworthy in this case because it appears together with failed authentication checks.

---

## Finding 4: Suspicious Authentication URL

The email directed the recipient to:

`http://login-verify-portal-check.com/auth`

The URL uses a domain that is unrelated to the visible recipient organization and presents itself as an authentication destination.

The use of an unfamiliar authentication domain in an urgent password-expiration message is a significant phishing indicator.

---

## Finding 5: Threat Intelligence Did Not Confirm Malicious Activity

VirusTotal returned:

**0/90 detections**

URLScan was unable to resolve the domain.

These results did not independently confirm that the URL was malicious.

However, the absence of a detection does not eliminate the possibility of phishing, particularly where a domain may be inactive, newly created, unavailable, or part of simulated training data.

---

# 9. Incident Classification

### Classification

**Suspicious / Suspected Phishing**

### Rationale

The classification is based on the combination of:

* Urgent password-expiration messaging
* Social-engineering characteristics
* SPF authentication failure
* DKIM authentication failure
* No DMARC record found
* Difference between visible sender and Return-Path domains
* Unfamiliar authentication URL
* DNS failure during URLScan investigation

The available evidence does not establish that the URL was definitively malicious or that an account was compromised.

---

# 10. Recommended Incident Response Actions

Based on this simulated investigation, the following actions would be recommended in an enterprise environment.

## 10.1 Quarantine the Message

Quarantine the suspicious email to prevent further interaction with the embedded link.

---

## 10.2 Search for Related Messages

Search the organization's email environment for:

* `account-update-notice.com`
* `unauthorized-relay.net`
* `login-verify-portal-check.com`
* `support@account-update-notice.com`
* `bounce@unauthorized-relay.net`

This can help determine whether other users received the same or similar message.

---

## 10.3 Determine Whether the User Interacted

Establish whether the targeted user:

* Opened the email
* Clicked the embedded URL
* Submitted credentials
* Downloaded any files
* Experienced unexpected authentication activity

---

## 10.4 Review Security Logs

If the user interacted with the link, relevant authentication, browser, network, and endpoint logs should be reviewed for suspicious activity.

Particular attention should be given to:

* Unexpected authentication attempts
* New login locations or devices
* Multiple failed login attempts
* Successful logins following credential submission
* Suspicious browser or endpoint activity

---

## 10.5 Protect the Account if Credentials Were Submitted

If the user entered credentials into the suspicious website, appropriate account-protection measures should be taken.

These may include:

* Resetting the affected password
* Reviewing recent authentication activity
* Revoking active sessions where appropriate
* Reviewing MFA activity
* Escalating the incident if evidence of account compromise is identified

---

## 10.6 Validate Indicators Before Blocking

The identified domains and URL should be validated before being added to enterprise blocklists.

If subsequent investigation confirms malicious activity, the relevant indicators can be blocked through appropriate email, DNS, web-filtering, or security controls.

---

## 10.7 User Awareness

Users should be reminded to access Microsoft 365 and other corporate services through known and trusted portals rather than authentication links received unexpectedly through email.

---

# 11. Investigation Limitations

This investigation has several limitations.

### Simulated Data

The investigation uses fictional training data rather than a real enterprise email environment.

Enterprise Corp and the users, domains, and email addresses in the scenario are fictional.

### Synthetic IP Address

The IP address:

`192.0.2.45`

belongs to a documentation/example IP range and should not be interpreted as a real attacker infrastructure address.

### Limited Telemetry

No endpoint, firewall, DNS, proxy, authentication, or SIEM logs were available.

As a result, the investigation could not determine whether a user actually clicked the URL or whether credentials were submitted.

### Threat Intelligence Limitations

VirusTotal did not report a detection at the time of analysis, and URLScan could not resolve the domain.

Threat intelligence results can change over time and should not be treated as definitive proof that an indicator is safe or malicious.

---

# 12. Conclusion

The simulated email demonstrated several characteristics associated with phishing, including urgency, a suspicious authentication request, failed SPF and DKIM authentication, absence of a DMARC record, a sender-domain anomaly, and an unfamiliar login URL.

Open-source threat intelligence did not independently confirm the URL as malicious during the investigation. VirusTotal returned 0/90 detections, while URLScan was unable to resolve the domain.

Considering the available evidence as a whole, the email was classified as:

**Suspicious / Suspected Phishing**

In a real enterprise environment, the next investigative priority would be determining whether the targeted user interacted with the email and, if so, reviewing authentication, endpoint, network, and email telemetry for evidence of compromise.

---

## Disclaimer

This project is a simulated cybersecurity investigation created for educational and portfolio purposes. It does not represent a real security incident or investigation of a real organization.
