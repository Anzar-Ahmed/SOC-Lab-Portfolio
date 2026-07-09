# 📧 Email Header Analysis

**Category:** Phishing Analysis
**Status:** In Progress
**Emails Analyzed:** 2/5

*Part of the Phishing Analysis section — SOC Lab Portfolio*

---

## 🎯 Objective

To analyze suspicious/phishing email samples using manual inspection techniques, focusing on identifying spoofed domains, branding inconsistencies, social engineering tactics, and authentication misalignment (SPF/DKIM/DMARC), without relying entirely on automated tools.

---

## 🧰 Tools Deployed

* **Sublime Text** — Used to open raw email source codes and manually inspect routing paths and header structure.
* **MXToolbox** — Used to verify email authentication protocols (SPF, DKIM, DMARC) and analyze header integrity.
* **WHOIS Lookup** — Used to investigate domain ownership and validate sender legitimacy.

---

## 📊 Investigation Matrix

| #  | Subject / Threat Lure                                          | Claimed Sender                                          | Core Authentication                  | Final Verdict |
| -- | -------------------------------------------------------------- | ------------------------------------------------------- | ------------------------------------ | ------------- |
| 01 | All unverified accounts will be suspended                      | Trust Wallet Support                                    | Not Verified / Suspicious Domain     | 🔴 Malicious  |
| 02 | Rachel, cosmicfusiontech.com will expire in 7 days - renew now | [renewals@namecheap.com](mailto:renewals@namecheap.com) | SPF: Pass / DKIM: Pass / DMARC: Pass | 🟢 Legitimate |

---

## 🔬 Triage Walkthroughs

### 📁 sample-01: Trust Wallet Phishing (Social Engineering + Domain Spoofing)

#### 🔍 Key Findings

* **Domain Mismatch:** Email claims to be from Trust Wallet but originates from `emails.gorgias.com`
* **Branding Issue:** “Trustwallet” used instead of official “Trust Wallet”
* **Improper Greeting:** “Hi Dear; Customer” indicates poor formatting
* **Urgency Tactic:** “All unverified accounts will be suspended” used to create panic
* **Malicious CTA:** “Confirm my wallet” button likely leads to phishing page

> **Verdict:** 🔴 Confirmed Phishing (Crypto-targeted social engineering attack)

---

### 📁 sample-02: Namecheap Notification (Legitimate Transactional Email)

#### 🔍 Key Findings

* **Proper Domain Alignment:** `namecheap.com` matches authenticated sender
* **Valid Authentication:** SPF, DKIM, DMARC all pass correctly
* **Trusted Infrastructure:** Email routed via authorized SendGrid service
* **Consistent Branding:** No formatting or identity issues

> **Verdict:** 🟢 Legitimate Email

---

## 🚩 Compiled Indicators of Compromise (IOCs)

| Indicator Target                                          | Type              | Contextual Function                     | Association |
| --------------------------------------------------------- | ----------------- | --------------------------------------- | ----------- |
| emails.gorgias.com                                        | Suspicious Domain | Third-party domain used for spoofing    | Case 01     |
| Confirm my wallet                                         | Phishing CTA      | Used to redirect user to malicious page | Case 01     |
| [kellyellin426@proton.me](mailto:kellyellin426@proton.me) | Attacker Inbox    | Used for capturing victim responses     | Case 01     |

---

## 🗺️ MITRE ATT&CK Tracking

| Identification | Structural Name                     | Application Details                                         |
| -------------- | ----------------------------------- | ----------------------------------------------------------- |
| T1566.002      | Phishing: Spearphishing Link        | User is tricked into clicking a malicious verification link |
| T1036.005      | Masquerading: Match Legitimate Name | Fake branding used to impersonate Trust Wallet              |

---

## 💡 Key Learning Points

* **Domain Verification is Critical:** Always verify whether the sender domain matches the claimed organization.
* **Branding Mistakes Reveal Attacks:** Small inconsistencies like spacing (“Trustwallet”) are strong phishing indicators.
* **Urgency is a Red Flag:** Attackers use fear tactics to force quick decisions.
* **CTA Links are High Risk:** Always analyze links before clicking, especially in financial/crypto emails.
