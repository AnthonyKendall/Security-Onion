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

It looks like the download is done and I need to reboot, so let me go ahead and do that. 

![image](https://github.com/user-attachments/assets/b4891598-eeeb-4f8b-b67d-6764d0b2218a)

The server has rebooted and now its time to login and configure the server. 

![image](https://github.com/user-attachments/assets/04ffe3a5-fc7f-4210-b235-592f2e1cbb30)

I click on Yes to proceed to the next step and click on Install 

![image](https://github.com/user-attachments/assets/11ee9cc1-9574-4fc2-a57a-046653bb07ee)


Next, we are going to select the eval node as this is just an example setup, however, for my home lab, I have the standalone version installed. 

![image](https://github.com/user-attachments/assets/effc9d6e-2754-4c2a-a825-d3786b33024a)

I type in AGREE to proceed. 

![image](https://github.com/user-attachments/assets/0ee5a39a-febc-44dd-b255-0dcf1319af24)

Then I select Standard. Note that you could have this in an AirGap setup if you dont want Security Onion to communicate with the internet. 

![image](https://github.com/user-attachments/assets/99cc934b-2e50-4791-b175-b025ded0a20b)

Then you can rename the server to what ever you would like to call it, however, for this example we will just keep the default name. 

![image](https://github.com/user-attachments/assets/62b67f0f-5dd1-4508-a2e9-e3dbb015b5e5)

Next, we need to choose the NIC card that will be the one to access the website. We will leave this as default as well. 

![image](https://github.com/user-attachments/assets/5f26d1a4-0626-403e-a07d-dd0b60490078)

You can pick DHCP or Static for the IP. In this example, we will use DHCP. 

![image](https://github.com/user-attachments/assets/3b89b1a3-ac13-4514-9add-1cbbff1a380d)

Next, we select the monitoring NIC. There is only going to be one option, so just select the remaining NIC. 

![image](https://github.com/user-attachments/assets/d2146594-b1ce-41f0-b7eb-f5af71e5cb2e)

Next, we need to create a email to login to the web interface. This can be a fake email, does not need to be real at all. So we will just use a simple one.

![image](https://github.com/user-attachments/assets/bdaa281b-389e-44e3-b586-17be57ce07c3)

Then we need to enter a password. 

![image](https://github.com/user-attachments/assets/e7df914b-c85b-4a32-989b-627d3ac9fe5b)

Next, you will be prompted to select how you would like to access the interface. I will select the IP option 

![image](https://github.com/user-attachments/assets/bd349ad2-4c83-40a5-b790-ae639d543e42)

Select yes for this step.

![image](https://github.com/user-attachments/assets/68877911-bb23-4fbd-afe8-eba965d9bb6f)

Now we need to enter the subnet into the firewall so we can access it. If you do this step incorrectly, you will not be able to connect to the security onion server. 

![image](https://github.com/user-attachments/assets/63af9d5a-8d48-4b55-8ba1-7410915bbdad)

I select No for this step, but feel free to select yes if you would like. 

![image](https://github.com/user-attachments/assets/837cb692-4119-4e0b-b457-a665ab3fd3b9)

Final check that Security Onion gives you to review all the edits you added. If everything looks good, then go ahead and select yes. 

![image](https://github.com/user-attachments/assets/0d2a4e45-9ba2-47f9-966f-1518465f3f1d)

Now security onion will apply those changes and update the server with the config. This will take a couple of minutes, so grab a bite to eat if you want. 

![image](https://github.com/user-attachments/assets/c92b1116-ed16-4a5a-bfa3-c20f21331e2d)


Okay the setting have been configured and this is the screen you should now see. 

![image](https://github.com/user-attachments/assets/6b57ada8-334e-4fc1-8535-af0f3530950a)

Lets navigate to the web interface with the listed IP shown. 

Here you can see, we can successfully reach the web interface of Security Onion. 

![image](https://github.com/user-attachments/assets/cc1bb947-ed69-4665-8973-c87ecf1db1e8)

We have logged in and are now met with the overview page of Security Onion. 

![image](https://github.com/user-attachments/assets/fbc3f45a-42da-4d03-8330-fdeba0778867)

Now, going right to the alerts, it looks like we already have one alert that triggered from our host machine. 

![image](https://github.com/user-attachments/assets/7300f98f-1478-417a-9cfe-10b73c94c12e)

Lets test the TAP NIC to make sure it can recieve more alerts. Simply navigate to "Grid" then scroll down and click on the 2nd icon 

![image](https://github.com/user-attachments/assets/5e2a4e82-9f80-43a8-8884-3da8f3fcc094)

Now give it about 5 minutes to actually send the alerts. 

After waiting, we navigate to the Alerts section and click on refreash and you can see we can recieve more traffic and different alerts which is excatly what we want. 

![image](https://github.com/user-attachments/assets/4b13926e-bc5d-41b8-9f5c-e403f693369a)

![image](https://github.com/user-attachments/assets/df3c9d0a-fca0-4d38-b0ae-bcde59e5d7ee)

Now from here, the rest of this setup is just waiting for alerts and fine tuning them to mitigate as many false positives as possible. 

For example, lets say we want to exclude my host VM from triggering this "ET INFO Spotify P2P Client" alert. We need to click on the alert and select "Tune Detection". 

![image](https://github.com/user-attachments/assets/99876a4a-8c87-4731-a119-4306a19323a8)

Now something I think is cool that Security onion does is tell you more information about the alert and what it really means. You just simply navigate to the overview tab to see more information on the alert. 

![image](https://github.com/user-attachments/assets/47869299-2400-455e-9df2-f16839629bc1)

![image](https://github.com/user-attachments/assets/a11729f6-2f7c-419c-b083-bd06616c7fff)

If you think this alert is just completley useless, just simply turn the rule off entirely then like I just did. 

![image](https://github.com/user-attachments/assets/d8d16e18-af0b-427a-bc1c-5a0354349d06)

Now, lets say you wanted this rule enabled, but just wanted a certian group or individual devices from alerting due to the activity being for legit purposes. 

Well lets navigate to the tuning tab

![image](https://github.com/user-attachments/assets/da1d230b-21de-4652-9f02-f7ba68fb62aa)

Click the plus icon 

![image](https://github.com/user-attachments/assets/e14c157c-dcdd-4363-8c31-42eb5d3e6bb5)

Next we want to do Suppress, by src IP then the actual IP itself. 

![image](https://github.com/user-attachments/assets/f4e29dbb-b28d-41de-877c-8ae55419ee4e)

Once the information has been plugged in, click on "create" at the bottom. 

![image](https://github.com/user-attachments/assets/a166df45-cced-43d9-8675-3bb56d4e6e42)

After a few secounds you will see the rule has been applied 

![image](https://github.com/user-attachments/assets/b51a5228-c721-467f-bf96-7fb70eaa535e)

To get the alerts out of your dashboard, first we need to acknowledge the alert and esculate it to a case. 

Click on the Bell icon to acknowledge the case. That way it goes to you and saves your team from having to look at it and waste time. 

![image](https://github.com/user-attachments/assets/d719adaf-352b-4b65-9c76-88ee2a7277cb)

After you click on the bell icon, navigate to the top of the page and click the drop down arrow 

![image](https://github.com/user-attachments/assets/64fd3a33-275e-4b25-a6f3-87d386cc5f0f)

From here, select the acknowledged slider 

![image](https://github.com/user-attachments/assets/ee58cae0-6fb7-4041-831e-c383ab919ff6)

After you select that, all your acknowledgements will show up. Select the one you want to turn into a case by selecting the triange icon. 

![image](https://github.com/user-attachments/assets/ecef9b7a-1e9c-4d2a-85db-7f12b21644da)

Select "Escalate to new case" 

![image](https://github.com/user-attachments/assets/2247c36d-ea5d-4e86-8ed3-4632ddfb3f2d)

Once you escalate the case, navigate to the cases tab on the left. 

![image](https://github.com/user-attachments/assets/9a87c305-2ffc-4558-90e2-45645b7cbd6b)

Once you are here, click on the binoculars icon 

![image](https://github.com/user-attachments/assets/ddf3ae4f-4ee3-4589-a22b-53d0a36a4774)


This is where you are able to add attachments, info from another case and etc.. 

![image](https://github.com/user-attachments/assets/a76452d7-43d0-46b1-a05f-81ad9a2c1848)

From here, we just excluded our host machine from triggering this alert again, so we will just add a quick note. Also note to assign yourself the case. 

![image](https://github.com/user-attachments/assets/e2bd1520-c744-40aa-a8ca-5509153039ad)

Now that we added a note to the case and assigned it to us, lets close it out. 

Simply go to the "Status" section under Summary over on the right side and set the case to close. 

![image](https://github.com/user-attachments/assets/ddc9ae0c-31a8-4439-a951-b969640d7361)

After you close the case, it will not be out of your dashboard and successfully reviewed by you. 

There is much more you can do in this server such as add more IP's to the firewall, add more users with different roles, add elastic agents to log for Windows event logs and so much more. Play around with it and discover the amazing things this tool can do. 






















Lets test to make sure that the TAP NIC card is working and receiving alerts. A good way to do this is to navigate to Security Onion and click on Test alerts. Security Onion will send Test alerts to your dashboard. If you recieve them, this lets you know that the TAP port is working correctly. 

![image](https://github.com/user-attachments/assets/2faa949d-a1be-4896-9f60-9ff4ea842f51)
























References 

https://www.youtube.com/watch?v=Jb_sb_vLrB0&list=PLljFlTO9rB17E0hOetV_R4Lc0WbEy8q_Y&index=2 ------ Security Onion setup

https://www.youtube.com/watch?v=VwVyM_wZTps ------ Unifi Switch config
