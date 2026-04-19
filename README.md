# SIEM-Splunk: Live-Phishing-Attack &nbsp;&nbsp; <img width="100" height="98" alt="629b450a95f79dc9fa7256f1" src="https://github.com/user-attachments/assets/ecb9b323-1aec-4023-9835-7e21bf87384f" />

Used Splunk SIEM to monitor and investigate a live phishing attack in real time across a corporate network. Identified key indicators of compromise, including suspicious DNS activity, PowerShell execution, and reverse shell connections, and compiled these findings into detailed reports to map the full scope of the breach.

**Incident Report**

On April 19, 2026, host win-3450 was compromised through a phishing-based initial access, leading to full attacker control via a PowerShell reverse shell. The attacker conducted reconnaissance, accessed sensitive network resources, staged data locally, and exfiltrated data using DNS tunneling. Evidence from SIEM alerts and Splunk logs confirms a complete attack chain involving command execution, lateral access, data collection, and exfiltration. This incident represents a confirmed compromise with data exfiltration, including sensitive financial documents and cryptocurrency-related credentials.

**Attack Timeline & Findings**

On 04/19/2026 16:43:04.563, the first true positive alert came in (Figure 1).A suspicious email attachment (Important: Pending Invoice!.eml) was opened via Outlook. This was found to be a malicious file (Figure 2). Shortly after, PowerShell was executed and used to download and run a remote script: **powercat.ps1** from GitHub. A reverse shell was established to: **2.tcp.ngrok.io:19282** (Figure 3).


<img width="1529" height="524" alt="1005" src="https://github.com/user-attachments/assets/e4da3d41-e827-426a-959b-17075a5860dd" />

*Figure 1: Suspicious Attachment found in email*

<img width="871" height="744" alt="1005b" src="https://github.com/user-attachments/assets/be49526a-90c7-4606-9cd8-b2d55a2f006d" />

*Figure 2: Malcicious attachment - invioce.pdf.lnk*

<img width="1618" height="426" alt="image" src="https://github.com/user-attachments/assets/f8c809a1-cd71-49e0-843a-b02056c5a5ad" />

*Figure 3: powercat.ps1 from GitHub*



<img width="1892" height="788" alt="dashboard 1" src="https://github.com/user-attachments/assets/5901e671-c362-4f22-bd7e-95f69b47c7de" />

*Figure :*

<img width="1833" height="769" alt="dashboard 2 false positives" src="https://github.com/user-attachments/assets/c8f9f82b-22b7-430c-a872-eb9ceaf3d29a" />

*Figure :*

<img width="1524" height="770" alt="dashboard 3" src="https://github.com/user-attachments/assets/fc534ec3-d57f-4465-96dd-72504255eff4" />

*Figure :*



