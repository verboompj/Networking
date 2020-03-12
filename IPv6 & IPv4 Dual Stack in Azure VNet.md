# IPv6 & IPv4 Dual Stack in Azure VNet

Azure supports IPv6 in a broad scope. VNets for instance can be configured for Dual Stack, meaning running IPv4 and IPv6 side by side over the same Virtual Network, just as Virtual Machines connected to a VNet. This How-To is a quick demonstration of what can be setup with minimal efforts.


### Goal: To Create 2 VM’s, connected to a VNET, communicating over both ipv4 as well as ipv6.


#### Overview 
1 VNET , dual stack IP ( IPv4 & IPv6 )
-	IPv4 VNET range : 172.20.0.0/20 
2 subnets :
	Frontend ( 172.20.0.0/24 )
	Backend ( 172.20.1.0/24 )
-	IPV6 VNET range: ace:cab:deca::/48
	Frontend ( ace:cab:deca:fe::/64) ( fe for frontend works nicely here 😊 )
	Backend ( ace:cab:deca:ba::/64) ( ba for backend) 


#### Step-by-Step

1.	Create a Resource Group ( I used "IPV6RG" in my case) and select a region (EU West in my case)

2.	Next, create a VNET , I used the portal for this. Make sure to check "Add IPV6 address space" in the 2nd tab. The name I used is IPv6VNET

![alt text][VNET enable IPv6]

[logo]: https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/1vnetaddipv6.png "VNET enable IPv6"

