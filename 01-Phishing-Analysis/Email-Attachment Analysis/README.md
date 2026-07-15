<div align="center">

<p align="center"> <img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f2027,100:2c5364&height=200&section=header&text=Email%20Attachment%20Analysis&fontSize=35&fontColor=ffffff" /> </p> <p align="center"> <img src="https://img.shields.io/badge/Category-Email%20Security%20%7C%20Malware%20Analysis-blue?style=for-the-badge"> <img src="https://img.shields.io/badge/Status-Complete-success?style=for-the-badge"> <img src="https://img.shields.io/badge/Tools-emldump.py%20%7C%20eioc.py%20%7C%20VirusTotal-orange?style=for-the-badge"> </p> <p align="center"> <b>Part of SOC Lab Portfolio → Phishing Analysis</b> </p>

![Category](https://img.shields.io/badge/Category-Email%20Security%20%7C%20Malware%20Analysis-blue?style=for-the-badge&logo=linux&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-green?style=for-the-badge)
![Tools Used](https://img.shields.io/badge/Tools-emldump.py%20%7C%20eioc.py%20%7C%20VirusTotal-orange?style=for-the-badge)
io)*


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

We executed the following extraction script to isolate Stream 4, decode the raw base64 data streams (`-d`), and output the structured file inside our safe environment:

```bash
python3 ../Tools/emldump.py sample1.eml -s 4 -d > quotation.iso
$ python3 ../Tools/emldump.py sample1.eml
1:      1442 M  Multipart/alternative
2:       143    Text/plain
3:      1244    Text/html
4:    114688    Application/octet-stream (quotation.iso)

$ python3 ../Tools/emldump.py sample1.eml -s 4 -d > quotation.iso
$ file quotation.iso
quotation.iso: ISO 9660 CD-ROM filesystem data 'ISO_IMAGE'

python3 eioc.py ../04_Attachment_Analysis/sample1.eml

[ Suspicious Email ]
         │
         ▼
[ Download Attachment (quotation.iso) ]
         │
         ▼
[ Extract Attachment using emldump.py ]
         │
         ▼
[ Generate IOCs using eioc.py ]
         │
         ▼
[ Analyze Hashes on VirusTotal ]
         │
         ▼
[ Confirm Malicious Payload (20/41 Score) ]
