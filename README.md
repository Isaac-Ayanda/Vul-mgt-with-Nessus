# Vulnerability Management Lab Project with Nessus
#### In this lab, I introduced unique flaws such as installing outdated softwares, left unpatched, and more in the system environment and made concerted and continuous efforts to scan for vulnerabilities and performed remediation processes in order to lower susceptibility. 
## Objective

- Prepare Nessus Vulnerability Management Scanner
- Prepare Client Virtual Machine
- Perform Basic Scan against the Windows 10 VM
- Setup the VM To Accept Authenticated Scans and Install Outdated Softwares
- Perform Vulnerability Scan after providing credentials to Nessus
- Compare and Remediate Vulnerabilities
- Verify Remediations

### Environments and Technologies Used:

- Nessus Essentials
- VMware Workstation Player
- MS Windows 10 ISO
- Virtual Machine

<details close>

<div>

</summary>

<b>

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

## Prepare Client Virtual Machine
1. Install and launch VMware Workstation Player on your local machine to setup Windows 10 virtual machine. Click Player → File → New Virtual Machine → Browse then select the ISO image → Next → Add the name → disk size → Next → Customize Hardware (set Memory,CPU, Network Adaptor:Bridge (so that both VM and local machine can communicate with each other)) → Finish.
![Setup VM ](./images/vm-v.png)
2. As VM launches, click next and complete the installation of Windows 10 Pro accordingly, including username and password.
![Win 10 VM Setup](./images/vm-v2.png)
![Win 10 VM Setup](./images/vm-v3.png)
![Win 10 VM Setup](./images/vm-v4.png)


## Perform Basic Scan against the Windows 10 VM
1. This first basic scan is to confirm that all the setup is in order and you can get a scan result. First we locate its IP4 address by login into the VM: In the search field on the Start bar → type CMD → ipconfig. 
![identify the IP of the Windows 10 VM](./images/ipaddress.png)

2. Ping the VM from our local machine in this case; using ```ping 10.0.0.187 -t``` to confirm if we can reach it. But it timeouts. 
![Windows 10 VM timeouts](./images/iptimeouts.png)
3. We would need to disable the internal firewall in the VM. Type wf.msc in the search bar on the VM to launch the Windows defender firewall console. To disable the firewall properties we turn off the firewall state for the Domain Profile, Private Profile and Public Profile. After this, notice that the IP pinging stops timing out showing that the VM is now reachable.
![Disable defender](./images/wf.msc.png)
![Disable defender](./images/wf.msc2.png)
![VM stops timing out](./images/VM-reachable.png)
4. Next, perform a basic scan to confirm that the settings are in order: At Nesses Essentials web portal, click New Scan → Basic Network Scan → Add Name (You can name it any name), Targets (add the IP of the windows 10 VM)  → Save. Click on the play button to launch the scan. 
![Basic network Scan](./images/BN-scan.png)
![Basic network Scan](./images/BN-scan2.png)
![Basic network Scan](./images/BN-scan3.png)
![Basic network Scan](./images/BN-scan4.png)

5. Further vulnerabilities are detected when credentials are used than the basic scan with no credentials. When the scan has finished we can click on it to display the scan results. Nessus uses colours to indicate the vulnerabilty severity level. Critical, High, Medium, Low and Info. We can also click on the Vulnerability tab next to the Host tab to see more details. We can click on each result listed, review the description and implement the suggested solution to remediate the vulnerability. Clicking the 'Target Credential Status...', from the description and output, we see that Nessus detected SMB on port 445 but no credentials were provided. SMB local checks were not enabled.
![Basic Scan result](./images/BN-scan5.png)
![Basic Scan result](./images/BN-scan6.png)
![Basic Scan result](./images/BN-scan7.png)
![Basic Scan result](./images/BN-scan8.png)

## Setup the VM To Accept Authenticated Scans and Install outdated softwares
1. Next, we go to the VM and launch the Services pane by typing services in the search bar. Enable Remote Registry and turn it on (this will allow the scanner to connect to the VM registry and crawl to look for insecure configurations): Double click on it → Start Type: Automatic → Apply/Start → OK → Close services pane.
![Launch services window on VM](./images/services.png)
![enable remote registry](./images/enable-rr.png)
![enable remote registry](./images/enable-rr2.png)
![remote registry enabled](./images/enable-rr3.png)

