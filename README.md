# PROJECTNAME
Security Onion

## Objective
The objective of Security Onion is to provide a comprehensive, open-source Linux distribution for network security monitoring (NSM), intrusion detection, and log management. It integrates various security tools, such as Suricata, Zeek, and the Elastic Stack, to help organizations detect, analyze, and respond to cybersecurity threats. Security Onion aims to offer a unified platform for threat hunting, incident response, and security monitoring, making it easier for security teams to identify and mitigate potential risks in real-time.

### Skills Learned
- Network Traffic Analysis: Monitoring and analyzing network traffic using tools like Zeek and Suricata.
- Intrusion Detection: Setting up and configuring intrusion detection systems (IDS) to identify security threats.
- Log Management: Collecting, parsing, and managing logs for effective threat hunting and incident response.
- Security Incident Response: Investigating and responding to security incidents using Security Onion's dashboards and tools.
- Data Correlation: Correlating data from multiple sources (network, host, and logs) for comprehensive threat analysis.
- Threat Hunting: Using Elastic Stack for searching and identifying anomalies or malicious activity.
- Alert Tuning: Customizing and fine-tuning alerts to reduce false positives and enhance detection accuracy.
- Elastic Stack Management: Working with Elasticsearch, Kibana, and Logstash for data visualization and analysis.


## Steps Taken

First, I had to navigate to Security Onion's website to find the documentation to get the ISO file. For this setup, I will be installing it as a standalone ISO. However, you could install it in Amazon Cloud, Azure, etc.. just refer to the documentation. 

![Snag_e6e5227](https://github.com/user-attachments/assets/318e52e3-1e64-497a-b111-b3bb6eb07958)


Next, I went ahead and downloaded the ISO. 

![image](https://github.com/user-attachments/assets/0daf027a-b9ed-400d-9989-c57482c8c680)


Next, I validated the hash that is on the site with what I have downloaded. 










Now, you do need a switch that does support many to one port mirroring (SPAN). I have the Unifi Pro 48 switch in my lab, so I will be using this. If you would like another reccoemndation, I suggest the Dell PowerConnect 7048P (https://hardwarestorm.com/dell-powerconnect-7048p-network-switch.html)

Unifi only has 1-1 SPAN ports on their UI. In order to bypass this, we need to ssh into the switch and make the config on the backend to allow multiple SPAN ports. First, I use PuTTY to remote into the switch. 

![image](https://github.com/user-attachments/assets/d5a75d18-68ab-4579-a8e4-a7cfd6b1b7c4)

Next, I do the command "telnet localhost"

![image](https://github.com/user-attachments/assets/2cd1a71b-aa06-4962-b1df-11f4b665c3eb)

Next, I type "en" then "config"

![image](https://github.com/user-attachments/assets/f86ff2ae-d682-4c57-8444-b6fcad6a5cbf)

Next, I type the command "show monitor session all" 

![image](https://github.com/user-attachments/assets/3989d025-e05b-45ef-a275-fa0d5c0d4e39)

From here, all we need to do is type the command to have the switch know which ports are the source ports and which one is the TAP port. A good reference video I like to credit is https://www.youtube.com/watch?v=VwVyM_wZTps

Since mine is already configured, I will not replicate this command all the way, however, I will tell you the commands to type. Before you proceed, please note that this command is temporary. If youre switch reboots for updates or whatever, the command gets erased and you have to do this all over again to config the switch. Moving on, you also need to decide which session you want to configure. It does not matter if you pick any of them since typically these will be disabled and hidden to most who dont know how to get here. So for this example, well just stick with session 1. Also you have to pick which port you want to have TAP enabled on so make sure you have Security Onion TAP port connected to the right port. I will use port 42, however, you can use which ever port you want. So type in the CLI "monitor session 1 destination interface 0/42". This will have the UNIFI switch make that specific port the TAP port. Next, we need to target the source ports. Type in "monitor session 1 source interface 0/2" Again, this is just an example port. You would just need to replicate that command per port you want to monirot. Lastly, just type the command "monitor session 1 mode" to enable the session and that is it. Security onion will now start moniroting the ports specified. 


Now that the switch has been configued, lets head back over to security onion. 

It looks like I am getting alerts now from all of my internal network. 

![image](https://github.com/user-attachments/assets/2faa949d-a1be-4896-9f60-9ff4ea842f51)



Here is a view of my switch. I have all ports on the bottom row activated with my different network devices. Port 42 is the TAP port and the others are the source ports that I point to the TAP port. 

![image](https://github.com/user-attachments/assets/c17f596d-02fc-4d53-95c2-7ebe7383c34d)









































References 

https://www.youtube.com/watch?v=Jb_sb_vLrB0&list=PLljFlTO9rB17E0hOetV_R4Lc0WbEy8q_Y&index=2 ------ Security Onion setup

https://www.youtube.com/watch?v=VwVyM_wZTps ------ Unifi Switch config
