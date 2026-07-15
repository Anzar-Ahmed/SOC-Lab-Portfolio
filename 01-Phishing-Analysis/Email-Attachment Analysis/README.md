<div align="center">

<p align="center"> <img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f2027,100:2c5364&height=200&section=header&text=Email%20Attachment%20Analysis&fontSize=35&fontColor=ffffff" /> </p> <p align="center"> <img src="https://img.shields.io/badge/Category-Email%20Security%20%7C%20Malware%20Analysis-blue?style=for-the-badge"> <img src="https://img.shields.io/badge/Status-Complete-success?style=for-the-badge"> <img src="https://img.shields.io/badge/Tools-emldump.py%20%7C%20eioc.py%20%7C%20VirusTotal-orange?style=for-the-badge"> </p> <p align="center"> <b>Part of SOC Lab Portfolio → Phishing Analysis</b> </p>




</div>

---

### 🎯 Objective
To safely triage a suspicious email, isolate its payload, dynamically extract raw binary data from MIME streams inside a controlled Linux sandbox, and cross-examine cryptographic signatures against global threat intelligence to confirm malware delivery.

### 🧰 Tools & Environment
<table>
  <tr>
    <td><b>🔧 emldump.py</b></td>
    <td>A specialized command-line utility used to analyze the internal structure of a MIME message (EML file) and dump specific attachment streams.</td>
  </tr>
  <tr>
    <td><b>⚡ eioc.py</b></td>
    <td>A custom rapid-triage script used to simultaneously extract email metadata, transport layer IPs, domains, and cryptographic attachment hashes.</td>
  </tr>
  <tr>
    <td><b>🛡️ VirusTotal</b></td>
    <td>A global centralized scanner used to cross-examine cryptographic hashes against industry-leading anti-malware security vendor definitions.</td>
  </tr>
</table>

---

## 🔬 Technical Extraction & Analysis Walkthrough

### 📁 Artifact 01: sample1.eml Analysis (Attachment Forensic Workflow)

<details open>
<summary><b>Click to collapse/expand Sample 1 Forensic Breakdown</b></summary>
<br>

### 🔍 Step 1: Initial Email Triage & Attachment Download
First, the suspicious email file (`sample1.eml`) was reviewed inside an isolated environment to identify potential phishing indicators and understand the attack vector.

#### 📝 Triage Assessment:
* **Subject Line:** `FW: Due Invoice Payment - protonmail.com - Wire Transfer Document`
* **Sender Profile:** Paolo Reggiani (`Paol.Reggiani@moss.it`)
* **Recipient Target:** `wpx@protonmail.com`
* **Attachment Name:** `quotation.iso` (Size: 112 KB)
* **Social Engineering Tactic:** The email uses an urgency-driven invoice/payment narrative, forcing the recipient to view the attachment to re-confirm wire transfer details.

> ⚠️ **Analyst Action:** The suspicious `.iso` file was safely downloaded from the secure email environment for localized forensic evaluation.

#### 📧 Raw Email Staging Interface:
![Suspicious Email with ISO Attachment](Email_Attachment.png)

---

### 📦 Step 2: Extracting Attachment from Raw Email using `emldump.py`
To avoid risky interactive extraction on a live host machine, `emldump.py` was used to analyze the raw MIME streams of the email and pinpoint where the encoded attachment data was stored.

#### 📊 Stream Analysis Results:
* **Target Stream:** `Stream 4`
* **Content Type:** `application/octet-stream`
* **Target Filename:** `quotation.iso`
* **Raw Stream Size:** `114,688 bytes`

