---
title: "Rede para conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs"
description: "Propriedades da rede de configuração dos conjuntos de dimensionamento de máquina virtual do Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: guybo
ms.openlocfilehash: a8520c6d8962cc362fc935f6b515a299c0ce75b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Rede para conjuntos de dimensionamento de máquinas virtuais do Azure

Quando você implanta um conjunto de dimensionamento de máquinas virtuais do Azure pelo portal, determinadas propriedades de rede são padronizadas, por exemplo um Azure Load Balancer com regras NAT de entrada. Este artigo descreve como usar alguns dos recursos mais avançados de rede que podem ser configurados com conjuntos de dimensionamento.

Todos os recursos discutidos neste artigo podem ser configurados usando modelos do ARM (Azure Resource Manager). Exemplos da CLI do Azure e PowerShell também estão incluídos para os recursos selecionados. Use a CLI 2.10 e o PowerShell 4.2.0 ou posterior.

## <a name="accelerated-networking"></a>Rede Acelerada
A [Rede Acelerada](../virtual-network/virtual-network-create-vm-accelerated-networking.md) permite SR-IOV (Virtualização de E/S de Raiz Única) para uma VM (máquina virtual), melhorando muito seu desempenho de rede. Para usar a rede acelerado com conjuntos de dimensionamento, defina enableAcceleratedNetworking como **true** nas configurações de networkInterfaceConfigurations do conjunto de dimensionamento. Por exemplo:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="create-a-scale-set-that-references-an-existing-azure-load-balancer"></a>Criar um conjunto de dimensionamento que faz referência a um Azure Load Balancer existente
Quando um conjunto de dimensionamento é criado usando o portal do Azure, um balanceador de carga novo é criado para a maioria das opções de configuração. Se você criar um conjunto de dimensionamento, que precisa fazer referência a um balanceador de carga existente, isso pode ser feito usando a CLI. O script de exemplo a seguir cria um balanceador de carga e, em seguida, cria um conjunto de dimensionamento que faz referência a ele:
```bash
az network lb create -g lbtest -n mylb --vnet-name myvnet --subnet mysubnet --public-ip-address-allocation Static --backend-pool-name mybackendpool

az vmss create -g lbtest -n myvmss --image Canonical:UbuntuServer:16.04-LTS:latest --admin-username negat --ssh-key-value /home/myuser/.ssh/id_rsa.pub --upgrade-policy-mode Automatic --instance-count 3 --vnet-name myvnet --subnet mysubnet --lb mylb --backend-pool-name mybackendpool

```

## <a name="configurable-dns-settings"></a>Configurações DNS configuráveis
Por padrão, os conjuntos de dimensionamento assumem as configurações DNS específicas da VNET e da sub-rede na qual eles foram criados. No entanto, você pode definir diretamente as configurações DNS de um conjunto de dimensionamento.
~
### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Como criar um conjunto de dimensionamento com servidores DNS configuráveis
Para criar um conjunto de dimensionamento com uma configuração DNS personalizada usando a CLI 2.0, adicione o argumento **--dns-servers** ao comando **vmss create**, seguido por endereços IP do servidor separados por espaços. Por exemplo:
```bash
--dns-servers 10.0.0.6 10.0.0.5
```
Para configurar servidores DNS personalizados em um modelo do Azure, adicione uma propriedade dnsSettings à seção de networkInterfaceConfigurations do conjunto de dimensionamento. Por exemplo:
```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Como criar um conjunto de dimensionamento com nomes de domínio configuráveis de máquina de virtual
Para criar um conjunto de dimensionamento com um nome DNS personalizado para máquinas virtuais usando a CLI 2.0, adicione o argumento **--vm-domain-name** ao comando **vmss create**, seguido por uma cadeia de caracteres representando o nome de domínio.

Para configurar o nome de domínio em um modelo do Azure, adicione uma propriedade **dnsSettings** à seção **networkInterfaceConfigurations**  do conjunto de dimensionamento. Por exemplo:

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": "true",
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

A saída, para um nome de dns de máquina virtual individual teria no seguinte formato: 
```
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>IPv4 público por máquina virtual
Em geral, as máquinas de virtuais do conjunto de dimensionamento do Azure não exigem seus próprios endereços IP públicos. Na maioria dos cenários, é mais econômico e seguro associar um endereço IP público a um balanceador de carga ou a uma máquina virtual individual (também conhecida como um jumpbox) que encaminha as conexões de entrada para dimensionar máquinas virtuais do conjunto de dimensionamento, conforme necessário (por exemplo, por meio de regras NAT de entrada).

