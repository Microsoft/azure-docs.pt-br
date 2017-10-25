## <a name="nic"></a>NIC
Um recurso de placa de interface de rede (NIC) fornece conectividade de rede com uma sub-rede existente em um recurso de VNet. Embora você possa criar uma NIC como um objeto autônomo, você precisa associá-la a outro objeto para fornecer a conectividade. Uma NIC pode ser usada para conectar uma VM a uma sub-rede, um endereço IP público ou um balanceador de carga.  

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **virtualMachine** |A VM à qual a NIC está associada. |/subscriptions/{guid}/../Microsoft.Compute/virtualMachines/vm1 |
| **macAddress** |Endereço MAC da NIC |qualquer valor entre 4 e 30 |
| **networkSecurityGroup** |NSG associado à NIC |/subscriptions/{guid}/../Microsoft.Network/networkSecurityGroups/myNSG1 |
| **dnsSettings** |Configurações de DNS para a NIC |veja [PIP](#Public-IP-address) |

Uma Placa de Interface de rede ou NIC representa uma interface de rede que pode ser associada a uma VM (máquina virtual). Uma VM pode ter uma ou mais NICs.

![NICs em uma única VM](./media/resource-groups-networking/Figure3.png)

### <a name="ip-configurations"></a>Configurações de IP
NICs têm um objeto filho chamado **ipConfigurations** que contém as seguintes propriedades:

| Propriedade | Descrição | Valores de exemplo |
| --- | --- | --- |
| **subnet** |A sub-rede à qual a NIC está conectada. |/subscriptions/{guid}/../Microsoft.Network/virtualNetworks/myvnet1/subnets/mysub1 |
| **privateIPAddress** |Endereço IP da NIC na sub-rede |10.0.0.8 |
| **privateIPAllocationMethod** |Método de alocação de IP |Dinâmico ou estático |
| **enableIPForwarding** |Se a NIC pode ser usada para roteamento |true ou false |
| **primary** |Se a NIC é a NIC primária para a VM |true ou false |
| **publicIPAddress** |PIP associado à NIC |consulte [Configurações DNS](#DNS-settings) |
| **loadBalancerBackendAddressPools** |Pools de endereços de back-end aos quais a NIC está associada | |
| **loadBalancerInboundNatRules** |Regras de NAT do balanceador de carga às quais a NIC está associada | |

Endereço IP de público exemplo, no formato JSON:

    {
        "name": "lb-nic1-be",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkInterfaces",
        "location": "eastus",
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "ipConfigurations": [
                {
                    "name": "NIC-config",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
                    "etag": "W/\"0027f1a2-3ac8-49de-b5d5-fd46550500b1\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "privateIPAddress": "10.0.0.4",
                        "privateIPAllocationMethod": "Dynamic",
                        "subnet": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
                            }
                        ]
                    }
                }
            ],
            "dnsSettings": { ... },
            "macAddress": "00-0D-3A-10-F1-29",
            "enableIPForwarding": false,
            "primary": true,
            "virtualMachine": {
                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
            }
        }
    }

### <a name="additional-resources"></a>Recursos adicionais
* Leia a [documentação de referência da API REST](https://msdn.microsoft.com/library/azure/mt163579.aspx) para obter NICs.