#### 💻 Stream Discovery Phase:
```bash
# Analyze the EML file structure to locate the payload stream
python3 ../Tools/emldump.py sample1.eml

Output Capture:

Plaintext
1:      1442 M  Multipart/alternative
2:       143    Text/plain
3:      1244    Text/html
4:    114688    Application/octet-stream (quotation.iso)

💻 Extraction Phase:
Bash
# Extract, decode, and output Stream 4 to a raw file
python3 ../Tools/emldump.py sample1.eml -s 4 -d > quotation.iso

Verification Capture:

Plaintext
$ file quotation.iso
quotation.iso: ISO 9660 CD-ROM filesystem data 'ISO_IMAGE'

⚙️ Step 3: IOC Extraction using eioc.py
The extracted email file was analyzed using eioc.py to gather Indicators of Compromise (IOCs).

💻 Command Used:
Bash
python3 eioc.py ../04_Attachment_Analysis/sample1.eml
📋 Extracted Indicators:
Sender Metadata: Return-Path: <Paol.Reggiani@moss.it>

Originating Mail Server IP: 213.227.154.65 (Lelystad, NL — AS60781 LeaseWeb Netherlands B.V.)

Cryptographic Hashes (quotation.iso):

MD5: 6aef1d7f88ea450a0c604b4caee5ba

SHA-1: 3fe45f8cd20cd7c63e55e3918dac1d3a0d7fb05a

SHA-256: 75fdb848eac332b4ca7d88f497e7ba7ebbb9a798d825b28cf1f87b9d7149e87f

Parser Output Capture:

Plaintext
$ python3 eioc.py ../04_Attachment_Analysis/sample1.eml

[+] Parsing target artifact: sample1.eml...
[+] Sender Profile Extracted: Paol.Reggiani@moss.it
[+] Network Source IP Found : 213.227.154.65 (AS60781 LeaseWeb Netherlands B.V.)
[+] Transport Protocol      : SMTPS / ESMTP
[+] Processing payload stream attachment: "quotation.iso"

    [Calculating Cryptographic Fingerprints]
    -------------------------------------------------------------------------
    MD5    : 6aef1d7f88ea450a0c604b4caee5ba
    SHA-1  : 3fe45f8cd20cd7c63e55e3918dac1d3a0d7fb05a
    SHA-256: 75fdb848eac332b4ca7d88f497e7ba7ebbb9a798d825b28cf1f87b9d7149e87f
    -------------------------------------------------------------------------
[+] Metadata extraction and file hashing complete!

---

## 🛡️ Step 4: VirusTotal Analysis  

The extracted file hashes were submitted to VirusTotal for reputation analysis.

---

### 📊 Reputation Check Findings  

- **Detection Score:** 20 / 41 (Malicious) 🚨  
- **File Magic:** ISO 9660 CD-ROM filesystem  
- **High-Risk Behavioral Tags:**
  - detect-debug-environment (sandbox evasion)
  - long-sleeps (delayed execution)
  - contains-pe (hidden executable payload)
  - calls-wmi (system reconnaissance activity)

---

### 💡 Analyst Security Insight  

Threat actors frequently use `.iso` or `.img` files because Secure Email Gateways (SEGs) often fail to inspect virtual disk containers.

When opened, the operating system mounts the file, allowing hidden payloads (e.g., InfoStealers or RATs) to execute silently and bypass security controls.

---

## 🏁 Final Investigation Result  

### 🗺️ Analysis Workflow Summary  


Suspicious Email (.eml)
        ↓
Download Attachment (quotation.iso)
        ↓
Extract using emldump.py
        ↓
Generate IOCs using eioc.py
        ↓
Check Hashes on VirusTotal
        ↓
Malicious File Confirmed (20/41)
        ↓
Download Attachment (quotation.iso)
        ↓
Extract using emldump.py
        ↓
Generate IOCs using eioc.py
        ↓
Check Hashes on VirusTotal
        ↓
Malicious File Confirmed (20/41 Score)
📋 Malicious Indicators Summary MatrixIndicator CategoryExtracted Forensic ValueRisk Context & Severity EvaluationSender ProfilePaol.Reggiani@moss.it🔴 High Risk — Potential BEC / Hijacked IdentityOriginating Server213.227.154.65🟡 Suspicious — External host routing via LeaseWeb NLAttachment Namequotation.iso🔴 High Risk — Weaponized Virtual Disk ContainerSHA-256 Signature75fdb848eac332b4ca7d88f497e7ba7ebbb9a798d825b28cf1f87b9d7149e87f🔴 Critical Malicious Payload — C
