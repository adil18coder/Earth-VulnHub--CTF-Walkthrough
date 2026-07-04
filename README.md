# Earth-VulnHub--CTF-Walkthrough

This repository contains a structured, step-by-step walkthrough for solving the **Earth** CTF machine. The writeup is broken down into modular files to make it easier to follow each phase of the penetration testing methodology.

##  Machine Overview
* **Name:** Earth
* **Difficulty:** Easy / Medium
* **OS:** Linux
* **Objective:** Gain initial access, escalate privileges to `root`, and capture both flags.
* 

##  Walkthrough Chapters

To view the detailed steps for each phase, please navigate to the respective files below:

1.  **[Phase 1: Reconnaissance & Initial Access](./1-Reconnaissance.md)** *Port scanning, web application discovery, and bypassing input filters using Decimal IP encoding to catch a reverse shell.*

2.  **[Phase 2: Privilege Escalation Analysis](./2-PrivEsc-Analysis.md)** *Identifying suspicious SUID binaries, exfiltrating the file to Kali Linux, and performing dynamic analysis using `strace`.*

3.  **[Phase 3: Exploitation & Root Capture](./3-Exploitation.md)** *Manipulating the application using hidden trigger files, resetting the root password, and retrieving the flags.*

##  Key Skills Learned
* Web application filter evasion (Alternative IP representations).
* Binary exfiltration over the network using Netcat (`/dev/tcp`).
* Dynamic analysis and system call monitoring with `strace`.
* Identifying logic flaws in SUID binaries (`F_OK` file presence checks).
