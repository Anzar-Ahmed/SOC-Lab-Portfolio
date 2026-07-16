<div align="center">

<p align="center"> 
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f2027,100:2c5364&height=200&section=header&text=PDF%20Phishing%20Triage&fontSize=35&fontColor=ffffff" /> 
</p> 

<p align="center"> 
  <img src="https://img.shields.io/badge/Category-Email%20Security%20%7C%20Maldoc%20Analysis-blue?style=for-the-badge"> 
  <img src="https://img.shields.io/badge/Status-Complete-success?style=for-the-badge"> 
  <img src="https://img.shields.io/badge/Tools-pdfid.py%20%7C%20pdf--parser.py%20%7C%20VirusTotal-orange?style=for-the-badge"> 
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
