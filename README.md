<p align="center">
    <img src="https://i.imgur.com/8EzPyDZ.png" alt=osTicket logo"/>  
    
### If you come across this page before it's finished, I apologize. Documenting unfortunately takes quite a bit of time, just know that this project is done. Just putting the finishing touches on it! :)
    
## Tools, Utilities, Services Used
- Microsoft Azure <br>
- Remote Desktop <br>

## Environments Used
- Windows 10 <br>
- Windows Server 2022
  

## Program Walkthrough
<p align="center">
    Objective: Lay out the Active Directory architecture. We are going to set up a virtual network within Azure, and use Windows Server 2022 VM to create a AD Domain Controller. We will then set up a Windows 10 VM as a client device, and join it to the domain. Once that foundation is setup, we will create users using powershell, as well as configure, and manage Group Policy to manage accounts. We may add more to this, stick around and see!
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
   Next comes out VM that will serve as our Domain Controller. A Domain Controller is simply a centralized Windows server that manages user authentication, security policies, and access to resources in a network. Go to create VM, and start to fill out your details. Ensure that you place your VM region in the same place as your VNet, and resource group. For Image you will choose Windows Server 2022, with 2 vcpus and 8 GiB of memory. It should resemble this.
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
   Choose your login credentials. Make sure to take note and store them somewhere in case you forget. Click next until you get to Netowrking. Make sure your Virtual netowrk is set to the VNet you created. Leave everything else as defauly and select Review and create.
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
   After VM is created, set Domain Controllers NIC Private IP address to be static. We are doing this because later on in this lab, we will configure our client device's dns settings to point to our domain controller. If by chance, that domain controllers IP changes, then the client loses ability to resolve domains. It also adds consistency within our setup. Navigate to your domain controller VM -> Netowrk Settings -> Find the Network Interface box, and click it.
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
    <img src="https://i.imgur.com/UGC7Q83.png" height="60%" width="60%" alt="placeholder"/>
</p>
<br>

