# Network Security Suite: Automated Firewall & Signature-Based IDS

A lightweight, Python-based network security solution utilizing the **Scapy** library. This suite consists of a real-time **Automated Firewall/IDS** that monitors network traffic, manages IP access controls, and mitigates specific malware signatures, alongside a **Packet Injection Tool** designed to safely simulate signature-based network attacks for validation.

---

##  Features

### 1. Automated Firewall & IDS (`firewall.py`)
* **Real-time Traffic Sniffing:** Captures and analyzes live IP packets across network interfaces.
* **Signature-Based Detection:** Inspects packet payloads for specific threat vectors, specifically targeting the legacy *Nimda worm* web signature (`GET /scripts/root.exe`).
* **Rate Limiting / DDoS Mitigation:** Dynamically tracks traffic volume per source IP address and automatically blocks entities exceeding a threshold of 40 packets per second.
* **Access Control Lists (ACLs):** Integrates file-based, pre-configured whitelists and blacklists to bypass or instantly block designated traffic.
* **Linux iptables Integration:** Directly interacts with the Linux kernel firewall to dynamically append `DROP` rules for malicious IPs.
* **Automated Logging:** Permanently records security events and blocking triggers inside timestamped text files.

### 2. Attack Simulator (`packet_flooder.py`)
* **Custom Packet Crafting:** Compiles raw layer-3 (IP) and layer-4 (TCP) packets.
* **Payload Injection:** Mounts a mock malicious HTTP payload explicitly carrying the Nimda signature to safely test IDS detection thresholds.

---

## Tech Stack & Dependencies

* **Language:** Python 3.x
* **Core Library:** Scapy (Network packet manipulation tool)
* **System Utilities:** Linux `iptables`, `os`, `sys`, `time`

---

## Prerequisites & Installation

> **System Note:** Because this application manipulates system network configurations and listens to low-level socket traffic, it **must be run on a Linux-based environment** (or VM) with administrative root privileges.

1. **Clone or download** this repository to your target machine.
2. Ensure Python 3 is installed.
3. Install the **Scapy** framework via pip:
   ```bash
   pip install scapy
   ```

---

## Configuration

Before launching the security suite, create two text configuration files in the root directory of the application:

* **`whitelist.txt`**: Add safe IP addresses (one per line) that should never be blocked by the rate limiter.
* **`blacklist.txt`**: Add known malicious IP addresses (one per line) that should be dropped instantly on sight.

*Example formatting:*
```text
192.168.1.10
10.0.0.5
```

---

##  Usage Instructions

### 1. Initializing the Firewall
Run the script utilizing `sudo` to accommodate network sniffing and `iptables` modifications:

```bash
sudo python3 firewall.py
```
*The script will validate root permissions, parse your IP access lists, and begin actively monitoring network traffic.*

### 2. Simulating an Attack (Testing)
To verify that the signature-based IDS functions correctly:
1. Open `packet_flooder.py` and replace `"192.168.xxx.xxx"` with the IP address of your monitoring machine.
2. Run the deployment test script:
   ```bash
   sudo python3 packet_flooder.py
   ```
3. Observe `firewall.py` automatically dropping the packet, generating an `iptables` defense rule, and writing the activity to the `/logs` directory.

---

##  Repository Structure

```text
├── firewall.py          # Main security daemon (sniffing, rate-limiting, ACLs)
├── packet_flooder.py    # Testing tool for payload injection and signature validation
├── whitelist.txt        # Pre-configured authorized IP file
├── blacklist.txt        # Pre-configured denied IP file
├── logs/                # Automatically generated directory housing security event text logs
└── README.md            # Project overview and instruction manual
```

---

##  Disclaimer
This software is developed strictly for **educational and threat-validation purposes** as part of an academic internship project. Running attack simulation tools against unauthorized external networks is illegal. The author holds no liability for unintended network interruptions caused by local script executions.
