<div align="center">

<p align="center"> 
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:1f1c2c,100:928dab&height=200&section=header&text=PDF%20Analysis&fontSize=35&fontColor=ffffff" /> 
</p> 

<p align="center"> 
  <img src="https://img.shields.io/badge/Category-PDF%20Security%20%7C%20Maldoc%20Analysis-purple?style=for-the-badge"> 
  <img src="https://img.shields.io/badge/Status-Complete-success?style=for-the-badge"> 
  <img src="https://img.shields.io/badge/Tools-pdfid.py%20%7C%20pdf--parser.py%20%7C%20VirusTotal-blue?style=for-the-badge"> 
</p> 

<p align="center"> 
  <b>Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)</b> 
</p>

</div>


---

### 🎯 Objective
This investigation demonstrates advanced static triage of a evasive PDF document used in a phishing campaign. The core objective is to prove that relying solely on cryptographic hash values is insufficient for modern SOC triage, as zero-day payloads easily bypass signature-based detection. We walk through the command-line analysis of a PDF container to discover embedded execution flags and safely extract hidden Indicators of Compromise (IOCs) without executing the file.

### 🧰 Tools & Environment
<table>
  <tr>
    <td><b>🔍 pdfid.py</b></td>
    <td>A command-line tool designed by Didier Stevens to scan a PDF structure, detecting the presence of specific keywords, hidden objects, scripts, and automatic execution streams.</td>
  </tr>
  <tr>
    <td><b>⚙️ pdf-parser.py</b></td>
    <td>A deep forensic utility used to parse, isolate, and extract raw data streams, structural objects, and specific payload actions (such as embedded URIs) from inside a PDF file.</td>
  </tr>
  <tr>
    <td><b>🛡️ VirusTotal</b></td>
    <td>A centralized global threat intelligence repository utilized in this scenario to evaluate the limitations of signature-based file matching.</td>
  </tr>
</table>

---

## 🔬 Technical Analysis & Step-by-Step Walkthrough

### 📁 Artifact: Statement.pdf (Deceptive Amazon Phishing Lure)

<details open>
<summary><b>Click to collapse/expand Forensic Investigation</b></summary>
<br>

### 🔍 Step 1: Initial Visual Triage & Signature Database Query
The incoming suspicious document, disguised as an official Amazon Account Billing statement (`Statement.pdf`), warns the recipient of unusual billing activity and lures them into clicking a call-to-action button labeled "Verify Now".

#### 📧 Email Attachment Preview:
![Amazon Phishing PDF](Statement.pdf.png)

During the triage phase, we computed the SHA-256 cryptographic signature of the target file (`pdf-doc-vba-eicar-dropper.pdf`) and queried it against global threat intelligence platforms:
* **Target File Name:** `pdf-doc-vba-eicar-dropper.pdf`
* **Computed SHA-256 Hash:** `e90e263bce015c0ad6640d2581582aee4f940accc1d688a25d9a319e39c4110`

#### ❌ The "Hash Check Only" Trap:
The hash query on VirusTotal returned **"No matches found"**, indicating a zero-day variant or a dynamically modified, unrecognized payload.

![VirusTotal Hash Not Found](Virustotal-Result.png)

> 💡 **Analyst Insight:** Threat actors frequently modify superficial bytes of malicious documents (e.g., changing benign metadata fields or adding null bytes) to completely change the resulting file hash. If a Tier-1 SOC analyst stops their investigation at a "clean" VirusTotal hash check, they will falsely close a critical intrusion attempt. This emphasizes the necessity of behavioral and structural analysis.

---

### ⚙️ Step 2: Structural Triage using `pdfid.py`
To identify structural indicators (such as automated script execution flags) without launching a PDF reader and risking host infection, we executed `pdfid.py` inside our isolated Linux sandbox.

#### 💻 Command Executed:
```bash
python3 ../../Tools/pdfid.py pdf-doc-vba-eicar-dropper.pdf
📌 Purpose of the Command:
The pdfid.py tool scans the internal structure of a PDF file and highlights key indicators that are commonly associated with malicious documents, including:

/JavaScript → Presence of embedded scripts

/OpenAction → Automatic execution upon opening the file

/AA (Additional Actions) → Hidden triggers

/URI → Embedded links (often phishing URLs)

/ObjStm → Object streams that may hide malicious content

🐚 Terminal Output Capture:
🧠 Analyst Insight:
This step is critical in identifying suspicious behaviors without rendering the file, thereby avoiding potential exploitation. Even if the file appears benign or is undetected by antivirus engines, structural anomalies revealed by pdfid.py can indicate malicious intent. This reinforces the importance of behavioral and structural analysis over simple hash-based detection, especially when dealing with unknown or zero-day threats.

🕸️ Step 3: Extracting Hidden Link/Payload with pdf-parser.py
Given the confirmation of active elements and auto-run routines, we utilized pdf-parser.py to inspect the internal structural objects of the PDF, targeted on isolating hidden hyperlinks (/URI).

💻 Command Executed:

python3 ../../Tools/pdf-parser.py pdf-doc-vba-eicar-dropper.pdf
🐚 Terminal Output Capture:
🕵️ Forensic Findings (Object 1 Triage):
Upon parsing the elements, we analyzed the details of obj 1 0. The structural content revealed a hidden external reference link linked directly to the front-facing "Verify Now" button:

Trigger Type: /Link

Suspicious URI: [https://script.google.com/macros/s/AKfycbwABk1V3TSZ7dwG4lOsmbewJuEt2TExXS8cSaYuhcQwcxoffsDSJS8VLR4h0pkJFwkUQ/exec](https://script.google.com/macros/s/AKfycbwABk1V3TSZ7dwG4lOsmbewJuEt2TExXS8cSaYuhcQwcxoffsDSJS8VLR4h0pkJFwkUQ/exec)
Threat Analysis: The payload utilizes a legitimate third-party infrastructure URL (script.google.com) to execute a Google Apps Script macro. This technique (known as Living-off-the-Cloud or LoTC) is designed to bypass standard DNS blocklists. Upon clicking, the script logs system properties and redirects the user to credential harvesting infrastructure or executes a secondary drive-by download.

🏁 Final Investigation Result
🗺️ Document Triage Workflow
[ 📄 Suspicious PDF File ]
            │
            ▼
 [ 🔍 VirusTotal Check ] ───> Result: "No Matches Found" (Bypassed static signature defenses)
            │
            ▼
 [ ⚙️ Static Structural Triage ] ───> Utilized pdfid.py ───> Identified: `/JS`, `/OpenAction`
            │
            ▼
 [ 🕸️ Structural Parsing ] ───> Utilized pdf-parser.py ───> Analyzed: Object 1 structure
            │
            ▼
 [ 🎯 Indicator Extraction ] ───> Isolated URL: `[script.google.com/macros/s/](https://script.google.com/macros/s/)...`
            │
            ▼
 [ 🛑 Action Taken ] ───> Confirmed Phishing Campaign. Blocked extracted domain/URI on proxy/firewall.
