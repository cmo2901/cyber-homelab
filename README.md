# Cybersecurity Homelab & End-to-end Cyberattack

## Overview
<p>The aim of this homelab is to emulate an enterprise network. It consists of a centralized Active Directory domain controller, Windows and Linux clients, an SMTP server running Mailhog, and a security server utilizing Wazuh for logging and alerts.</p>
<p> Following the configuration of the virtual network and all necessary virtual machines, I used a Kali Linux machine to simulate a cyberattack, going through the stages of Reconaissance, Initial Access, Lateral Movement and Privilege Escalation, and Data Exfiltration and Persistence.</p>

## Tools Used
<p> Active Directory</p>
<p> Wazuh</p>
<p> Mailhog</p>
<p> Linux (Ubuntu, Kali)</p>
<p> Hydra, NMap, Secure Copy</p>

## Reconaissance and Initial Access
</p> To start off my cyberattack, I needed to gather basic information about systems and services on the target network. I achieved this via an Nmap scan of one of the servers on the network, and found it to be running SMTP as well as SSH over port 22.





