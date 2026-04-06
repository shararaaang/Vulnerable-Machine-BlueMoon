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
