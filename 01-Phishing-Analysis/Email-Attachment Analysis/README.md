<div align="center">

# 📁 Email Attachment Extraction & Malicious Payload Analysis

![Category](https://img.shields.io/badge/Category-Email%20Security%20%7C%20Malware%20Analysis-blue?style=for-the-badge&logo=linux&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-green?style=for-the-badge)
![Tools Used](https://img.shields.io/badge/Tools-emldump.py%20%7C%20eioc.py%20%7C%20VirusTotal-orange?style=for-the-badge)

*Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)*

</div>

---

### 🎯 Objective
To safely triage a suspicious email, download its payload, dynamically isolate/extract the underlying file structure from raw MIME streams inside a Linux sandbox, parse key Indicators of Compromise (IOCs), and cross-examine the cryptographic signatures against threat intelligence databases to verify malware delivery.

### 🧰 Tools & Environment
* **emldump.py (Python Utility):** A specialized command-line tool used to analyze the structure of a MIME message (EML file) and dump specific attachment streams.
* **eioc.py (Custom Python Parser):** A rapid-triage script to extract email metadata, transport path IPs, domains, and attachment hashes simultaneously.
* **VirusTotal:** A centralized malware scanning platform utilized to cross-examine cryptographic file hashes against industry-standard anti-malware engine definitions.

---

## 🔬 Technical Extraction & Analysis Walkthrough

### 📁 Step 1: Initial Email Triage & Attachment Download
First, the suspicious email was reviewed inside an isolated mail reader to identify potential phishing indicators and analyze the social engineering scenario:
* **Subject Line:** `FW: Due Invoice Payment - protonmail.com - Wire Transfer Document`
* **Sender:** Paolo Reggiani (`Paol.Reggiani@moss.it`)
* **Recipient:** `wpx@protonmail.com`
* **Attachment Name:** `quotation.iso` (Size: 112 KB)
* **Social Engineering Narrative:** The email uses an urgency-based social engineering tactic by masquerading as a critical bank/wire transfer confirmation, prompting the victim to mount and open the attachment.

After mapping the initial parameters, the suspicious `.eml` container was safely downloaded and moved into a forensic staging folder.

#### 📧 Raw Email Interface:
![Suspicious Email with ISO Attachment](Email_Attachment.png)

---

### 📦 Step 2: Extracting Attachment from Raw Email using `emldump.py`
To avoid interacting with the file inside a live host system, we performed a secure extraction. We leveraged `emldump.py` to analyze the raw MIME streams of the file:
1. **Structure Scan:** Running the command-line utility targeted Stream 4, which was classified as `application/octet-stream` containing the `quotation.iso` file payload (114,688 bytes).
2. **Payload Extraction:** The attachment stream was isolated, decoded dynamically (`-d`), and safely dumped into our staging directory as a raw ISO disk image:
```bash
python3 ../Tools/emldump.py sample1.eml s4 -d > quotation.iso
