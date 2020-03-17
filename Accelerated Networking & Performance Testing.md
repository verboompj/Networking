# This How-To is a followup on IPv6 / Dual Stack Networking in Azure

## The goal is to setup an accelerated NIC using the CLI and do some network performance testing

In the previous step-by-step : [IPv6 & IPv4 Dual Stack in Azure VNet](https://github.com/verboompj/Networking/blob/master/IPv6%20%26%20IPv4%20Dual%20Stack%20in%20Azure%20VNet.md) , we created 2 VMS in an Azure VNet running both IPv4 as well as IPv6 , so a Dual Stack network configuration.


In this article i will show how to create an accelerated NIC and do some network performance testing between 2 VMS in 2 different subnets within a single azure VNet. 

Accelerated Networking is described here: https://docs.microsoft.com/en-us/azure/virtual-network/create-vm-accelerated-networking-cli 

### Tools and Tests

I'll be using iperf3 (www.iperf.fr) for network bandwidth testing and NTttcp as well

For my tests i have created 4 VM's, 2 with and 2 withoud Accelerated Networking enabled on their NIC. 
I choose to re-run my scripts from my previous Step-by-Step,  [IPv6 & IPv4 Dual Stack in Azure VNet](https://github.com/verboompj/Networking/blob/master/IPv6%20%26%20IPv4%20Dual%20Stack%20in%20Azure%20VNet.md) with 2 changes. 

### Changes in setup

* 1 Accelerated Networking is enabled on 2 of the 4 VM's I deployed for this test.

* 2 As VM type i selected the D4S-v2 Instances. These VM types have plenty of Bandwidth for testing: 6 Gbps aggregated NIC performance. 
The available bandwidth per VM series is described at [Microsoft Docs](https://docs.microsoft.com/en-us/azure/virtual-machines/dv2-dsv2-series).
You can choose later VM series such as the DSv3 or ESv3 for even higher aggregated bandwidth per VM. 
In this case the DSv2 series: 

### Deployment Step-by-Step

As said, I re-used my previous deployment with some minor changes. 
I used the same IPV6VNET , and used the same subnets : Backend and Frontend.
They have been setup for both IPv4 and IPv6, and I'll mainly be testing using IPv6

The changes in this deployment concern step 9 : 

#### 9.	Now create the NIC for this VM and configure IPV4

`az network nic create \`

`--name BANIC0  \`

`--resource-group IPV6RG \`

`--network-security-group BANSG1  \`

`--vnet-name IPv6VNET  \`

`--subnet Backend  \`

##### `--accelerated-networking true \` 

`--private-ip-address-version IPv4 \`

`--public-ip-address baPublicIP_v4` 

note the " --accelerated-networking true " 
I added this for both the NIC's and therefore both the VM's i created for the tests on Accelerated Networking.

The other change is the VM " SKU"  or type in step 11 : 

#### 11.	Create a VM , in this case BAVM2, that uses the newly created NIC , NSG and IP configuration

`az vm create \`

`--name BAVM1 \`

`--resource-group IPV6RG \`

##### `--admin-username [YOUR ADMIN USERNAME]  \`

`--nics BANIC0 \`

##### `--size Standard_DS4_v2 \`

`--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest`

And thats it, run the same changes on the FEVM1 as well for the 2nd VM in the FrontEnd Subnet

### Check and verify:

Accelerated Networking is ON for the new VM's, and they both received IPv4 and IPv6 Adresses 

### lets push some packets !

I'll start with the basic, Non accelerated VM's , so the VM's without Accelerated Networking enabled.

First i downloaded [Iperf3](https://iperf.fr/iperf-download.php) onto both machines and extracted the files.

I disabled the firewall for this test.

Next i ran the first VM , FEVM1,  as Server in iperf : ` iperf3 -s` For a full list of commands run ` iperf3 -h`  

[img]

On the BAVM1 I ran the socalled Client mode , with the following parameters: 
`iperf3 -c ace:cab:deca:fe::5 -b 0 -P 1 -6` 
* the `-b 0` means -b for Bandwidth, 0 for unlimited
* the `-P 1` for the number of parralel running flows / Streams the client initiates
* and the `-6` to force connectivity over IPv6 

[img]

The results show a 1.7Gbps of bandwidth consumed by iperf. Not quite the 6Gbps the VM has available on Aggregated bandwidth.
This has to do with the number of streams that iperf uses, and the limits on the NIC that come with running a single stream.
If we re-run iperf to leverage 8 parralel streams, watch what happends:

`iperf3 -c ace:cab:deca:fe::5 -b 0 -P 8 -6` 

[img]

We hit the defined limits as described for this specific VM type.




`NTttcp -s -6 -m 64,*,ace:cab:deca:fe::4 -t 300`



