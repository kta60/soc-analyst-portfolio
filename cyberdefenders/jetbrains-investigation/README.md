# JetBrains TeamCity Compromise – Network Traffic Investigation

## Project Overview

This project documents a hands-on network forensic investigation of a compromised JetBrains TeamCity server. Using Wireshark to analyze captured network traffic, I traced the attack from initial exploitation through account creation, command execution, credential tampering, and post-exploitation activity.

## Investigation Environment

- **Platform:** CyberDefenders
- **Challenge:** JetBrains
- **Primary Tool:** Wireshark
- **Evidence Source:** PCAP network capture
- **Investigation Type:** Network forensics / incident investigation
- **Target Application:** JetBrains TeamCity

 ## Attack Summary

The investigation identified malicious activity targeting a JetBrains TeamCity server. Analysis of the PCAP revealed that the attacker exploited a TeamCity vulnerability, created an unauthorized administrative account, deployed a malicious JSP plugin, executed commands on the compromised server, modified credential data, and performed additional post-exploitation activity.

## Key Findings

- **Attacker IP Address:** 23.158.56.196
- **TeamCity Version:** 2023.11.3
- **Exploited Vulnerability:** CVE-2024-27198
- **Unauthorized Account:** c91oyemw
- **Malicious JSP Plugin:** NSt8bHTg.jsp
- **Modified Credentials:** a1l4m:youarecompromised
- **MITRE ATT&CK Technique:** T1565.001 – Stored Data Manipulation

 ## Investigation Methodology

 The investigation was conducted using Wireshark to analyze the supplied PCAP file. Display filters were used to isolate suspicious HTTP traffic, identify the attacker, examine malicious requests, and reconstruct the sequence of actions performed against the TeamCity server.

 ## Investigation Findings

 ### 1. Identification of the Attacker

 Analysis of the network traffic identified 23.158.56.196 as the external IP address responsible for the malicious activity against the TeamCity server.

 **Wireshark Filter Used:**

`ip.src == 23.158.56.196`

This filter isolated traffic originating from the attacker IP and allowed the subsequent HTTP requests and malicious activity to be examined separately from normal network traffic.

### 2. TeamCity Version Identification

Analysis of the HTTP traffic revealed that the compromised server was running JetBrains TeamCity version 2023.11.3.

**Evidence:**

Inspection of the HTTP responses in Wireshark exposed TeamCity application information that identified the server version as 2023.11.3. Determining the exact version was important because it allowed the observed attack activity to be correlated with known TeamCity vulnerabilities affecting that release.

### 3. Exploitation of CVE-2024-27198

The attacker exploited CVE-2024-27198, an authentication bypass vulnerability affecting JetBrains TeamCity, to gain unauthorized access to the server.

**Evidence and Analysis:**

The captured HTTP traffic showed unauthorized requests to TeamCity endpoints that were consistent with exploitation of the authentication bypass vulnerability. This activity allowed the attacker to interact with protected TeamCity functionality without legitimate authentication.

### 4. Unauthorized Account Creation

Following exploitation of the TeamCity server, the attacker created an unauthorized user account named `c9loyenw`, providing a mechanism for continued access to the compromised environment.

**Evidence and Analysis:**

Analysis of the captured HTTP requests revealed activity associated with the creation of the unauthorized TeamCity account `c9loyenw`. The account creation occurred after the authentication bypass activity, indicating that the attacker used the compromised access to establish an additional account on the server.

### 5. Malicious JSP Plugin

Further analysis identified a malicious JSP file named `NSt8bHTg.jsp` within the TeamCity plugins directory. The attacker used this file to send commands to the compromised server through HTTP POST requests.

**Evidence and Analysis:**

Wireshark revealed repeated HTTP POST requests from the attacker to `/plugins/NSt8bHTg/NSt8bHTg.jsp`. Inspection of the form data showed commands being passed through the `cmd` parameter, demonstrating that the JSP file was being used to execute commands on the compromised server.

**Wireshark Filter Used:**

http.request.method == "POST" && http.request.uri contains "NSt8bHTg.jsp"

### 6. Command Execution Through the Malicious JSP

Inspection of the HTTP POST data showed that the attacker used the `cmd` parameter of `NSt8bHTg.jsp` to execute operating system commands on the compromised TeamCity server.

**Observed Command:**

docker run --rm -it -v /:/host ubuntu chroot /host

**Analysis:**

This command mounted the host filesystem inside a Docker container and used `chroot` to access it, giving the attacker a way to interact with the underlying host environment rather than remaining isolated inside the container.

### 7. Credential File Tampering

The attacker used the JSP webshell to access `/tmp/Creds.txt` and later overwrite the file with new credentials.

**Malicious Command Observed:**

bash -c 'echo "username:a1l4m,password:youarecompromised" > /tmp/Creds.txt'

**MITRE ATT&CK Mapping:**

- **T1565.001 – Stored Data Manipulation:** The attacker deliberately modified credentials stored in `/tmp/Creds.txt`.

  The use of the `>` redirection operator overwrote the existing contents of the credentials file, demonstrating intentional manipulation of data stored on the compromised system.

  ## Attack Timeline

  1. The attacker targeted the vulnerable TeamCity server from `23.158.56.196`.
2. CVE-2024-27198 was exploited to bypass authentication.
3. An unauthorized TeamCity account, `c9loyenw`, was created.
4. The malicious JSP file `NSt8bHTg.jsp` was used to execute commands.
5. The attacker attempted host-level access using Docker and `chroot`.
6. Credentials in `/tmp/Creds.txt` were overwritten with attacker-controlled values.

   ## Investigation Evidence

   The following screenshots document key stages of the Wireshark investigation and provide supporting evidence for the findings described above.

   ### Evidence 1 – Attacker Traffic Identification

   Wireshark traffic analysis showing malicious HTTP activity originating from the attacker IP address `23.158.56.196`.

   ### Evidence 2 – Malicious JSP Command Execution

   Wireshark analysis showing HTTP POST requests to `/plugins/NSt8bHTg/NSt8bHTg.jsp`, where attacker-controlled commands were submitted through the `cmd` parameter.

   ### Evidence 3 – Credential File Tampering

   Wireshark packet analysis revealed the attacker executing a command that overwrote `/tmp/Creds.txt` with the credentials `username:a1l4m,password:youarecompromised`.

   ### Evidence 4 – Attempted Container Escape

   Wireshark revealed the attacker attempting to access the underlying host filesystem using the command `docker run --rm -it -v /:/host ubuntu chroot /host`.

   ## Skills Demonstrated

   - Network traffic analysis with Wireshark
- PCAP investigation and packet inspection
- HTTP request and response analysis
- Webshell activity identification
- Indicator of Compromise (IOC) extraction
- Post-exploitation activity analysis
- MITRE ATT&CK mapping

- ## Lessons Learned

- This investigation strengthened my ability to reconstruct an attack from network traffic rather than relying on individual alerts. I gained practical experience using Wireshark filters, inspecting HTTP traffic, identifying webshell command execution, correlating exploitation with a known CVE, and mapping observed attacker behavior to the MITRE ATT&CK framework.
