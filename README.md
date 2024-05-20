# Detection-Lab
A home lab set-up with logs and telemetry collected in a SIEM for detection analysis

## Objective
The Detection Lab project aimed to establish a controlled environment for simulating and detecting cyber attacks. The primary focus was to ingest and analyze logs within a Security Information and Event Management (SIEM) system, generating test telemetry to mimic real-world attack scenarios. This hands-on experience was designed to deepen understanding of network security, attack patterns, and defensive strategies.

### Skills Learned
- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used
- Security Information and Event Management (SIEM) system for log ingestion and analysis.
- Network analysis tools (such as Wireshark) for capturing and examining network traffic.
- Telemetry generation tools to create realistic network traffic and attack scenarios.

## Steps
1. This home lab set up needs to be first designed and I used a free online tool draw.io to accomplish this. The home network will be a 10.0.2.0/24 subnet and all VMs will be spun up in Oracle Virtualbox. Then I spun up a windows 2022 server VM that I promoted to the role of Domain Controller. Next I spun up an Ubuntu Linux VM that I where I installed Splunk server. The set up is designed so that all the VMs and Active Directory DC will forward telemetry to the Splunk server so we it can be indexed and searched using SPL queries. To achieve this I installed Sysmon and Splunk Universal forwarder on each of the assets that will send telemetry to Splunk.

