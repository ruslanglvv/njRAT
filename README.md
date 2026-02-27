# Malware Analysis Report

## njRAT Variant Analysis

---

## 1. Executive Summary

This report presents a technical analysis of a malware sample belonging to the **njRAT family**, obtained from MalwareBazaar. The analyzed specimen demonstrates typical characteristics of a modular Remote Access Trojan (RAT), including persistence mechanisms, command-and-control (C2) communication, keylogging capabilities, DDoS functionality, plugin support, and self-protection techniques.


---

## 2. Initial Installation and Persistence ("INS" Function)

The function labeled `INS` is responsible for installation and persistence.

---

### 2.1 File Replication and Masquerading

The malware copies itself into a designated directory and attempts to disguise its presence.

📸 **[Screenshot 1 – Self-copy routine in INS function]**
![[Annotation 2026-02-24 174308.png]]
![photo1](/image1.png)



---

### 2.2 Firewall Bypass

The malware executes:

netsh firewall add allowedprogram

This command adds the malware executable to Windows Firewall exceptions, allowing inbound communication required for RAT control.

📸 **[Screenshot 2 – Firewall exception command in code]**
![[Annotation 2026-02-24 174308 1.png]]

---

### 2.3 Registry Persistence

The malware writes itself into:

- HKCU
    
- HKLM
    

It ensures execution at system startup by modifying autorun registry keys. It also periodically verifies and restores these entries if removed.

📸 **[Screenshot 3 – Registry persistence (HKCU & HKLM)]
![[Annotation 2026-02-26 103748.png]]**

---

### 2.4 USB Propagation via Autorun

The sample contains a USB propagation mechanism:

- Iterates over logical drives
    
- Copies itself to removable media
    
- Creates `autorun.inf`
    
- Hides the files
    

📸 **[Screenshot 4 – USB spread routine and autorun.inf creation]**
![[Annotation 2026-02-26 104219.png]]

---

## 3. Command and Control (C2) Communication

### 3.1 RC Function

The `RC` function establishes communication with the remote C2 server.

- Creates TCP connection
    
- Reads packet length
    
- Passes payload to `im` function
    

📸 **[Screenshot 5 – RC function network handling logic]**
![[Annotation 2026-02-26 105143.png]]

---

## 4. Command Execution Engine ("im" Function)

The `im` function processes attacker commands received from C2.

---

### 4.1 System Manipulation Commands

Detected commands include:

- Disable Task Manager
    
- Disable CMD
    
- Disable Registry Editor
    
- Disable System Restore
    
- Block keyboard/mouse
    
- Open/Close CD tray
    
- Beep/piano sounds
    

📸 **[Screenshot 6 – Command parsing inside im function]
![[Annotation 2026-02-25 105704.png]]**

📸 **[Screenshot 7 – Registry sabotage commands]**
![[Annotation 2026-02-26 111343.png]]

---

### 4.2 DDoS Capability (UDP Flood)

The malware implements a UDP flooding mechanism:

- Target IP
    
- Target Port
    
- Delay parameter
    
- Infinite send loop
    
- Stop command (`udpstp`)
    

📸 **[Screenshot 8 – UDP flood loop implementation]**
![[Annotation 2026-02-26 110953.png]]

---

### 4.3 Download and Execute

The malware can download and execute external files.

Observed external resource:

hxxps://dl.dropbox.com/s/p84aaz28t0hepul/Pass.exe?dl=0

Likely credential stealer module.

📸 **[Screenshot 9 – Download & execute logic]**
![[Annotation 2026-02-25 110918.png]]


---

### 4.4 Plugin System

The malware supports dynamic plugin loading:

- Receives binary blob
    
- Decompresses
    
- Stores in registry
    
- Loads via reflection
    
- Executes dynamically
    

📸 **[Screenshot 11 – Plugin loading and reflection execution]**
![[Annotation 2026-02-26 111654.png]]

---

## 5. Keylogger ("WRK" Function)

The `WRK` function implements a polling-based keylogger:

📸 **[Screenshot 12 – GetAsyncKeyState loop]**

![[Annotation 2026-02-26 112058.png]]


---

## 6. Self-Protection and Anti-Analysis

### 6.1 GUI-Based Termination Protection

The malware detects:

- Task Manager
    
- Process Hacker
    
- Process Explorer
    
- ProcessViewer
    

It disables the “End Process” button when termination is attempted.

📸 **[Screenshot 15 – Anti-TaskManager detection logic]**
![[Annotation 2026-02-26 112308.png]]

---


## 7. System Information Exfiltration

The malware collects:

- Username
    
- Machine date
    
- OS details
    
- CPU information
    
- Camera status
    
- Encrypted string “HacKed”
    
- Executable name
    

📸 **[Screenshot 19 – System information gathering routine]**
![[Annotation 2026-02-25 113719.png]]

---

## 8. Indicators of Compromise (IOCs)

### SHA-256

2e4aa0e3181c96b6001135a8f5cdf00a041ad0e0cb6f61385b5942aae3e194bd

### Filename

2659b0ef527aa8381990f87c9daf0d01.exe

### C2 Server

4.tcp.eu.ngrok.io

### External Resource

hxxps://dl.dropbox.com/s/p84aaz28t0hepul/Pass.exe?dl=0


---

## 10. Conclusion

The analyzed sample is a fully functional njRAT variant featuring modular architecture, persistence, network communication, credential theft, and active self-defense.

The presence of:

- Plugin loading
    
- Registry storage
    
- USB spreading
    
- DDoS module
    

indicates a mature RAT implementation intended for long-term compromise and botnet deployment.

---
## 11. Sources

https://bazaar.abuse.ch/sample/2e4aa0e3181c96b6001135a8f5cdf00a041ad0e0cb6f61385b5942aae3e194bd/#file_info