No entanto, alguns cenários exigem que as máquinas de virtuais do conjunto de dimensionamento do Azure tenham seus próprios endereços IP públicos. Um exemplo é o jogo, onde um console precisa fazer uma conexão direta com uma máquina virtual da nuvem, que está fazendo o processamento da física do jogo. Outro exemplo é onde as máquinas virtuais precisam fazer conexões externas umas com as outras em regiões em um banco de dados distribuído.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Como criar um conjunto de dimensionamento com IP público por máquina de virtual
Para criar um conjunto de dimensionamento que atribui um endereço IP público para cada máquina virtual com a CLI 2.0, adicione o parâmetro **--public-ip-per-vm** ao comando **vmss create**. 

Para criar um conjunto de dimensionamento usando um modelo do Azure, verifique se a versão da API do recurso Microsoft.Compute/virtualMachineScaleSets seja, pelo menos, **2017-03-30** e adicione uma propriedade JSON **publicIpAddressConfiguration** à seção ipConfigurations do conjunto de dimensionamento. Por exemplo:

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```
Modelo de exemplo: [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Como consultar os endereços IP públicos das máquinas virtuais em um conjunto de dimensionamento
Use o comando **az vmss list-instance-public-ips** para listar os endereços IP públicos atribuídos às máquinas virtuais do conjunto de dimensionamento usando a CLI 2.0.

Para listar os endereços IP públicos do conjunto de dimensionamento usando o PowerShell, use o comando _Get-AzureRmPublicIpAddress_. Por exemplo:
```PowerShell
PS C:\> Get-AzureRmPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

Você também pode consultar diretamente os endereços IP públicos referenciando a ID de recurso da configuração de endereço IP público. Por exemplo:
```PowerShell
PS C:\> Get-AzureRmPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Para consultar os endereços IP públicos atribuídos às máquinas virtuais do conjunto de dimensionamento usando o [Azure Resource Explorer](https://resources.azure.com) ou a API REST do Azure com a versão **2017-03-30** ou superior.

Para exibir os endereços IP públicos de um conjunto de dimensionamento usando o Resource Explorer, consulte a seção **publicipaddresses** em seu conjunto de dimensionamento. Por exemplo: https://resources.azure.com/subscriptions/_your_sub_id_/resourceGroups/_your_rg_/providers/Microsoft.Compute/virtualMachineScaleSets/_your_vmss_/publicipaddresses

```
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Saída de exemplo:
```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Vários endereços IP por NIC
Todas as NICs anexadas a uma VM em um conjunto de dimensionamento têm uma ou mais configurações IP associadas a elas. Cada configuração é atribuída a um endereço IP privado. Cada configuração também pode ter um recurso de endereço IP público associado a ela. Para entender quantos endereços IP podem ser atribuídos a uma NIC e quantos endereços IP públicos você pode usar em uma assinatura do Azure, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="multiple-nics-per-virtual-machine"></a>Várias NICs por máquina virtual
Você pode ter até 8 NICs por máquina virtual, dependendo do tamanho da máquina. O número máximo de NICs por máquina está disponível no [artigo Tamanho de VM](../virtual-machines/windows/sizes.md). O exemplo a seguir é um perfil de rede do conjunto de dimensionamento mostrando várias entradas NIC e vários IPs públicos por máquina virtual:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": "true",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": "false",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg-per-scale-set"></a>NSG por conjunto de dimensionamento
Os Grupos de Segurança de Rede podem ser aplicados diretamente a um conjunto de dimensionamento referenciando-os na seção de configuração da interface de rede das propriedades de máquina virtual do conjunto de dimensionamento.

Por exemplo: 
```
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre redes do Azure, confira [essa documentação](../virtual-network/virtual-networks-overview.md).
