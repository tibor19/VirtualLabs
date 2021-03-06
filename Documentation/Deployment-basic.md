# Basic deployment

This templates allows you to deploy a basic Remote Desktop Services scenario.

[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fintelequia%2FVirtualLabs%2Fmaster%2Fsimple-architecture%2Frds-base-azuredeploy.json)
<a href="http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2Fintelequia%2FVirtualLabs%2Fmaster%2FDeploy%2FArchitecture-basic%2Frds-base-azuredeploy.json" target="_blank">
    <img src="http://armviz.io/visualizebutton.png"/>
</a>

1. Fill the form. You can get info about each field if you hold the cursor over the info icon.
   * Choose your subscription and create or select an existing resource group.
   * Set the admin credentials. A member of Administrator Group account will be created. Remember this credentials to access to VMs after de deployment. The supplied password must be between 8-123 characters long and must satisfy at least 3 of password complexity requirements from the following: contains an uppercase character, contains a lowercase character, contains a numeric digit or contains a special character.
   * Set the name of the domain that will be created and that all the VMs will join it. The domain name must contain a dot '.' like 'mydomain.local'.
   * Set a prefix for the gateway public DNS. The fqdn will look something like 'contoso-gateway.cloudapp.azure.com'.
   * Select the size for Frontend, and Backend and Session Host VMs. [Check which VM sizes are available by region](https://azure.microsoft.com/en-us/regions/services/).
2. Now wait for the green check in the notifications hub. It will take **1 to 2 hours** for completes the resources deployment.
3. Export and copy self-signed certificates to client computers
   * A self-signed certificate have been used for the deployment. This certificate will need to be installed on the local client machines. See how do it following the steps described in [this guide](Documentation/UserAccessWebCert.md). 
4. Configure Session Collection
   * Set Domain Users as user group
   * [Publish your first remoteApp ](./RemoteDesktopRemoteApp.md)
5. Connect to the deployment through RD Web Access and RD Gateway
    * Enter the DNS name for the deployment in your browser https:// **{your-dns-label-prefix}** . **{location-of-resource-group}** .cloudapp.azure.com/RDWeb
    
![RDS basic architecture](./images/Architecture%20basic.png)

## What does this template do?

The first Azure Resource Manager template is ‘rds-base-azuredeploy.json’, it creates a Virtual Network, three subnets (Frontend, Backend and SessionHost ones), three Network Security Groups (one for each subnet to allow internal and external RDS communications), and a Standard Storage account in which vhds will be stored.
It is linked to other templates, this enable us decomposing our deployment into a set of targeted, purpose-specific templates:

* **rds-ad-azuredeploy.json**  
This template helps us to create and configurate two Windows Server 2016 virtual machine which gets the Domain Controller role.
* **rds-fe-azuredeploy.json**  
This template helps us to create a Windows Server 2016 virtual machine, join it in to the domain and configure Web and Gateway roles.
* **rds-sh-azuredeploy.json**  
This template helps us to create a Windows Server 2016 virtual machines (one with GPU and one with CPU), join them in to the domain and configure Session Host role.
* **rds-be-azuredeploy.json**  
This template helps us to create a Windows Server 2016 virtual machine, join it to the domain and configure Licensing and Connection Broker roles. After running the previous sub deployments, this template is ready to ensemble RDS scenario as Remote Desktop Management Server. Here it activates Licensing, specifies DNS name as the RD Gateway server name for deployment properties, as well as create and create two a Virtual Host Collection and a RemoteAppCollection **install self-signed certificates for the RD Gateway and Connection Broker**.

---

### RDS roles overview
* **Active Directory Domain Services (AD DS)**  
Stores one domain directory partition, the information about the domain in which it is located.
* **Web Access (RDWeb)**  
Provides user logon and selection of desktops and applications
* **Gateway (RDGW)**  
Routes RDP traffic between the Internet and a tenant’s network
* **Connection Broker (RDCB)**  
Distributes connections to RDSH servers
* **License Server (RDLic)**  
Tracks usage of the system
* **Session Host (RDSH)**  
User’s desktop and applications execute on the RDSH servers

If you want to learn more...
* [Active Directory Domain Services (AD DS)](https://technet.microsoft.com/en-us/library/cc731053)
* [Web Access (RDWeb)](https://technet.microsoft.com/en-us/library/cc731923)
* [Gateway (RDGW)](https://technet.microsoft.com/en-us/library/cc731150)
* [Connection Broker (RDCB)](https://technet.microsoft.com/en-us/library/cc772245)
* [Licensing (RDLic)](https://technet.microsoft.com/en-us/library/cc725933)
* [Session Host (RDSH)](https://technet.microsoft.com/en-us/library/cc742822)


