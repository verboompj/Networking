# Azure Virtual WAN Step-by-Step

Azure Virtual WAN enables customers to leverage Microsoft's global infrastructure as a connectivity backbone. 
[Check out this great article on Docs](https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-about)

Azure Virtual WAN brings together many Azure cloud connectivity services such as site-to-site VPN , ExpressRoute , point-to-site user VPN into a single operational interface. 

We also see Azure Virtual WAN's VNet transitive connectivity pick up next to the existing VNet Peering model.


## Goal: To Create a Virtual WAN , HUB, VNet Connection and a VPN Site

I’ve created a step by step for setting up a Azure Virtual WAN and, as a part 2, connecting a branch-office using a Mikrotik Router and IPSEC tunnel over the Internet.

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/0.png)

## Azure Virtual Wan components
* Virtual WAN : The virtualWAN resource represents a virtual overlay of your Azure network and is a collection of multiple resources. 
* Hub: A virtual hub is a Microsoft-managed virtual network. The hub contains various service endpoints to enable connectivity from your on-premises network (vpnsite). The hub is the core of your network in a region. There can only be one hub per Azure region. When you create a hub using Azure portal, it creates a virtual hub VNet and a virtual hub vpngateway.
* Hub virtual network connection: The Hub virtual network connection resource is used to connect the hub seamlessly to your Azure virtual network
* Hub route table: You can create a virtual hub route and apply the route to the virtual hub route table. You can apply multiple routes to the virtual hub route table. 
* VPN Sites: The site resource represents your on-premises VPN device and its settings. 




![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/1.png)



#### Step 1, creating a Virtual WAN

Create the Virtual Wan overlay service from the Azure marketplace of from the portal directly
Name it, and select a resource group.

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/2.png)




#### Step 2, add your first (regional) virtual Hub 
Name it, select a region to deploy this hub to and include the VPN Gateway service for VPN sites.

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/3.png)

* Select a private IP range for the HUB, make sure it’s non overlapping and fits within your IP plan. The actual vNets in Azure are not part of this range, but should be routable to this HUB IP range. So again, no overlap from Onprem networks or Azure vNets you would like to connect. 
* The actual routing is covered later, and is full mesh by default. If you want to take control of the actual routing rules, add a Routing table. I chose not to in this example.
* Select the number of required Scale Units , Scale units allow you to add capacity per 500Mbit.  

The actual provisioning of the VPN gateway can take upto 30 minutes. This is a background process and the results are displayed under the Health section of your virtual WAN. You can continue whilst this is running in the background

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/4.png)

#### Step 3, create a VPN site

Name your site and supply the public IP address of your local Router as Public IP address
Add the local site’s IP ranges here, this information will automatically added to the VPN Gateway’s routing table that is part of your virtual HUB (step2) , and hit confirm

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/5.png)

#### Step 4, associate the site with the HUB 

Hit refresh in the VPN Sites overview page , select your site and click New Hub Association

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/6.png)


Next, associate with the HUB created in step2 and supply a PSK passphrase of your liking.

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/7.png)

If the association process times out, check if the prior tast was already completed , by checking the Virtual Gateway service Status is reported as `Succeeded` in the virtual WAN’s Health status

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/8.png)


#### Step 5 , add a Virtual Network Connection

This process "peers" an existing Azure vNet to the virtual WAN network , allowing communication to and from Azure resources from your VPN sites.

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/9.png)

And that’s it ! , now, you can download a VPN configuration from the overview page , the xml file contains the VPN configuration details for your onprem appliance to connect to the virtual WAN

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/10.png)

#### Step 6, Optional
You can choose to enable full mesh routing between Branches as well, by selecting the option in the Configuration page

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/11.png)

As you can see its quite easy setting up your first HUB as a part of a full Virtual WAN.
Next up, setting up your local router or firewall to connect to Azure Virtual WAN in Part 2 
