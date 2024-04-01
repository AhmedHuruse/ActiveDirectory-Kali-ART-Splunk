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
  
  <img src="https://i.imgur.com/IGBV9hV.png" height="80%" width="80%" alt="Active Directory Homelab Steps"/>
- Step 2: On our Windows Server we install Active Directory Domain Services. After that we upgrade into Domain Controller and create our domain. After that we move on to creating "objects" which can be users, groups, computer, etc. We create organizational units to mimic and enterprise environment. These units are segemented in a company usually by department so that users/employees can be placed in the correct unit with their certain privilages. Here we make and Organizational Unit called "IT" and add user a called Jenny Smith with a user login ID of jsmith and also give her a password. We do the same for another OU and user. We make an OU called "HR" and add a user named Terry Smith with a login ID of tsmith and also a password. For this project we will only create two users. After that we, we hop on our target machine to change its domain to the active directory domain called "ahuruse" and restart to ave changes. Then we log in with the Jenny Smith user ID and password that we set up.
  
  <img src="https://i.imgur.com/ntRa1Cv.png" height="40%" width="40%" alt="Active Directory Homelab Steps"/> <img src="https://i.imgur.com/EEMZOOP.png" height="40%" width="40%" alt="Active Directory Homelab Steps"/>
- Step 3: We move onto our Kali Linux machine to begin our brute forcing setup. We begin by updating and upgrading all packaged. Next we create a new directory to put all of our resources in called "ad-project". After that we install the brute forcing tool "crowbar". Crowbar allows for brute forcing a user account with commonly used password from the Kali wordlists and can be edited to insert passwords an attacker may have gathered through reconnaissance. When crerowbar gets a successful match on password and enters; it automatically establishes a remote connection with either RDP, OpenVPN, and SSH Private Keys and VNC Keys.
  
  <img src="https://i.imgur.com/GkpL8eD.png" height="40%" width="40%" alt="Active Directory Homelab Steps"/>  <img src="https://i.imgur.com/KkHiupD.png" height="40%" width="40%" alt="Active Directory Homelab Steps"/> 
- Step 4: Kali Linux comes with popular wordlist of paswords that are in the directory "usr/share/wordlists/". In this project, we want to use the "rockyou.txt.gz" wordlist file. We unzip this file using "gunzip" to convert it to a normal .txt file. The new unziped file of "rockyou.txt gets copied with the "cp" command into our "ad-project" directory. With the "ls -lh" command, we see that the file has 134m of passwords. We dont want to use all of these passwords so we grab the first 20 passwords from the top of list in the file with the "head -n 20 rockyou.txt" command. We then output these results into a new .txt file we created under our "ad-project" directory called "passwords.txt".
  
 <img src="https://i.imgur.com/qB7NWnI.png" height="40%" width="40%" alt="Active Directory Homelab Steps"/>  <img src="https://i.imgur.com/uBeh2g9.png" height="40%" width="40%" alt="Active Directory Homelab Steps"/> 

- Step 5: For this project, I already know the passwords for the users in target machine and active directory because I create them. I will add the password of the user that I will brute force (jsmith) to the passwords.txt file using the "nano" command to edit the file and add the password. I will double check if it saved correctly by using the "cat" command against the file.
  
   <img src="https://i.imgur.com/Lreu1Jq.png" height="30%" width="30%" alt="Active Directory Homelab Steps"/> <img src="https://i.imgur.com/TGhqBV8.png" height="30%" width="30%" alt="Active Directory Homelab Steps"/> <img src="https://i.imgur.com/LLiJNUk.png" height="30%" width="30%" alt="Active Directory Homelab Steps"/>
- Step 6: Switching over to our target machine. We make sure to configure Windows to allow remote connections to the computer so that our attacker machine can use RDP to connect after brute forcing the password. We will also add the users computers that can be connected to which will be jsmith and tsmith.
  
  <img src="https://i.imgur.com/DF7DnM2.png" height="40%" width="40%" alt="Active Directory Homelab Steps"/> <img src="https://i.imgur.com/mLa4kZ6.png" height="40%" width="40%" alt="Active Directory Homelab Steps"/>

