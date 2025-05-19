# Cybersecurity Homelab & End-to-end Cyberattack

## Overview
<p>The aim of this homelab is to emulate an enterprise network. It consists of a centralized Active Directory domain controller, Windows and Linux clients, an SMTP server running Mailhog, and a security server utilizing Wazuh for logging and alerts. Login credentials and other configurations were intentionally insecure for the purpose of simulating a cyberattack.</p>
<p> Following the configuration of the virtual network and all necessary virtual machines, I used a Kali Linux machine to simulate a cyberattack, going through the stages of Reconaissance, Initial Access, Lateral Movement and Privilege Escalation, and Data Exfiltration and Persistence.</p>

## Tools Used
<p> Virtualbox</p>
<p> Active Directory</p>
<p> Wazuh</p>
<p> Mailhog</p>
<p> Linux (Ubuntu, Kali)</p>
<p> Hydra, NMap, Secure Copy, netexec, Evil-WinRM</p>

## Reconaissance and Initial Access
<p> To start off my cyberattack, I needed to gather basic information about systems and services on the target network. I achieved this via an Nmap scan of one of the servers on the network, and found it to be running SMTP as well as SSH over port 22.</p>


![nmap scan](https://github.com/user-attachments/assets/64d6ac25-45c6-4744-bb66-7a8e6ab0e127)
<p>Because SSH was open, I knew this could be a potential point of entry onto this server. After an initial connection attempt I found this server to be protected by a password. Kali Linux comes with the 'rockyou.txt' wordlist by default, so I utilized this in a brute force attack. </p>


![hydra bruteforce with rockyou ](https://github.com/user-attachments/assets/e675602b-df38-442b-a7db-22e1ae84be0d)
<p> With these credentials I was able to successfully connect to this server from my Kali machine over SSH.</p>
<p> Following my successful connection, I downloaded net-tools and ran netstat to see what ports were active. I noticed 1025 and 8025 were being utilized by this server; a Google query led me to believe this server was running Mailhog. With this knowledge in mind, I went to hxxp[://]localhost:8025 and gained access to the Mailhog interface.</p>

![sign in to mailhog port 8025](https://github.com/user-attachments/assets/96679bc6-4350-45fe-978e-1597587110cd)

<p> With access to an email server, I planned to target a user on the enterprise network with a phishing email to compromise their credentials. Using an HTML file to create a fake website and a PHP file that would log any credentials entered into the website to a log file, I formatted an email to a client on the network:</p>

![phishing_email](https://github.com/user-attachments/assets/b490fddf-5658-46f7-b32f-eb531f51af69)

<p>Phished credentials:</p>

![phished_corp_credentials](https://github.com/user-attachments/assets/e6b41e47-5f20-400c-a220-f022c4b933fe)

<p> With these compromised credentials I achieved initial access to a corporate machine:</p>

![initial_access_corp_machine](https://github.com/user-attachments/assets/d4e0d33a-e39b-4905-a01f-a5c1ef91a3f2)


## Lateral Movement and Privilege Escalation
<p>With access to a corporate machine, I needed to figure out how to move throughout the network and gain elevated privileges. I discovered through an Nmap scan of the 10.0.2.100 client that ports 5985 and 5986 (WinRM) were open:</p>

![nmap_scan_for_open_winrm](https://github.com/user-attachments/assets/1b646959-a928-4137-9470-04346880ed39)

<p>To see if I could leverage WinRM being open, I used netexec to test credentials against the 10.0.2.100 client:</p>

![testing_creds_win_machine_with_netexec](https://github.com/user-attachments/assets/4a6c2852-9bac-41ce-b9e3-332493394077)

<p>Since these credentials appeared to work, I utilized Evil-WinRM to access 10.0.2.100:</p>

![accessing_win_client_evil-winrm](https://github.com/user-attachments/assets/aa409c17-6905-48b4-b2d9-8d3e2cf1cca8)

<p>From here, I gathered information about this machine's domain controller as well as ran an Nmap scan of the domain controller's IP to see what services were open:</p>

![gathering_dc_information](https://github.com/user-attachments/assets/de67b015-53a1-416f-879c-5fccdbbc7d3c)


![nmap_scan_dc_open_rdp](https://github.com/user-attachments/assets/7f8a82cc-bc2e-4101-a2fc-05f8d3dce952)

<p>With access to administrator credentials and knowledge of the domain controller's address and open RDP, I attempted an RDP connection from my Kali machine:</p>


![domain_controller_compromised](https://github.com/user-attachments/assets/f4365b04-0120-4ca2-8cdb-814eb859c66f)

## Data Exfiltration and Persistence
<p>With access to the domain controller, I hoped to find sensitive files I could exfiltrate as well as implement methods of persistent access. I started by using Secure Copy to move a sensitive production file to my Kali machine: </p>

![exfil_prod_file_with_securecopy](https://github.com/user-attachments/assets/b9b82741-78f4-44d3-8ba9-c7ea44c2c47e)

<p>With the file moved to my Kali machine, I began implementing persistence mechanisms, starting with a new user account that I added to the Local Administrators and Domain Administrators groups: </p>

![living_off_the_land](https://github.com/user-attachments/assets/894de93c-22f6-44b8-bac2-3d1cd5cfd3f8)

<p>Following this, I connected to hxxp[://]10.0.2.50:8000 and downloaded a reverse shell script:</p>

![downloading_reverse_shell](https://github.com/user-attachments/assets/03f89ff3-b3ec-458c-969c-483fdf0d01b3)

<p>I then created a scheduled task that would run this script. This script listens on port 4444, so I set my Kali machine to listen on that port. </p>


![scheduled_task_for_persistence](https://github.com/user-attachments/assets/47446eda-24ed-4458-9f5a-7b21dd2b2560)


![scheduled_task_running](https://github.com/user-attachments/assets/9741a1b6-14e5-4d21-a0e7-897a22021e30)

<p>To ensure this scheduled task wouldn't be blocked in the future, I bypassed existing execution policies: </p>


![bypassing_execution_policy](https://github.com/user-attachments/assets/374c3167-3ff7-49dd-8b5f-8282bd4d5447)

## Generated Alerts
<p>From the defensive security point of view, these are the alerts generated by my simulated cyberattack:</p>

![triggered_alerts](https://github.com/user-attachments/assets/7690fea7-4701-4c73-bfb4-78e894d8a84f)





