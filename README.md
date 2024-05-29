# Vulnerability Management Lab Project with Nessus
## Objective

1. Prepare Nessus Vulnerability Management Scanner
2. Prepare Client Virtual Machine and Make it Vulnerable
3. Perform Vulnerability Scan
4. Remediate Vulnerabilities
5. Verify Remediations and start all over from 3. till vulnerability is low or at an acceptable level.

### Environments and Technologies Used:

- Nessus Essentials
- VMware Workstation Player
- MS Windows 10 ISO
- Virtual Machine

### Operating Systems Used:

- VM Windows 10 PRO (21H2)
- VM Linux Ubuntu 20.12
<details close>

<div>

</summary>

## Prepare Nessus Vulnerability Management Scanner
1. Download and install VMWare player.
2. Download Window 10 ISO
3. Download, install Nessus on local machine after registering and receiving activation code.
![Download and install Nessus](./images/download-nessus.png)
![Download and install Nessus](./images/download-nessus1.png)
![Download and install Nessus](./images/download-nessus2.png)
4. Next on the installation welcome page that displays, Click connect via SSL, advanced, proceed to localhost. 
![Download and install Nessus](./images/download-nessus3.png)
![Download and install Nessus](./images/download-nessus4.png)
5. Then click next and select - Register for Nessus Essentials, then click continue. 
![Download and Install Nessus](./images/download-nessus5.png)
6. Then provide activation code from your email. Then create username and password. Next, Nessus download plugins to finish setup.
![Download and Install Nessus](./images/download-nessus6.png)
![Download and Install Nessus](./images/download-nessus7.png)
![Download and Install Nessus](./images/download-nessus8.png)
![Download and Install Nessus](./images/download-nessus9.png)
![Nessus Installation complete](./images/download-nessus10.png)

## Prepare Client Virtual Machine and Make it Vulnerable
- Install and launch VMware Workstation Player on your local machine. 
![Vulnerable VM ](./images/vm-v.png)
- 
![OpenVAS url](./images/vm-v2.png)
![OpenVAS url](./images/old-software.png)

## Configure OpenVAS to Perform First Unauthenticated Scan against our Vulnerable VM
- Login to OpenVAS → Assets → Hosts → New Host. Add the Windows client VM PRIVATE IP Address and add comment as Win10-Vulnerable. Create a New Target from the Host, name it “Azure Vulnerable VMs”. Take note of the credentials. We will add SMB credentials later.
![Windows Client IP](./images/windows-client.png)
![Unauthenticated Scan](./images/unauthenticated-scan.png)
![New Target](./images/unauthenticated-scan2.png)
![Unauthenticated Scan](./images/unauthenticated-scan3.png)
- Create a new Task: Title the "Name" and "Comment" as “Scan - Azure Vulnerable VMs”. Select “Azure Vulnerable VMs” as Scan Targets → . Save the Task. “Start” the “Scan - Azure Vulnerable VMs” Task by clicking the play button.
![Unauthenticated Scan](./images/unauthenticated4.png)
![Unauthenticated Scan](./images/unauthenticated5.png)


Take Note of the Status:
- Once the scan is finished, click the date under “Last Report” to see the results. Take note of Tabs, specifically the “Results” tab. Even though we installed a super old version of Firefox, note that it does not show up here. Note that this is because we aren’t running a credentialed scan so the scanner could not discover it. We will configure credential scans next.
![OpenVAS url](./images/old-software2.png)
## Make Configurations for Credentialed Scans (Within VM)
- Disable Windows Firewall, Disable User Account Control, Enable Remote Registry, Set Registry Key, Launch Registry Editor (regedit.exe) in “Run as administrator” mode and grant Admin Approval, if requested. Navigate to KEY_LOCAL_MACHINE hive. Open SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System key. Create a new DWORD (32-bit) value with the following properties:  Name: LocalAccountTokenFilterPolicy  Value: 1. Close Registry Editor Restart the VM.


## Make Configurations for Credentialed Scans (OpenVAS)
- Go to Configuration → Credentials → New Credential. Name / Comment → “Azure VM Credentials”. Allow Insecure Use: Yes. Username: azureuser. Password: Cyberlab123!, Save. Go to Configuration → Targets → CLONE the Target we made before. NEW Name / Comment: “Azure Vulnerable VMs - Credentialed Scan”. Ensure the Private IP is still accurate
Credentials → SMB → Select the Credentials we just made: Azure VM Credentials, Save

## Execute Credentialed Scan against our Vulnerable Windows VM
- Within Greenbone / OpenVAS, go to Scans → Tasks, CLONE the “Scan - Azure Vulnerable VMs” Task, then Edit it:
Name / Comment → “Scan - Azure Vulnerable VMs - Credentialed”, Targets: Azure Vulnerable VMs - Credentialed Scan
Save, Click the Play button to launch the new Credentialed Scan, wait for it to finish, It will take longer than the last one. Wait for it to finish, After the credentialed scan finishes, you can immediately see the difference in findings: 

- Check SMB Login under “Results”
Further inspect the individual vulnerabilities and see all the Criticals from the out-of-date FireFox
Remove the Filter (upper right)

## Remediate Vulnerabilities & Verify
- Log back into your Win10-Vulnerable VM
Uninstall Adobe Reader, VLC Player, and Firefox
Restart the VM and Verify Remediations. 
