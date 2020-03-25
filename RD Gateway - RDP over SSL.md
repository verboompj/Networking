# Leverage an RD Gateway Service as SSL termination service between remote users and your Azure or OnPrem Network

The RD Gateway service has always been able to tunnel RDS traffic over SSL. Its been used as a solution to tunnel RDS traffic from remote users into an (OnPremises) RDS farm.

The same solution has always allowed for the option to "Connect to a remote PC" via the Web Interface, using the url :

`https://[yourdomain]/RDWeb/Pages/en-US/desktops.aspx` 

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/53.PNG)

So in essence, this solution could potentially allow you and your users to connect to their Office PC's over SSL from home. Securly using RDP over an SSL Tunnel. That sounds like a cool, and quick solution to allow WFH, Working from home.

Its not the best managed solution, and connecting to your desktop is a bit clumpsy , but hey , a couple of vrtual servers, a dns name and a $ 100 certificate will get you there, and you are making use of all the hardware you already have.

If you are looking for a managed, seamlessly integrated solution with all the bells and whistles, Windows Virtual Desktop ,Citrix Cloud or VMWare Horizon View might be more to your liking. 

# 

I have helped customers based on 2 different scenario's: 

1. Connect to Windows 10 VM's ( "VDI  like") in Azure 
![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/50.png)

2. Connect to Physical Desktops in an OnPrem network
![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/51.png)

# 

## Prerequisites

* A Public DNS domain:  $ 15 dollar/year 
* A certificate : $ 100 dollar/year
* A Virtual Machine to act as the RDS Gateway Server

### Step-by-Step

This is a quick step by step based on the 1st scenario, Setting up a RD Gateway server in Azure, using Azure App Services Domain for the Public Domain , App Services Certificates for the Certificate and Azure KeyVault for the certificate management


#### Step 1. Register a Public DNS name 
Pretty straight forward , find it on the [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.Domain?tab=Overview) 

#### Step 2. Aquire a Certificate
Same goes for the Certificate, [check it out here](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.SSL?tab=Overview) 

#### Step 3. Export the Certificate using KeyVault
A little bit more complicated, and involves PowerShell, but this great howto will certainly get you there: https://azure.github.io/AppService/2017/02/24/Creating-a-local-PFX-copy-of-App-Service-Certificate.html 

#### Step 4. Build your RD Gateway deployment.
There are already some great how-to's online, so i'm not going to cover that in here

https://turbofuture.com/computers/How-To-Setup-a-Remote-Desktop-Gateway-Windows-Server-2016 

https://ryanmangansitblog.com/2013/03/27/deploying-remote-desktop-gateway-rds-2012/

https://nedimmehic.org/2018/03/26/remote-desktop-services-2016-gateway/ 

Most of these How-To's use a self signed certificate, in our case we don't have to, as we purchased a domainname and cert.
Select Existing Certificate for all roles/servers in this dialog : and import your Certificate accordingly for all roles

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/54.png)

Also, i'd like to point out that the server you are deploying the RD Gateway and other roles to has to be domain joined.
You can choose to install the Connection Broker, RD Gateway and RD Web on a single server.
If you need the solution to scale, multiple servers per role could be what you need. 

#### Step 5. Client Connections

Make sure that the Office PC/Desktops you want to leverage thru this RD Gateway have Remote Desktop enabled locally. You can do so using a Group Policy for instance.

Connecting via Web is possible, again using the `https://[yourdomain]/RDWeb/Pages/en-US/desktops.aspx` link.
This is , however, using an ActiveX add-in that most modern browsers no longer support. I.E.7 still does, and is part of Win 10 ( still) so you can test it there. 

The alternative , or even better way, is to create an RDP file. Simply run `mstsc` and configure and save your RDP connection.
Mind the checkbox for 'Use My RD Gateway Credential for the remote computer'

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/56.png)

#### Step 6. Advanced Settings

Both the Web interface as the RDP file allow for customizations such as:
* Printer Redirection
* Drive Redirection
* (USB) PnP Redirection
* AUdio Redirection

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/55.png)

For the RDP file only:
* Multi Monitor configurations
* RemoteFX redirection 

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/58.png)
![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/59.png)

So that's it, easy , cost effective and leveraging your existing Desktops. 
Drop a comment , tell me what you think.
