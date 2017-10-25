---
title: "Analisar a segurança de rede com exibição de grupo de segurança do Observador de Rede do Azure – CLI 1.0 do Azure | Microsoft Docs"
description: "Este artigo descreve como usar a CLI 1.0 do Azure para analisar um título de máquinas virtuais com o modo de exibição de grupo de segurança."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f27157129bea4e47a2e0e6cc1169b9e4887bdd78
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli-10"></a>Analisar a segurança de máquina Virtual com o modo de exibição de grupo de segurança usando a CLI 1.0 do Azure

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [CLI 2.0](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

Exibição de grupo de segurança retorna as regras de segurança de rede configurados e eficaz que são aplicadas a uma máquina virtual. Esse recurso é útil para auditoria e diagnosticar grupos de segurança de rede e as regras configuradas em uma VM para garantir que o tráfego está sendo corretamente permitido ou negado. Neste artigo, mostraremos como recuperar as regras de segurança configuradas e em vigor para uma máquina virtual usando a CLI do Azure

Este artigo usa a CLI 1.0 do Azure para plataforma cruzada, que está disponível para Windows, Mac e Linux. Atualmente, o Observador de Rede usa a CLI 1.0 do Azure para dar suporte à CLI.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de rede](network-watcher-create.md) para criar um Observador de rede.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo recupera as regras de segurança configuradas e em vigor para uma determinada máquina virtual.

## <a name="get-a-vm"></a>Obter uma VM

Uma máquina virtual é necessária para executar o cmdlet `vm list`. O comando a seguir lista as máquinas virtuais em um grupo de recursos:

```azurecli
azure vm list -g resourceGroupName
```

Se você conhecer a máquina virtual, poderá usar o `vm show` para obter sua Id de recurso:

```azurecli
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Recuperar o modo de exibição de grupo de segurança

A próxima etapa é recuperar o resultado de exibição do grupo de segurança. A adição do sinalizador "– json" formatará os resultados em json.

```azurecli
azure network watcher security-group-view -g resourceGroupName -n networkWatcherName -t targetResourceId --json
```

## <a name="viewing-the-results"></a>Exibição dos resultados

O exemplo a seguir é uma resposta abreviada dos resultados retornados. Os resultados mostram todas as regras de segurança efetiva e aplicados na máquina virtual dividida em grupos de **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** e **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic",
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testvm",
          "securityRules": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/test-nsg/securityRules/default-allow-rdp",
              "protocol": "TCP",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 1000,
              "direction": "Inbound",
              "provisioningState": "Succeeded",
              "name": "default-allow-rdp",
              "etag": "W/\"00000000-0000-0000-0000-000000000000\""
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/networkSecurityGroups//defaultSecurityRules/",
            "description": "Allow inbound traffic from all VMs in VNET",
            "protocol": "*",
            "sourcePortRange": "*",
            "destinationPortRange": "*",
            "sourceAddressPrefix": "VirtualNetwork",
            "destinationAddressPrefix": "VirtualNetwork",
            "access": "Allow",
            "priority": 65000,
            "direction": "Inbound",
            "provisioningState": "Succeeded",
            "name": "AllowVnetInBound"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Visite [auditoria de segurança grupos NSG (rede) com o Observador de Rede](network-watcher-nsg-auditing-powershell.md) para aprender a automatizar a validação dos grupos de segurança de rede.

Saiba mais sobre as regras de segurança que são aplicadas aos recursos de rede no artigo [Visão geral de exibição do grupo de segurança](network-watcher-security-group-view-overview.md)
