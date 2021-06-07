---
title: Consultas de log no Azure Monitor
description: Informações de referência para a linguagem de consulta Kusto usada pelo Azure Monitor. Inclui elementos adicionais específicos ao Azure Monitor e elementos sem suporte às consultas do Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 529fc432bf8777ef7e2b527f08e9cb59e42bf156
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041018"
---
# <a name="log-queries-in-azure-monitor"></a>Consultas de log no Azure Monitor
Os logs de Azure Monitor baseiam-se no Azure Data Explorer e as consultas de log são escritas usando a mesma linguagem de consulta Kusto (KQL). Essa é uma linguagem avançada criada para ser fácil de ler e criar, portanto, você deve conseguir começar a escrever consultas seguindo algumas diretrizes básicas.

As áreas em Azure Monitor em que você usará consultas incluem o seguinte:

- [Log Analytics](../logs/log-analytics-overview.md). A ferramenta principal no portal do Azure para editar consultas de log e analisar seus resultados interativamente. Mesmo que pretenda usar uma consulta de log em outro lugar no Azure Monitor, normalmente você escreverá e testará essa consulta no Log Analytics antes de copiá-la para o local final.
- [Registrar em log as regras de alerta](../alerts/alerts-overview.md). Identifique de forma proativa os problemas dos dados em seu espaço de trabalho.  Cada regra de alerta é baseada em uma consulta de log que é executada automaticamente em intervalos regulares.  Os resultados são inspecionados para determinar se um alerta deve ser criado.
- [Pastas de trabalho](../visualize/workbooks-overview.md). Inclua os resultados das consultas de log usando visualizações diferentes em relatórios visuais interativos no portal do Azure.
- [Painéis do Azure](../visualize/tutorial-logs-dashboards.md). Fixe os resultados de qualquer consulta em um painel do Azure que permita que você visualize dados de log e de métrica juntos e, opcionalmente, compartilhe com outros usuários do Azure.
- [Aplicativos lógicos](../logs/logicapp-flow-connector.md).  Use os resultados de uma consulta de log em um fluxo de trabalho automatizado usando aplicativos lógicos.
- [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult). Use os resultados de uma consulta de log em um script do PowerShell de uma linha de comando ou de um runbook de automação do Azure que usa Get-AzOperationalInsightsSearchResults.
- [API de logs de Azure monitor](https://dev.loganalytics.io). Recuperar dados de log do espaço de trabalho de qualquer cliente da API REST.  A solicitação de API inclui uma consulta que é executada no Azure Monitor para determinar os dados a serem recuperados.

## <a name="getting-started"></a>Introdução
A melhor maneira de começar a aprender a escrever consultas de log usando o KQL está aproveitando os tutoriais e exemplos disponíveis.

- [Tutorial log Analytics](./log-analytics-tutorial.md) -tutorial sobre como usar os recursos do log Analytics que é a ferramenta que você usará no portal do Azure para editar e executar consultas. Ele também permite que você escreva consultas simples sem trabalhar diretamente com a linguagem de consulta. Se você nunca usou o Log Analytics, comece por aqui para entender a ferramenta que você usará com os outros tutoriais e exemplos.
- [Tutorial do KQL](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) – orientação guiada por meio de conceitos básicos de KQL e operadores comuns. Esse é o melhor lugar para começar a se familiarizar com o próprio idioma e com a estrutura de consultas de log. 
- [Consultas de exemplo](../logs/example-queries.md) – descrição das consultas de exemplo disponíveis no log Analytics. Você pode usar as consultas sem modificação ou usá-las como exemplos para aprender KQL.
- [Exemplos de consulta](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) – consultas de exemplo que ilustram uma variedade de diferentes conceitos.



## <a name="reference-documentation"></a>Documentação de referência
A [documentação para KQL](/azure/data-explorer/kusto/query/) , incluindo a referência para todos os comandos e operadores, está disponível na documentação do data Explorer do Azure. Mesmo que você obtenha proficiente usando o KQL, você ainda usará regularmente a referência para investigar novos comandos e cenários que você ainda não usou antes.


## <a name="language-differences"></a>Diferenças da linguagem
Embora Azure Monitor use o mesmo KQL que o Data Explorer do Azure, há algumas diferenças. A documentação do KQL especificará os operadores que não têm suporte pelo Azure Monitor ou que têm funcionalidade diferente. Os operadores específicos para Azure Monitor são documentados no conteúdo de Azure Monitor. As seções a seguir fornecem uma lista das diferenças entre as versões do idioma para referência rápida.

### <a name="statements-not-supported-in-azure-monitor"></a>Instruções não compatíveis com o Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Parâmetros de consulta](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funções não compatíveis com o Azure Monitor

* [cluster ()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [banco de dados ()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operadores não compatíveis com o Azure Monitor

* [Junção entre clusters](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plug-ins não compatíveis com o Azure Monitor

* [Plug-in do Python](/azure/kusto/query/pythonplugin)
* [Plug-in sql_request](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Operadores adicionais no Azure Monitor
Os seguintes operadores dão suporte a recursos específicos do Azure Monitor e não estão disponíveis fora do Azure Monitor.

* [aplicativo ()](../logs/app-expression.md)
* [recurso ()](./resource-expression.md)
* [espaço de trabalho ()](../logs/workspace-expression.md)

## <a name="next-steps"></a>Próximas etapas
- Percorra um [tutorial sobre como escrever consultas](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor).
- Acesse a [documentação de referência completa da linguagem de consulta Kusto](/azure/kusto/query/).