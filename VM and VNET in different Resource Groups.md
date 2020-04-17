# Setting up a VM that connects to a VNET in a different ResourceGroup.

In this example , one would create a VM in ResourceGroup "A" , leveraging a VNET and Subnet that is already present in ResourceGroup "B"
This is a very common scenario, where for instance the Network Team manages the central VNET and all of its sub-components ( Subnets, IP-Adressing, Gateways, ExpressRoutes, etc), all contained within a specific Resource Group that is subjected to the Azure RBAC model. 

Teams deploying services into the subscription would do so in their own respective ResourceGroups, whilst still leveraging the centrally controlled VNET of ResourceGroup "A" , and leveraging all the services available in that VNET.

### Setup 

To create a VM with its NIC connected to a Vnet that lives outside of the ResourceGroup the VM is created in, its prefered to first declare a variable for the actual VNET and SUBNET you want to use, and simply call that variable on deployment of your VM.

Example code : `export SUBNETID="$(az network vnet subnet show --resource-group [ResourceGroup containing VNET] --vnet-name [NAME of VNET] -n [NAME of Subnet]  --query id -o tsv)"`

The example does an export to tsv as variable `$SUBNETID` of the target Vnet and Subnet. It is actually capturing the full ResourceID
You can display the Resource ID by querying the $Subnet 

The results:

![Screenshot](https://github.com/verboompj/Networking/blob/master/Pictures/70subnetid.PNG)

Next, we create the VM and call the variable `SUBNETID` 

`az vm create \`
`  --resource-group mynewrg \`
`  --name myVM18 \`
`  --image UbuntuLTS \`
`  ## --subnet $SUBNETID \`
`  --admin-username azureuser \`
`  --generate-ssh-keys \`
`  --storage-sku Premium_LRS \`
`  --os-disk-size-gb 50 \`
`  --size Standard_F4s_v2 \`
`  --accelerated-networking true \`



