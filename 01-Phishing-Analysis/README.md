<div align="center">

# 📧 Email Header Analysis

![Category](https://img.shields.io/badge/Category-Phishing%20Analysis-blue?style=for-the-badge&logo=gmail&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-green?style=for-the-badge)
![Emails Analyzed](https://img.shields.io/badge/Emails%20Analyzed-2%2F2-success?style=for-the-badge)

*Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)*

</div>

---

### 🎯 Objective
To analyze raw email headers from suspicious/phishing samples using a text editor (Sublime Text) to identify sender spoofing, authentication mismatches (SPF/DKIM/DMARC), and Reply-To/Return-Path inconsistencies — without relying on automated header-analysis tools, in order to build strong manual analysis fundamentals.

### 🧰 Tools Deployed
* **`Sublime Text`** — Used to open raw email source codes and manually check routing paths.
* **`MXToolbox`** — Used to verify if email security protocols (SPF/DKIM/DMARC) are aligned with the visible sender.

---

## 📊 Investigation Matrix

| # | Subject / Threat Lure | Claimed Sender | Core Authentication | Final Verdict |
|---|---|---|---|---|
| **01** | Your Bank Account has been blocked due to unusual activities | `alerts@chase.com`[cite: 2] | SPF: Pass* / DMARC: Pass* / DKIM: Timeout[cite: 2] | 🔴 **Malicious**[cite: 2] |
| **02** | Rachel, cosmicfusiontech.com will expire in 7 days - renew now | `renewals@namecheap.com`[cite: 2] | SPF: Pass / DMARC: Fail* / DKIM: Fail*[cite: 2] | 🔴 **Malicious**[cite: 2] |

> ⚠️ **Important Security Note:** In both cases, basic gateway filters failed to block these emails because the attackers used trusted platforms (ProtonMail & SendGrid)[cite: 2]. The phishing attempts were only caught by performing a manual **Domain Alignment Check**[cite: 2].

---

## 🔬 Triage Walkthroughs

### 📁 Case 01: Chase Bank Impersonation (Friendly-From Spoofing)
<details>
<summary><b>Click to expand Case Investigation Details</b></summary>
<br>

#### 📋 Profile & Triage
* **Scenario:** A fake bank email pretending to be Chase Bank, using fake urgency ("account blocked") to scare the user into giving up credentials[cite: 2].
* **Methodology:** Opened the raw email source in `Sublime Text` to look at the hidden routing paths behind the user interface[cite: 2].

#### 🔍 Forensic Analysis (How I caught it)
* **Fake Sender Address:** The user sees `From: alerts@chase.com`, but the hidden headers show that replies are actually going to `Reply-To: kellyellin426@proton.me` and `Return-Path: kellyellin426@proton.me`[cite: 2]. 
* **Real Source Tracked:** Traced the delivery trail back to a ProtonMail server IP: `185.70.40.140`[cite: 2]. Real Chase Bank servers had nothing to do with this email[cite: 2].
* **Deceptive SPF/DMARC Pass:** The security status shows `spf=pass`[cite: 2]. However, checking deeper shows it only passed because the attacker authenticated their *own* personal ProtonMail account (`smtp.mailfrom=protonmail.com`)[cite: 2]. It never verified or matched the visible `chase.com` domain[cite: 2].

> **Verdict:** 🔴 **Confirmed Phishing**[cite: 2]

#### 🛡️ Threat Mitigation (SOC Action Plan)
1. Blocked the sender IP `185.70.40.140` and the malicious email address `kellyellin426@proton.me` at the email gateway[cite: 2].
2. Ran an organization-wide search to find and delete this exact same email from all other user mailboxes[cite: 2].

</details>

---

### 📁 Case 02: Namecheap Lure (Advanced ESP Cloud Abuse)
<details>
<summary><b>Click to expand Case Investigation Details</b></summary>
<br>

#### 📋 Profile & Triage
* **Scenario:** A domain expiration warning pretending to be Namecheap, urging the user to click a "Renew Now" link[cite: 2].
* **Methodology:** Checked the raw header layout in Sublime Text and validated the security keys using `MXToolbox`[cite: 2].

#### 🔍 Forensic Analysis (How I caught it)
* **DKIM Signature Mismatch:** The email has a valid signature, but it belongs to `d=sendgrid.info` instead of matching the visible sender domain `namecheap.com`[cite: 2]. This is a major domain misalignment flag[cite: 2].
* **Bulk Mailer Abuse:** The attacker registered a real account on SendGrid (a bulk emailing service) and used high-reputation servers to send phishing emails that easily bypass basic security checks[cite: 2].
* **DMARC Failure:** Because the background sender domain (SendGrid) does not align with the visible brand (Namecheap), the overall security logic triggers a hard DMARC failure[cite: 2].

> **Verdict:** 🔴 **Confirmed Phishing**[cite: 2]

#### 🛡️ Threat Mitigation (SOC Action Plan)
1. Documented the unique SendGrid header tracking keys and reported the compromised account to SendGrid's abuse team to shut it down[cite: 2].
2. Checked network logs to ensure no local users clicked on the malicious "Renew Now" link[cite: 2].

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
| `alerts@chase.com` | Spoofed String | Fake display email impersonating a bank[cite: 2]. | Case 01[cite: 2] |
| `kellyellin426@proton.me` | Exfiltration Box | Attacker's inbox catching victim replies[cite: 2]. | Case 01[cite: 2] |
| `185.70.40.140` | Threat Relay IP | ProtonMail server IP used to launch the attack[cite: 2]. | Case 01[cite: 2] |
| `renewals@namecheap.com` | Spoofed Identity | Fake identity used to target domain administrators[cite: 2]. | Case 02[cite: 2] |
| `sendgrid.info` | Hijacked Domain | The legitimate email relay service abused by the attacker[cite: 2]. | Case 02[cite: 2] |

---

## 🗺️ MITRE ATT&CK Tracking

| Identification | Structural Name | Application Details |
|---|---|---|
| [T1566.002](https://attack.mitre.org/techniques/T1566/002/)[cite: 2] | Phishing: Spearphishing Link[cite: 2] | Using urgency context to trick a user into clicking a malicious link[cite: 2]. |
| [T1036.005](https://attack.mitre.org/techniques/T1036/005/)[cite: 2] | Masquerading: Match Legitimate Name[cite: 2] | Forging the visible sender name to look exactly like a trusted corporate brand[cite: 2]. |

---

## 💡 Key Learning Points
* **Alignment is Everything:** A green "SPF: Pass" means nothing if the backend domain doesn't match the actual `From` address the user sees on their screen[cite: 2].
* **Attackers Love Legitimate Cloud Platforms:** Attackers heavily abuse services like SendGrid and ProtonMail because security filters trust their IP addresses by default[cite: 2]. Blue teams must look at *alignment*, not just baseline trust[cite: 2].
* **Check Reply-To Fields First:** Looking closely at the `Reply-To` and `Return-Path` fields is one of the fastest ways to catch identity spoofing without needing heavy external tools[cite: 2].

---

<div align="center">

### 📂 Navigate

[⬅️ Back to Phishing Analysis Overview](../) &nbsp;\|\nbsp; [Email Content Analysis ➡️](../02-Email-Content-Analysis)[cite: 2]

</div>
