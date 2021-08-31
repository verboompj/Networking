# This How-To is a quick howto on integrating supported OnPrem network devices in M365 Defender.

Azures hybrid (security)capabilities now extend all the way upto your network devices. Integrating these type of devices allows for proactive vulnerability management and CVE advisories for these kinds of devices.
https://docs.microsoft.com/en-us/microsoft-365/security/defender-endpoint/network-devices?view=o365-worldwide

I'll describe the steps I took to enroll a lab environment end-to-end.

## Start with Azure ARC

I started by enrolling my home lab into Azure ARC for management, monitoring, alerting and Update management of my OnPrem assets.
https://docs.microsoft.com/en-us/azure/azure-arc/servers/overview.

Adding on top, I enrolled all nodes into Azure Security Center. 
I've integrated ASC with Microsoft Defender for Endpoint using the steps described here: https://docs.microsoft.com/en-us/azure/security-center/security-center-wdatp?WT.mc_id=Portal-Microsoft_Azure_Security&tabs=windows

Enrolling a device (Server) in Azure Security Center: Azure Defender Mode - entitles it for Endpoint Protection. 
In my case Defender for Endpoint on both Windows and Linux.

ASC (Azure Security Center) optionally enrolls Endpoint Vulnerability Assesment based on the integrated and license-included Qualys agent
https://docs.microsoft.com/en-us/azure/security-center/defender-for-servers-introduction

Now, managing the nodes from a security perspective can be done thru the M365 Defender portal: https://security.microsoft.com/homepage. No M365 licenses required.
My Linux servers are also enrolled: https://docs.microsoft.com/en-us/microsoft-365/security/defender-endpoint/linux-install-manually?view=o365-worldwide


## Integrating Network devices

To allow vulnerability assesment of OnPrem network devices, deploy the MDATPScanAgent onto a (windows) server in your network. This agent is configured through the M365 Defender portal and can be configued with SNMP settings/credentials and IP ranges to scan. 
See this great article for deployment details : https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/network-device-discovery-and-vulnerability-assessments/ba-p/2267548

Currently, the following operating systems are supported: 

- Cisco IOS, IOS-XE, NX-OS 
- Juniper JUNOS 
- HPE ArubaOS, Procurve Switch Software 
- Palo Alto Networks PAN-OS 

I'm using an old Cisco C3560G switch as a test device and after setting it up with an IP and configuring SNMP ( Serial port anyone ? :D ) the scanner picked up the device

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/found2.PNG)


