# configuring-on-premises-Active-Directory

<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" height="40%" width="70%"alt="Microsoft Active Directory Logo"/>
</p>

<h1>Configuring Active Directory (On-Premises) Within Azure</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<!-- <h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com) -->

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create Resources
- Ensure Connectivity between the client and Domain Controller
- Install Active Directory
- Create an Admin and Normal User Account in AD
- Join Client-1 to your domain (mydomain.com)
- Setup Remote Desktop for non-administrative users on Client-1
- Create additional users and attempt to log into client-1 with one of the users

<br />
<h2>Setting Up a Resource Group and Domain Controller in Azure</h2>

Create Resource Group 
<br /> 
![image](https://github.com/user-attachments/assets/efaac63f-ac84-47af-8e10-f5971f086419)

Create a Virtual Network and Subnet
![image](https://github.com/user-attachments/assets/6aac93a6-08af-4027-b4b8-6fc82a6f2d1d)

Create a Domain Controller VM (Windows Server 2022) named “DC-1”
![image](https://github.com/user-attachments/assets/fda9d8d6-d077-499b-8eec-e643e69ee323)

Set up another VM by naming it "client-1"
![image](https://github.com/user-attachments/assets/7f035a0f-c36c-4327-9ee2-b5bc560273b5)


Once the VM is created, we'll move on to setting the Domain Controller's NIC Private IP address to be static.
![image](https://github.com/user-attachments/assets/f6c8113d-9a22-4ad0-bacc-9081f5ac8ffe)

Search up Remote Desktop Connection and proceed to log in with the dc-1 public IP address with credentials using 
<p/>
  
Username: labuser

<p/>
  
Password: Cyberlab123!

![image](https://github.com/user-attachments/assets/a4640763-29a7-413d-a490-3a7f05df24c2)
![image](https://github.com/user-attachments/assets/3ac50b6a-cd1e-45d0-8e1a-3e2efa1ea265)

Once logged in, go to the search bar, right click, click run, and type wr.msc to disable the firewall.
![image](https://github.com/user-attachments/assets/218b5948-bb82-423c-b7c4-91c3ead8414b)
<p/>
  
Make sure Domain Profile, Private Profile, and Public Profile's firewall state is turned "off".
![image](https://github.com/user-attachments/assets/973936cf-1d72-4050-a5fb-0b1681963a89)

<p/>

Head back to Azure to set Client-1's DNS settings to DC-1's Private IP address (You can find dc-1's private IP address in its properties).
![image](https://github.com/user-attachments/assets/d3ac8c21-5aa0-47f2-b982-0d8caf9be89b)

Go to the main virtual machine page and restart Client-1.
![image](https://github.com/user-attachments/assets/02cab635-71e3-4007-9a9f-f94ab3ac5f92)


Client-1 is now refreshed, so we'll be using Remote Desktop to log in to client-1 by using its IP address.
<p/>
  
With the use of Powershell, we'll be able to make sure the ping is successful between dc-1 and client-1.
<p/>
  
Run "ipconfig /all" = The output will result in being DC-1's private IP address (10.0.0.4)
![image](https://github.com/user-attachments/assets/3db55b80-4019-4755-bf72-7513844f9d96)
![image](https://github.com/user-attachments/assets/1578a1c1-5ae3-4a82-8a9b-8dfe89303843)

<br />
<h2>Installing Active Directory</h2>

To install Active Directory, go ahead and use Remote Desktop and log into DC-1.
<p/>
  
Search Server Manager > Add Roles and Features > Server Roles > Install Active Directory Domain Services 
![image](https://github.com/user-attachments/assets/083dd0f4-c4f0-4b3f-8363-129f64daa075)

<p/>

Go back to Server Manager and at the top right, click on the flag to deploy configuration.
<p/>

Add new forest > root domain name > mydomain.com
  
![image](https://github.com/user-attachments/assets/1ce940f8-9417-48e1-bbe3-0809f21e9332)

<p/>
  
Example purposes, password will be Password1
![image](https://github.com/user-attachments/assets/2ad7084b-df9a-45a9-bc80-0456544742a2)

<p/>

Uncheck "Create DNS delegation options" and proceed to install to the very end.
![image](https://github.com/user-attachments/assets/fa14c5a4-0ba9-440e-9fae-495e23b32691)

DC-1 will restart after installing Active Directory. 

After restarting, log back into DC-1 as user: mydomain.com\labuser

<p/>

<br />
<h2>Create an Admin and Normal User Account in AD</h2>

Open up Active Directory Users and Computers (ADUC) in the search bar and create an Organizational Unit (OU) and name it "_EMPLOYEES" as it's spelled.

<p/>
  
Right Click Mydomain.com > New > Organizational Unit > "_EMPLOYEES".
![image](https://github.com/user-attachments/assets/a38a180e-e960-4418-8fcb-3d74b7de46c6)

<p/>

We'll add another Organizationl Unit, but we'll name it "_ADMINS".
![image](https://github.com/user-attachments/assets/ef40c804-956f-4002-bfb0-aa326c7ad7e9)

<p/>

Create a new employee named "Jane Doe" and use the same password with the username "jane_admin" and password "Cyberlab123!".
![image](https://github.com/user-attachments/assets/1984d443-00c0-4ede-9ba6-d42157f82e38)

<p/>

Proceed to add Jane to the "Domain Admins" Security Group.

<p/>
  
Right click Jane > Properties > Member Of > "Domain Admins> Apply > Ok
![image](https://github.com/user-attachments/assets/b86489d3-e114-4f31-bd86-ec0b909317fb)

<p/> 

Log out and close the connection to DC-1 and log back in using "mydomain.com/jane_admin" (will be the admin account we use).

<p/>
  
<br />
<h2>Join Client-1 to the domain </h2>

To join Client-1 to the domain (mydomain.com), login to Client-1 as a local admin using "labuser"
![image](https://github.com/user-attachments/assets/3d27a958-0600-4878-bbd4-e90db35c7b14)

<p/>
  
Right click the start button and click System. Find Rename this PC (Advanced) > mydomain.com
![image](https://github.com/user-attachments/assets/a72816cb-dd5d-411a-8527-9838b282f18c)

  
<p/>

Login to DC-1 as Jane and verify Client-1 shows up in the ADUC.
If Client-1 shows up, create a new OU named "_CLIENTS" and add Client-1 to it.

![image](https://github.com/user-attachments/assets/5a4c511a-ac2f-4fd9-8799-a35f4621e628)


<p/>

<br />
<h2>Setup Remote Desktop for non-administrative users on Client-1</h2>

To start setting up Remote Desktop for non-adminsistrative users on Client-1, log into Client-1 as mydomain.com/jane_admin.
![image](https://github.com/user-attachments/assets/5098853c-20c6-418d-bb95-89abb56990b6)


<p/>

Right click start > Settings > Remote Desktop > User Accounts> Allow "Domain Users" to access remote desktop.
![image](https://github.com/user-attachments/assets/a361debd-a654-4ea1-a3e3-2ba462d3aaa5)

<p/>

In doing this action, we can now log into Client-1 as a normal non-adminstrative user now.


<br />
<h2>Create additional users and attempt to log into Client-1 as one of the users</h2>

To start this, we must log into DC-1 as jane_admin and proceed to open up Powershell _ise as an administrator
![image](https://github.com/user-attachments/assets/17aa662b-b08c-4fbd-8518-900957623114)

<p/>

At the top left click "new script" and copy/paste the script into it and click enter to run. Many accounts will begin to get created.
![image](https://github.com/user-attachments/assets/782279a7-7fc7-42ef-9c86-3b9128953373)
![image](https://github.com/user-attachments/assets/c83f99e4-6ac0-48ae-a56d-190c3a3e0a53)



<p/>

When most of the usernames are created, go and open ADUC and analyze the accounts in the _EMPLOYEES OU. 
![image](https://github.com/user-attachments/assets/929b1e3c-ab08-4d9e-9841-fdb8ba3ec81a)


<p/>

To test this out, choose a random user and use that username to log in to Client-1.
<p/>

  The password will be in the script that we pasted in in Powershell.
![image](https://github.com/user-attachments/assets/2d6d24c8-f75a-46e2-b6bc-aca42dfd2dd9)
![image](https://github.com/user-attachments/assets/69674278-3d9c-4120-bf68-1d1ca71e4b17)

<p/>
  
This will conclude the process of using Azure to create a Resource Group, 2 separate Virtual Machines (DC-1, Client-1) preparing Active Directory, deploying it, and creating users through Powershell! Many steps were taken during this process and it's important to pay attention to the little details for it can cause small errors and set you back.









