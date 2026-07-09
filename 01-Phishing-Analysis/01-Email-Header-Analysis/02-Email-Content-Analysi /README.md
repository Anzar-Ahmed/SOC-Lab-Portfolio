### 📥 Sample #1 — "All unverified accounts will be suspended"

#### 📋 Case Profile

* **Scenario:** Crypto phishing email impersonating Trust Wallet support.
* **Objective:** Trick users into clicking a malicious "Confirm my wallet" button to steal wallet credentials.
* **Triage Process:** Visual inspection of email content combined with domain verification and branding analysis.

---

#### 🔍 Main Forensic Findings

* **Domain Spoofing:** The email claims to be from Trust Wallet but originates from a non-official domain (`emails.gorgias.com`). This indicates sender spoofing and lack of domain authenticity.
* **Branding Discrepancy:** The legitimate brand is written as **“Trust Wallet”**, whereas the email uses **“Trustwallet”**, showing incorrect word spacing — a common phishing indicator.
* **Improper Greeting & Formatting:** The greeting **“Hi Dear; Customer”** contains awkward punctuation and unprofessional formatting, which is not typical of legitimate corporate communication.
* **Urgency-Based Social Engineering:** The message states that *“all unverified accounts will be suspended”*, creating panic and forcing immediate action.
* **Suspicious Call-to-Action:** The **“Confirm my wallet”** button is likely malicious and may redirect to a phishing page designed to steal sensitive information.
* **Date Inconsistency:** The subject and email body contain mismatched dates, indicating poor phishing template construction.

> **Analysis Verdict:** 🔴 **Confirmed Phishing** — Social engineering combined with domain spoofing targeting cryptocurrency wallet users.

---

#### 🛡️ Defensive Action Plan (SOC Playbook)

1. **User Awareness:** Educate users to avoid clicking on suspicious wallet verification links.
2. **Domain Blocking:** Block `emails.gorgias.com` (if confirmed malicious in context) at email gateway level.
3. **Link Analysis:** Extract and analyze embedded URLs in a sandbox environment.
4. **Threat Hunting:** Search for similar phishing patterns targeting crypto-related services.
5. **Reporting:** Report phishing domain to threat intelligence platforms.

---

#### 📸 Evidence & Visual Indicators

* **Phishing Email Layout (Trust Wallet Spoof)**
  ![Sample 1 Email View](screenshots/sample1-email-view.png)

* **Highlighted Suspicious Elements**
  ![Sample 1 Highlighted](screenshots/sample1-highlighted.png)

---
