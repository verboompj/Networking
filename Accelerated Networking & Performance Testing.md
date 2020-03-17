# This How-To is a followup on IPv6 / Dual Stack Networking in Azure

## The goal is to setup an accelerated NIC using the CLI and do some network performance testing

In this step-by-step : [IPv6 & IPv4 Dual Stack in Azure VNet](https://github.com/verboompj/Networking/blob/master/IPv6%20%26%20IPv4%20Dual%20Stack%20in%20Azure%20VNet.md) , we created 2 VMS in an Azure VNet running both IPv4 as well as IPv6 , so a Dual Stack network configuration.


In this article i will show how to create an accelerated NIC , as described here: https://docs.microsoft.com/en-us/azure/virtual-network/create-vm-accelerated-networking-cli and do some network performance testing between 2 VMS in 2 different subnets within a single azure VNet.

I'll be using iperf3 (www.iperf.fr) for network bandwidth testing and NTttcp as well



`NTttcp -s -6 -m 64,*,ace:cab:deca:fe::4 -t 300`



