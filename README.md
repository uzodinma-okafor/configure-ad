<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
Hello again! This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines (VM).<br />


<h2>What is Active Directory?</h2>

Active Directory (AD) is a Microsoft directory service that runs on a Windows Server and allows administrators to manage resources, assign permissions, change account passwords, and control access to network resources within an organization. There is an on-premises software version of AD that's usually installed into computers. There is also a cloud based version of AD found on Microsoft Azure.

The purpose of this project is to set up and configure an on-premises Active Directory within Azure VMs.

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Part 1: Setup Resources In Azure
- Part 2: Ensure Connection between Client and Domain Controller
- Part 3: Install Active Directory and Admin Creation
- Part 4: Create X-Amount of Client Users using PowerShell Script

<h2>Deployment and Configuration Steps</h2>

<p>
  <h2>Part 1: Setup Resources in Azure</h2>
<img src="https://i.imgur.com/L3byC8z.png" height="80%" width="80%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/sLEEO5q.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

- Create the Domain Controller VM (Windows Server 2022) named “DC-1”
  
- Take note of the Resource Group and Virtual Network (Vnet) that get created at this time
-	Set Domain Controller’s NIC Private IP address to be static. DC-1 > Networking > NIC > IP Configurations
-	Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created for DC-1.
-	Ensure that both VMs are in the same Vnet (you can check the topology with the Network Watcher). Take a quick look below at a visual presentation of what we're doing:
  <img src="https://i.imgur.com/gBEzWB4.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
</p>
<br /><hr>
<p>
  
  <img src="https://i.imgur.com/wIKFQ4K.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
  </p>
<p>
<h2>Part 2: Ensure Connectivity between the client and Domain Controller</h2>
- Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with "ping -t (ip address)"
  
  - The outcome we should see is a perpetual ping. However, the request times out instead as seen in pic above. Let's fix that error.
</p>
<br /><hr>
<p>
   <img src="https://i.imgur.com/HO5a6A9.png" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/qWY1TqJ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
  </p>
  <p>
-	Login to the Domain Controller and enable ICMPv4 in on the local windows Firewall
  
  - Open "Windows Defender Firewall with Advanced Security" >> In left side window pane, click "Inbound Rules"
  - Expand the program window and sort Inbound Rules by "ICMPv4" Protocol >> select and right click the 2 Inbound rules starting with "Core Networking Diagnostics - ICMP Echo Request..." (where one has a "Private" profile and the other a "Domain" profile as seen in left pic above) and select "Enable Rule" for both rules
-	Check back at Client-1 to see the ping succeed. It should ping as seen in right pic above.
</p>
<br /><hr>
<p><img src="https://i.imgur.com/Hyhz52R.png" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/hVrvKjW.png" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/MJoDKvk.png" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/OG3pK2e.png" height="50%" width="50%" alt="Disk Sanitization Steps"/></p>
<p>
<h2>Part 3: Install Active Directory & Admin Creation</h2>
- Configure Active Directory

  - Click "notification" to Select: "Promote this server to a Domain Controller"
  - Select: "Add a new forest" (mydomain.com or your choice)
  - Choose a Password and make note of this
  - Complete Installation ("Next"; "Next"; "Next"; "Next" and "Install")
  - Allow the server to close, which will disconnect the Remote Desktop.
  - Restart and then log back into DC-1 as user: mydomain.com\labuser
</p>
<br /><hr>
<p><img src="https://i.imgur.com/kVVwcTQ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/OriaoYz.png" height="50%" width="50%" alt="Disk Sanitization Steps"/></p>
<p>
- Create Admin and Normal User Accounts in AD

  - Navigate to Active Directory Users and Computers (ADUC)
  - Create and take note of names and passwords:
    - an Organizational Unit (OU) called “_EMPLOYEES” (as in left pic above)
    - a new OU named “_ADMINS”(as in left pic)
    - a new employee named “Jane Doe” with the username of “jane_admin” (For practice purposes, select "Password never expires") (as in 3 pics in right pic above)
</p>
<br /><hr>
<p><img src="https://i.imgur.com/IQBdLLG.png" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/7RcFReS.png" height="50%" width="50%" alt="Disk Sanitization Steps"/></p>
<p>
- Add jane_admin to the “Domain Admins” Security Group

  - Select the _ADMIN Jane Doe and right click to Select Properties >> Select "Member Of" tab
  - Add Domain Users: "Domain" >> Select "Check Names" to open name options
  - Select "Domain Admins" >> Complete by Selecting "Ok"; "Ok"; "Apply"; "Ok" (Refer to pics above for reference)
  - Log out and close the Remote Desktop connection to DC-1
  - Log back in as mydomain\jane_admin
