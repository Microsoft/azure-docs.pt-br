---
title: "Monitorar operações, eventos e contadores para NSGs | Microsoft Docs"
description: Saiba como habilitar logs operacionais , de eventos e de contadores para NSGs
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2017
ms.author: jdial
ms.openlocfilehash: 552f37dd704de25159bc0f0ad34fdae9ed8b73f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>Análise de logs para NSGs (grupos de segurança de rede)

Você pode habilitar as seguintes categorias de log de diagnóstico para NSGs:

* **Evento:** contém entradas para as regras NSG que são aplicadas às VMs e funções de instância com base no endereço MAC. O status para essas regras é coletado a cada 60 segundos.
* **Contador de regras:** contém entradas de quantas vezes cada regra NSG é aplicada para negar ou permitir tráfego.

> [!NOTE]
> Os logs de diagnóstico estão disponíveis apenas para NSGs implantados por meio do modelo de implantação do Azure Resource Manager. Você não pode habilitar o log de diagnóstico para NSGs implantados por meio do modelo de implantação clássico. Para entender melhor esses dois modelos, leia o artigo [Understanding Azure deployment models](../resource-manager-deployment-model.md) (Noções básicas sobre os modelos de implantação do Azure).

O log de atividades (anteriormente conhecido como logs de auditoria ou operacionais) está habilitado por padrão para NSGs criados por meio de qualquer modelo de implantação do Azure. Para determinar quais operações foram concluídas em NSGs no log de atividades, procure entradas contendo os seguintes tipos de recursos: 

- Microsoft.ClassicNetwork/networkSecurityGroups 
- Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
- Microsoft.Network/networkSecurityGroups
- Microsoft.Network/networkSecurityGroups/securityRules 

Leia o artigo [Visão geral do Log de Atividades do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) para saber mais sobre os logs de atividade. 

## <a name="enable-diagnostic-logging"></a>Habilitar registro em log de diagnóstico

O log de diagnóstico deve ser habilitado para *cada* NSG do qual você quer coletar dados. O artigo [Visão geral dos Logs de Diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) explica para onde os logs de diagnóstico podem ser enviados. Caso não tenha um NSG, conclua as etapas no artigo sobre como [criar um grupo de segurança de rede](virtual-networks-create-nsg-arm-pportal.md) para criar um. Você pode habilitar o log de diagnóstico do NSG usando qualquer um dos seguintes métodos:

### <a name="azure-portal"></a>Portal do Azure

Para usar o portal a fim de habilitar o log, entre no [portal](https://portal.azure.com). Clique em **Mais serviços** e digite *grupos de segurança de rede*. Escolha o NSG para o qual quer habilitar o log. Siga as instruções para recursos de não computação no artigo [Habilite os logs de diagnóstico no portal](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs). Selecione **NetworkSecurityGroupEvent**, **NetworkSecurityGroupRuleCounter** ou ambas as categorias de log.

### <a name="powershell"></a>PowerShell

Para usar o PowerShell a fim de habilitar o log, siga as instruções no artigo [Habilitar os Logs de Diagnóstico pelo PowerShell](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs). Avalie as seguintes informações antes de inserir um comando do artigo:

- Você pode determinar o valor a ser usado para o parâmetro `-ResourceId` substituindo o seguinte [texto], conforme apropriado, e digitando o comando `Get-AzureRmNetworkSecurityGroup -Name [nsg-name] -ResourceGroupName [resource-group-name]`. A saída de ID do comando é semelhante a */subscriptions/[ID da Assinatura]/resourceGroups/[grupo de recursos]/providers/Microsoft.Network/networkSecurityGroups/[nome do NSG]*.
- Se quiser coletar apenas dados da categoria de log, adicione `-Categories [category]` ao fim do comando no artigo, onde a categoria é *NetworkSecurityGroupEvent* ou *NetworkSecurityGroupRuleCounter*. Se não quiser usar o parâmetro `-Categories`, a coleta de dados será habilitada para ambas as categorias de log.

### <a name="azure-command-line-interface-cli"></a>CLI (interface de linha de comando) do Azure

Para usar a CLI a fim de habilitar o log, siga as instruções no artigo [Habilitar os Logs de Diagnóstico pela CLI](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs). Avalie as seguintes informações antes de inserir um comando do artigo:

- Você pode determinar o valor a ser usado para o parâmetro `-ResourceId` substituindo o seguinte [texto], conforme apropriado, e digitando o comando `azure network nsg show [resource-group-name] [nsg-name]`. A saída de ID do comando é semelhante a */subscriptions/[ID da Assinatura]/resourceGroups/[grupo de recursos]/providers/Microsoft.Network/networkSecurityGroups/[nome do NSG]*.
- Se quiser coletar apenas dados da categoria de log, adicione `-Categories [category]` ao fim do comando no artigo, onde a categoria é *NetworkSecurityGroupEvent* ou *NetworkSecurityGroupRuleCounter*. Se não quiser usar o parâmetro `-Categories`, a coleta de dados será habilitada para ambas as categorias de log.

## <a name="logged-data"></a>Dados registrados

Os dados formatados pelo JSON são gravados em ambos os logs. Os dados específicos gravados para cada tipo de log são listados nas seguintes seções:

### <a name="event-log"></a>Log de eventos
Esse log contém informações sobre quais regras NSG são aplicadas às VMs e instâncias de função de serviço de nuvem, com base no endereço MAC. Os seguintes dados de exemplo são registrados para cada evento:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-B791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"3389-3389",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter-log"></a>Log de contador de regra

Esse log contém informações sobre cada regra aplicada aos recursos. Os seguintes dados de exemplo são registrados toda vez que uma regra é aplicada:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]TESTRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

## <a name="view-and-analyze-logs"></a>Exibir e analisar os logs

Para saber como exibir os dados do log de atividade, leia o artigo [Visão Geral dos Logs de Atividades do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Para saber como exibir os dados do log de diagnóstico, leia o artigo [Visão Geral dos Logs de Diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Se você enviar dados de diagnóstico ao Log Analytics, será possível usar a solução de gerenciamento de [análise de Grupo de Segurança de Rede do Azure](../log-analytics/log-analytics-azure-networking-analytics.md) (visualização) para obter informações avançadas. 
