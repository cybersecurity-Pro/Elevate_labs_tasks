# Network Reconnaissance Report

## Introduction

The purpose of this task is to perform **basic network reconnaissance** using Nmap, a popular network scanning tool.  
Network reconnaissance is a key skill in cybersecurity that allows security professionals to identify **active devices, open ports, and running services** on a network. Understanding which ports are exposed helps in evaluating potential **security risks**.

In this report, we focus on scanning a **home/local network** to identify open ports and the services running on them. The task demonstrates the use of a **TCP SYN scan (-sS)** and optional packet analysis with **Wireshark**.

---

## Steps Performed / Methodology (Nmap Scan)

1. **Install Nmap**  
   - On **Kali Linux**, Nmap comes pre-installed.  
   - For my case, I am using **Windows**, so I downloaded it from the official website: [https://nmap.org/download.html](https://nmap.org/download.html).  

2. **Check your IP address**  
   - On Windows, you can find your IP by opening **Command Prompt** and running:  
     ```
     ipconfig
     ```  
   - This provides your **IPv4 address**, which is needed to specify the target for scanning.  

3. **Run TCP SYN Scan**  
   - I used the **Nmap GUI** version on Windows. For Linux, Nmap CLI can be used.  
   - In the target field, I entered **my own IP address**, since scanning someone else’s device without permission is illegal.  
   - The TCP SYN scan (`-sS`) identifies **open ports** on the device in a stealthy manner.
     ```
     nmap -sS <MY_IP>
     ```

4. **Capture Results**  
   - Nmap displayed the **IP address and open ports**, which I noted down for analysis.  
   - A screenshot of the scan output is presented below

**Screenshot:**

![Namp_scan](./Screenshots/Nmap_scan.png)
  
---


## Results & Analysis

The TCP SYN scan was performed on my own device (IP: `MY_IP`). The following open ports were detected:

| Port  | State | Service       |
|-------|-------|---------------|
| 135   | open  | msrpc         |
| 139   | open  | netbios-ssn   |
| 445   | open  | microsoft-ds  |
| 5357  | open  | wsdapi        |
| 6646  | open  | mcafee        |
| 7070  | open  | realserver    |


**Observations:**
- Ports **135, 139, 445** are related to **Windows file sharing and remote procedure calls**, which are commonly open on Windows devices.  
- Ports **5357, 7070, 6646** are associated with **network services like WSD, RealServer, and McAfee**.  
- Open ports can pose a **security risk** if services are misconfigured or vulnerable to attacks. Proper firewall configuration and service management help mitigate these risks.  


---

# TCP SYN scan on Local port ranges

## 1️⃣ How I Ran the Scan

### Command used:
```
nmap -sS 192.168.0.0/24
```

### Explanation:

1) -sS → TCP SYN scan (half-open scan). It’s faster and stealthier than a full TCP connect scan.

2) 192.168.0.0/24 → scans all IPs in my local network (192.168.0.1 to 192.168.0.254).

### Purpose: Identify active devices and their open TCP ports to check for potential security risks.

## 2️⃣ Scan Results

| IP Address    | Open Ports (Service)                                                      | Notes                                     |
| ------------- | ------------------------------------------------------------------------- | ----------------------------------------- |
| 192.168.0.1   | 22/tcp (ssh) <br> 53/tcp (domain) <br> 80/tcp (http) <br> 1900/tcp (upnp) | Likely router/WAP device.                 |
| 192.168.0.101 | No open ports detected                                                    | Device is up but no exposed TCP services. |


### Observation: 

Only 2 hosts were active in the scanned 256 IP range.

## 3️⃣ Potential Security Risks

1) 192.168.0.1 (Router/WAP)

   - SSH (22/tcp): Could allow unauthorized remote access if weak/default credentials are used.

   - HTTP (80/tcp): Web interface exposed → risk if firmware is outdated or weak password is used.

   - UPnP (1900/tcp): Can be exploited by malware to open ports automatically.

   - DNS (53/tcp): Usually low risk internally, but misconfigured DNS could cause problems.

2) 192.168.0.101

   - All ports closed → minimal exposure, low risk.

**Screenshot:**

![ss](./Screenshots/ss.png)

## 4️⃣ Conclusion

1) The network has 2 active devices.

2) Router/WAP has several open ports that could be exploited if not secured properly.

3) Recommendations:

   - Update router firmware.

   - Use strong, unique passwords.

   - Disable unnecessary services like UPnP if not required.


---


# Wireshark Analysis (Optional)

For this part, I used Wireshark to capture and analyze the network traffic while running an Nmap scan.

## Installation
Wireshark can be downloaded from its official website: https://www.wireshark.org/download.html
.
After downloading, I installed it on my Windows system.

## Selecting Capture Interface
Once Wireshark starts, you are presented with several options for capturing network traffic. Since I was running on a laptop connected to Wi-Fi, I selected the Wi-Fi interface (as shown in the screenshot).

**Screenshot:**

![wifi](./Screenshots/wifi.png)

## Capturing Traffic
With Wireshark running, I started capturing traffic while executing an Nmap SYN scan on my own device (nmap -sS <my_ip>).
The capture displayed multiple TCP, TLS, and DNS packets, including the SYN packets generated by the scan.

**Screenshot:**

![raw capture](./Screenshots/Raw_pcap.png)

## Filtering Packets
To make the scan traffic easier to analyze, Wireshark supports display filters. For example, filtering for SYN packets from my device can be done using:
```
tcp.flags.syn == 1 && ip.addr == <MY_IP>
```

This allows us to clearly see the scan attempts sent to different ports.

**Screenshot:**

![Filter](./Screenshots/Filter_1.png)

## Observations

   - Normal traffic (TLS, DNS) was mixed with Nmap packets in raw capture.

   - Applying filters made it clear that Nmap was systematically sending SYN requests to multiple ports.

   - This aligns with how a SYN scan works: sending SYN packets without completing the full TCP handshake.

---

# Conclusion:
Wireshark and Nmap are useful tools for network reconnaissance. Nmap helps identify active devices and open ports, while Wireshark allows monitoring and analyzing network traffic. Together, they provide insights into the network’s structure and potential security risks.


