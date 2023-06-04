<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)


<h2>Deployment and Configuration Steps</h2>

What we'll be doing is creating a virtual network using Azure and within that network, we'll have a VM running Windows Server 2022 and another VM running Windows 10. The VM running Windows Server will be our Domain Controller, "DC-1". We will then connect the VM running Windows 10 to the domain. This VM will be called "Client-1". So first, lets create both VMs in Azure. Important note: Set DC-1'S NIC private IP address to be static. If set up correctly, our network topology, which can be viewed on the Network Watcher, will look like the second screenshot signifying that both VMs are on the same Vnet.
<p>
<img src="https://imgur.com/shItOzP.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<img src="https://imgur.com/z5dELz9.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
</p>
<br />

Next, we're going to ensure connectivity between the client and domain controller. Connect to Client-1 using RDP and ping DC-1's private ip address using ping -t x.x.x.x . This command executes a perpetual ping. Notice that the request is timing out. We need enable ICMPv4 traffic in on the local Windows firewall on DC-1. Enable the one highlighted and the one below it.
<p>
<img src="https://imgur.com/uSRLJZJ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<img src="https://imgur.com/lAUQ37U.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Observe the ping on Client-1, notice that the request is longer timing out. Connectivity is established.
<img src="https://imgur.com/O25E5Xf.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

Going back to DC-1, install Active Directory Domain Services through the Server Manager. After that, we're going to do some post-deployment configuration and promote the Server as a Domain Controller. Create a new forest and name it my.domain.com. Restart DC-1 and login in as the user mydomain.com\labuser.
<p>
<img src="https://imgur.com/lJIhDfB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<img src="https://imgur.com/any763P.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
<img src="https://imgur.com/uhUiU6N.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
</p>
<br />
Great! Now we're going to create some Organizational Units(OU). Go to Active Directory Users and Computers and create two Organizational Units called, "_EMPLOYEES" and "_ADMINS". 
<p>
<img src="https://imgur.com/RMAgE4D.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

Once we've done that, create a new employee named "John Doe" with the username of "john_admin". Make the password one you can remember. After creating the user, add this person to the "Domain Admins" Security Group. Log out of DC-1 and log back in as "mydomain.com\john_admin".
<p>
<img src="https://imgur.com/aEIE1do.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<img src="https://imgur.com/KhKqZzs.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
</p>
<br />

Now we're going to join "Client-1" to the domain. On the Azure Portal, change Client-1's DNS settings to DC-1's Private IP address. Restart Client-1. Login back in as labuser to "Client-1" and join it to the domain. Go to settings-> Rename this PC(advanced). When prompted, input the username and password of the new user created earlier. A pop up should appear reading "Welcome to the domain".
<p>
<img src="https://imgur.com/8fBHMyc.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<img src="https://imgur.com/HqYzWBm.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
</p>
<br />

Perfect! Now lets verify Client-1 is on the domain by logging back into DC-1, going to Active Directory Users and Computers, and checking the "Computers" folder. We should see Client-1 listed.
<p>
<img src="https://imgur.com/E1MjuCO.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

Next, log into Client-1 as mydomain.com\john_admin, or whatever username you created. What we're going to do is allow normal domain users to remote desktop into Client-1. Right-click the Windows button, select System-> Remote Desktop-> Select users that can remotely access this PC. Select add and type "Domain Users" and click "Check names".
<p>
<img src="https://imgur.com/eNSUgFC.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

Almost done! Login DC-1 and open Powershell_ise as an administator. Create a new file in Powershell, then go here: https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1 and copy and paste the script into it. Run the script. Observe the accounts being created. When the accounts are finished being created, go to "Active Directory Users and Computers" and open up the "_EMPLOYEES" OU and notice the list of users.
<p>
<img src="https://imgur.com/2kCt6fW.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<img src="https://imgur.com/Xskg6x0.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
</p>
<br />

Lastly, pick one of these users and attempt to log into Client-1 with one of these accounts. The password for the accounts can be found in the script. Do not forget to add mydomain.com\ then the username. Hopefully you were successful and enjoyed this tutorial :)
<p>
<img src="https://imgur.com/Yr1UpUY.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />
