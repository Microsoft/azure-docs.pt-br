---
title: Analisar segurança de rede-exibição do grupo de segurança-API REST do Azure
titleSuffix: Azure Network Watcher
description: Este artigo descreverá como usar a API REST do Azure para analisar uma segurança de máquinas virtuais com o modo de exibição de grupo de segurança.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 2efd3e9c9ca97ea3d94b03bd5e440cd24d5da5da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960573"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analisar a segurança de sua máquina virtual com o modo de exibição de Grupo de Segurança usando a API REST

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI do Azure](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> A API de exibição do grupo de segurança não está mais sendo mantida e será substituída em breve. Use o [recurso de regras de segurança em vigor](./network-watcher-security-group-view-overview.md) , que fornece a mesma funcionalidade. 

Exibição de grupo de segurança retorna as regras de segurança de rede configurados e eficaz que são aplicadas a uma máquina virtual. Esse recurso é útil para auditoria e diagnosticar grupos de segurança de rede e as regras configuradas em uma VM para garantir que o tráfego está sendo corretamente permitido ou negado. Neste artigo, mostraremos como recuperar as regras de segurança efetivas e aplicadas para uma máquina virtual usando a API REST


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Nesse cenário, chame a API Rest do Observador de Rede para obter o modo de exibição do grupo de segurança de uma máquina virtual. O ARMclient é usado para chamar a API REST usando o PowerShell. O ARMClient é encontrado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de rede](network-watcher-create.md) para criar um Observador de rede. O cenário também pressupõe que exista um grupo de recursos com uma máquina virtual válida a ser usada.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo recupera as regras de segurança efetivas e aplicadas para uma determinada máquina virtual.

## <a name="log-in-with-armclient"></a>Fazer logon com o ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Recuperar uma máquina virtual

Execute o script a seguir para retornar uma máquina virtual. O código a seguir precisa de variáveis:

- **SubscriptionId** -a ID da assinatura também pode ser recuperada com o cmdlet **Get-AzSubscription** .
- **resourceGroupName** - o nome de um grupo de recursos que contém as máquinas virtuais.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

As informações necessárias são a **id** no tipo `Microsoft.Compute/virtualMachines` na resposta, conforme mostrado no exemplo a seguir:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Obter a exibição de grupo de segurança para máquina virtual

O exemplo a seguir solicita a exibição do grupo de segurança de uma máquina virtual de destino. Os resultados desse exemplo podem ser usados para comparar as regras e segurança definidas pela origem a fim de procurar os descompassos de configuração.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Exibir a resposta

O exemplo a seguir é a resposta retornada do comando anterior. Os resultados mostram todas as regras de segurança efetiva e aplicados na máquina virtual dividida em grupos de **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** e **EffectiveSecurityRules**.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Visite [auditoria de segurança grupos NSG (rede) com o Observador de Rede](network-watcher-security-group-view-powershell.md) para aprender a automatizar a validação dos grupos de segurança de rede.