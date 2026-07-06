# 🖥️ Endpoint Security Monitoring Lab

## 📌 Objective
To simulate attacker activity on a Windows endpoint and investigate persistence mechanisms using system tools and EDR.

---

## 🚨 Scenario
A suspicious alert was generated indicating potential unauthorized persistence on a Windows system.

The objective was to investigate the endpoint and identify:
- Persistence techniques
- Suspicious processes
- Indicators of compromise

---

## 🛠️ Tools Used
- Sysmon
- Autoruns (Sysinternals)
- Windows Event Viewer
- CMD / PowerShell
- LimaCharlie EDR

---

## ⚔️ Attack Simulation

### Initial Access

![Meterpreter](screenshots/1_meterpreter.png)

- Reverse shell established
- Attacker gained access

---

## 🔧 Persistence Techniques Identified

### 1. Registry Persistence

![Command](screenshots/registry_1_command.png)
![Evidence](screenshots/registry_2_entry.png)
![Detection](screenshots/registry_3_autoruns.png)

---

### 2. Service Persistence

![Command](screenshots/service_1_command.png)
![Evidence](screenshots/service_2_created.png)
![Detection](screenshots/service_3_autoruns.png)

---

### 3. Scheduled Task Persistence

![Command](screenshots/task_1_command.png)
![Evidence](screenshots/task_2_created.png)
![Detection](screenshots/task_3_autoruns.png)

---

## 🔍 Investigation

### Process Analysis

![Process](screenshots/process.png)

- Identified suspicious process

---

### Network Analysis

![Network](screenshots/netstat.png)

- Detected unusual outbound connection

---

### Event Log Analysis

![Logs](screenshots/event_logs.png)

- Event ID 4720 observed (New user creation)

---

### Autoruns Analysis

![Autoruns](screenshots/autoruns.png)

- Multiple persistence mechanisms detected

---

## 📡 EDR Detection

![LimaCharlie](screenshots/limacharlie_alert.png)

- Suspicious activity flagged by EDR

---

## 🧠 MITRE ATT&CK Mapping

- Persistence → T1547 (Registry Run Keys)
- Persistence → T1050 (New Service)
- Persistence → T1053 (Scheduled Task)
- Command & Control → T1071

---

## 🚩 Findings

- Multiple persistence mechanisms established
- Suspicious process execution observed
- Malicious network activity detected

---

## 🧾 Conclusion

This lab demonstrates how attackers maintain access using persistence techniques and how defenders can detect them using endpoint monitoring tools.

---

## 📚 Lessons Learned

- Always verify startup entries
- Correlate process + network + logs
- Use multiple tools for validation
