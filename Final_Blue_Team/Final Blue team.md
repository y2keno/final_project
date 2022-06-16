Joaquin Serrano

# Blue Team: Summary of Operations

### Table of Contents:
   * Network Topology
   * Description of Targets
   * Monitoring the Targets
   * Patterns of Traffic & Behavior
   * Suggestions for Going Further

### Network Topology:
The following machines were identified on the network:
* Target 1
   * Operating System: Debian GNU/Linux
   * Purpose: The WordPress Host
   * IP Address: 192.168.1.110

* Capstone
   * Operating System: Ubuntu 18.04
   * Purpose: Vulnerable Web Server
   * IP Address: 192.168.1.105

* Kali 
   * Operating System: Debian Kali 5.4.0
   * Purpose: The Penetration Tester 
   * IP Address: 192.168.1.90

* ELK   
   * Operating System: Ubuntu 18.04
   * Purpose: The ELK (Elasticsearch & Kibana) Stack
   * IP Address: 192.168.1.100

### Network Diagram:
![](https://github.com/y2keno/final_project/blob/e7e77e1930f42eb569709dcb59afd2bdd7f4ff9a/Final_Blue_Team/images/image2.png)
  
### Description of Targets:
- The target of this attack was: Target 1: 192.168.1.110.
- Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers.

### Monitoring the Targets

This scan identifies the services below as potential points of entry: 
- Target 1:
   * Port 22/TCP open ssh OpenSSH 6.7p1 Debian 5+deb8u4
   * Port 80/TCP open http OpenHTTP Apache httpd 2.4.10. ((Debian ))
   ![](https://github.com/y2keno/final_project/blob/e7e77e1930f42eb569709dcb59afd2bdd7f4ff9a/Final_Blue_Team/images/image1.png)
  

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

### Excessive HTTP Errors
Excessive HTTP Errors are implemented as follows:
- WHEN count () GROUPED OVER top 5 ‘http.response.status_code’ IS ABOVE 400 FOR THE LAST 5 minutes

Alert 1 is implemented as follows:
* Metric: 
   * WHEN count() GROUPED OVER top 5 ‘http.response.status_code’
* Threshold:
   * IS ABOVE 400
* Vulnerability Mitigated:
   * Enumeration/Brute Force
* Reliability: 
   * The reliability of the alert is very high. 
   * Measuring by error codes: 400 and above will filter out any normal or successful responses. 400+ codes are client and server errors which are of more
   ![](https://github.com/y2keno/final_project/blob/e7e77e1930f42eb569709dcb59afd2bdd7f4ff9a/Final_Blue_Team/images/image4.png)

### HTTP Request Size Monitor
HTTP Request Size Monitor is implemented as follows:
- WHEN sum() of http.request.bytes OVER all documents IS ABOVE 3500 FOR THE LAST 1 minute

Alert 2 is implemented as follows:
   * Metric: 
   * WHEN sum() of HTTP.request.bytes OVER all documents
   * Threshold: 
   * IS ABOVE 3500
   * Vulnerability Mitigated: 
   * Code injection in HTTP requests (XSS and CRLF) or DDOS
   * Reliability: 
   * The reliability of the alert is Medium. 
   * This alert can create false positives due to the possibility of a large non-malicious HTTP request or legitimate HTTP traffic. 
  
### CPU Usage Monitor

CPU Usage Monitor is implemented as follows: 
- WHEN max() OF system.process.cpu.total.pct OVER all documents IS ABOVE 0.5 FOR THE LAST 5 minutes

Alert 3 is implemented as follows:
   * Metric: 
   * WHEN max () OF system.process.cpu.total.pct OVER all documents
   * Threshold: 
   * IS ABOVE 0.5
   * Vulnerability Mitigated: 
   * Malicious Software, active programs (malware or viruses) running taking up resources. 
   * Reliability: 
   * The reliability of the alert if very high.
   * This can still help determine where to improve on CPU usage, even if there isn’t an active malicious program. 
  ![](https://github.com/y2keno/final_project/blob/e7e77e1930f42eb569709dcb59afd2bdd7f4ff9a/Final_Blue_Team/images/image5.png)

### Suggestions for Going Further 

Each alert above pertains to a specific vulnerability/exploit. Recall that alerts only detect malicious behavior, but do not stop it. For each vulnerability/exploit identified by the alerts above, suggest a patch. E.g., implementing a blocklist is an effective tactic against brute-force attacks. It is not necessary to explain how to implement each patch.
The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:
* Excessive HTTP Errors
   * Patch: WordPress Hardening
      * Install security plugin(s)
      * Block requests to /?author= by configuring web server settings
      * Remove WordPress logins from being publicly accessible specifically:
         * /wp-admin
         * /wp-login.php
      * Disable unused WordPress features and settings like:
         * WordPress XML-RPC 
         * WordPress REST API 
      * Implementation of regular updates to WordPress
         * PHP version
         * WordPress Core
         * Plugins.
   * Why It Works:
      * The WordPress security plug provides: 
      * WordPress links (permalinks) can include authors (users)
      * Removal of public access to WordPress login helps reduce the attack surface
      * XML-RPC uses HTTP as its method of data transport
      * Regular updates to WordPress, the PHP version, and plugins are an easy way to implement patches or fixes to vulnerabilities
* CPU Usage Monitor
   * Patch: Virus or Malware Hardening
      * Implement good antivirus software. 
      * Implement and configure Host-Based Intrusion Detection Systems (HIDS)
   * Why it works:
      * The removal, detection, and overall prevention of vulnerable threats to computers can be done by Anti-Virus software.
         * Modern antivirus covers more than viruses and is a good solution for protecting a computer.  
      * HIDS monitors and analyzes the internal computer system. 
         * They also monitor and analyze network packets.
* HTTP Request Size Monitor
   * Patch: Code Injection/DDOS Hardening
      * Implementation of Input validation on forms
      * Implementation of HTTP Request Limit on the webserver
      * Limits can include:
         * Max size of a request
         * Max URL Length
         * Max length of a query string
   * Why It Works: 
      * Input Validation can protect against malicious data attempts to the server via the website or application in/across an HTTP request.
      * If an HTTP requests URL length; with a query string over the size limit of the request, a 404 range of error will occur.
         * This will reject requests that are too large.