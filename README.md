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

<h3>Step 1: Setup Resources in Azure</h3>

Make two virtual computers. The first one will be the boss (Domain Controller). Call it DC-1 and use Windows Server 2022. Remember the Virtual Network (Vnet) and resource group that get made!

![image](https://github.com/user-attachments/assets/5e49fc64-e774-4cbc-9336-5802f2ae9edb)

(i) Make DC-1’s internet card have the same IP all the time:
Go to DC-1’s network settings → Click networking → Click the link next to the network thing → IP Configurations → ipconfig1 → Change from dynamic to static so the IP doesn’t change.

![image](https://github.com/user-attachments/assets/41d8f494-3bf6-4007-a2ca-79cf6e23529c)


<h3>Step 2: Setup Resources in Azure</h3>

Go to Client-1's Virtual Machine. On the left, click Networking, then click the link next to NIC. Click DNS server, pick Custom, and type in DC-1's private IP address. Click Save.

When it's done updating,  Restart Client-1.

![image](https://github.com/user-attachments/assets/b8694e3e-2c24-4b29-8f61-eda93bdc0fe7)


![image](https://github.com/user-attachments/assets/19f4274a-6f43-44c5-8e73-b7fe0a784357)

<h3>Step 3: Setup Resources in Azure</h3>

Log in to Client-1 with Microsoft Remote Desktop, open the command line, and type ping -t 10.0.0.4 to check if DC-1 (10.0.0.4) is online, the Command line will start to ping DC-1 successfully.
note: we can open PowerShell and run 'ipconfig /all' The output for the DNS settings should show DC-1’s private IP Address
 
![image](https://github.com/user-attachments/assets/a281c01a-d467-4eec-b897-080e2d48817f)

<h3>Step 4: Install Active Directory</h3>

Log into DC-1, open Server Manager. click "Add roles and features." At Server Roles, Make sure to check "Active Directory Domain Services." Select next and finish installing

![image](https://github.com/user-attachments/assets/4f139380-e5a5-45ae-bc6a-f0c172cad819)

Next, Click the flag in the top right of Server Manager. Pick "Promote this server to a domain controller." Choose "Add a new forest" and type mydomain.com. Click next, make a password, click next again, follow the steps, and hit install!

![image](https://github.com/user-attachments/assets/7d718eba-5312-42ae-b4fb-cacbeaa81a75)


![image](https://github.com/user-attachments/assets/48f601b8-9129-45de-a6a7-02ef3896f5ea)

Next, DC-1 will automatically restart. Log back into DC-1 as user: mydomain.com\labuser


![image](https://github.com/user-attachments/assets/f5ebd2fc-d5c9-4be5-9fba-6b60b55ef691)


<h3>Step 5: Create a Domain Admin user within the domain</h3>

On DC-1, open Server Manager, click Tools in the top right corner, and select Active Directory Users and Computers.

![image](https://github.com/user-attachments/assets/b6dd47ce-7f8e-4f43-be03-162f0db292e6)


Right-click mydomain.com, select New, then choose Organizational Unit to create two folders: name one _EMPLOYEES and the other _ADMINS.


![image](https://github.com/user-attachments/assets/e47010e2-c561-4153-a971-c35fb91339da)


Next, Right-click mydomain.com and click "Refresh" to move the new organizational units to the top, then navigate to the _ADMINS organizational unit, right-click, select "New" and "User," enter "Jane Doe" for the first/last name and "jane_admin" for the login name, create a password, uncheck all boxes, click "Next," and finally select "Finish.

![image](https://github.com/user-attachments/assets/422b197c-5508-4e4b-ac0d-7451d34385ff)

Navigate to the _ADMINS organizational unit, right-click Jane Doe, select Properties, click the "Member Of" tab, click Add, type "Domain Admins," click Check Names, then OK and Apply; finally, log out of DC-1 as "labuser" and log back in as "mydomain.com\jane_admin."

![image](https://github.com/user-attachments/assets/ed732cfb-0599-4fb0-bf01-fc790e03bd7d)


<h3>Step 6: Join Client-1 to your domain (mydomain.com)</h3>

Log back into Client-1 using Microsoft Remote Desktop as the original local admin (labuser), right-click the start menu, select System, then choose Rename this PC (advanced) -> Change, select "Domain" under Member of, enter "mydomain.com" and click OK, followed by entering the username "mydomain.com\jane_admin," typing the password, and pressing OK

![image](https://github.com/user-attachments/assets/acb5ea11-0921-427e-b248-dca1eddf9224)
![image](https://github.com/user-attachments/assets/f19b822c-098d-4c65-a220-6e28ed51c36b)
![image](https://github.com/user-attachments/assets/1b656c0f-83a4-4fb2-bdfd-5e87e14b96ad)


<h3>Step 7: Setup Remote Desktop for non-administrative users on Client-1</h3>

Log back into Client-1 using mydomain.com\jane_admin, right-click the start menu, select System, then go to Remote Desktop under User Accounts, click "Select users that can remotely access this PC," click Add, type "domain users," click Check Names, and press OK, then OK again.

![image](https://github.com/user-attachments/assets/6ae8b2cb-d565-45f3-907d-6a80e64f78e0)


<h3>Step 8: Create a bunch of additional users and attempt to log into client-1 with one of the users</h3>

Log back into DC-1 as jane_admin, open PowerShell_ise as an administrator, create a new script, paste the script's contents, and run it using the green arrow button;

![image](https://github.com/user-attachments/assets/bc97e972-02d8-4502-b533-bcf875952e10)

once the users are created, navigate to Active Directory Users and Computers (mydomain.com → _EMPLOYEES) to view the accounts, then log into Client-1 with one of them.

![image](https://github.com/user-attachments/assets/4910e48e-3dab-4391-82d7-f4aa53ab9675)

Lets log into Client-1 with one of the users that were created (in our instance "jof.luteg", password will be Password1)

![image](https://github.com/user-attachments/assets/7bbdd049-79d2-45b6-b955-d2dbd5042af8)

![image](https://github.com/user-attachments/assets/6507e204-7374-4ee7-990c-d76865e0b9ef)

There you have it. You have implementated on-premises Active Directory and created users within Azure Virtual Machines!

