---
title: Gerenciar logs de fluxo do Grupo de segurança de rede com o Observador de Rede do Azure - API REST | Microsoft Docs
description: Esta página explica como gerenciar logs de fluxo do Grupo de segurança de rede no Observador de Rede do Azure com a API REST
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2ab25379-0fd3-4bfe-9d82-425dfc7ad6bb
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 4569ea0ff43993c4605c55c529dbcb951ef502ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681771"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Configurar logs de fluxo do Grupo de segurança de rede usando a API REST

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Logs de fluxo do Grupo de Segurança de Rede são um recurso do Observador de Rede permite que você exiba informações sobre o tráfego IP de entrada e saída por meio de um Grupo de Segurança de Rede. Esses logs de fluxo são escritos no formato json e mostram os fluxos de entrada e de saída por regra, a NIC à qual o fluxo se aplica, as informações de cinco tuplas sobre o fluxo (IP de Origem/Destino, Porta de Origem/Destino, Protocolo) e se o tráfego foi permitido ou negado.

## <a name="before-you-begin"></a>Antes de começar

O ARMclient é usado para chamar a API REST usando o PowerShell. O ARMClient é encontrado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede.

> [!Important]
> Para chamadas da API REST do Observador de Rede o nome do grupo de recursos no URI da solicitação é o grupo de recursos que contém o Observador de Rede, não os recursos nos quais você está executando as ações de diagnóstico.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo mostra como habilitar, desabilitar e consultar logs de fluxo usando a API REST. Para saber mais sobre registro em log de fluxo do Grupo de segurança de rede, visite [Registro em log de fluxo do Grupo de segurança de rede - Visão geral do](network-watcher-nsg-flow-logging-overview.md).

Neste cenário, você:

* Habilitar logs de fluxo (versão 2)
* Desabilitar logs de fluxo
* Consultar status dos logs de fluxo

## <a name="log-in-with-armclient"></a>Fazer logon com o ARMClient

Faça logon no armclient com suas credenciais do Azure.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Registrar o provedor Insights

Para o registro de fluxo em log funcionar, o provedor **Microsoft.Insights** deve ser registrado. Se você não tiver certeza se o provedor **Microsoft.Insights** está registrado, execute o script a seguir.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Habilitar os logs do fluxo de Grupo de segurança de rede

O comando para habilitar a versão 2 do fluxo de logs é mostrado no exemplo a seguir. Para a versão 1, substitua o campo 'version' com '1':

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

A resposta retornada do exemplo anterior é a seguinte:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="disable-network-security-group-flow-logs"></a>Desabilitar os logs de fluxo do Grupo de segurança de rede

Use o exemplo a seguir para desabilitar logs de fluxo. A chamada é semelhante à habilitação de logs de fluxo, exceto quando **false** estiver definido para a propriedade habilitada.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

A resposta retornada do exemplo anterior é a seguinte:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="query-flow-logs"></a>Consultar logs de fluxo

A seguinte chamada REST consulta o status dos logs de fluxo em um Grupo de segurança de rede.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

Veja a exemplo um exemplo de resposta retornada:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="download-a-flow-log"></a>Baixar um log de fluxo

O local de armazenamento de um log de fluxo é definido no momento da criação. Uma ferramenta conveniente para acessar esses logs de fluxo salvos em uma conta de armazenamento é o Gerenciador de Armazenamento do Microsoft Azure, que pode ser baixado aqui: https://storageexplorer.com/

Se uma conta de armazenamento for especificada, os arquivos de captura de pacote serão salvos em uma conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Próximas etapas

Saiba como [Visualizar seus logs de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [Visualizar seus logs de fluxo NSG com ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
