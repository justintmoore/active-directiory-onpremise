<p align="center">
    <img src="https://i.imgur.com/8EzPyDZ.png" alt=osTicket logo"/>  
    

    
## Tools, Utilities, Services Used
- Microsoft Azure <br>
- Remote Desktop <br>

## Environments Used
- Windows 10 <br>
- Windows Server 2022
  

## Program Walkthrough
<p align="center">
    Objective: Lay out the foundation for our Active Directory architecture . We are going to set up a virtual network within Azure, create a Windows 10 VM for our client, and use Windows Server 2022 VM to use as our future AD Domain Controller. Once that foundation is setup, our later labs will install and configure Active Directory, join our client VM to the domain, we will create users using powershell, as well as configure, and manage Group Policy to manage accounts. We may add more to this, stick around and see!
</p>
<br>

<p align="center">
    Active Directory (AD) is a directory service developed and maintained by Microsoft. It is used to centrally manage user accounts, passwords, permissions, and devices across a network. AD enables organizations to scale efficiently by organizing and securing access to resources, such as files, printers, and applications, while also enforcing policies and authentication protocols.
</p>
<br>

<p align="center">
   First off, we need to set up our resource group in Azure to house our virtual machines (VM). If you don't know what a resource group is, you can make your way to my repository and look for "Exploring Azure".
</p>

<p align="center">
    <img src="https://i.imgur.com/BeryOun.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br> 

<p align="center">
   Once your resource group is created, we can create our virtual network (VNet). While creating the VM would automatically create a VNet, I would like to get some more reps in! In the search bar above type in Virtual Network, and select it. Select create virtual network. 
</p>

<p align="center">
    <img src="https://i.imgur.com/ZuOfx1v.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br> 

<p align="center">
   Your VNet should be in the resource group we just created, so for me that would be "Active-Directory-Lab". Choose a Vnet name, and choose the region chosen for your resource group. Highly important that resource group and VNet should be in the same region. We can leave default settings for everything else, and click Review and Create.
</p>

<p align="center">
    <img src="https://i.imgur.com/BQV2Enj.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br> 

<p align="center">
   Next comes out VM that will serve as our Domain Controller (DC). A Domain Controller is simply a centralized Windows server that manages user authentication, security policies, and access to resources in a network. Go to create VM, and start to fill out your details. Ensure that you place your VM region in the same place as your VNet, and resource group. For Image you will choose Windows Server 2022, with 2 vcpus and 8 GiB of memory. It should resemble this.
</p>

<p align="center">
    <img src="https://i.imgur.com/b3Iulg7.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br> 

<p align="center">
   Choose your login credentials. Make sure to take note and store them somewhere in case you forget. Make sure that 
</p>
<br>
<p align="center">
   Choose your login credentials. Make sure to take note and store them somewhere in case you forget. Click next until you get to Networking. Make sure your Virtual network is set to the VNet you created. Leave everything else as defauly and select Review and create.
</p>
<br>
<p align="center">
   We must now create our client machine, go back to VMs -> Create VM. Slect the same resource group as our other resources, choose 2 vcpus, and 8 GiB of memory, and a Windows10 image. Continue on the Networking, where you will select our created VNet, then review and create. Note that if you're having trouble selecting a region, backing out of VM, and going back will fix the region issues.
</p>

<p align="center">
    <img src="https://i.imgur.com/pIRVAgw.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br> 

<p align="center">
   After VM is created, set Domain Controllers NIC Private IP address to be static. We are doing this because later on in this lab, we will configure our client device's dns settings to point to our domain controller. If by chance, that domain controllers IP changes, then the client loses ability to resolve domains. It also adds consistency within our setup. Navigate to your domain controller VM -> Network Settings -> Find the Network Interface box, and click it.
</p>

<p align="center">
    <img src="https://i.imgur.com/VX2IA4I.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br> 

<p align="center">
   Towards the bottom you will see a "ipconfig1" link, click that. Under Private IP address settings, choose static. Save it.
</p>

<p align="center">
    <img src="https://i.imgur.com/2PO2WBd.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br>

<p align="center">
   Normally we wouldn't do this, but we log into our dc-1 VM, disbale the firewall for testing connectivity. Go to VMs, highlight and copy dc-1 public IP. Open up Remote Desktop Protocol, paste the IP, and connect. This will take you to the Server Manager Page.
</p>

<p align="center">
    <img src="https://i.imgur.com/jwshcai.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br>

<p align="center">
   Now that you are in, lets go to the Windows icon on the bottom left corner, right clight, and choose run. Type in "wf.msc". 
</p>

<p align="center">
    <img src="https://i.imgur.com/u8gyoqR.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br>

<p align="center">
   This opens up your Windows Defender Firewall. Under Overview click the link "Windows Defender Firewall Properties" -> Under Domain Profile, Private Profile, and Public Profile, turn the "firewall state" to OFF. Apply changes, and click ok.
</p>

<p align="center">
    <img src="https://i.imgur.com/mRaoP8U.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br>

<p align="center">
   Now we must work on our client. We have to set the clients dns settings to point to the domain controllers IP address. So, find your domain controllers private IP, and copy it. Go to the client VM -> Network Settings -> Network interface -> DNS Servers. Now change the DNS servers from "inherit from virtual network, to custom. Paste the IP address.
</p>
<br>
<p align="center">
   This will now allow our client device to query the domain controller to resolve IPs. For example, if the client searches "google.com", the client will look to the domain controller as it's DNS Server.
</p>

<p align="center">
    <img src="https://i.imgur.com/EMbRIBW.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br>

<p align="center">
   Now that we have updated the clients dns settings, we must reset the VM, for changes to take affect. There is alot of back and forth so I advise that you read a step, do a step.
</p>
<br>

<p align="center">
So head back to the VM page, select the client box, and choose restart at the top. After it restarts, we will login into the client VM and attempt to ping the domain controller. 
</p>
<br>

<p align="center">
Get the public IP of the client VM. Open up RDP, paste the IP, and connect.
</p>
<br>

<p align="center">
Now that we are logged on to the client device. Go BACK to the VM page, and collect the private IP for the domain controller VM. If you happened to remember the IP, then more power to you!
</p>
<br>

<p align="center">
Go back to your client RDP session, Open up Powershell. Use the command ping <domainControllersIP>. If you get a Reply, you have successful connected your client VM to you Domain Controller VM.
</p>

<p align="center">
    <img src="https://i.imgur.com/2Xz3tS4.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br>

<p align="center">
Now we must check the DNS settings to ensure that we are connected to the Domain Controller VM. In that same window use the command "ipconfig /all". Towards the bottom you will see DNS Servers, ensure that it indeed matches. That wraps up this portion of the lab, next we install and deploy Active Directory.
</p>

<p align="center">
    <img src="https://i.imgur.com/3lGW7mE.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br>
