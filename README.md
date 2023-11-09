
# On-premises Active Directory Deployed in the Cloud (Azure)
Active Directory serves as a centralized system for efficiently managing numerous user accounts, including account information, passwords, and permissions, alongside overseeing a large-scale management of devices. This guide provides details on how to implement on-premises Active Directory using Azure Virtual Machines..<br />

## Environments and Technologies is being used 

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

## Operating Systems being Used

- Windows Server 2022
- Windows 10 (21H2)

## High-Level Deployment and Configuration Steps

- Domain Controller VM (Windows Server 2022) named “DC-1”
- Domain Controller’s NIC Private IP address to be static
- ICMPv4 (ping) was allowed on the Domain Controller
- Create an Admin and Normal User Account in Active Directory
- Join Client to domain
- Attempt to login Client-1 with one of the users

| Terms | Descriptions|
|-------| ------------|
| Resource Group | A resource group is like a container that encompasses associated resources within an Azure solution. It has the flexibility to comprise all resources related to the solution or selectively include only those resources intended for group management.
| Virtual Machine | A virtual machine is a digital representation of a physical computer. Software for virtual machines enables the execution of programs and operating systems, data storage, network connections, and various computing tasks. Similar to physical computers, virtual machines need regular maintenance, including updates and system monitoring.
| Remote Desktop | Remote desktop refers to the capability of connecting to and utilizing a distant desktop computer from a separate computer. Users of remote desktop can retrieve their desktop, modify files, and utilize applications as if they were physically present at their desktop computer.
| Active Directory Domain Services | are the core functions in Active Directory that manage users and computers and allow sysadmins to organize the data into logical hierarchies.
| Powershell |  is a cross-platform task automation solution made up of a command-line shell, a scripting language, and a configuration management framework. PowerShell runs on Windows, Linux, and macOS.
| Domain Controller |  are the servers in your network that host AD DS. DCs respond to authentication requests and store AD DS data
|Organizational Unit (OU) | Organizational units (OUs) in an Active Directory Domain Services (AD DS) managed domain to let you logically group objects such as user accounts, service accounts, or computer accounts. You can then assign administrators to specific OUs, and apply group policy to enforce targeted configuration settings.
|Join Client to Domain |Enlisting a computer into a domain entails linking it to a network overseen by a central server called a domain controller. A domain constitutes a cohesive arrangement of computers, users, and resources, streamlining centralized management by a network administrator. In essence, the act of connecting a computer to a domain serves to enhance security, streamline network administration, and boost overall productivity.. |

## Deployment and Configuration Steps
Initially, the creation of a resource group is required to facilitate the inclusion of your virtual machines, namely the Domain Controller (DC-1) and the Client Virtual Machine (Client-1). For the Domain Controller VM, a Windows Server 2022 system image will be employed, which represents a serialized copy capturing the complete state of a computer system stored in a non-volatile form, such as a file.. 

<p align="center">
<img src="https://i.imgur.com/lKmRcIy.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
The Client VM (Windows 10) named “Client-1” was created with the same Resource Group and Vnet that was created in DC-1. 
<p align="center">
<img src="https://i.imgur.com/w34Z93S.png" height="80%" width="80%" alt="client 1 vm settings"/>
</p>
Private IP address set to static (static IP addresses are necessary for devices that need constant access.)
<p align="center">
<img src="https://i.imgur.com/n3KceWF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

Secondly, Verify the connection status between the client device and domain controller by accessing Client-1 through Remote Desktop Connection (RDP) and initiating a continuous ping to DC-1's private IP address using the "ping -t" command. Ensure that ICMPv4 (ping) is permitted on the Domain Controller's (DC-1) Firewall in Windows Firewall settings (specifically under Core Networking Diagnostics - ICMP Echo Request - ICMPv4-In). Upon re-logging into Client-1, confirm the success of the ping operation.

<p align="center">
<img src="https://i.imgur.com/FWLTP8X.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

Pictured below displays that the ICMP rule has been allowed on the Windows firewall for inbound traffic:
<p align="center">
<img src="https://i.imgur.com/f4i0pdh.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>  
</p>
<br />  
While operating in DC-1, we opted to 'add roles and features' to activate Active Directory Domain Services. Subsequently, DC-1 was promoted to serve as a Domain Controller (DC) for a new forest configured under the domain name mydomain.com. Following this setup, Remote Desktop was restarted, and we logged back into DC-1 with the user credentials mydomain.com\labuser.

<p align="center">
<img src="https://i.imgur.com/ipCHp9t.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p align="center">
  <img src="https://i.imgur.com/Ccbt7ak.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

Next, we configure the organizational units for the admins and employees in Active Directory (AD) while continuing to be in DC-1 (Remote Desktop Connection).  The accounts can now be viewed in Active Directory in the appropriate organizational unit. In the Active Directory, right-click on your <b>domain name</b> and move your mouse to hover <b>new</b>--><b>Organizational Unit</b> and left-click to create folders for your AD. We will create employees, admins, and security groups.

