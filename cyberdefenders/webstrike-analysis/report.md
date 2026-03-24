# Web Traffic Analysis – Identifying Attack Source (CyberDefenders)

**Role:** SOC Analyst (Simulated Investigation)

---

## Objective
The objective of this investigation was to analyze network traffic from a PCAP file and identify the source of suspicious activity, including determining the geographical origin of the attack.

---

## Scenario Summary
A packet capture file was provided for analysis. The goal was to examine the traffic, identify any suspicious communication, and determine where the attack originated from.

---

## Tools Used
- Wireshark  
- CyberDefenders Platform  
- IP Geolocation (ipinfo.io)

---

## Investigation Process

### 1. Initial Traffic Review
The PCAP file was loaded into Wireshark to begin analysis of network activity.

---

### 2. Identifying Communicating Hosts
Using **Statistics → Conversations → IPv4**, I identified two primary IP addresses involved in the communication:

- 117.11.88.124  
- 24.49.63.79  

From the packet statistics, both IPs showed active communication, with 117.11.88.124 sending slightly more packets.

---

### 3. Analyzing HTTP Traffic
To focus on web activity, I applied the following filter:

This revealed multiple HTTP GET requests originating from **117.11.88.124** to **24.49.63.79**.

The requests included paths such as:
- `/`
- `/products`
- `/reviews`
- `/admin`
- `/uploads`

This pattern suggests possible **directory enumeration or probing activity**, which is commonly associated with reconnaissance attempts.

---

### 4. Identifying the Attacker
Since HTTP requests are initiated by the client, the source IP of the GET requests (**117.11.88.124**) was identified as the likely attacker.

---

### 5. Geolocation Analysis
The IP address **117.11.88.124** was analyzed using an external IP geolocation service.

The results showed that the traffic originated from:

- **City:** Tianjin  
- **Country:** China

### 6. Identifying the User-Agent
To further investigate the activity, HTTP request headers were analyzed in Wireshark.

A POST request to `/reviews/upload.php` was examined, which indicated a file upload attempt. By expanding the Hypertext Transfer Protocol section, the User-Agent string used by the attacker was identified.

The User-Agent was:

`Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0`

This suggests the attacker was using a Linux-based system with Firefox, which can help in detecting and filtering similar malicious activity. 

---

## Findings

- **Attacker IP Address:** 117.11.88.124  
- **Victim IP Address:** 24.49.63.79  
- **Protocol:** HTTP  
- **Observed Activity:** Multiple HTTP GET requests  
- **Attack Behavior:** Web probing / directory enumeration  
- **Geographical Origin:** Tianjin, China
 -**User-Agent:** Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0

---

## Indicators of Compromise (IOCs)

- 117.11.88.124 (Suspicious external IP)

---

## Conclusion
Based on the analysis of network traffic and HTTP request patterns, the attack was determined to originate from the IP address 117.11.88.124. Geolocation of this IP indicates that the source of the activity was Tianjin, China. The observed behavior suggests reconnaissance activity targeting web application endpoints.

---

## Recommendations
- Block the identified IP address at the firewall level  
- Monitor for similar HTTP probing behavior  
- Restrict access to sensitive endpoints such as `/admin`  
- Implement web application security controls (e.g., WAF)  

---

## Evidence

### Figure 1: IPv4 Conversations
![Conversations](evidence/conversations.png)

### Figure 2: HTTP GET Requests
![HTTP Traffic](evidence/http-get.png)

### Figure 3: User-Agent Identification
![User Agent](evidence/user-agent.png)

