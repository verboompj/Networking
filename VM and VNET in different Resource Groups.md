In this example , one would create a VM in ResourceGroup "A" , leveraging a VNET and Subnet that is already present in ResourceGroup "B"
This is a very common scenario, where for instance the Network Team manages the central VNET and all of its sub-components ( Subnets, IP-Adressing, Gateways, ExpressRoutes, etc), all contained within a specific Resource Group that is subjected to the Azure RBAC model. 

Teams deploying services into the subscription would do so in their own respective ResourceGroups, whilst still leveraging the centrally controlled VNET of ResourceGroup "A" , and leveraging all the services available in that VNET.

To create a VM with its NIC connected to a Vnet that lives outside of the ResourceGroup the VM is created in, its prefered to first declare a variable for the actual VNET and SUBNET you want to use, and simply call that variable on deployment of your VM.

Example code : `export SUBNETID="$(az network vnet subnet show --resource-group [ResourceGroup containing VNET] --vnet-name [NAME of VNET] -n [NAME of Subnet]  --query id -o tsv)"`

The example does an export to tsv as variable `$SUBNETID` of the target Vnet and Subnet. It is actually capturing the full ResourceID
You can display the Resource ID by querying the $Subnet 
