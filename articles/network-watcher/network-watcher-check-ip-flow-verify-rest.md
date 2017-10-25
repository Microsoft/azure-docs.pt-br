---
title: "Verifique o tráfego com a verificação de fluxo de IP do Observador de Rede do Azure – REST | Microsoft Docs"
description: "Esse artigo descreve como verificar se o tráfego de ou para uma máquina virtual é permitido ou negado"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 3ccef9ef521b86ffc1eb6047174f4f9e5d9e4296
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Verifique se o tráfego é permitido ou negado com a verificação de fluxo de IP, um componente do Observador de Rede do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST do Azure](network-watcher-check-ip-flow-verify-rest.md)


Fluxo de IP Verifique se é um recurso do Observador de Rede que permite verificar se o tráfego é permitido para ou de uma máquina virtual. A validação pode ser executada para o tráfego de entrada ou saída. Esse cenário é útil para obter o estado atual de se uma máquina virtual pode se comunicar com um recurso externo ou um back-end. A Verificação de Fluxo de IP pode ser usada para verificar se as regras do Grupo de Segurança de Rede (NSG) estão configuradas corretamente e solucionar problemas de fluxos que estão sendo bloqueados por regras do NSG. Outro motivo para usar IP fluxo Verifique se é para garantir que deseja bloquear o tráfego está sendo bloqueado corretamente por NSG.

## <a name="before-you-begin"></a>Antes de começar

O ARMclient é usado para chamar a API REST usando o PowerShell. O ARMClient é encontrado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede.

## <a name="scenario"></a>Cenário

Esse cenário usa a verificação de fluxo de IP para verificar se uma máquina virtual pode se comunicar com outra máquina pela porta 443. Se o tráfego é negado, ele retorna a regra de segurança que está negando esse tráfego. Para saber mais sobre a verificação de fluxo de IP, visite [Visão geral de verificação de fluxo de IP](network-watcher-ip-flow-verify-overview.md)

Nesse cenário, você irá:

* Recuperar uma máquina virtual
* Chamar a verificação de fluxo de IP
* Verificar os resultados

## <a name="log-in-with-armclient"></a>Fazer logon com o ARMClient

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Recuperar uma máquina virtual

Execute o script a seguir para retornar para uma máquina virtual. O código a seguir precisa de valores para as variáveis:

* **subscriptionId** - a Id de assinatura a ser usada.
* **resourceGroupName** - o nome de um grupo de recursos que contém as máquinas virtuais.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

A informação necessária é a id no tipo `Microsoft.Compute/virtualMachines`. Os resultados devem ser semelhantes ao seguinte exemplo de código:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>Chamar a Verificação de fluxo de IP

O exemplo a seguir cria uma solicitação para verificar o tráfego para uma máquina virtual especificada. A resposta retorna se o tráfego é permitido ou não. Se o tráfego é negado, a resposta também indica a regra que bloqueia o tráfego.

> [!NOTE]
> A verificação de fluxo de IP exige que o recurso de máquina virtual seja alocado.

O script exige a Id do recurso de uma máquina virtual e de uma cartão do adaptador de rede na máquina virtual do recurso. Esses valores são fornecidos pelo resultado anterior.

> [!Important]
> Para todas as chamadas da API REST do Observador de Rede o nome do grupo de recursos no URI da solicitação é o grupo de recursos que contém a instância do Observador de Rede, não os recursos nos quais você está executando as ações de diagnóstico.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>Como entender os resultados

A resposta retornada informa se o tráfego é permitido ou negado. A resposta é semelhante a um dos exemplos a seguir:

**Permitido**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**Negado**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>Próximas etapas

Se o tráfego estiver sendo bloqueado, mas não devesse estar, consulte [Gerenciar Grupos de Segurança de Rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para saber mais sobre Grupos de Segurança de Rede.












