# Leverage an RD Gateway Service as SSL termination service between remote users and your Azure or OnPrem Network

So The RD Gateway service has always been able to tunnel RDS traffic over SSL. Its been used as a solution to tunnel RDS traffic from remote users into an (OnPremises) RDS farm.

The same solution has always allowed for the option to "Connect to a remote PC" via the Web Interface, using the url :

`https://[yourdomain]/RDWeb/Pages/en-US/desktops.aspx` 

![Screenshot](https://raw.githubusercontent.com/verboompj/Networking/master/Pictures/53.PNG)

So in escence, this solution could potentially allow you and your users to connect to their Office PC over SSL from home.
That sounds like a cool, and quick solution to allow working from home.

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

#### Step 2. Aquire a Certificate

#### Step 3. Export the Certificate using KeyVault