*Ref 1: Network Diagram*
![image](https://github.com/Davinci042/Detection-Lab/assets/103445073/aed6ddde-7990-41d6-aa98-02afad8a4bc6)


2. Installing Virtual box
   1. Go to Virtualbox.org
   2. Click on Download Virtualbox 7.0
   3. Based on my host Machine OS being windows 11 I selected the appropriate link to download virtualbox 7.0
   4. Go to download folder and right click and click open in terminal. Then type the command on Powershell Get-FileHash Vitualbox filename.exe and hit Enter. Copied the file hash and headed over to the virtualbox website to compare the hash of what I just downloaded with what is on the website to ensure that the file was not tampered with (Both file hashes should be similar).
   5. Go to Download folder and double click on the virtualbox 7.0 installation file, Click Yes on the pop-up box that would appear. You may get a dependency requirement error and vitualbox will not download. Search on google and find the required dependency and download it then proceed to download virtualbox.
   
3. Spin-up 2 Windows 10 VM on Virtualbox
   1. Go to <a href="https://www.microsoft.com/en-ca/software-download/windows 10">https://www.microsoft.com/en-ca/software-download/windows 10</a>
   2. Click on Download Tool now button
   3. Double click on the mediacreation tool file you just downloaded and click on Yes
   4. Accept license agreement
   5. Click on create installation media and hit Next button
   6. Select language, edition & Architecture (I used recommended settings) & hit Next
   7. Choose iso file option as media option to use & hit Next. Take note of where you saved the iso file
   8. Go to Virtualbox and click on New
   9. Put VM name, choose Folder you want to save the VM files on your host machine & select the iso image you saved earlier.
   10. Check the box skip unattended installation & click Next. On the next screens choose your VM compute settings Memory 4GB, CPU = 1, Virtual hard disk 50GB.
   
4. Spin-up a Kali Linux VM. Download a pre-built Kali linux iso from Kali.org and install it on virtual box.
5. Spin-up a Windows 2019 server VM on virtual box. First download the iso image file from microsoft evaluation center (I used google to look up and get the url). During the installation make sure to select Windows server 2022 desktop experience
6. Spin-up a splunk server. Go to ubuntu.com and download ubuntu server 22.04.3 to download the iso image file. During installation we make the splunk beefier because we expect it to collect telemetry data from other VMs so we set the Virtual hard disk size of 100GB. Start the Splunk server VM and follow the steps to install it.
7. Go to virtual box and ensure that you configure a NAT network. Ensure all your VMs are on the same NAT network
8. Configure Splunk
   1. Start the Splunk VM and configure the ip address to be static. Check current ip address using the cmd ip a and hit enter. You need to edit the splunk server config file to achieve this.
   2. On the prompt enter cmd sudo nano /etc/netplan/00-installer-config.yaml and hit Enter
   3. Under DHCP setting change to no and press enter for new line
   4. Hit tab key 3 times and type addresses: [10.0.2.10/24] and hit Enter
   5. Press the tab key 3 times again and type nameservers: and hit Enter
   6. Press tab button 5 times and type addresses: [8.8.8.8] and hit Enter
   7. Press tab button 3 times and type routes: and press Enter
   8. Press the tab button 5 times and type - to: default and press Enter
   9. Press tab button 6 times and type via: 10.0.2.1
   10. Save this configuration by holding Ctrl + X on keyboard and type Y for Yes then hit Enter
   11. Clear screen and type sudo netplan apply and hit Enter. You will get a couple of warnings about permissions but ignore due to this being a lab environment.
   12. Verify the IP is static now by using the cmd ip a and hit Enter. Confirm the IP address is 10.0.2.10
   13. Verify you have connectivity by using the cmd ping google.com. If you get connection cancel ping using Ctrl + X
   14. Go to host machine and from your browser go to www.splunk.com and sign up for a free account and sign in.
   15. Head to Products and click on free trials and downloads. Scroll down to Splunk enterprise and select Linux as your operating system. We are interested in the .deb file, so go ahead and select Download now and save in directory of your choice.
   16. Go back to splunk VM and install the Guest add ons for virtualbox using the cmd sudo apt-get install virtualbox + tab (to see what options are available). We are interested in the guest-additions-iso. Type that out to complete the cmd and hit Enter. Type Y for Yes and hit Enter. Once you see a screen hit Enter
   17. Clear screen and on top click on devices. Head over to shared folders and click on shared folder settings. Now we want to add a folder by clicking on the + icon on the right side of the mini window. Select the folder path where the splunk installer is. Leave folder name as default and for options select read only, auto mount and make permanent. Hit Ok and hit Ok again.
   18. Back unto the splunk VM type sudo reboot and hit Enter to reboot the virtual machine
   19. Log back in to Splunk with username and password.
   20. Add user to the vboxsf group. Type sudo adduser username vboxsf and hit Enter
   21. Type in the password.
   22. If you get the response the group vboxsf does not exist, you need to download another additional guest installation offer. Type sudo apt-get install virtualbox-guest-utils and hit Enter.
   23. Clear screen, reboot and log back in. Now try to add user again as in step 20.
   24. Next step is to create a new directory called share. Use cmd mkdir share. Use ls -l to view directories
   25. Now we want to mount our shared folder unto our directory called share by using the cmd sudo mount -t vboxsf -o uid=1000, gid=1000 sharedfoldername_from_step_17 and hit Enter. If you get error here, try exiting the session to log out and then log back in so that the new user you added can take effect.
   26. Change directories into that share folder cd share.
   27. Type ls -la to list out all the files in the share folder which should include the splunk installer.
   28. To install splunk type sudo dpkg -i splunk_installer_filename and hit Enter.
   29. Once you see complete you are good to change into the directory of where splunk is located on our server which should be cd /opt/splunk and hit Enter
   30. Type ls - la and hit Enter. You will notice that all of the users and groups belong to splunk as it limits the permissions to that user. Change into the user splunk by typing sudo -u splunk bash and hit Enter
   31. Change into bin directory cd bin (which contains all the binaries splunk can use) and hit Enter. Type ./splunk start and hit Enter to run the installer. You will get a license and terms agreement. Go ahead and hit Q and type in Y to accept. Enter an administrative username and enter a password.
   32. To make sure splunk starts up ebverytime our VM reboots. Type exit to go out of the user splunk. Change to bin directory cd bin. Type ./splunk enable boot-start -user splunk and hit Enter.
       
9. Configure Splunk Universal Forwarder and Sysmon: For Brevity I will only describe the steps to install sysmon on one of the windows 10 VMs named SPIDERMAN.
      1. First make sure you set-up static IP for the VM by going to the windows settings, internet & Network then properties and ipv4 settings and enter manual settings i.e IP address of 10.0.2.30, subnet mask of 255.255.255.0 and default gateway of 10.0.2.1. Ensure the VMs are pointing to the primary DNS 8.8.8.8.
      2. From your windows VM, head over to splunk.com and sign in.
      3. Click on Products and then free trials and downloads. Scroll down and select Universal forwarder. Click on get my free download and select the correct OS which in my case is Windows 10 64-bit.
      4. Once the download is completed, go to downlads folder and double click the msi file. Click on check box to accept the license agreement. Select the first option that is on-premise splunk enterprise instance. Click on Next
      5. Type username as admin and leave generate random password checked. Click Next.
      6. Skip deployment server as we do not currently have one.
      7. For receiving indexer this will be the Ip address of the splunk server which is 10.0.2.10 and use port 9997 which is the default port for receiving events for splunk. Hit Next
      8. Hit Install.
      9. Now we will start downloading and installing sysmon
      10. Got o browser again and on new tab search sysmon. Select Sysmon by sysinyternals. Scroll down and click on download sysmon.
      11. Search for sysmon configuration. I used sysmon by olaf. Search for sysmon olaf config and go to the github link. Scroll down and select sysmonconfig.xml. Click on raw on the right hand side, right click save as and save under downlaods directory.
      12. Go to download directory and you will see the sysmon folder you downloaded from sysinternals. Right click on it and click on extract all. Click Extract.
      13. Click on the file explorer bar and right click and copy the path
      14. Open up powershell from start button and run as administrator.
      15. Type cd paste file path
      16. Type .\Sysmon64.exe -i ..\sysmonconfig.xml and hit Enter. Hit Agree on the pop up window and it will install sysmon. You will see a sysmon started message on the window. close powershell
          ![image](https://github.com/Davinci042/Detection-Lab/assets/103445073/f793e178-9fad-4189-9082-9e294fe97fac)

      17. Your splunk universal forwarder shpould have finished installing by now. Click on finish.
      18. Next we need to instruct our splunk forwarder on what we want to send over to our splunk server by configuriong a file called inputs.conf This file is located under C:\ drive \Program files \ Splunk Universal forwarder \ etc\ system \ default.
      19. Right click on this file and copy it. Go to ...\system\local directory and paste the inputs.conf file here. DO NOT EDIT THE INPUTS.CONF FILE UNDER THE DEFAULT DIRECTORY. The local directory requires administrative privileges. Open notepad as admin and paste the contents of inputs.conf from this  <a href="https://github.com/Davinci042/Splunk-Univ-Forwarder-inputs.conf">repository</a>. Note that we are forwarding Windows event logs from Application, Security, System and Sysmon/Operational. The index we are using is pointing to an index name called endpoint. This is important to know because whaever events we generate under these categories will only be sent to the index called endpoints in splunk. Save the notepad file under C:\Programfiles\SplunkUniversalForwarder\etc\system\local directory and use file names as inputs.conf and save as types as All files. Click on save and exit.
      20. Anytime you update inputs.conf file you must restart splunk universal forwarder service. Search up services on windows search bar and run as administartor. Look for Splunk forwarder service. Under column of log on as double click on Log on asif it is NT SERVICE. Change to Local system account. Click on OK. Now log on as is now Local System. Right click on the Splunk Forwarder and click on restart. If you get error warning just hit OK. Now start the service again.
      21. Now we can finalize our splunk configuration. Open a new splunk portal by typing on a brower address 10.0.2.10:8000 and hit Enter
      22. Log in using credentials we created during the splunk install on the server.
      23. From home screen select settings at the top and head over to indexes. You need to create the index endpoint just like we have in the inputs.conf file. Click on NEw Index button. Type name as endpoint and click on save.
      24. Next we need to enable our splunk server to receiver the data. Click on setting, click on forwarding and receiving. Under the Receive data, click on configfure receiving, Click on new receiving port and put it as 9997 and hit save.
      25. Click on Apps and select search and reporting. Skip tour. Type under the search bar index=endpoint and timeframe can be last 24 hours, then click on search. You should see some events come through. You can scroll down and you will see your VM name as the host sending the telemetry.
      26. Now you can start building queries to look for malicious activity from the splunk environment.
      27. We can follow the same steps to install sysmon and splunk universal forwarder on our Active directory Domain Controller server. The only thing to note is that when installing Splunk Universal forwarder on the AD server use the server IP address ad the deployment server address and use default port 8089.
      28. After we have repeated the same process for all endpoints i.e the other windows VM named THE PUNISHER and our ADDC named HYDRA-DC, we should start seeing all 3 host when we search index=endpoint in splunk.
          ![image](https://github.com/Davinci042/Detection-Lab/assets/103445073/fb60e3a1-f68d-43cf-85a7-4b730896e7d1)
          ![image](https://github.com/Davinci042/Detection-Lab/assets/103445073/61da853b-e259-45b4-be6e-d7f9d22420a7)




