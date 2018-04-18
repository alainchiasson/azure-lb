# Get a list of locations

az account list-locations

# Create a resource group

az group create --name lb-test --location canadacentral

# Create a lodbalancer in created resource group

az network lb create --resource-group lb-test --name reflex-lb

##NOTE: Some defaults were created when the lb was creted

```
{
  "loadBalancer": {
    "backendAddressPools": [
      {
        "etag": "W/\"c64053ea-f293-4359-a3bf-4b1136ddc08f\"",
        "id": "/subscriptions/fb6ad26a-aa88-4d30-9c15-2fefd4fb3d5a/resourceGroups/lb-test/providers/Microsoft.Network/loadBalancers/reflex-lb/backendAddressPools/reflex-lbbepool",
        "name": "reflex-lbbepool",
        "properties": {
          "provisioningState": "Succeeded"
        },
        "resourceGroup": "lb-test"
      }
    ],
    "frontendIPConfigurations": [
      {
        "etag": "W/\"c64053ea-f293-4359-a3bf-4b1136ddc08f\"",
        "id": "/subscriptions/fb6ad26a-aa88-4d30-9c15-2fefd4fb3d5a/resourceGroups/lb-test/providers/Microsoft.Network/loadBalancers/reflex-lb/frontendIPConfigurations/LoadBalancerFrontEnd",
        "name": "LoadBalancerFrontEnd",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "publicIPAddress": {
            "id": "/subscriptions/fb6ad26a-aa88-4d30-9c15-2fefd4fb3d5a/resourceGroups/lb-test/providers/Microsoft.Network/publicIPAddresses/PublicIPreflex-lb",
            "resourceGroup": "lb-test"
          }
        },
        "resourceGroup": "lb-test"
      }
    ],
    "inboundNatPools": [],
    "inboundNatRules": [],
    "loadBalancingRules": [],
    "outboundNatRules": [],
    "probes": [],
    "provisioningState": "Succeeded",
    "resourceGuid": "356b377f-e707-49aa-9bd9-e6f18e596df8"
  }
}
```


# create frontend-ip - internal (did not do - by default it's dynamic.)

az network lb frontend-ip create \
      --resource-group lb-test \
      --lb-name reflex-lb \
      --name lb-ip \
      --private-ip-address 10.0.0.7 \
      --subnet-name nrpvnetsubnet \
      --subnet-vnet-name nrpvnet

# Create backend pool (tested: OK)

az network lb address-pool create \
     --resource-group lb-test \
     --lb-name reflex-lb \
     --name backend-pool

# Create loadbalancer rules - one for every rule !! (tested: OK)

az network lb rule create  \
  --resource-group lb-test \
  --lb-name reflex-lb \
  --name HTTP_80 \
  --protocol tcp \
  --frontend-port 80 \
  --backend-port 80 \
  --backend-pool-name backend-pool

# Create a probe for every rule (Is this really needed ? )

az network lb probe create \
  --resource-group lb-test \
  --lb-name reflex-lb \
  --name HTTP_80_PROBE \
  --port 80 \
  --protocol tcp \
  --interval 300

# Link Probe to rule  ( this could have ben done in the creation of the rule IF the probe was created before)
az network lb rule update \
  --resource-group lb-test \
  --lb-name reflex-lb \
  --name HTTP_80 \
  --probe-name HTTP_80_PROBE

# VM's should already have NICs on them. Lets create one

az network nic create \
  --resource-group nrprg
  --name lb-nic1-be
  --subnet-name nrpvnetsubnet
  --subnet-vnet-name nrpvnet
  --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb"
  --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"



### TRY2

az network lb create --resource-group lb-test --name reflex-lb

```
{
  "loadBalancer": {
    "backendAddressPools": [
      {
        "etag": "W/\"ba4747b8-99dc-48d0-97f7-07dfa526bbdf\"",
        "id": "/subscriptions/fb6ad26a-aa88-4d30-9c15-2fefd4fb3d5a/resourceGroups/lb-test/providers/Microsoft.Network/loadBalancers/reflex-lb/backendAddressPools/reflex-lbbepool",
        "name": "reflex-lbbepool",
        "properties": {
          "provisioningState": "Succeeded"
        },
        "resourceGroup": "lb-test"
      }
    ],
    "frontendIPConfigurations": [
      {
        "etag": "W/\"ba4747b8-99dc-48d0-97f7-07dfa526bbdf\"",
        "id": "/subscriptions/fb6ad26a-aa88-4d30-9c15-2fefd4fb3d5a/resourceGroups/lb-test/providers/Microsoft.Network/loadBalancers/reflex-lb/frontendIPConfigurations/LoadBalancerFrontEnd",
        "name": "LoadBalancerFrontEnd",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "publicIPAddress": {
            "id": "/subscriptions/fb6ad26a-aa88-4d30-9c15-2fefd4fb3d5a/resourceGroups/lb-test/providers/Microsoft.Network/publicIPAddresses/PublicIPreflex-lb",
            "resourceGroup": "lb-test"
          }
        },
        "resourceGroup": "lb-test"
      }
    ],
    "inboundNatPools": [],
    "inboundNatRules": [],
    "loadBalancingRules": [],
    "outboundNatRules": [],
    "probes": [],
    "provisioningState": "Succeeded",
    "resourceGuid": "47e4f154-e411-4b5a-8469-225c43b005bb"
  }
}
```

az network lb rule create  \
  --resource-group lb-test \
  --lb-name reflex-lb \
  --name HTTP_80 \
  --protocol tcp \
  --frontend-port 80 \
  --backend-port 80 \
  --backend-pool-name reflex-lbbepool

```
{
  "backendAddressPool": {
    "id": "/subscriptions/fb6ad26a-aa88-4d30-9c15-2fefd4fb3d5a/resourceGroups/lb-test/providers/Microsoft.Network/loadBalancers/reflex-lb/backendAddressPools/reflex-lbbepool",
    "resourceGroup": "lb-test"
  },
  "backendPort": 80,
  "disableOutboundSnat": null,
  "enableFloatingIp": false,
  "etag": "W/\"12d33533-721b-48de-9ede-d5857c003493\"",
  "frontendIpConfiguration": {
    "id": "/subscriptions/fb6ad26a-aa88-4d30-9c15-2fefd4fb3d5a/resourceGroups/lb-test/providers/Microsoft.Network/loadBalancers/reflex-lb/frontendIPConfigurations/LoadBalancerFrontEnd",
    "resourceGroup": "lb-test"
  },
  "frontendPort": 80,
  "id": "/subscriptions/fb6ad26a-aa88-4d30-9c15-2fefd4fb3d5a/resourceGroups/lb-test/providers/Microsoft.Network/loadBalancers/reflex-lb/loadBalancingRules/HTTP_80",
  "idleTimeoutInMinutes": 4,
  "loadDistribution": "Default",
  "name": "HTTP_80",
  "probe": null,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "lb-test"
}
```

az network nic ip-config  address-pool add --address-pool reflex-lbbepool --lb-name reflex-lb --resource-group lb-test --nic-name vm-2180 --ip-config-name ipconfig1

az network nic ip-config  address-pool add --address-pool reflex-lbbepool --lb-name reflex-lb --resource-group lb-test --nic-name vm-1784 --ip-config-name ipconfig1
