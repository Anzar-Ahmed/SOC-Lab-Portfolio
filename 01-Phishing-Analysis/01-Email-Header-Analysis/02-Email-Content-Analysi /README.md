<div align="center">

# 📧 Email Content Analysis

![Category](https://img.shields.io/badge/Category-Phishing%20Analysis-blue?style=for-the-badge&logo=gmail&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-green?style=for-the-badge)
![Samples Analyzed](https://img.shields.io/badge/Samples%20Analyzed-2%2F2-success?style=for-the-badge)

*Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)*

</div>

---

### 🎯 Objective
To inspect and break down the visual layer, text formatting, and social engineering tricks used in suspicious emails. This analysis helps identify how attackers try to trick users into giving up sensitive data by manipulating their behavior.

### 🧰 Analysis Methodology
* **Visual Inspection:** Checking logos, text layout, and branding mistakes.
* **Psychological Lures:** Finding scare tactics like urgency or account suspension threats.
* **Grammar & Spelling Auditing:** Spotting broken sentences, wrong punctuation, and AI-generated text patterns.

---

## 🔬 Content Triage Walkthroughs

### 📁 sample-01: Trust Wallet Phishing (Social Engineering + Visual Spoofing)

<details>
<summary><b>Click to expand Phishing Indicators Breakdown</b></summary>
<br>

### 🔍 Phishing Indicators Breakdown

#### 🟥 1. Domain Verification Issue
* **Claimed Brand:** Trust Wallet Support
* **The Catch:** The email claims to be from Trust Wallet, but it actually comes from `7wq1vg3kn9woejk4@emails.gorgias.com`.
* **Verdict:** ❌ Real crypto platforms do not use third-party customer support domains (`gorgias.com`) to send critical security or verification alerts.

#### 🟡 2. Branding & Word Spacing Discrepancy
* **Official Name:** `"Trust Wallet"` (Written as two separate words).
* **Email Text:** The attacker uses `"Trustwallet-Support"` and `"Trustwallet Team"` (joined together).
* **Verdict:** ❌ This spacing mistake shows that an outsider manually created the template and failed to match the official brand name.

#### ⚠️ 3. Bad Greeting & Punctuation
* **Greeting Used:** `"Hi Dear; Customer"`
* **Verdict:** ❌ Very awkward and unprofessional grammar. Official automated emails from financial systems have strict quality controls and never include strange semi-colons or broken language.

#### 🚨 4. Urgency / Fear Tactic
* **The Lure:** `"FWD: All unverified accounts will be suspended on 10/30/2022..."`
* **Trick:** The email sets a strict, immediate deadline to create panic, forcing the user to act quickly before thinking or checking the facts.
* **Verdict:** ❌ This is a classic social engineering trick to bypass the user's logic.

#### 🎯 5. Suspicious Call-to-Action (CTA)
* **Target Element:** The blue button labeled `"Confirm my wallet"`.
* **Verdict:** ❌ **High Risk.** Clicking this button redirects the user to a fake phishing website designed to steal their wallet's secret seed phrase or login credentials.

---

### 🧠 Summary & Verdict
This email is a clear phishing attempt. It creates fake urgency, contains obvious branding mistakes, uses broken grammar, and uses a random third-party subdomain to bypass email filters.

> ➡️ **Final Verdict:** 🔴 **Confirmed Phishing Email**

</details>

---

### 📁 sample-02: Trust Wallet Lure (AI Generation & Subtle Grammar Errors)

<details>
<summary><b>Click to expand Phishing Indicators Breakdown</b></summary>
<br>

### 🔍 Phishing Indicators Breakdown

#### 🟢 1. Deceptive Branding (AI Assisted)
* **Observation:** Unlike `sample-01`, this email writes `"Trust Wallet"` perfectly with correct spacing and capitalization. 
* **Greeting:** Uses a standard corporate greeting: `"Dear Customer"`.
* **Analyst Note:** This shows that attackers are now using AI tools (like ChatGPT) to write emails. This removes the obvious red flags and makes the email look professional at first glance.

#### 🟥 2. Subtle Grammatical Flaws (The AI Giveaway)
* **The Error Line:** `"Due to the recently update of NFT's & Coins, all unverified accounts will be suspended."`
* **Grammar Mistake 1:** It says `"recently update"` instead of the correct phrase *"recent update"*.
* **Grammar Mistake 2:** Wrong apostrophe placement in `"NFT's"` for making it plural. Professional companies use correct plurals like *NFTs*.

#### 🟡 3. Spelling Mistake (The Detection Barometer)
* **The Footer Text:** `"For further assistence with this issue, please contact our support team."`
* **The Slip:** The word **`assistence`** is spelled wrong (the correct spelling is *assistance*).
* **Verdict:** ❌ Even if attackers use AI to write the main body, they often make mistakes when manually editing the template. These typos are great indicators for defenders to spot a scam.

#### 🚨 4. Fear-Based Urgency
* **The Hook:** `"all unverified accounts will be suspended."`
* **Trick:** Even though the email text looks cleaner than before, the underlying trick is the same: scare the user with account suspension to make them panic.

#### 🎯 5. Suspicious Call-to-Action (CTA)
* **Target Element:** The blue button labeled `"Go to verification"`.
* **Verdict:** ❌ **High Risk.** It mimics a real security workflow but links directly to an attacker-controlled page to steal crypto wallets.

---

### 🧠 Summary & Verdict
This sample shows how phishing is changing. While AI helped the attacker fix the brand spacing and generic greeting, subtle grammar mistakes (`recently update`) and a clear typo (`assistence`) expose it as a fraud.

> ➡️ **Final Verdict:** 🔴 **Confirmed Phishing Email**

</details>

---

## 💡 Key Content Analysis Takeaways
* **AI Improves the Shell, But Mistakes Happen:** Attackers use AI to fix obvious errors, but manual copy-pasting still introduces spelling mistakes (like `assistence`) that reveal the scam.
* **Watch Out for Subtle Grammar:** Small errors like `"recently update"` or wrong apostrophes (`NFT's`) are major warning signs in official corporate emails.
* **Urgency is Always Suspicious:** No matter how clean or professional an email looks, if it threatens to suspend your account unless you act immediately, it is almost certainly phishing.

---

<div align="center">

### 📂 Navigate

[⬅️ Back to Email Header Analysis](../01-Email-Header-Analysis)  |  [Back to Portfolio Main Overview](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)

</div>
