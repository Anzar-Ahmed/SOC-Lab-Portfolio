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
  <b>Part of [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)</b> 
</p>

</div>

---

### 🎯 Objective
To demonstrate that relying solely on file hashes for document triaging is insufficient, as unknown/zero-day files will bypass static hash checks. This lab walks through the static analysis of a deceptive PDF using command-line utilities to find embedded links, analyze structural objects, and safely extract suspicious Indicators of Compromise (IOCs) without executing the file.

### 🧰 Tools & Environment
<table>
  <tr>
    <td><b>🔍 pdfid.py</b></td>
    <td>A tool by Didier Stevens used to scan a PDF file to check for specific suspicious header elements, JavaScript, actions, and embedded contents.</td>
  </tr>
  <tr>
    <td><b>⚙️ pdf-parser.py</b></td>
    <td>A utility used to parse the internal elements of a PDF file, extract object contents, and inspect structural items such as actions or hidden URIs.</td>
  </tr>
  <tr>
    <td><b>🛡️ VirusTotal</b></td>
    <td>A threat intelligence platform utilized here to prove why static signatures can fail against newly crafted payloads.</td>
  </tr>
</table>

---

## 🔬 Technical Analysis & Step-by-Step Walkthrough

### 📁 Artifact: Statement.pdf (Deceptive Amazon Phishing Lure)

<details open>
<summary><b>Click to collapse/expand Forensic Investigation</b></summary>
<br>

### 🔍 Step 1: Initial Visual & Hash Verification
The suspicious document, disguised as an Amazon Account Hold Notice (`Statement.pdf`), warns the user of unusual payment activity and urges them to click "Verify Now" to restore access. 

#### 📧 Document Preview:
![Amazon Phishing PDF](Statement.pdf.png)

To perform an initial threat intelligence query, we computed the SHA-256 cryptographic signature of the target PDF file (`pdf-doc-vba-eicar-dropper.pdf`) and searched it on VirusTotal:
* **Target File:** `pdf-doc-vba-eicar-dropper.pdf`
* **SHA-256 Hash:** `e90e263bce015c0ad6640d2581582aee4f940accc1d688a25d9a319e39c4110`

#### ❌ The "Hash Check Only" Trap:
Upon querying the signature, VirusTotal returned **"No matches found"**.

![VirusTotal Hash Not Found](Virustotal-Result.png)

> 💡 **Analyst Insight:** If a SOC analyst only checks the hash value on threat databases, they might wrongly assume the file is safe and mark the alert as a false positive. Threat actors frequently create fresh document variants to ensure unique hashes, effectively bypassing signature-based protection tools. Deep static analysis is essential.

---

### ⚙️ Step 2: Structural Triage using `pdfid.py`
To identify hidden features, embedded scripts, or execution flags without rendering the file, we ran `pdfid.py` against our target file inside our isolated Linux sandbox.

#### 💻 Command Used:
```bash
python3 ../../Tools/pdfid.py pdf-doc-vba-eicar-dropper.pdf