</p>
<br /><hr>
<p>
  <h2>Part 4: Create X-Amount of Client Users using PowerShell Script</h2>
  
  <img src="https://i.imgur.com/1Z00GXq.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
  <img src="https://i.imgur.com/PN4Prgi.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
  </p>
<p>
- Join Client-1 to your domain (din-okafor.com)

- (top 2 pics in black) From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address
- In Azure, Locate DC's Private IP address in the VM DC's Overview
- Open the VM Client-1 >> Select "Networking" >> Select the "Network Interface" link
- Select "DNS Servers" in the Left Column >> Choose "Custom" DNS Servers & enter the DC's Private IP address as the DNS Server
- From the Azure Portal, restart Client-1

- Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart)
  - Log into Client-1 (Remote Desktop) as original local admin (labuser)
  - (Bottom pic) Right Click Start menu >> Select "System" >> (1) Select "Rename this PC (advanced)" >> (2) Select "Change" >> (3) In "Domain" box type:din-okafor.com >> (4) Select "OK" >> (5) In Computer Name/Domain Changes box: -"din-okafor.com\jane_admin" and password >> (6) Select "OK" and restart when prompted
- Login to the Domain Controller via Remote Desktop >> Navigate to Active Directory Users and Computers (ADUC) >> Verify Client-1 shows up inside “Computers” container on the root of the domain
</p>
<br /><hr>
<p><img src="https://i.imgur.com/wAFGLkS.png" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/Q0hvZnn.png" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/H697YyZ.png" height="50%" width="50%" alt="Disk Sanitization Steps"/></p>
<p>
- Configure Remote Desktop for non-administrative users on Client-1

- Log into Client-1 as mydomain.com\jane_admin (top pic)
- Open system properties (2nd pic, 1) >> (2) Click “Remote Desktop” >> (3) Click "Select users that can remotely access this PC" >> (4) Click "Add" >> (see 3rd pic) Allow “domain users” access to remote desktop 
- Now, you can log into Client-1 as a normal, non-administrative user (The normal way to do this is with Group Policy because it allows you to change many systems at once)
</p>
<br /><hr>
<p>
<img src="https://i.imgur.com/AGiAIb8.png" height="80%" width="80%" alt="Create Random Users"/>
  <img src="https://i.imgur.com/BIqLVDs.png" height="80%" width="80%" alt="Create Random Users"/>
</p>
<p>
- Create a bunch of random additional users and try attempting to log into client-1 with one of the users

- Within DC-1 Remote Desktop 
- Open PowerShell ISE by right clicking to "Run as Administrator" 
- Open new file
- Paste the contents of [this script file](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) into it (randomly creating new users with "Password1" as their passwords for testing purposes)
- Open Active Directory and check the "_EMPLOYEES" OU to see the list of random users being added 

</p>
<br />
<hr>
<p> <img src="https://i.imgur.com/jubCZnM.png" height="80%" width="80%" alt="Create Random Users"/><img src="https://i.imgur.com/TgNAQAU.png" height="80%" width="80%" alt="Create Random Users"/><img src="https://i.imgur.com/oziMID3.png" height="80%" width="80%" alt="Create Random Users"/></p>
<p>
- Test by choosing random name and accounts

- Choose a random name, take note of account info
- Sign out of Client-1
- Log into Client-1 again, using new account name to test access
</p>
<br /><hr>

<p>
<img src="https://i.imgur.com/1UPzkSS.png" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/OXlitbp.png" height="50%" width="50%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/Mb2HwGb.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
</p>
<p>
- Fixing Common Account & Password Issues

- Log into DC-1 >> In ADUC, Navigate to: _EMPLOYEES >> Choose Name of employee whose account you want to adjust and Right Click to find properties
  - Select Account tab 
- Unlock Account when user is locked out (see 1st 2 pics above)
  - Check box to Unlock Account
- Reset Passwords (see last 2 pics in black margin above)
  - Right Click Name of Account you want to adjust
  - Scroll down the Drop Down Menu to "Reset Password"
  - Make new password to share with the employee/user
  - Can also have the employee/user change that password at his or her next login
  - Can also have the employee/user's account unlocked 
</p>
<br /><hr>

<p>
Thats is it ! In the [next tutorial](https://github.com/uzodinma-okafor/azure-network-protocols), we will go over various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups.
</p>
<br /><hr>
