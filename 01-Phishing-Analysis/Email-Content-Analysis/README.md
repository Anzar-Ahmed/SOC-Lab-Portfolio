
<div align="center">



# 📧 Email Content Analysis

<p align="center"> 
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:8B0000,100:FF4C4C&height=200&section=header&text=Email%20Content%20Analysis&fontSize=35&fontColor=ffffff" /> 
</p> 

<p align="center"> 
  <img src="https://img.shields.io/badge/Category-Email%20Security%20%7C%20Content%20Analysis-red?style=for-the-badge"> 
  <img src="https://img.shields.io/badge/Status-Complete-success?style=for-the-badge"> 
  <img src="https://img.shields.io/badge/Focus-Phishing%20%7C%20Social%20Engineering-orange?style=for-the-badge"> 
</p> 

<p align="center"> 
  <b>Part of SOC Lab Portfolio → Phishing Analysis</b> 
</p>

---
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



### 📁 sample-03: Amazon Lure (Perfect Content Wrapper vs Header Reality)



<details>

<summary><b>Click to expand Phishing Indicators Breakdown</b></summary>

<br>



### 🔍 Phishing Indicators Breakdown



#### 🟢 1. Flawless Visual Content & Formatting

* **Observation:** The email design is perfectly clean. It includes the official **Amazon logo**, accurate spacing, high-quality layout, and a completely professional generic greeting: `"Dear Amazon Customer,"`.

* **The Threat Lure:** It simulates a strict notification claiming an order was canceled due to fake activity and that the account is suspended.



#### ⚠️ 2. The Danger of Timing & Context (The Real-World Impact)

* **The Scenario:** Imagine a real user who actually ordered a product from Amazon just a few hours ago. 

* **The Reaction:** If they receive this email shortly after making a purchase, the timing matches their real-world actions perfectly. Because there are **zero spelling or design mistakes** in the body text, the user will easily believe this is a genuine alert about their recent order. 

* **The Result:** Instead of treating it with skepticism, they will panic and perform **direct actions immediately** (like clicking the link or copying the security code), completely falling into the attacker's trap.



#### 🟥 3. The Header Breakdown (The Real Identity)

* **Claimed Display Name:** `Amazon Help Center`

* **Actual Hidden Sender Address:** `amz@fareast.com.sg`

* **Verdict:** ❌ **Absolute Domain Spoofing.** While the content layer is perfectly crafted to exploit real-world situations, the email header instantly exposes the scam. Amazon will never distribute official corporate account actions or security flags from a random, unrelated domain registered in Singapore (`.com.sg`).



#### 🚨 4. High-Urgency Hook

* **Subject Line:** `[ACTION REQUEST] You have been red flagged for violating our terms`

* **The Strategy:** Using aggressive phrases like `[ACTION REQUEST]`, `red flagged`, and `violating our terms` creates maximum panic, hoping the user will bypass critical thinking out of fear.



#### 🎯 5. Deceptive Call-to-Action

* **Target Link:** `"Fraudulent Activity Report"` link along with a fake validation code (`EWK1DOOSJ9RE`).

* **Verdict:** ❌ Designed to push the victim into an urgent credential harvesting login sequence.



---



### 🧠 Summary & Verdict

This case is a perfect example of why **Content Analysis alone is never enough**. If an attacker's timing aligns with a user's real-world activity, a clean layout will easily fool them. Defenders and users must always check the **Email Header** data to catch the domain mismatch behind the design shell.



> ➡️ **Final Verdict:** 🔴 **Confirmed Phishing Email**



</details>



---



## 💡 Key Content Analysis Takeaways

* **Content Can Lie, Headers Don't:** Attackers can copy corporate layouts and logos perfectly (like the Amazon sample) to look legitimate. Never trust an email based on appearance alone; always verify the actual sender address in the header.

* **Context & Timing Make Phishing Dangerous:** If a user recently interacted with a service (like buying an item on Amazon), they are highly likely to trust a fake email that matches that event. This is why automated verification is essential.

* **AI Improves the Shell, But Mistakes Happen:** Attackers use AI to fix obvious spelling and formatting flaws, but minor manual copy-pasting or editing still leaves errors behind (like `assistence` or `recently update`).



---



<div align="center">



### 📂 Navigate



[⬅️ Back to Email Header Analysis](../01-Email-Header-Analysis)  |  [Back to Portfolio Main Overview](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)



</div>

