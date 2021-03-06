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

*Note that I’m listing my Azure vNet IP range, not my Azure Virtual HUB IP Range as remote IP range.

Again, I need to do this manual step because of lack of an automation option for my particular router. You can lookup the tasks that Automation would normally take care of by downloading and examining the VPN Configuration File in the Azure Virtual WAN Overview page. 

So, lets get started.

#

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

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/23.png)

Download the file , and look for the IP addresses listed under IpAddresses section as “Instance0” and “Instance1” Pick the first instance(0) address.

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/24.png)

The other settings ( Action, Level, IPSEC protocols and Proposal) should default to the settings as shown in the screenshot. If not adjust your settings to match the values of the previous screenshot.
Repeat this policy setup for each local subnet , if you have more than one local subnet.

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/25.png)

#### All right, Step 2, Setting up the IPSEC parameters 

The next tab in the Mikrotik Webfig interface brings you to the “Proposals” step, and this actually configures the IKE phase 2 proposal.
I choose to edit the Default Proposal that is there by default, and edited it to match the right values.

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/26.png)

Here we need to take the azure preferences into account : [IKE Parameters](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec) , and take a look at the IKE phase 2 parameters. Details listed here as the parameters for IKEv2 or RouteBased configuration. Note the SA lifetime of `27.000 seconds` or in this case 7.5 hours.

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/27.png)

Hit Apply & OK to close the tab. You can skip the “Groups” tab in the Webfig interface if you choose the default configuration values.

#### Step 3 , Peers
Under the “Peers” tab we need to configure the IKE policy for our Azure vWan Gateway.

Click Add New,
Give the peer relation a descriptive name,

Add the `Public IP` (SA Dest IP Address of step 1 ) + the /32 mask.

Select the default profile , IKE2 for the Exchange mode and check the `Send INITIAL_CONTACT` checkbox

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/28.png)


#### Step 4 , Tying it all together
All right, we are half way there, the next tab is the Identities Tab in the Webfig interface.
Here we map the Peers and Policies created earlier and actually use the Pre-Shared-Key (PSK), or Secret in the case of Mikrotik, that we generated when we first created the Azure virtual WAN.
If you think by now, oh my, what brilliant PSK did I come up again?... No worries, its listed in the VPN Configuration Download file of step 1, under the PSK header:

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/29.png)

So, click Add New.

Select the Peer you created in step2, select Pre Shared Key for Authentication Mode, and type in your secret/PSK of your Azure vWan.
Check if your other settings match the default values as shown in the screenshot:

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/30.png)

#### Step 5 Next, hit the “Profiles” Tab
Select the default profile and edit it to match the IKE phase 1(Main Mode) parameters as listed in the previously mentioned doc on [IKE parameters](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpn-devices)
Again, we are looking for the IKEv2 based configuration, known as the RouteBased IPSEC configuration.

Again, we are looking for the IKEv2 based configuration, known as the RouteBased IPSEC configuration.
So Phase 1 of IKEv2 😊

* Hashing Algorithm: I chose `SHA1`
* Encryption Algorithms matching SHA1 : `AES128, AES256`.
* Make sure you select the `1024bit Diffie-Hellman Group option` , listed as `modp1024`
* Note the Lifetime of `28.800` Seconds, or 8 hours in this case.
* Check `NAT traversal` checkbox,
* And verify your default options to match as listed in the screenshot.

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/31.png)

That’s it form an IPSEC configuration standpoint. Next up, a NAT rule in your Firewall config and verifying connectivity !

#### Step 6, NAT rules

In order to route packets from your Azure WAN to local subnets we need to create a SourceNAT Rule.
In your Webfig, go to IP, and the Firewall
Go to the NAT tab and click Add New

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/32.png)

Make sure the rule is enabled, As Chain select `srcnat`

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/33.png)

Src. Address : Your LAN ip Range. In my case, the LAN ip range that my Mikrotik LAN interface is assigned to. `192.168.x.x /24`

Dst Address: The Azure vNET IP range `10.1.0.0/20`

Scroll down and check the Accept checkbox

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/34.png)

Hit OK to accept your new NAT entry, and that’s it !
Again, its worth mentioning that these steps can be part of an automated deployment when selecting a solution by one of our virtual WAN partners.

Specifically mapping Azure vNets and local subnets, these mappings are normally part of the automation option. Check the downloadable VPN Config (step 1) and you will see that all vNET subnets and HUB networks associated with this vWAN are listed in there.

#### Step 7, Verify & Troubleshooting
Allright, lets do a ping from OnPrem to an Azure host:

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/35.png)

Success !

To verify connectivity between your device and Azure vWan, go back to IPsec in the IP menu, and check the Remote Peers tab, and verify a connection is established:

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/36.png)

Under Policies, verify the IKEv2 Phase 2 was established for each source/destination mapping:

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/37.png)

And lastly in the Overview page of your Azure Virtual WAN , go to Health and verify traffic flowing:

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/38.png)

Note that if you change your VPN Gateway configuration , it could again take up to 30 minutes to become fully available again, the status can also be verified under VPN Gateway Status.

#### Troubleshooting 
1st steps: Reboot your local device, next, try the Attempt connection Repair option of the VPN Sites configuration page in Azure vWan:

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/39.png)

Mikrotik can do great logging as well, just add the IPSEC topic to your logging, under System, Logging:

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/40.png)

And consume logs under the Log menu item in Webfig. 

#

Have fun ! Drop a comment if you have any questions.
