# IPv6 & IPv4 Dual Stack in Azure VNet


Azure supports IPv6 in a broad scope. VNets for instance can be configured for Dual Stack, meaning running IPv4 and IPv6 side by side over the same Virtual Network, just as Virtual Machines connected to a VNet. This How-To is a quick demonstration of what can be setup with minimal efforts.



### Goal: To Create 2 VM’s, connected to a VNET, communicating over both ipv4 and ipv6.

       


#### Overview 
1 VNET , dual stack IP ( IPv4 & IPv6 )
##### IPv4 VNET range : 172.20.0.0/20 
2 subnets:
* Frontend ( 172.20.0.0/24 )
* Backend ( 172.20.1.0/24 )
##### IPV6 VNET range: ace:cab:deca::/48
* Frontend ( ace:cab:deca:fe::/64) ( fe for frontend works nicely here 😊 )
* Backend ( ace:cab:deca:ba::/64) ( ba for backend) 





### Step-by-Step


##### 1.	Create a Resource Group 
I named it `IPV6RG` and selected the EU West region

##### 2.	Next, create a VNET 
I used the portal for this. Make sure to check "Add IPV6 address space" in the 2nd tab. The name I used is `IPv6VNET`

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

#### 8.	Allow outbound communications in the next NSG Rule

`az network nsg rule create \`

`--name allowAllOut  \`

`--nsg-name BANSG1  \` 

`--resource-group IPV6RG  \`

`--priority 300  \`

`--description "Allow All Out"  \`

`--access Allow  \`

`--protocol "*"  \`

`--direction Outbound  \`

`--source-address-prefixes "*"  \`

`--source-port-ranges "*"  \`

`--destination-address-prefixes "*"  \`

`--destination-port-ranges "*"`

#### 9.	Now create the NIC for this VM and configure IPV4

`az network nic create \`

`--name BANIC0  \`

`--resource-group IPV6RG \`

`--network-security-group BANSG1  \`

`--vnet-name IPv6VNET  \`

`--subnet Backend  \`

`--private-ip-address-version IPv4 \`

`--public-ip-address baPublicIP_v4` 

#### 10.	Add the IPv6 configuration to the newly created NIC
`az network nic ip-config create \`

`--name BAIp6Config_NIC0  \`

`--nic-name BANIC0  \`

`--resource-group IPV6RG \`

`--vnet-name IPv6VNET \`

`--subnet Backend \`

`--private-ip-address-version IPv6 \`

#### 11.	Create a VM , in this case BAVM1, that uses the newly created NIC , NSG and IP configuration

`az vm create \`

`--name BAVM1 \`

`--resource-group IPV6RG \`

##### `--admin-username [YOUR ADMIN USERNAME]  \`

`--nics BANIC0 \`

`--size Standard_E4s_v3 \`

`--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest`


#### 12.	After completing, the first VM is live and running in Dual (IP) Stack:

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/4vmrunning.png)
![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/5firstnicveri.png)

#### 13.	Deploy a second VM in the Frontend Tier , start with the NSG and its rules: 

`az network nsg create \`

`--name FENSG1  \`

`--resource-group IPV6RG  \`

`--location westeurope`

Rules: 

`az network nsg rule create \`

`--name allowRdpIn  \`

`--nsg-name FENSG1  \`

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


`az network nsg rule create \`

`--name allowAllOut  \`

`--nsg-name FENSG1  \`

`--resource-group IPV6RG  \`

`--priority 300  \`

`--description "Allow All Out"  \`

`--access Allow  \`

`--protocol "*"  \`

`--direction Outbound  \`

`--source-address-prefixes "*"  \`

`--source-port-ranges "*"  \`

`--destination-address-prefixes "*"  \`

`--destination-port-ranges "*"`

#### 14.	The NIC for FEVM1: 

`az network nic create \`

`--name FENIC0  \`

`--resource-group IPV6RG \`

`--network-security-group FENSG1  \`

`--vnet-name IPv6VNET  \`

`--subnet Frontend  \`

`--private-ip-address-version IPv4 \`

Add IPv6 Config: 

`az network nic ip-config create \` 

`--name FEIp6Config_NIC0  \`

`--nic-name FENIC0  \`

`--resource-group IPV6RG \`

`--vnet-name IPv6VNET \`

`--subnet Frontend \`

`--private-ip-address-version IPv6 \`

 
#### 15.	And create the second VM:

`az vm create \`

`--name FEVM1 \`

`--resource-group IPV6RG \`

##### `--admin-username [YOUR ADMIN USERNAME] \`

`--nics FENIC0 \`

`--size Standard_E4s_v3 \`

`--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest`

#### 16.	Verify connectivity using RDP from Backend to Frontend using IPV4 and IPV6 Addresses:
![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/6rdsconnect.png)

### Great, now explore your IPV6 options in Azure 😊 ! 

