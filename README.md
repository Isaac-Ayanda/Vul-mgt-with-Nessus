# Vulnerability Management Lab Project with Nessus
## Objective

1. Prepare Nessus Vulnerability Management Scanner
2. Create Client Virtual Machine and Make it Vulnerable
3. Perform Vulnerability Scan
4. Remediate Vulnerabilities
5. Verify Remediations and start all over from 3. till vulnerability is low or at an acceptable level.

### Environments and Technologies Used:

- Nessus Essentials
- VMWare Workstation Player
- Windows 10 ISO
- Virtual Machine

### Operating Systems Used:

- VM Windows 10 PRO (21H2)
- VM Linux Ubuntu 20.12
<details close>

<div>

</summary>

## Prepare Nessus Vulnerability Management Scanner
- At the Azure portal, select "OpenVAS secured and supported by HOSSTED" from the marketplace.
![OpenVAS from marketplace ](./images/select-openvas.png)
- Click it and select "pre-set configuration”. Select a workload environment as Dev/Test and D-Series as workload type (create new resource group, VM name, region, username and password, disable boot diagnostic as it is not necessary) and continue to Create Vitual machine runing OpenVAS
![OpenVAS from marketplace ](./images/select-openvas2.png)
![OpenVAS from marketplace ](./images/select-openvas3.png)
- SSH into the VM using the public IP and manually install OpenVAS.
![OpenVAS VM installation](./images/openvas-url0.png)
- Ensure config files are properly setup. Otherwise, OpenVAS may not be setup correctly. To continue with manually setup run the following commands:
```
sudo apt update
sudo apt upgrade -y
sudo apt dist-upgrade -y
sudo apt install gvm
sudo gvm-setup
sudo gvm-check-setup
```
![OpenVAS VM installation](./images/openvas-url0.png)

- You might need to leave it for up to a day or so before logging into the web server, to make sure everything on the web server has been configured correctly. To ensure the scan files are available and up to date, you can also run ```sudo gvm-feed-update```
- Next, use the username, password and url provided on the OpenVAS VM to access the OpenVAS portal (or use admin/admin). You can reset the password after login and check that the config files are present.
![OpenVAS url](./images/openvas-url.png)
![OpenVAS Config files](./images/openvas-url2.png)
## Create Client Virtual Machine and Make it Vulnerable
- At the Azure portal, search for Virtual Machines and create a new Virtual Machine (Resource group: Vulnerability-Management, VM Name: Win10-Vulnerable, same region as the OpenVAS VM, Virtual Network: Same as OpenVAS, Image: Windows 10 Pro, Size: Any size with 2 vCPUs, Add Username/ password, Networking: Same Vnet as OpenVAS, then Create the VM)
![Vulnerable VM ](./images/vm-v.png)
- After the VM has been created, ensure you can RDP into it with the appropriate credential. After logging in, make the VM vulnerable by
first disabling the windows firewall (type the Wf.msc command in the search bar → click on windows defender Firewall Properties → disable domain, public and private firewall state. Select “Apply” and “Ok”.), install an old Version of firefox: Firefox Setup 97.0b5, Install an old version of VLC Player: vlc-1.1.7-win32 and install an old Version of Adobe Reader: 10. Restart the VM and leave the VM for now.
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
