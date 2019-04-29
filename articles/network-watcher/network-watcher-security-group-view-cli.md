---
title: Analisar a segurança de rede com exibição de grupo de segurança do Observador de Rede do Azure - CLI do Azure | Microsoft Docs
description: Este artigo descreve como usar a CLI do Azure para analisar um título de máquinas virtuais com o modo de exibição de grupo de segurança.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: fc86b2fd7156ff84b7d91fd39c79caccb815312c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727798"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analisar a segurança de máquina Virtual com o modo de exibição de grupo de segurança usando a CLI do Azure

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI do Azure](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

Exibição de grupo de segurança retorna as regras de segurança de rede configurados e eficaz que são aplicadas a uma máquina virtual. Esse recurso é útil para auditoria e diagnosticar grupos de segurança de rede e as regras configuradas em uma VM para garantir que o tráfego está sendo corretamente permitido ou negado. Neste artigo, mostraremos como recuperar as regras de segurança configuradas e em vigor para uma máquina virtual usando a CLI do Azure

Para executar as etapas deste artigo, será necessário [instalar a interface de linha de comando do Azure para Mac, Linux e Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de rede](network-watcher-create.md) para criar um Observador de rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo recupera as regras de segurança configuradas e em vigor para uma determinada máquina virtual.

## <a name="get-a-vm"></a>Obter uma VM

Uma máquina virtual é necessária para executar o cmdlet `vm list`. O comando a seguir lista as máquinas virtuais em um grupo de recursos:

```azurecli
az vm list -resource-group resourceGroupName
```

Se você conhecer a máquina virtual, poderá usar o `vm show` para obter sua Id de recurso:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Recuperar o modo de exibição de grupo de segurança

A próxima etapa é recuperar o resultado de exibição do grupo de segurança.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Exibição dos resultados

O exemplo a seguir é uma resposta abreviada dos resultados retornados. Os resultados mostram todas as regras de segurança efetiva e aplicados na máquina virtual dividida em grupos de **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** e **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Visite [auditoria de segurança grupos NSG (rede) com o Observador de Rede](network-watcher-nsg-auditing-powershell.md) para aprender a automatizar a validação dos grupos de segurança de rede.

Saiba mais sobre as regras de segurança que são aplicadas aos recursos de rede no artigo [Visão geral de exibição do grupo de segurança](network-watcher-security-group-view-overview.md)
