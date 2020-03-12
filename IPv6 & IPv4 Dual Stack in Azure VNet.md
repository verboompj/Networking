# IPv6 & IPv4 Dual Stack in Azure VNet

Azure supports IPv6 in a broad scope. VNets for instance can be configured for Dual Stack, meaning running IPv4 and IPv6 side by side over the same Virtual Network, just as Virtual Machines connected to a VNet. This How-To is a quick demonstration of what can be setup with minimal efforts.


### Goal: To Create 2 VM’s, connected to a VNET, communicating over both ipv4 as well as ipv6.


#### Overview 
1 VNET , dual stack IP ( IPv4 & IPv6 )
##### IPv4 VNET range : 172.20.0.0/20 
2 subnets:
* Frontend ( 172.20.0.0/24 )
* Backend ( 172.20.1.0/24 )
##### IPV6 VNET range: ace:cab:deca::/48
* Frontend ( ace:cab:deca:fe::/64) ( fe for frontend works nicely here 😊 )
* Backend ( ace:cab:deca:ba::/64) ( ba for backend) 


#### Step-by-Step

##### 1.	Create a Resource Group 
I used "IPV6RG" in my case) and select a region (EU West in my case)

##### 2.	Next, create a VNET 
I used the portal for this. Make sure to check "Add IPV6 address space" in the 2nd tab. The name I used is IPv6VNET

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/1vnetaddipv6.png)

##### 3.	Add 2 subnets 
Again add IPv6 as well. Use a /64 mask for the IPv6 address range for the subnets

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/2addsubnetipv6.png)

##### 4.	Now we switch to Azure Cloud Shell. 
Make sure you select Bash as your shell

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/3azclicloudshell.png)

#### 5.	Start with the first VM
Create a new IP configuration for your VM, BAVM1 in my case (BAck-end VM 1 ) 
This VM will have a Public IPv4, Private IPv4 and Private IPV6 Address
The Public IPv4 will be used for RDP Access to the VM 
First up, the public IP of the first VM, the “Back-end VM 1“ or BAVM1 

Copy/Paste these lines as a whole to your Cloud Shell:

`az network public-ip create \`

`--name baPublicIP_v4  \`

`--resource-group IPV6RG  \`

`--location westeurope  \`

`--sku STANDARD  \`

`--allocation-method static  \`

`--version IPv4`

#### 6.	Next, an NSG for this VM’s NIC 
`az network nsg create \`

`--name BANSG1  \`

`--resource-group IPV6RG  \`

`--location westeurope`

#### 7.	Add a rule to the NSG, in this case "open" for RDP

`az network nsg rule create \`

`--name allowRdpIn  \`

`--nsg-name BANSG1  \`

`--resource-group IPV6RG  \`

`--priority 100  \`

`--description "Allow Remote Desktop In"  \`

`--access Allow  \`

`--protocol "*"  \`

`--direction Inbound  \`

`--source-address-prefixes "*"  \`

`--source-port-ranges "*"  \`

`--destination-address-prefixes "*"  \`

`--destination-port-ranges 3389`
