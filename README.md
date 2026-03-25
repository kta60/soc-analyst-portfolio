# WebStrike Investigation – Web Shell Upload & Data Exfiltration Analysis
This project documents a full network traffic investigation using Wireshark, where a web-based attack was analyzed from initial reconnaissance to web shell deployment and data exfiltration.
## Attack Overview

This investigation revealed a multi-stage web-based attack:

1. Reconnaissance via HTTP GET requests (directory probing)
2. Malicious file upload using a disguised PHP web shell
3. Execution of the uploaded web shell from the server
4. Establishment of a reverse shell connection to the attacker
5. Attempted data exfiltration of sensitive system files
   ## Key Findings

- **Attacker IP:** 117.11.88.124  
- **Victim IP:** 24.49.63.79  
- **Protocol:** HTTP  
- **Attack Type:** Web exploitation (file upload + web shell)  
- **Malicious File:** image.jpg.php  
- **Upload Directory:** /reviews/uploads/  
- **Reverse Shell Port:** 8080  
- **Exfiltrated File Target:** passwd
  ## Tools Used

- Wireshark (network traffic analysis)  
- CyberDefenders platform  
- IP geolocation (ipinfo.io)
