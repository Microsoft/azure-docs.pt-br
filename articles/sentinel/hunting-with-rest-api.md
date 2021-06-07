---
title: Gerenciar consultas de busca e transmissão ao vivo no Azure Sentinel usando a API REST | Microsoft Docs
description: Este artigo descreve como os recursos de busca do Azure Sentinel permitem que você aproveite Log Analytics a API REST para gerenciar consultas de busca e transmissão ao vivo.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: 64f05e18ff757d9f086cf06d74109bf64e32a05c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795689"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>Gerenciar consultas de busca e transmissão ao vivo no Azure Sentinel usando a API REST

O Azure Sentinel, sendo criado parcialmente na Azure Monitor Log Analytics, permite que você use Log Analytics API REST para gerenciar consultas de busca e transmissão ao vivo. Este documento mostra como criar e gerenciar consultas de busca usando a API REST.  As consultas criadas dessa forma serão exibidas na interface do usuário do Azure Sentinel.

Consulte a referência da API REST definitiva para obter mais detalhes sobre a [API de pesquisas salvas](/rest/api/loganalytics/savedsearches).

## <a name="api-examples"></a>Exemplos de API

Nos exemplos a seguir, substitua esses espaços reservados pela substituição prescrita na tabela a seguir:

| Espaço reservado | Substitua por |
|-|-|
| **SubscriptionId** | o nome da assinatura à qual você está aplicando a consulta de busca ou transmissão ao vivo. |
| **ResourceGroupName** | o nome do grupo de recursos ao qual você está aplicando a consulta de busca ou transmissão ao vivo. |
| **{savedSearchId}** | uma ID exclusiva (GUID) para cada consulta de busca. |
| **WorkspaceName** | o nome do espaço de trabalho Log Analytics que é o destino da consulta. |
| **DisplayName** | um nome de exibição de sua escolha para a consulta. |
| **Ndescrição** | uma descrição da consulta de busca ou transmissão ao vivo. |
| **Táticas** | as táticas relevantes MITRE ATT&CK que se aplicam à consulta. |
| **Consultá** | a expressão de consulta para sua consulta. |
|  

### <a name="example-1"></a>Exemplo 1

Este exemplo mostra como criar ou atualizar uma consulta de busca de um determinado espaço de trabalho do Azure Sentinel.  Para uma consulta transmissão ao vivo, substitua *"category": "procurando consultas"* por *"category": "transmissão ao vivo queries"* no **corpo da solicitação**: 

#### <a name="request-header"></a>Cabeçalho da solicitação

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>Corpo da solicitação

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>Exemplo 2

Este exemplo mostra como excluir uma consulta de busca ou transmissão ao vivo para um determinado espaço de trabalho do Azure Sentinel:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>Exemplo 3

Este exemplo mostra a você recuperar uma consulta de busca ou transmissão ao vivo para um determinado espaço de trabalho:

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a gerenciar consultas de busca e transmissão ao vivo no Azure Sentinel usando a API de Log Analytics. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- [Buscar ameaças proativamente](hunting.md)
- [Use blocos de anotações para executar campanhas de busca automatizadas](notebooks.md)