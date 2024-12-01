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

Here is the hash of the file path. 

![image](https://github.com/user-attachments/assets/3bb6522a-6fa1-4494-8278-c39430101a39)


The hash from Security Onion is B087A0D12FC2CA3CCD02BD52E52421F4F60DC09BF826337A057E05A04D114CCE , so I went ahead and created a script to compare the hashes to make sure they add up. From the scripts results they match. 

![image](https://github.com/user-attachments/assets/8c49a27d-e2fd-4f15-a1ce-b30d0cd165be)


Now that the hashes have been verified, I know this ISO's integrity has not been tampered with. I will continue with the installation now by turning this ISO into a bootable drive using Balena Etcher. 

From here, you just select the ISO and then put it on a USB and flash it. However, once after this step it would be hard to take screenshots, so I will put this on a VM to continue the screenshots, but the process would be the same when in front of the computer. 

I just pre-loaded all the requirements needed into VMware and now am ready to go. Please watch the reference video if you need to see the small steps taken. 

![image](https://github.com/user-attachments/assets/fe39f94b-4795-4add-99bb-ae5e64b8fc98)


When you start, you will be prompted with this UI 

![image](https://github.com/user-attachments/assets/8d82e2b0-a04d-4199-b01b-a7eb39bd5ad2)

Everything is getting set up and will look like this, so just give it some time. Get some water and hydrate! 

![image](https://github.com/user-attachments/assets/fe95d575-024e-446f-8f14-b407df6b1101)

Once everything is complete, you should see this screen now. We will go ahead and type in yes to proceed. 

![image](https://github.com/user-attachments/assets/ada6beba-53a6-4549-9291-c9363f35825f)

You will now create an admin account to be able to access the system. Make sure you keep track of this password. Put it in your password manager like Keeper or 1Password. 

![image](https://github.com/user-attachments/assets/8ca37faa-8b67-433b-9199-30f1386c030f)

Once you have entered your admin account, the download will take place. This process takes a few minutes as well, so get some more water while you wait. 

![image](https://github.com/user-attachments/assets/33dbd949-0523-4604-9c46-81a332a97203)

While we wait for this to download, we can multi task and work on the next step by making sure our network switch is properly configured to send traffic to the TAP port. 

Now, you do need a switch that does support many to one port mirroring (SPAN). I have the Unifi Pro 48 switch in my lab, so I will be using this. If you would like another recommendation, I suggest the Dell PowerConnect 7048P (https://hardwarestorm.com/dell-powerconnect-7048p-network-switch.html)

Unifi only has 1-1 SPAN ports on their UI. In order to bypass this, we need to ssh into the switch and make the config on the backend to allow multiple SPAN ports. First, I use PuTTY to remote into the switch. 

![image](https://github.com/user-attachments/assets/d5a75d18-68ab-4579-a8e4-a7cfd6b1b7c4)

Next, I do the command "telnet localhost"

![image](https://github.com/user-attachments/assets/2cd1a71b-aa06-4962-b1df-11f4b665c3eb)

Next, I type "en" then "config"

![image](https://github.com/user-attachments/assets/f86ff2ae-d682-4c57-8444-b6fcad6a5cbf)

Next, I type the command "show monitor session all". Mine is already setup, but yours would be blank like the rest of the sessions.

![image](https://github.com/user-attachments/assets/3989d025-e05b-45ef-a275-fa0d5c0d4e39)

From here, all we need to do is type the command to have the switch know which ports are the source ports and which one is the TAP port. A good reference video I like to credit is https://www.youtube.com/watch?v=VwVyM_wZTps

Since mine is already configured, I will not replicate this command all the way, however, I will tell you the commands to type. Before you proceed, please note that this command is temporary. If your switch reboots for updates or whatever, the command gets erased and you have to do this all over again to config the switch. Moving on, you also need to decide which session you want to configure. It does not matter if you pick any of them since typically these will be disabled and hidden to most who don’t know how to get here. So for this example, well just stick with session 1. Also you have to pick which port you want to have TAP enabled on so make sure you have Security Onion TAP port connected to the right port. I will use port 42, however, you can use whichever port you want. So type in the CLI "monitor session 1 destination interface 0/42". This will have the UNIFI switch make that specific port the TAP port. Next, we need to target the source ports. Type in "monitor session 1 source interface 0/2" Again, this is just an example port. You just need to replicate that command per port you want to monitor. Lastly, just type the command "monitor session 1 mode" to enable the session and that is it. Security onion will now start monitoring the ports specified. 

Here is a view of my switch. I have all ports on the bottom row activated with my different network devices. Port 42 is the TAP port and the others are the source ports that I point to the TAP port. 

![image](https://github.com/user-attachments/assets/c17f596d-02fc-4d53-95c2-7ebe7383c34d)

Now that the switch has been configured, lets head back over to security onion. 



It looks like I am getting alerts now from all of my internal network. 

![image](https://github.com/user-attachments/assets/2faa949d-a1be-4896-9f60-9ff4ea842f51)
























References 

https://www.youtube.com/watch?v=Jb_sb_vLrB0&list=PLljFlTO9rB17E0hOetV_R4Lc0WbEy8q_Y&index=2 ------ Security Onion setup

https://www.youtube.com/watch?v=VwVyM_wZTps ------ Unifi Switch config
