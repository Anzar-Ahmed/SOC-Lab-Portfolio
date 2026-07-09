<div align="center">

# 📧 Email Header Analysis

![Category](https://img.shields.io/badge/Category-Phishing%20Analysis-blue?style=for-the-badge&logo=gmail&logoColor=white)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow?style=for-the-badge)
![Emails Analyzed](https://img.shields.io/badge/Emails%20Analyzed-2%2F5-orange?style=for-the-badge)

*Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)*

</div>

---

### 🎯 Objective
To analyze raw email headers from suspicious/phishing samples using a text editor (Sublime Text) to identify sender spoofing, authentication mismatches (SPF/DKIM/DMARC), and Reply-To/Return-Path inconsistencies — without relying on automated header-analysis tools, in order to build strong manual analysis fundamentals.

### 🧰 Tools Deployed
* **`Sublime Text`** — Raw source ingestion, routing extraction, and hex/string verification.
* **`MXToolbox`** — Programmatic alignment evaluation and cryptographic integrity validation.

---

## 📊 Investigation Matrix

| # | Subject / Threat Lure | Claimed Sender | Core Authentication | Final Verdict |
|---|---|---|---|---|
| **01** | Your Bank Account has been blocked due to unusual activities | `alerts@chase.com`[cite: 2] | SPF: Pass* / DMARC: Pass* / DKIM: Timeout[cite: 2] | 🔴 **Malicious**[cite: 2] |
| **02** | Rachel, cosmicfusiontech.com will expire in 7 days - renew now | `renewals@namecheap.com`[cite: 2] | SPF: Pass / DMARC: Fail* / DKIM: Fail*[cite: 2] | 🔴 **Malicious**[cite: 2] |
| **03** | *(pending)*[cite: 2] | | | 🟡 Pending |
| **04** | *(pending)*[cite: 2] | | | 🟡 Pending |
| **05** | *(pending)*[cite: 2] | | | 🟡 Pending |

> ⚠️ **Critical Architectural Note:** In both investigated cases, standard perimeter filters were bypassed because the attackers utilized legitimate cloud platforms (ProtonMail & SendGrid) to secure initial protocol passes[cite: 2]. The anomalies were only uncovered via deep-dive **Domain Alignment Tracking**[cite: 2].

---

## 🔬 Triage Walkthroughs

### 📁 Case 01: Chase Bank Impersonation (Friendly-From Spoofing)
<details>
<summary><b>Click to expand Case Investigation Details</b></summary>
<br>

#### 📋 Profile & Triage
* **Scenario:** Urgency-driven banking alert weaponized for credential harvesting[cite: 2].
* **Methodology:** Manual isolation of delivery headers inside `Sublime Text` to intercept alternative feedback values[cite: 2].

#### 🔍 Forensic Analysis
* **Header Manipulation:** User-facing string displays `From: alerts@chase.com`, but transactional feedback vectors are routed directly to `Reply-To: kellyellin426@proton.me` and `Return-Path: kellyellin426@proton.me`[cite: 2].
* **Origin Trace:** Tracking hops lead directly back to outbound proxy `mail-40140.protonmail.ch (185.70.40.140)`[cite: 2]. Authorized Chase infrastructure had zero engagement with this transmission[cite: 2].
* **Alignment Breakdown:** `Authentication-Results` confirms a protocol pass because the sender authenticated their own registered asset (`smtp.mailfrom=protonmail.com`), explicitly lacking organizational alignment with the target brand[cite: 2].

> **Verdict:** 🔴 **Confirmed Phishing**[cite: 2]

#### 🛡️ Threat Mitigation
1. Append IP `185.70.40.140` and account string `kellyellin426@proton.me` to the gateway blocklist[cite: 2].
2. Execute a tenant-wide lookup query to purge matching structural anomalies from all active mailboxes[cite: 2].

</details>

---

### 📁 Case 02: Namecheap Lure (Advanced ESP Cloud Abuse)
<details>
<summary><b>Click to expand Case Investigation Details</b></summary>
<br>

#### 📋 Profile & Triage
* **Scenario:** Domain expiration scare tactic designed to drive external link execution[cite: 2].
* **Methodology:** Multi-hop tracking combined with programmatic cryptographic key alignment auditing via `MXToolbox`[cite: 2].

#### 🔍 Forensic Analysis
* **Cryptographic Discrepancy:** The message contains a technically valid signature syntax, but the signing identity is bound to `d=sendgrid.info`[cite: 2]. This produces a hard organizational alignment failure against the display sender domain `namecheap.com`[cite: 2].
* **Infrastructure Hijack:** The threat actor actively hijacked/leased a legitimate bulk mail tier (SendGrid) to piggyback on high-reputation cloud relays, tricking baseline checks that fail to enforce organizational strictness[cite: 2].
* **DMARC Enforcement:** Because the underlying SPF/DKIM data sets point to a completely decoupled entity, the global checking logic triggers an inherent DMARC failure[cite: 2].

> **Verdict:** 🔴 **Confirmed Phishing**[cite: 2]

#### 🛡️ Threat Mitigation
1. Map internal tracking parameters and submit a formal take-down declaration to SendGrid's abuse desk[cite: 2].
2. Audit SIEM firewall / proxy connections to ensure no internal accounts executed callbacks to the embedded URL vector[cite: 2].

</details>

---

## 📸 Artifacts & Validation Logs

#### 🧾 Structural Logging (Case 01)
* **Raw State:** Complete string extraction prior to active sorting steps[cite: 2].
  ![Raw Header View](screenshots/header-full.png)[cite: 2]
* **Annotated Discrepancies:** Isolation of the mismatched delivery targets[cite: 2].
  ![Highlighted Header](screenshots/header-highlighted.png)[cite: 2]

#### 🌐 Reputation & Diagnostic Checks
* **Network Tracing (Case 01):** Verification verifying the proxy source is distinct from authorized assets[cite: 2].
  ![MXToolbox Result](screenshots/mxtoolbox.png)[cite: 2]
  ![WHOIS Result](screenshots/whois.png)[cite: 2]
* **Alignment Validation (Case 02):** MXToolbox report confirming the absolute divergence of the signature profile[cite: 2].
  ![Sample 2 Alignment Failure](screenshots/sample2-header-auth.png)[cite: 2]

---

## 🚩 Compiled Indicators of Compromise (IOCs)

| Indicator Target | Type | Contextual Function | Association |
|---|---|---|---|
| `alerts@chase.com` | Spoofed String | Masquerading display wrapper[cite: 2]. | Case 01[cite: 2] |
| `kellyellin426@proton.me` | Exfiltration Box | Target intercepting victim responses[cite: 2]. | Case 01[cite: 2] |
| `185.70.40.140` | Threat Relay IP | ProtonMail asset utilized as an attack vector[cite: 2]. | Case 01[cite: 2] |
| `renewals@namecheap.com` | Spoofed Identity | High-trust impersonation targeting domain admins[cite: 2]. | Case 02[cite: 2] |
| `sendgrid.info` | Hijacked Domain | Cryptographic signing proxy exploited to route traffic[cite: 2]. | Case 02[cite: 2] |

---

## 🗺️ MITRE ATT&CK Tracking

| Identification | Structural Name | Application Details |
|---|---|---|
| [T1566.002](https://attack.mitre.org/techniques/T1566/002/)[cite: 2] | Phishing: Spearphishing Link[cite: 2] | Exploiting urgency themes to force execution of malicious external addresses[cite: 2]. |
| [T1036.005](https://attack.mitre.org/techniques/T1036/005/)[cite: 2] | Masquerading: Match Legitimate Name[cite: 2] | Engineering display profiles to exactly echo authoritative entities[cite: 2]. |

---

## 💡 Engineering Takeaways & Lessons
* **Alignment Outweighs Status:** A generic protocol validation pass holds no value if the signing domain (`d=`) or envelope source fails to mirror the user-facing header interface[cite: 2].
* **Reputation is Neutral:** Legitimate transactional delivery services (SendGrid) and high-privacy platforms (ProtonMail) are aggressively exploited by attackers to mask footprint anomalies[cite: 2]. Blue teams must prioritize structural validation over baseline platform trust[cite: 2].

---

<div align="center">

### 📂 Navigation

[⬅️ Back to Phishing Analysis Overview](../) &nbsp;\|\nbsp; [Email Content Analysis ➡️](../02-Email-Content-Analysis)[cite: 2]

</div>
