# Leverage an RD Gateway Service as SSL termination service between remote users and your Azure or OnPrem Network

The RD Gateway service has always been able to tunnel RDS traffic over SSL. Its been used as a solution to tunnel RDS traffic from remote users into an (OnPremises) RDS farm.

The same solution has always allowed for the option to "Connect to a remote PC" via the Web Interface, using the url :

`https://[yourdomain]/RDWeb/Pages/en-US/desktops.aspx` 

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/53.PNG)

So in escence, this solution could potentially allow you and your users to connect to their Office PC's over SSL from home.
That sounds like a cool, and quick solution to allow WFH, Working from home.

Its not the best managed solution, and connecting to your desktop is a bit clumpsy , but hey , a couple of vrtual servers, a dns name and a $ 100 certificate will get you there.

# 

I have helped customers based on 2 different scenario's: 

1. Connect to Windows 10 VMS in Azure 
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

Build your Rd gateway setup. There are already some great how-to's online :

https://turbofuture.com/computers/How-To-Setup-a-Remote-Desktop-Gateway-Windows-Server-2016 
Or
https://ryanmangansitblog.com/2013/03/27/deploying-remote-desktop-gateway-rds-2012/
Or 
https://nedimmehic.org/2018/03/26/remote-desktop-services-2016-gateway/ 

Most of these How-ToS use a self signed certificate, in our case we don't have to, as we purchased a domainname and cert.
Select Existing Certificate for all roles/servers in this dialog : and import your Certificate accordingly for all roles

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/54.png)

Also, i'd like to point out that the server you are deploying the RD Gateway and other roles to has to be domain joined.
You can choose to install the Connection Broker, RD Gateway and RD Web on a single server.
If you need the solution to scale, multiple servers per role could be what you need. 




