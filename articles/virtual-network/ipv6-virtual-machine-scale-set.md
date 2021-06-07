---
title: Implantar conjuntos de dimensionamento de máquinas virtuais com IPv6 no Azure
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar conjuntos de dimensionamento de máquinas virtuais com IPv6 em uma rede virtual do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f969d7edc22c9c36481ca42449193af5f8c7b0d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84709990"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Implantar conjuntos de dimensionamento de máquinas virtuais com IPv6 no Azure

Este artigo mostra como implantar um conjunto de dimensionamento de máquinas virtuais de pilha dupla (IPv4 + IPv6) com um balanceador de carga externo de pilha dupla em uma rede virtual do Azure. O processo para criar um conjunto de dimensionamento de máquinas virtuais compatível com IPv6 é praticamente idêntico ao processo de criação de VMs individuais descritas [aqui](ipv6-configure-standard-load-balancer-template-json.md). Você começará com as etapas que são semelhantes àquelas descritas para VMs individuais:
1.    Crie IPs públicos IPv4 e IPv6.
2.    Crie um balanceador de carga de pilha dupla.  
3.    Criar regras de NSG (grupo de segurança de rede).  

A única etapa que é diferente das VMs individuais é criar a configuração da NIC (interface de rede) que usa o recurso do conjunto de dimensionamento de máquinas virtuais: networkProfile/networkInterfaceConfigurations. A estrutura JSON é semelhante à do objeto Microsoft. Network/networkInterfaces usado para VMs individuais com a adição de configuração da NIC e da IpConfiguration IPv4 como a interface primária usando o atributo **"Primary": true**  , como mostrado no exemplo a seguir:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Exemplo de JSON de modelo de conjunto de dimensionamento de máquinas virtuais

Para implantar um conjunto de dimensionamento de máquinas virtuais de pilha dupla (IPv4 + IPv6) com o Load Balancer externo de pilha dupla e o modelo de exibição de rede virtual [aqui](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o suporte a IPv6 em redes virtuais do Azure, consulte [o que é IPv6 para a rede virtual do Azure?](ipv6-overview.md).
