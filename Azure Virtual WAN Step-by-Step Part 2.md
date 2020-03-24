# Getting started with Azure Virtual WAN Part 2 - Setting Up a Local Device


In my previous article we have setup a basic Azure Virtual WAN. In this write-up I'll continue where we left off and configure a local, OnPremises device ( Router/Firewall) to connect to our Virtual WAN.

I’ll be using a Mikrotik Router (release 6.44.3) , a highly configurable device that can do IKEv2 IPSEC. This allows me to configure a Route Based VPN Configuration .

It should be noted that this Mikrotik device is not on the Validated VPN Devices list, nor on the virtual WAN partner list. In practice this means that it’s a manual configuration, with some more steps involved in setting up the local devices’ configuration, such as setting up routing policies/maps in the local device. Automated deployments make for a much easier configuration of these local/OnPrem devices.

For this Lab setup, however, I’m OK with a manual setup. I run multiple VLAN’s that I’d like to hook up to Azure vWan, so the total picture looks like this:

* Local IP ranges                                 Azure IP Ranges (vNets)
* 192.168.x.0/24                                   10.1.0.0/20
* 172.16.x.0/24
* 172.16.x.0/24
