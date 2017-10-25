---
title: "Automatizar a auditoria do NSG com a exibição de grupo de segurança do Observador de Rede do Azure | Microsoft Docs"
description: "Esta página fornece instruções sobre como configurar a auditoria de um Grupo de Segurança de Rede"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 57f2200e541eeb629f72d60ffa0acb2d8233c018
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizar a auditoria do NSG com a exibição de grupo de segurança do Observador de Rede do Azure

Os clientes normalmente enfrentam o desafio de verificar a postura de segurança de sua infraestrutura. Esse desafio não é diferente para as VMs no Azure. É importante ter um perfil de segurança semelhante com base nas regras do NSG (Grupo de Segurança de Rede) aplicadas. Com a exibição do Grupo de segurança, agora você pode obter a lista de regras aplicadas a uma VM dentro de um NSG. Você pode definir um perfil de ouro de segurança do NSG e iniciar a Exibição do grupo de segurança com uma cadência semanal e comparar a saída para o perfil de ouro e criar um relatório. Dessa forma, você pode identificar com facilidade todas as VMs que não estão de acordo com o perfil de segurança recomendado.

Se você estiver familiarizado com os Grupos de segurança de rede, visite [Visão geral de segurança de rede](../virtual-network/virtual-networks-nsg.md)

## <a name="before-you-begin"></a>Antes de começar

Nesse cenário, você compara uma linha de base válida com os resultados da exibição do grupo de segurança retornados para uma máquina virtual.

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede. O cenário também pressupõe que exista um grupo de recursos com uma máquina virtual válida a ser usada.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo obtém a exibição do grupo de segurança de uma máquina virtual.

Neste cenário, você:

- Recuperar um conjunto de regras válido
- Recuperar uma máquina virtual com API Rest
- Obter a exibição de grupo de segurança para máquina virtual
- Avaliar a resposta

## <a name="retrieve-rule-set"></a>Recuperar o conjunto de regras

A primeira etapa neste exemplo é trabalhar com uma linha de base existente. O exemplo a seguir é um json extraído de um Grupo de Segurança de Rede existente usando o cmdlet `Get-AzureRmNetworkSecurityGroup`, que é usado como a linha de base para este exemplo.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Converter o conjunto de regras em objetos do PowerShell

Nesta etapa, lemos um arquivo json criado anteriormente com as regras que devem estar no Grupo de Segurança de Rede para este exemplo.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Recuperar o Observador de Rede

A próxima etapa é recuperar a instância do Observador de Rede. A variável `$networkWatcher` é passada para o cmdlet `AzureRmNetworkWatcherSecurityGroupView`.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Obter uma VM

Uma máquina virtual é necessária para executar o cmdlet `Get-AzureRmNetworkWatcherSecurityGroupView`. O exemplo a seguir obtém um objeto de VM.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Recuperar o modo de exibição de grupo de segurança

A próxima etapa é recuperar o resultado de exibição do grupo de segurança. Esse resultado é comparado com o json de "linha de base" mostrado anteriormente.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analisar os resultados

A resposta é agrupada por interfaces de rede. Os diferentes tipos de regras retornados são regras de segurança padrão e eficazes. O resultado é dividido pela forma de aplicação, em uma sub-rede ou uma NIC virtual.

O script PowerShell a seguir compara os resultados da Exibição de grupo de segurança para uma saída existente de um NSG. O exemplo a seguir é um exemplo simples de como os resultados podem ser comparados com o cmdlet `Compare-Object`.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

O exemplo a seguir é o resultado. Você pode ver que duas das regras que estavam no primeiro conjunto de regras não estavam presentes na comparação.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Próximas etapas

Se as configurações foram alteradas, confira [Gerenciar grupos de segurança de rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para rastrear as regras de segurança e o Grupo de Segurança de Rede que estão em questão.