2. Ensure file and printer sharing are turned on the VM. 
![enable file & printer sharing](./images/fandpshare.png)
![enable file & printer sharing](./images/fandpshare2.png)

3. Change notification settings to 'Never notify' in the User Account Control Settings, to disable it on the VM. 
![change User Account Control](./images/useraccountc.png)
![change User Account Control](./images/useraccountc2.png)

4. Add a Key to the registry to further disable user account control for the remote account that will be used to connect to the computer. Go to HKEY_LOCAL_MACHINE → SOFTWARE → Microsoft → Windows → CurrentVersion → Policies → System → then create 'LocalAccountTokenFilterPolicy' and set the value to 1. Then Restart the VM.

![Add a key to registry](./images/addkeytoregistry.png)
![Add a key to registry](./images/addkeytoregistry2.png)
![Add a key to registry](./images/addkeytoregistry3.png)
![Add a key to registry](./images/addkeytoregistry4.png)
![Add a key to registry](./images/addkeytoregistry5.png)

5. Install outdated softwares. Like, VLC player version 1.1.7, Firefox, Adobe reader version 10
![Install outdated softwares](./images/outdateds1.png)


## Perform Vulnerability Scan after providing credentials to Nessus
1. Configure the last Basic scan parameters in preparation for authenticated scan. Click on Windows 10 Single Host (i.e the last scan) → Configure → Credentials → click Windows → Add Username and Password → Save. 
![Edit Basic scan](./images/editbasicscan.png)

2. Then run the scan again. We have enable credential scanning and configured the VM to accept remote scans. Nessus scans the common open ports, inspect the registry, inspect the services and file systems to discover vulnerabilities.
![Run the scan again](./images/rundscana.png)
![Run the scan again](./images/rundscana2.png)


## Compare and Remediate Vulnerabilities
1. After Nessus has complete the scan, click on it to view the scan details. We see that from the latest scan the VM has 43 critical vulnerabilities, out of wich 42 marked as high, 9 vulerabilities marked as medium and 188 marked as info. From the foregoing we can see that credential scan allows more granular scan and enables more vulnerabilities to be discovered.
![the scan report before](./images/scanreportb.png)
![the scan report latest](./images/scanreport.png)

2. Click on the Vulnerabilities tab to see the list of findings. By clicking on each we see the details and suggested steps for remidiation. We can also click on the Remidiations tab and see the high level steps to take immediate to lower the vulnerabilities, like (patching) run Windows O/S updates till there are no updates, upgrading Firefox, Adobe and VLC player to the latest version or uninstalling the softwares if they are no more being used.
![the scan report](./images/listoffindings.png)
![Remidiations Actions suggested by Nessus](./images/listofremidiation.png)


## Verify Remediation
- Log back into your Win10-Vulnerable VM
Install the latest version of all softwares and restart the VM  (or uninstall the outdated software Adobe Reader, VLC Player, and Firefox). Run Windows updated over and over again, untill there are no more updates. Restart the VM and verify remediations by runing another scan via Nessus. Check the scan details, apply remidiation steps. Take these steps till vulnerabilities report is low or at an acceptable level.
![Perform software updates](./images/adobe-update.png)
![Uninstall unused softwares](./images/uninstall.png)
![Perform Windows Update](./images/windows-updates.png)
![Perform vulnerability scan again](./images/performscan.png)
![Vulnerabiltiy low after scan](./images/performscan2.png)
![Vulnerabiltiy scan report](./images/performscan30.png)
![Vulnerabiltiy scan report](./images/performscan3.png)

## Conclusion
Vulnerability management is about continuously searching for vulnerabilities and taking remediations steps. In an organization or enterprise network, it is important to have third party/ operating systems patching setup, tested and deployed at regular intervals especially putting in place automated patching after taking the remidiation steps. Also, there will be the need to put in place standards and procedures while working with variuos departments, performing automated network and resources scans (using possibly a domain account) to continously identify and remidiate vulnerabilities, using a secured build standard updates (already remidiated) before they are applied especially to production environments.

