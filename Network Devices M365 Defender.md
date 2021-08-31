# This How-To is a quick howto on integrating supported OnPrem network devices in M365 Defender.

Azures hybrid capabilities now extend all the way upto your netowork devices.
https://docs.microsoft.com/en-us/microsoft-365/security/defender-endpoint/network-devices?view=o365-worldwide

## Azure Security Center & M365 Defender

Enrolling a device (Server) in Azure Security Center: Azure Defender On- entitles it for Endpoint Protection, in my case Defender for Endpoint on Linux.
It also enrolls Endpoint Vulnerability Assesments based on the integrated and license-included Qualys agent
https://docs.microsoft.com/en-us/azure/security-center/defender-for-servers-introduction

## Start with Azure ARC

In my case, I started by enrolling my home lab into Azure ARC for management, monitoring, alerting and Update management.
https://docs.microsoft.com/en-us/azure/azure-arc/servers/overview
Adding on top, I enrolled all nodes into Azure Security Center. I've integrated ASC with Defender using the steps described here: https://docs.microsoft.com/en-us/azure/security-center/security-center-wdatp?WT.mc_id=Portal-Microsoft_Azure_Security&tabs=windows

Now, managing the nodes from a security perspactive can be done thru the M365 Defender portal: https://security.microsoft.com/homepage. No M365 licenses required.
My Linux nodes are now also enrolled: https://docs.microsoft.com/en-us/microsoft-365/security/defender-endpoint/linux-install-manually?view=o365-worldwide





https://docs.microsoft.com/en-us/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux?view=o365-worldwide

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/19accelerated-networking-benefit.png)



https://help.ubuntu.com/community/CiscoConsole


