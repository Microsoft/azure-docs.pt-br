---
title: Expressão app() nas consultas de log do Azure Monitor | Microsoft Docs
description: A expressão do aplicativo é usada em uma consulta Azure Monitor log para recuperar dados de um aplicativo Application Insights específico no mesmo grupo de recursos, em outro grupo de recursos ou em outra assinatura.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2019
ms.openlocfilehash: 235a6bf4a0dd9afcac8751067b2eac3ddb37c0cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031235"
---
# <a name="app-expression-in-azure-monitor-query"></a>expressão app() na consulta do Azure Monitor

A expressão `app` é usada em uma consulta do Azure Monitor para recuperar dados de um aplicativo Application Insights específico no mesmo grupo de recursos, em outro grupo de recursos ou em outra assinatura. Isso é útil para incluir dados de aplicativo em uma consulta de log do Azure Monitor e para consultar dados em vários aplicativos em uma consulta do Application Insights.

> [!IMPORTANT]
> A expressão de aplicativo () não será usada se você estiver usando um [recurso de Application insights baseado em espaço de trabalho](../app/create-workspace-resource.md) , pois os dados de log são armazenados em um espaço de trabalho log Analytics. Use a expressão log () para gravar uma consulta que inclui o aplicativo em vários espaços de trabalho. Para vários aplicativos no mesmo espaço de trabalho, você não precisa de uma consulta entre espaços de trabalho.

## <a name="syntax"></a>Sintaxe

`app(`*ID*`)`


## <a name="arguments"></a>Argumentos

- *Identificador*: identifica o aplicativo usando um dos formatos na tabela a seguir.

| Identificador | Descrição | Exemplo
|:---|:---|:---|
| Nome do Recurso | Nome legível por humanos do aplicativo (também conhecido como "nome do componente") | app("fabrikamapp") |
| Nome Qualificado | Nome completo do aplicativo no formato: "subscriptionName/resourceGroup/componentName" | app('AI-prototype/Fabrikam/fabrikamapp') |
| ID | GUID do aplicativo | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| ID do recurso do Azure | Identificador do recurso do Azure |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Observações

* Você deve ter acesso de leitura ao aplicativo.
* A identificação de um aplicativo por seu nome presume que ele seja exclusivo em todas as assinaturas acessíveis. Se você tiver vários aplicativos com o nome especificado, a consulta falhará devido à ambiguidade. Nesse caso, você deve usar um dos outros identificadores.
* Use a expressão relacionada [workspace](../logs/workspace-expression.md) para consultar em workspaces do Log Analytics.
* No momento, não há suporte para a expressão app() na consulta de pesquisa ao usar o portal do Azure para criar uma [regra de alerta de pesquisa de logs personalizada](../alerts/alerts-log.md), a menos que um aplicativo Application Insights seja usado como o recurso para a regra de alerta.

## <a name="examples"></a>Exemplos

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Próximas etapas

- Veja a [expressão do espaço de trabalho](../logs/workspace-expression.md) para se referir a um espaço de trabalho do Log Analytics.
- Leia mais sobre como os [dados do Azure Monitor](./log-query-overview.md) são armazenados.
- Acesse a documentação completa da [linguagem de consulta Kusto](/azure/kusto/query/).