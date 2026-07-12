<div align="center">

# 🔗 Email URL Analysis & Threat Intel Validation

![Category](https://img.shields.io/badge/Category-Phishing%20Analysis-blue?style=for-the-badge&logo=wireshark&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-green?style=for-the-badge)
![Tools Used](https://img.shields.io/badge/Tools-CyberChef%20%7C%20eioc.py%20%7C%20VirusTotal%20%7C%20urlscan.io-orange?style=for-the-badge)

*Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)*

</div>

---

### 🎯 Objective
To safely extract, defang, and inspect hidden hyperlinks embedded within suspicious email files (`.eml`). The goal is to identify malicious domains, tracking links, or credential harvesting pages without accidentally clicking them.

### 🧰 Tools & Environment

#### 🛠️ Core Extraction Tools (Used in Sample 1)
* **Source Code Inspection:** Opening raw email text files to manually find hidden HTML links (`<a href=...`) inside buttons.
* **eioc.py (Python Script):** A quick command-line tool used to extract email headers, sender IPs, and hidden links all at once.
* **CyberChef:** A web tool used to decode messy email text, find links automatically, and safely disable them.

#### 🌐 Threat Intelligence Tools (Used in Sample 2)
* **VirusTotal:** A global scanner that checks if a link or domain is already reported as malicious by major security companies.
* **urlscan.io:** A web sandbox that safely visits a link for you to see its background network traffic, redirection paths, and server location.
* **URLVoid:** A domain reputation system that scans links against multiple security blocklists to verify safety.
* **WannaBrowser:** A safe browser simulator used to check live server response codes (like `200 OK`) and raw page layout without opening it on a local PC.
* **URL2PNG:** A screenshot automation tool that safely takes a picture of a suspicious landing page without any risk of infection.

---

## 🔬 Technical Extraction Walkthroughs

### 📁 sample-01: Chase Bank Lure (Manual Extraction & Triage)

Click to expand URL Analysis Breakdown

<details>
<summary><b>🔍 URL Analysis Breakdown</b></summary>
<br>

### 🛠️ 1. Raw Code Inspection (Source Verification)
* **Button Label Text:** `Reactivate Your Account`
* **Target Hyperlink:** `https://dsgo[.]to/CQECQECnpqY3NDSGtODt9ft2qtxzXGUveTV5fRYmTYAZsQ...`
* **Analyst Note:** The body text tells the user their bank account is blocked, but the underlying anchor tag forces their browser to visit an unknown domain (`dsgo.to`) instead of an official banking server.

### ⚙️ 2. Automated Parsing via Python (`eioc.py`)
* **Claimed Display Sender:** `alerts@chase.com`
* **Actual Return-Path:** `kellyellin426@proton.me`
* **Extracted Target URL:** `hxxps[://]dsgo[.]to/...`
* **Analyst Note:** The utility clearly captures the spoofing mismatch. The content pretends to be a critical notification from Chase Bank, but the internal links route directly to the external `dsgo.to` domain.

### 🧪 3. CyberChef Extraction and Defanging
* **The Recipe:** Processed the raw email data using a three-stage workflow: *From Quoted-Printable* ➡️ *Extract URLs* ➡️ *Defang URL*.
* **The Goal:** Disables the links by changing `https://` to `hxxps[://]` to ensure absolute safety during documentation and reporting, preventing accidental clicks.

#### 📋 Extracted & Defanged IOCs:
```text
hxxps[://]raw[.]githubusercontent[.]com/MalwareCube/SOC101/main/assets/...[.]jpg
hxxps[://]dsgo[.]to/CQECQECnpqY3NDSGtODt9ft2qtxzXGUveTV5fRYmTYAZsQ...