<p align="center">
<img src="https://i.imgur.com/D59IbY9.png" height="80%" width="80%" alt="Active Directory OU"/>
  </p>
</br>

Create a new OU named '_ADMINS' --> Create a new employee named **Karen What** (same password) with the username of **'karen_admin'**. Once the admin is created, add "karen_admin" to the "domain admins" security group.

<p align="center">
<img src="https://i.imgur.com/Qzlpsyk.png" height="80%" width="80%" alt="Add user to domain admins"/>
  </p>
  </br>
Log out and close the connection to dc-1 for the current user(mydomain.com\labuser) and log back in as "mydomain.com\karen_admin".
<p align="center">
<img src="https://i.imgur.com/WiQI5sG.png" height="50%" width="50%" alt="cmd displays new loggin user"/>
 </p>
  </br>
  
Next, we'll join Client-1 to the domain (mydomain.com); however, we must change the DNS on Client-1 to the private IP address of DC-1 so that we can properly add client-1 to the domain. Here we will select the NIC on client-1 to change the DNS to the private IP address of DC-1
 
  <p align="center">
    <img src="https://i.imgur.com/f2Vka1D.png" height="80%" width="80%" alt="select network interface client 1"/> </p>
    </br>
    Select 'DNS Servers'
      <p align="center">
    <img src="https://i.imgur.com/BlO0rjn.png" height="80%" width="80%" alt="select network interface client 1"/> </p>
    </br>
    Select the 'Custom' radio button for DNS server so that you can now enter the DC-1 private IP address.
  <p align="center">
  <img src="https://i.imgur.com/Rcjik7d.png" height="40%" width="40%" alt="select customer dns"/></p>
  </br>
  Now that we have successfully changed the DNS server to the private IP address of DC-1, we can add client-1 to the domain without error. You will receive a message letting you know that the client has been successfully added to the domain. This can be done by going to System > Rename This PC > enter domain name > select OK > select Apply. The update then requires a system restart.  
  <p align="center">
    <img src="https://i.imgur.com/v2B6jza.png" height="50%" width="50%" alt="add to domain"/></p>
    </br>
    A message displays that the client has been successfully added to the domain
    <p align="center">
  <img src="https://i.imgur.com/awUUK52.png" height="50%" width="50%" alt="message displays client added to domain"/>
  </p>
  Now, we can create our users that will be loaded into our <b>_EMPLOYEES OU</b> in the domain controller (DC-1). To create these employees we will run <b>PowerShell_ISE</b> as an <b>administrator</b>. A new File will be created then we can enter the pre-configured script into the file. When the script is run, random employees will be created.
  
<details>  
  <summary> <h6>pre-configured Powershell Script</h6> </summary>
  
  
```powershell
# ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password1"
$NUMBER_OF_ACCOUNTS_TO_CREATE = 10000
# ------------------------------------------------------ #

Function generate-random-name() {
    $consonants = @('b','c','d','f','g','h','j','k','l','m','n','p','q','r','s','t','v','w','x','z')
    $vowels = @('a','e','i','o','u','y')
    $nameLength = Get-Random -Minimum 3 -Maximum 7
    $count = 0
    $name = ""

    while ($count -lt $nameLength) {
        if ($($count % 2) -eq 0) {
            $name += $consonants[$(Get-Random -Minimum 0 -Maximum $($consonants.Count - 1))]
        }
        else {
            $name += $vowels[$(Get-Random -Minimum 0 -Maximum $($vowels.Count - 1))]
        }
        $count++
    }

    return $name

}

$count = 1
while ($count -lt $NUMBER_OF_ACCOUNTS_TO_CREATE) {
    $fisrtName = generate-random-name
    $lastName = generate-random-name
    $username = $fisrtName + '.' + $lastName
    $password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force

    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $firstName `
               -Surname $lastName `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_EMPLOYEES,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
    $count++
}
```
  
</details>
Here is the PowerShell script that has been loaded before executing it to generate 1000 random users.
  <p align="center">
  <img src="https://i.imgur.com/ez4THWm.png" height="80%" width="80%" alt="powershell with script loaded"/>
  </p>
  </br>
  Random users are created now after choosing to execute the code. Here we can now see the script loading the 1000 users:
<p align="center">    
  <img src="https://i.imgur.com/f2vKx8Y.png" height="80%" width="80%" alt="powershell execute code"/> </p>
  Those random Users are now reflected in Active Directory on the Domain Controller
  <p align="center">
  <img src="https://i.imgur.com/lHBM2nh.png" height="80%" width="80%" alt="active directory shows created users"/>
  </p>
  Attempt to login on Client-1 with a random user that has been created
  <p align="center">
  <img src="https://i.imgur.com/HFguOhB.png" height="80%" width="80%" alt="windows start menu shows login user"/>
  </p>
   <p align="center"><b><i>"THE END OF THIS LAB”</p></b></i>