-Step 7: We move back to our Kali machine and begin constructing our command with "crowbar". The command also needs to know specifications like which user to target, with what reomote connection method, with what password file, and with what IP address. This is done by the flags that are in the command. The "-b" flag represents the type of remote connection which in this case is RDP. The "-u" flag representing the the user which is "jsmith". The "-C" flag meaning the which file of passwords we want to use (passwords.txt). And finally the "-s" flag meaning which specific IP address in the domain we want to target. The command will run all of the passwords listed in the file to the target and if successful; it will show a line saying "connection successful" with the username and password right next to it.

<img src="https://i.imgur.com/YFRJXKS.png" height="80%" width="80%" alt="Active Directory Homelab Steps"/> 

-Step 8: We hop back on our target Windows 10 machine and disable Windows Defender and anti virus. We head to our kali serviced web page that has the malware on it. We download the malware and run it. Now that the malware has been executed, we open up the command prompt with administrator privilages. We type in "netstat -anob" and we see an established connection to our Kali attacker machine. We also use the PID from that connection output and open it up on windows task manager to see that it is indeed the malware that was executed

<img src="https://i.imgur.com/OKCqc6N.png" height="30%" width="30%" alt="Active Directory Homelab Steps"/> <img src="https://i.imgur.com/y4eibW7.png" height="30%" width="30%" alt="Active Directory Homelab Steps"/> <img src="https://i.imgur.com/CvvQhNA.png" height="30%" width="30%" alt="Active Directory Homelab Steps"/>

-Step 9: Jumping back to our Kali machine, it looks like we have a connection where we were listening in with our handler. Now we have control over the target machine. We now run a few commmands. We type in the "shell" command to establish a shell on our target machine. We also run the "net user", "net localgroup", and "ipconfig" commands to exfiltrate more information from the target machine. After this, we will check back on our Splunk SIEM to see what kind of telemetry has been generated.

<img src="https://i.imgur.com/im8iLwV.png" height="30%" width="30%" alt="Active Directory Homelab Steps"/> <img src="https://i.imgur.com/nxwJ7c5.png" height="30%" width="30%" alt="Active Directory Homelab Steps"/> <img src="https://i.imgur.com/0AF9kAv.png" height="30%" width="30%" alt="Active Directory Homelab Steps"/>


-Step 10: Back on our Splunk SIEM, we search for our "index=endpoint" but at the end of it we query the Kali attacker machine's IP address. It would like this "index=endpoint 192.168.20.11". We scroll down to discover that one destination port was targeted and it was the 3389 port for RDP. Some questions an analyst would ask are; "Should this machine be attempting to connect to RDP?, "What machine is this?", "Who does is belong to?", or "How is it able to listen into and scan my ports?". 

<img src="https://i.imgur.com/UmPMzkS.png" height="80%" width="80%" alt="Active Directory Homelab Steps"/>

Step 11: We can further dig deeper into other events that were logged by Splunk. This time we can search with the name of the malware at the end so; "index=endpoint Resume.pdf.exe. We have 13 events and 7 event codes generated. We delve deeper by investigating the first event code out of 7. By expanding this event, we can see that a parent process had spawned another process which ran a cmd.exe. We also see the process id 5428 which we could use to query our data to see what that command had done. Instead we opt with the process guid. We copy the process guid characters and paste it at the end of our "index=enpoint" instead using the malware name or another process id. This would looklike: "index=endpoint d542c381... ". This shows use exactly which commands the attacker used to exfiltrate data displayed in our Splunk.

 <img src="https://i.imgur.com/aP18Q4u.png" height="40%" width="40%" alt="Active Directory Homelab Steps"/> <img src="https://i.imgur.com/W0wafAz.png" height="40%" width="40%" alt="Active Directory Homelab Steps"/>



