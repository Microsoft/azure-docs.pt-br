---
title: Rede para conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Propriedades da rede de configuração dos conjuntos de dimensionamento de máquina virtual do Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: manayar
ms.openlocfilehash: a9141adfb1dd05efd73061379be89ddf27ab3832
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803052"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Rede para conjuntos de dimensionamento de máquinas virtuais do Azure

Quando você implanta um conjunto de dimensionamento de máquinas virtuais do Azure pelo portal, determinadas propriedades de rede são padronizadas, por exemplo um Azure Load Balancer com regras NAT de entrada. Este artigo descreve como usar alguns dos recursos mais avançados de rede que podem ser configurados com conjuntos de dimensionamento.

Todos os recursos discutidos neste artigo podem ser configurados usando modelos do ARM (Azure Resource Manager). Exemplos da CLI do Azure e PowerShell também estão incluídos para os recursos selecionados.

## <a name="accelerated-networking"></a>Rede Acelerada
A Rede Acelerada do Azure melhora o desempenho de rede habilitando a SR-IOV (virtualização de E/S de raiz única) para uma máquina virtual. Para saber mais sobre o uso de Rede Acelerada, consulte Rede acelerada para máquinas virtuais do [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) ou do [Linux](../virtual-network/create-vm-accelerated-networking-cli.md). Para usar a rede acelerado com conjuntos de dimensionamento, defina enableAcceleratedNetworking como **true** nas configurações de networkInterfaceConfigurations do conjunto de dimensionamento. Por exemplo: 
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
Quando um conjunto de dimensionamento é criado usando o portal do Azure, um balanceador de carga novo é criado para a maioria das opções de configuração. Se você criar um conjunto de dimensionamento que precisa para fazer referência a um balanceador de carga existente, você pode fazer isso usando a CLI. O script de exemplo a seguir cria um balanceador de carga e, em seguida, cria um conjunto de dimensionamento que faz referência a ele:
```bash
az network lb create \
    -g lbtest \
    -n mylb \
    --vnet-name myvnet \
    --subnet mysubnet \
    --public-ip-address-allocation Static \
    --backend-pool-name mybackendpool

az vmss create \
    -g lbtest \
    -n myvmss \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username negat \
    --ssh-key-value /home/myuser/.ssh/id_rsa.pub \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myvnet \
    --subnet mysubnet \
    --lb mylb \
    --backend-pool-name mybackendpool
```

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>Criar um conjunto de dimensionamento que referencia um Gateway de Aplicativo
Para criar um conjunto de dimensionamento que usa um gateway de aplicativo, referencie o pool de endereços de back-end do gateway de aplicativo na seção ipConfigurations do conjunto de dimensionamento como nesta configuração de modelo ARM:
```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> Observe que o gateway de aplicativo deve estar na mesma rede virtual do conjunto de dimensionamento, mas deve estar em uma sub-rede diferente do conjunto de dimensionamento.


## <a name="configurable-dns-settings"></a>Configurações DNS configuráveis
Por padrão, os conjuntos de dimensionamento assumem as configurações DNS específicas da VNET e da sub-rede na qual eles foram criados. No entanto, você pode definir diretamente as configurações DNS de um conjunto de dimensionamento.

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Como criar um conjunto de dimensionamento com servidores DNS configuráveis
Para criar um conjunto de dimensionamento com uma configuração DNS personalizada usando a CLI do Azure, adicione a **-- dns-servers** argumento para o **vmss criar** separados de comando, seguido por um espaço de endereços ip do servidor. Por exemplo: 
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
Para criar uma escala configurada com um nome DNS personalizado para máquinas virtuais usando a CLI, adicione o argumento **--vm-domain-name** ao comando **virtual machine scale set create**, seguido por uma cadeia de caracteres representando o nome do domínio.

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
Para listar os endereços IP públicos atribuídos às máquinas virtuais do conjunto de escalas usando a CLI, use o comando **az vmss list-instance-public-ips**.

Para listar os endereços IP públicos do conjunto de dimensionamento usando o PowerShell, use o comando _Get-AzPublicIpAddress_. Por exemplo: 
```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

Você também pode consultar diretamente os endereços IP públicos referenciando a ID de recurso da configuração de endereço IP público. Por exemplo: 
```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Você também pode exibir os endereços IP públicos atribuídos às máquinas virtuais do conjunto de dimensionamento consultando o [Azure Resource Explorer](https://resources.azure.com) ou a API REST do Azure com a versão **2017-03-30** ou superior.

Para consultar o [Azure Resource Explorer](https://resources.azure.com):

1. Abra o [Azure Resource Explorer](https://resources.azure.com) em um navegador da Web.
1. Expanda *inscrições* no lado esquerdo clicando em *+* ao lado dele. Se você tiver apenas um item em *assinaturas*, ele já poderá ser expandido.
1. Expanda a assinatura.
1. Expanda seu grupo de recursos.
1. Expanda *provedores*.
1. Expanda *Microsoft.Compute*.
1. Expanda *virtualMachineScaleSets*.
1. Expanda o conjunto de dimensionamento.
1. Clique em *publicipaddresses*.

Para consultar a API REST do Azure:

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Exemplo de saída do [Azure Resource Explorer](https://resources.azure.com) e API REST do Azure:
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
Você pode ter até 8 NICs por máquina virtual, dependendo do tamanho da máquina. O número máximo de NICs por máquina está disponível no [artigo Tamanho de VM](../virtual-machines/windows/sizes.md). Todas as NICs conectadas a uma instância de VM devem se conectar à mesma rede virtual. As NICs podem se conectar a diferentes sub-redes, mas todas as sub-redes devem fazer parte da mesma rede virtual.

O exemplo a seguir é um perfil de rede do conjunto de dimensionamento mostrando várias entradas NIC e vários IPs públicos por máquina virtual:

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

## <a name="nsg--asgs-per-scale-set"></a>NSG e Grupos de Segurança do Aplicativo por conjunto de dimensionamento
[Grupos de Segurança de Rede](../virtual-network/security-overview.md) permitem filtrar o tráfego de e para recursos do Azure em uma rede virtual do Azure usando regras de segurança. [Grupos de Segurança de Aplicativo](../virtual-network/security-overview.md#application-security-groups) permitem lidar com a segurança de rede de recursos do Azure e agrupá-los como uma extensão da estrutura de seu aplicativo.

Os Grupos de Segurança de Rede podem ser aplicados diretamente a um conjunto de dimensionamento referenciando-os na seção de configuração da interface de rede das propriedades de máquina virtual do conjunto de dimensionamento.

Grupos de Segurança do Aplicativo também podem ser especificados diretamente a um conjunto de dimensionamento referenciando-os na seção de configurações de IP do adaptador de rede das propriedades de máquina virtual do conjunto de dimensionamento.

Por exemplo:  
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
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
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

Para verificar se o Grupo de Segurança de Rede está associado ao conjunto de dimensionamento, use o comando `az vmss show`. O exemplo abaixo usa `--query` para filtrar os resultados e mostrar apenas a seção relevante da saída.

```bash
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

Para verificar se o Grupo de Segurança de Aplicativo está associado ao conjunto de dimensionamento, use o comando `az vmss show`. O exemplo abaixo usa `--query` para filtrar os resultados e mostrar apenas a seção relevante da saída.

```bash
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```



## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre as redes virtuais do Azure, confira [Visão geral das redes virtuais do Azure](../virtual-network/virtual-networks-overview.md).
