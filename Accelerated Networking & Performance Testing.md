# This How-To is a followup on IPv6 / Dual Stack Networking in Azure

## The goal is to setup an accelerated NIC using the CLI and do some network performance testing

In this previous step-by-step : [IPv6 & IPv4 Dual Stack in Azure VNet](https://github.com/verboompj/Networking/blob/master/IPv6%20%26%20IPv4%20Dual%20Stack%20in%20Azure%20VNet.md) , we created 2 VMS in an Azure VNet running both IPv4 as well as IPv6 , so a Dual Stack network configuration.


In this article i will show how to create an accelerated NIC and do some network performance testing between 2 VMS in 2 different subnets within a single azure VNet. 

Accelerated Networking is described here: https://docs.microsoft.com/en-us/azure/virtual-network/create-vm-accelerated-networking-cli 

#### Tools and Tests

I'll be using iperf3 (www.iperf.fr) for network bandwidth testing and NTttcp as well

For my tests i have created 4 VM's, 2 with and 2 withoud Accelerated Networking enabled on their NIC. 
I choose to re-run my scripts from my previous Step-by-Step,  [IPv6 & IPv4 Dual Stack in Azure VNet](https://github.com/verboompj/Networking/blob/master/IPv6%20%26%20IPv4%20Dual%20Stack%20in%20Azure%20VNet.md) with 2 changes. 

#### Changes in setup

* 1 As VM type i selected the D4S-v2 Instances. These VM types have plenty of Bandwidth for testing: 6 Gbps aggregated NIC performance. 
The available bandwidth per VM series is described at [Microsoft Docs](https://docs.microsoft.com/en-us/azure/virtual-machines/dv2-dsv2-series).
You can choose later VM series such as the DSv3 or ESv3 for even higher aggregated bandwidth per VM. 

In this case the DSv2 series: 

* 2 Accelerated Networking is enabled on 2 of the 4 VM's I deployed for this test.

As said, I re-used my previous deployment with some minor changes. 



`NTttcp -s -6 -m 64,*,ace:cab:deca:fe::4 -t 300`



