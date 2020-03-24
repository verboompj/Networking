# Getting started with Azure Virtual WAN Part 2 - Setting Up a Local Device

In my [previous article](https://github.com/verboompj/Networking/blob/master/Azure%20Virtual%20WAN%20Step-by-Step%20Part%201.md) we have setup a basic Azure Virtual WAN. In this write-up I'll continue where we left off and configure a local, 
OnPremises device ( Router/Firewall) to connect to our Virtual WAN.

## Goal: To connect a local site to the Azure Virtual WAN HUB

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/0.png)


I’ll be using a Mikrotik Router (release 6.44.3) , a highly configurable device that can do IKEv2 IPSEC. This allows me to configure a [Route Based VPN Configuration](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#vpntype) .

It should be noted that this Mikrotik device is not on the [Validated VPN Devices list](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpn-devices), nor on the [virtual WAN partner list](https://docs.microsoft.com/en-us/azure/virtual-wan/virtual-wan-locations-partners). In practice this means that it’s a manual configuration, with some more steps involved in setting up the local devices’ configuration, such as setting up routing policies/maps in the local device. Automated deployments make for a much easier configuration of these local/OnPrem devices.

For this Lab setup, however, I’m OK with a manual setup. I run multiple VLAN’s that I’d like to hook up to Azure vWan, so the total picture looks like this:

Local IP ranges | Azure IP Ranges (vNets)
----------------|------------------------
192.168.x.0/24 | 10.1.0.0/20
172.16.x.0/24
172.16.x.0/24

Note that I’m listing my Azure vNet IP range, not my Azure Virtual HUB IP Range as remote IP range.

Again, I need to do this manual step because of lack of an automation option for my particular router. You can lookup the tasks that Automation would normally take care of by downloading and examining the VPN Configuration File in the Azure Virtual WAN Overview page. 

So, lets get started.

#### Step 1. IPSEC Policy 
Login to the Mikrotik Router using Webfig and create a Policy object for all subnets
On the Mikrotik Webfig, go to IP, and IPsec menu. Click Add New under Policy.

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/20.png)

Create a policy for each onprem subnet as shown in the picture.

Add the (private IP) Source and Destination address ranges and mask, and don’t forget to select the “tunnel” checkbox.

Next, add the SA source Address : Your Mikrotik’s WAN/Public IP Address (no mask)

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/21.png)

Next, add the SA Destination address. You can acquire this Azure vWAN IP Address by downloading the VPN configuration file, presented in the Overview page of your Azure Virtual WAN:

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/22.png)
