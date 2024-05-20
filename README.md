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
![image](https://github.com/Davinci042/Detection-Lab/assets/103445073/49ced989-dd48-4f48-a15b-c82ddbb78fce)

2. Installing Virtual box
   i. Go to Virtualbox.org
   ii. Click on Download Virtualbox 7.0
   iii. Based on my host Machine OS being windows 11 I selected the appropriate link to download virtualbox 7.0
   iv. Go to download folder and right click and click open in terminal. Then type the command on Powershell Get-FileHash Vitualbox filename.exe and hit Enter. Copied the file hash and headed over to the virtualbox website to compare the hash of what I just downloaded with what is on the website to ensure that the file was not tampered with (Both file hashes should be similar).
   v. Go to Download folder and double click on the virtualbox 7.0 installation file, Click Yes on the pop-up box that would appear. You may get a dependency requirement error and vitualbox will not download. Search on google and find the required dependency and download it then proceed to download virtualbox.
   
3. Spin-up 2 Windows 10 VM on Virtualbox
   i. Go to <a href="https://www.microsoft.com/en-ca/software-download/windows 10">https://www.microsoft.com/en-ca/software-download/windows 10</a>
   ii. Click on Download Tool now button
   iii. Double click on the mediacreation tool file you just downloaded and click on Yes
   iv. Accept license agreement
   v. Click on create installation media and hit Next button
   vi. Select language, edition & Architecture (I used recommended settings) & hit Next
   vii. Choose iso file option as media option to use & hit Next. Take note of where you saved the iso file
   viii. Go to Virtualbox and click on New
   ix. Put VM name, choose Folder you want to save the VM files on your host machine & select the iso image you saved earlier.
   x. Check the box skip unattended installation & click Next. On the next screens choose your VM compute settings Memory 4GB, CPU = 1, Virtual hard disk 50GB.
   
4. Spin-up a Kali Linux VM. Download a pre-built Kali linux iso from Kali.org and install it on virtual box.
5. Spin-up a Windows 2019 server VM on virtual box. First download the iso image file from microsoft evaluation center (I used google to look up and get the url). During the installation make sure to select Windows server 2022 desktop experience
6. Spin-up a splunk server. Go to ubuntu.com and download ubuntu server 22.04.3 to download the iso image file. During installation we make the splunk beefier because we expect it to collect telemetry data from other VMs so we set the Virtual hard disk size of 100GB. Start the Splunk server VM and follow the steps to install it.
7. Go to virtual box and ensure that you configure a NAT network. Ensure all your VMs are on the same NAT network
8. Configure Sysmon & Splunk
   1. Start the Splunk VM and configure the ip address to be static. Check current ip address using the cmd ip a and hit enter. You need to edit the splunk server config file to achieve this.
   2. On the prompt enter cmd sudo nano /etc/netplan/00-installer-config.yaml and hit Enter
   3. 



