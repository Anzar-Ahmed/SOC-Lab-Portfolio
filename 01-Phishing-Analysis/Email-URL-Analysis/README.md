<div align="center">

# 🔗 Email URL Analysis

![Category](https://img.shields.io/badge/Category-Phishing%20Analysis-blue?style=for-the-badge&logo=wireshark&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-green?style=for-the-badge)
![Tools Used](https://img.shields.io/badge/Tools-CyberChef%20%7C%20eioc.py%20%7C%20TextEditor-orange?style=for-the-badge)

*Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)*

</div>

---

### 🎯 Objective
To safely extract, defang, and inspect hidden hyperlinks embedded within suspicious email files (`.eml`). The goal is to identify malicious domains, tracking links, or credential harvesting pages without accidentally clicking them.

### 🧰 Tools & Environment
* **CyberChef:** Used for parsing `Quoted-Printable` text, extracting embedded links, and defanging them automatically.
* **eioc.py (Python Script):** A custom automated terminal tool used to quick-parse email headers, IPs, and target URLs.
* **Source Code Inspection:** Inspecting raw email text to locate anchor tags (`<a>`) and actual hyperlink references (`href`).

---

## 🔬 Technical Extraction Walkthrough

### 📁 Artifact: sample1.eml Analysis

We processed the raw email file through three different verification steps to isolate and evaluate the embedded links.

<details>
<summary><b>Click to expand URL Analysis Breakdown</b></summary>
<br>

### 🛠️ Step 1: Raw Code Inspection (Source Verification)
By opening the email file inside a text editor and searching for standard HTML hyperlink elements (`<a href=...`), we isolated the exact button construction:

* **Button Label Text:** `Reactivate Your Account`
* **Target Hyperlink:** `https://dsgo[.]to/CQECQECnpqY3NDSGtODt9ft2qtxzXGUveTV5fRYmTYAZsQ...`
* **Analyst Note:** The body text tells the user their bank account is blocked, but the underlying anchor tag forces their browser to visit an unknown domain (`dsgo.to`) instead of an official banking server.

---

### ⚙️ Step 2: Automated Parsing via Python (`eioc.py`)
Running our dedicated command-line utility (`python3 eioc.py ../03_URL_Analysis/sample1.eml`) extracted the essential Indicators of Compromise (IOCs) rapidly:

* **Claimed Display Sender:** `alerts@chase.com`
* **Actual Return-Path:** `kellyellin426@proton.me`
* **Extracted Target URL:** `hxxps[://]dsgo[.]to/...`
* **Analyst Note:** The utility clearly captures the spoofing mismatch. The content pretends to be a critical notification from **Chase Bank**, but the internal links route directly to the external `dsgo.to` domain.

---

### 🧪 Step 3: CyberChef Extraction and Defanging
To ensure absolute safety during documentation and reporting, the raw data was processed in CyberChef using a three-stage recipe:
1. **From Quoted-Printable:** Decodes the raw body encoding.
2. **Extract URLs:** Automatically identifies all hidden web links.
3. **Defang URL:** Disables the links by changing `https://` to `hxxps[://]` and adding brackets to dots (`[.]`), preventing accidental execution.

#### 📋 Extracted & Defanged IOCs:
```text
hxxps[://]raw[.]githubusercontent[.]com/MalwareCube/SOC101/main/assets/...[.]jpg
hxxps[://]dsgo[.]to/CQECQECnpqY3NDSGtODt9ft2qtxzXGUveTV5fRYmTYAZsQ...
