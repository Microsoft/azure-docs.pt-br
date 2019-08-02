---
title: Estrutura de Logs do Azure Monitor | Microsoft Docs
description: Você precisa de uma consulta de log para recuperar dados de log do Azure Monitor.  Este artigo descreve como novas consultas de log são utilizadas no Azure Monitor e fornece conceitos necessários para serem compreendidos antes de criar uma.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: e243ebbc31f9e941678ac76a83738276995b5ba1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297147"
---
# <a name="structure-of-azure-monitor-logs"></a>Estrutura de Logs do Azure Monitor
A capacidade de obter informações rapidamente dentro de seus dados usando um [consulta de log](log-query-overview.md) é um recurso poderoso do Azure Monitor. Para criar consultas úteis e eficientes, você deve compreender alguns conceitos básicos, como onde os dados que você deseja estão localizados e como ela é estruturada. Este artigo apresenta os conceitos básicos que você precisa para começar.

## <a name="overview"></a>Visão geral
Dados em Logs do Azure Monitor são armazenados em um espaço de trabalho do Log Analytics ou um aplicativo de Application Insights. Ambos são ativadas pelos [Data Explorer do Azure](/azure/data-explorer/) que significa que eles usam sua linguagem de mecanismo e consulta de dados eficientes.

Dados em espaços de trabalho e aplicativos são organizados em tabelas, cada um dos quais armazena tipos diferentes de dados e tem seu próprio conjunto exclusivo de propriedades. A maioria dos [fontes de dados](../platform/data-sources.md) irá escrever suas próprias tabelas em um espaço de trabalho do Log Analytics, enquanto o Application Insights gravará um conjunto predefinido de tabelas em um aplicativo de Application Insights. Consultas de log são muito flexíveis, permitindo que você combine facilmente dados de várias tabelas e até mesmo usar uma consulta de recursos cruzados para combinar dados de tabelas em vários espaços de trabalho ou para escrever consultas que combinam dados de espaço de trabalho e aplicativos.

A imagem a seguir mostra exemplos de fontes de dados que gravam em tabelas diferentes são usadas em consultas de exemplo.

![Tabelas](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Espaço de trabalho do Log Analytics
Todos os dados coletados pelos Logs do Azure Monitor, exceto para o Application Insights são armazenados em uma [espaço de trabalho do Log Analytics](../platform/manage-access.md). Você pode criar um ou mais espaços de trabalho, dependendo de suas necessidades particulares. [Fontes de dados](../platform/data-sources.md) como logs de Logs de atividade e diagnóstico dos recursos do Azure, os agentes em máquinas virtuais e os dados do insights e soluções de monitoramento gravará dados em um ou mais espaços de trabalho que você configura como parte da sua migração. Outros serviços, como [Central de segurança do Azure](/azure/security-center/) e [Azure Sentinel](/azure/sentinel/) também usar um espaço de trabalho do Log Analytics para armazenar seus dados, portanto, podem ser analisado usando consultas de log junto com dados de monitoramento de outros fontes.

Diferentes tipos de dados são armazenados em tabelas diferentes no espaço de trabalho, e cada tabela tem um conjunto exclusivo de propriedades. Um conjunto padrão de tabelas são adicionadas ao espaço de trabalho quando ele é criado e novas tabelas serão adicionadas para diferentes fontes de dados, soluções e serviços, como eles são integrados. Você também pode criar tabelas personalizadas usando o [API do coletor de dados](../platform/data-collector-api.md).

Você pode procurar as tabelas em um espaço de trabalho e seu esquema na **esquema** guia no Log Analytics para o espaço de trabalho.

![Esquema de espaço de trabalho](media/scope/workspace-schema.png)

Use a seguinte consulta para listar as tabelas no espaço de trabalho e o número de registros coletados em cada um no dia anterior. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Consulte a documentação para cada fonte de dados para obter detalhes sobre as tabelas criadas por elas. Os exemplos incluem artigos [fontes de dados do agente](../platform/agent-data-sources.md), [logs de diagnóstico](../platform/diagnostic-logs-schema.md), e [soluções de monitoramento](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Permissões de espaço de trabalho
Ver [permissões de espaço de trabalho e escopo](../platform/manage-access.md#workspace-permissions-and-scope) para obter detalhes sobre como fornecer acesso aos dados em um espaço de trabalho. Além de conceder acesso ao espaço de trabalho em si, você pode limitar o acesso a tabelas individuais usando [RBAC de nível de tabela](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Aplicativo do Application Insights
Quando você cria um aplicativo no Application Insights, um aplicativo correspondente é criado automaticamente em Logs do Azure Monitor. Nenhuma configuração é necessária para coletar dados e o aplicativo gravará automaticamente os dados, como exibições de página, solicitações e exceções de monitoramento.

Ao contrário de um espaço de trabalho do Log Analytics, um aplicativo de Application Insights tem um conjunto fixo de tabelas. Você não pode configurar outras fontes de dados para gravar para o aplicativo, portanto, não há tabelas adicionais podem ser criadas. 

| Tabela | DESCRIÇÃO | 
|:---|:---|
| availabilityResults | Dados de resumo dos testes de disponibilidade. |
| browserTimings      | Dados sobre o desempenho do cliente, como o tempo gasto para processar os dados de entrada. |
| customEvents        | Eventos personalizados criados pelo seu aplicativo. |
| customMetrics       | Métricas personalizadas criadas pelo seu aplicativo. |
| dependências        | Chamadas do aplicativo para componentes externos. |
| Exceções          | Exceções geradas pelo tempo de execução do aplicativo. |
| pageViews           | Exibição de dados sobre cada site com informações sobre o navegador. |
| performanceCounters | Medidas de desempenho dos recursos de computação com suporte do aplicativo. |
| solicitações            | Detalhes de cada solicitação de aplicativo.  |
| traces              | Resultados do rastreamento distribuído. |

Você pode exibir o esquema para cada tabela na **esquema** guia no Log Analytics para o aplicativo.

![Esquema do aplicativo](media/scope/application-schema.png)

## <a name="standard-properties"></a>Propriedades padrões
Embora cada tabela de Logs do Azure Monitor tem seu próprio esquema, há padrão propriedades compartilhadas por todas as tabelas. Ver [propriedades padrão em Logs do Azure Monitor](../platform/log-standard-properties.md) para obter detalhes sobre cada um.

| Espaço de trabalho do Log Analytics | Aplicativo do Application Insights | DESCRIÇÃO |
|:---|:---|:---|
| TimeGenerated | timestamp  | Data e hora em que o registro foi criado. |
| Tipo          | itemType   | Nome da tabela que foi recuperado para o registro. |
| _ResourceId   |            | Identificador exclusivo para o recurso de registro está associado. |
| _IsBillable   |            | Especifica se os dados ingeridos são faturáveis. |
| _BilledSize   |            | Especifica o tamanho em bytes dos dados serão cobrados. |

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como usar [pesquisas de log do Log Analytics para criar e editar](../log-query/portals.md).
- Confira um [tutorial sobre como escrever consultas](../log-query/get-started-queries.md) utilizando a nova linguagem de consulta.
