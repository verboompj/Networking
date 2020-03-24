# Azure Virtual WAN Step-by-Step

Azure Virtual WAN enables customers to leverage Microsoft's global infrastructure as a connectivity backbone. 

Azure Virtual WAN brings together many Azure cloud connectivity services such as site-to-site VPN , ExpressRoute , point-to-site user VPN into a single operational interface. 

We also see Azure Virtual WAN's VNet transitive connectivity pick up next to the existing VNet Peering model.


## Goal: To Create a Virtual WAN , HUB, VNet Connection and a VPN Site

I’ve created a step by step for setting up a Azure Virtual WAN and, as a part 2, connecting a branch-office using a Mikrotik Router and IPSEC tunnel over the Internet.

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/0.png)

## Azure Virtual Wan components
* ##### Virtual WAN : 
The virtualWAN resource represents a virtual overlay of your Azure network and is a collection of multiple resources. 
* Hub: A virtual hub is a Microsoft-managed virtual network. The hub contains various service endpoints to enable connectivity from your on-premises network (vpnsite). The hub is the core of your network in a region. There can only be one hub per Azure region. When you create a hub using Azure portal, it creates a virtual hub VNet and a virtual hub vpngateway.
* Hub virtual network connection: The Hub virtual network connection resource is used to connect the hub seamlessly to your Azure virtual network
* Hub route table: You can create a virtual hub route and apply the route to the virtual hub route table. You can apply multiple routes to the virtual hub route table. 
* VPN Sites: The site resource represents your on-premises VPN device and its settings. 
