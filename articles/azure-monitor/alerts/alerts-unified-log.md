---
title: Alertas de log no Azure Monitor
description: Dispare emails, notificações, chame URLs de sites (WebHooks) ou automação quando a condição de consulta de log especificada for atendida
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 786e9b472d1f900e94e5d0cfa6a00e0f85547704
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037686"
---
# <a name="log-alerts-in-azure-monitor"></a>Alertas de log no Azure Monitor

## <a name="overview"></a>Visão geral

Os alertas de log são um dos tipos de alertas que têm suporte nos [alertas do Azure](./alerts-overview.md). Os alertas de log permitem que os usuários usem uma consulta [log Analytics](../logs/log-analytics-tutorial.md) para avaliar os logs de recursos a cada frequência definida e acionar um alerta com base nos resultados. As regras podem disparar uma ou mais ações usando [grupos de ações](./action-groups.md).

> [!NOTE]
> Os dados de log de um [espaço de trabalho log Analytics](../logs/log-analytics-tutorial.md) podem ser enviados para o repositório de métricas de Azure monitor. Os alertas de métricas têm [comportamento diferente](alerts-metric-overview.md), o que pode ser mais desejável, dependendo dos dados com os quais você está trabalhando. Para obter informações sobre o que e como você pode rotear logs para métricas, consulte [alerta de métrica para logs](alerts-metric-logs.md).

> [!NOTE]
> No momento, não há encargos adicionais para a versão da API `2020-05-01-preview` e alertas de log centrados no recurso.  Os preços para os recursos que estão na versão prévia serão anunciados no futuro e um aviso fornecido antes do início da cobrança. Se você optar por continuar usando a nova versão da API e alertas de log centrados no recurso após o período de aviso, você será cobrado na taxa aplicável.

## <a name="prerequisites"></a>Pré-requisitos

Os alertas de log executam consultas em Log Analytics dados. Primeiro, você deve começar a [coletar dados de log](../essentials/resource-logs.md) e consultar os dados de log para problemas. Você pode usar o [tópico Exemplos de consulta de alerta](../logs/example-queries.md) no log Analytics para entender o que você pode descobrir ou começar [a escrever sua própria consulta](../logs/log-analytics-tutorial.md).

O [colaborador de monitoramento do Azure](../roles-permissions-security.md) é uma função comum que é necessária para criar, modificar e atualizar alertas de log. O acesso & direitos de execução de consulta para os logs de recursos também são necessários. O acesso parcial aos logs de recursos pode falhar em consultas ou retornar resultados parciais. [Saiba mais sobre como configurar alertas de log no Azure](./alerts-log.md).

> [!NOTE]
> Alertas de log para Log Analytics usados para serem gerenciados usando a [API de alerta log Analytics](./api-alerts.md)herdado. [Saiba mais sobre como alternar para a API ScheduledQueryRules atual](../alerts/alerts-log-api-switch.md).

## <a name="query-evaluation-definition"></a>Definição de avaliação de consulta

A definição da condição de regras de pesquisa de logs começa com:

- Qual consulta deve ser executada?
- Como usar os resultados?

As seções a seguir descrevem os diferentes parâmetros que você pode usar para definir a lógica acima.

### <a name="log-query"></a>Consulta de log
A consulta [log Analytics](../logs/log-analytics-tutorial.md) usada para avaliar a regra. Os resultados retornados por essa consulta são usados para determinar se um alerta deve ser disparado. A consulta pode ser delimitada para:

- Um recurso específico, como uma máquina virtual.
- Um recurso em escala, como uma assinatura ou grupo de recursos.
- Vários recursos usando [a consulta entre recursos](../logs/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights). 
 
> [!IMPORTANT]
> As consultas de alerta têm restrições para garantir o desempenho ideal e a relevância dos resultados. [Saiba mais aqui](./alerts-log-query.md).

> [!IMPORTANT]
> O recurso centrado em recursos e [consulta entre recursos](../logs/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) só tem suporte usando a API scheduledQueryRules atual. Se você usar a [API de alerta do log Analytics](./api-alerts.md)herdado, será necessário alternar. [Saiba mais sobre como alternar](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>Intervalo de tempo de consulta

O intervalo de tempo é definido na definição da condição da regra. Em espaços de trabalho e Application Insights, ele é chamado de **período**. Em todos os outros tipos de recursos, ele é chamado de **intervalo de tempo de consulta de substituição**.

Assim como no log Analytics, o intervalo de tempo limita os dados de consulta ao intervalo especificado. Mesmo que o comando **atrás** seja usado na consulta, o intervalo de tempo será aplicado.

Por exemplo, uma consulta examina 60 minutos, quando o intervalo de tempo é de 60 minutos, mesmo se o texto contiver **atrás (1D)**. O intervalo de tempo e a filtragem de tempo de consulta precisam corresponder. No caso de exemplo, alterar o   /  **intervalo de tempo de consulta de substituição** de período para um dia funcionaria como esperado.

### <a name="measure"></a>Medida

Os alertas de log desativam o log em valores numéricos que podem ser avaliados. Você pode medir duas coisas diferentes:

#### <a name="count-of-the-results-table-rows"></a>Contagem de linhas da tabela de resultados

A contagem de resultados é a medida padrão. Ideal para trabalhar com eventos como logs de eventos do Windows, syslog e exceções de aplicativo. Dispara quando os registros de log acontecem ou não acontecem na janela de tempo avaliada.

Os alertas de log funcionam melhor quando você tenta detectar dados no log. Ele funciona menos bem quando você tenta detectar falta de dados nos logs. Por exemplo, alertas na pulsação da máquina virtual.

Para espaços de trabalho e Application Insights, ele é chamado com **base no** **número de resultados da** seleção. Em todos os outros tipos de recursos, ele é chamado de **medida** com **linhas da tabela** de seleção.

> [!NOTE]
> Como os logs são dados semiestruturados, eles são inerentemente mais latentes do que a métrica, que você pode enfrentar inconvenientes ao tentar detectar a falta de dados nos logs e deve considerar o uso de [alertas de métricas](alerts-metric-overview.md). Você pode enviar dados para o repositório de métricas de logs usando [alertas de métricas para logs](alerts-metric-logs.md).

##### <a name="example-of-results-table-rows-count-use-case"></a>Exemplo de caso de uso de contagem de linhas de tabela de resultados

Você deseja saber quando seu aplicativo respondeu com o código de erro 500 (erro interno do servidor). Você criaria uma regra de alerta com os detalhes a seguir:

- **Consulta:** 

```Kusto
requests
| where resultCode == "500"
```

- **Período de tempo/granularidade de agregação:** 15 minutos
- **Frequência de alerta:** 15 minutos
- **Valor de limite:** maior que 0

Em seguida, as regras de alerta monitoram as solicitações que terminam com o código de erro 500. A consulta é executada a cada 15 minutos, nos últimos 15 minutos. Se até mesmo um registro for encontrado, ele acionará o alerta e disparará as ações configuradas.

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>Cálculo de medida com base em uma coluna numérica (como o valor do contador de CPU)

Para espaços de trabalho e Application Insights, ele é chamado com **base em** **medição de métrica** de seleção. Em todos os outros tipos de recursos, ele é chamado de **medida** com a seleção de qualquer nome de coluna de número.

### <a name="aggregation-type"></a>Tipo de agregação

O cálculo feito em vários registros para agregar-os a um valor numérico. Por exemplo: 
- **Count** retorna o número de registros na consulta
- **Average** retorna a média da [**granularidade de agregação**](#aggregation-granularity) de coluna de medida definida.

Em espaços de trabalho e Application Insights, há suporte apenas em tipo de medida de **medida métrica** . O resultado da consulta deve conter uma coluna chamada AggregatedValue que forneça um valor numérico após uma agregação definida pelo usuário. Em todos os outros tipos de recursos, o **tipo de agregação** é selecionado no campo desse nome.

### <a name="aggregation-granularity"></a>Granularidade de agregação

Determina o intervalo que é usado para agregar vários registros a um valor numérico. Por exemplo, se você especificou **5 minutos**, os registros seriam agrupados por intervalos de 5 minutos usando o **tipo de agregação** especificado.

Em espaços de trabalho e Application Insights, há suporte apenas em tipo de medida de **medida métrica** . O resultado da consulta deve conter [bin ()](/azure/kusto/query/binfunction) que define o intervalo nos resultados da consulta. Em todos os outros tipos de recursos, o campo que controla essa configuração é chamado de **granularidade de agregação**.

> [!NOTE]
> Como [bin ()](/azure/kusto/query/binfunction) pode resultar em intervalos de tempo desiguais, o serviço de alerta converterá automaticamente a função [bin ()](/azure/kusto/query/binfunction) na função [bin_at ()](/azure/kusto/query/binatfunction) com o tempo apropriado em tempo de execução, para garantir os resultados com um ponto fixo.

### <a name="split-by-alert-dimensions"></a>Dividir por dimensões de alerta

Divida os alertas por número ou colunas de cadeia de caracteres em alertas separados agrupando em combinações exclusivas. Ao criar alertas centrados em recursos em escala (assinatura ou escopo do grupo de recursos), você pode dividir pela coluna ID de recurso do Azure. A divisão na coluna ID de recurso do Azure alterará o destino do alerta para o recurso especificado.

A divisão pela coluna ID de recurso do Azure é recomendada quando você deseja monitorar a mesma condição em vários recursos do Azure. Por exemplo, monitorar todas as máquinas virtuais para uso da CPU acima de 80%. Você também pode optar por não dividir quando quiser uma condição em vários recursos no escopo, como o monitoramento de que pelo menos cinco computadores no escopo do grupo de recursos têm o uso da CPU acima de 80%.

Em espaços de trabalho e Application Insights, há suporte apenas em tipo de medida de **medida métrica** . O campo é chamado de **agregação em**. Ele é limitado a três colunas. Ter mais de três grupos por colunas na consulta pode levar a resultados inesperados. Em todos os outros tipos de recursos, ele é configurado na seção **dividir por dimensões** da condição (limitada a seis divisões).

#### <a name="example-of-splitting-by-alert-dimensions"></a>Exemplo de divisão por dimensões de alerta

Por exemplo, você deseja monitorar erros para várias máquinas virtuais que executam seu site/aplicativo em um grupo de recursos específico. Você pode fazer isso usando uma regra de alerta de log da seguinte maneira:

- **Consulta:** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    Ao usar espaços de trabalho e Application Insights com a lógica de alerta de **medição métrica** , essa linha precisa ser adicionada ao texto da consulta:

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **Coluna de ID de recurso:** _ResourceId (divisão por coluna de ID de recurso nas regras de alerta só está disponível para assinaturas e grupos de recursos no momento)
- **Dimensões/agregadas em:**
  - Computer = VM1, VM2 (os valores de filtragem na definição de regras de alerta não estão disponíveis atualmente para espaços de trabalho e Application Insights. Filtrar no texto da consulta.)
- **Período de tempo/granularidade de agregação:** 15 minutos
- **Frequência de alerta:** 15 minutos
- **Valor de limite:** maior que 0

Essa regra monitora se alguma máquina virtual tinha eventos de erro nos últimos 15 minutos. Cada máquina virtual é monitorada separadamente e disparará ações individualmente.

> [!NOTE]
> As dimensões de divisão por alerta só estão disponíveis para a API scheduledQueryRules atual. Se você usar a [API de alerta do log Analytics](./api-alerts.md)herdado, será necessário alternar. [Saiba mais sobre como alternar](./alerts-log-api-switch.md). Os alertas centrados em recursos em escala só têm suporte na versão da API `2020-05-01-preview` e acima.

## <a name="alert-logic-definition"></a>Definição de lógica de alerta

Depois de definir a consulta para execução e avaliação dos resultados, você precisará definir a lógica de alerta e quando disparar ações. As seções a seguir descrevem os diferentes parâmetros que você pode usar:

### <a name="threshold-and-operator"></a>Limite e operador

Os resultados da consulta são transformados em um número que é comparado com o limite e o operador.

### <a name="frequency"></a>Frequência

O intervalo no qual a consulta é executada. Pode ser definido de 5 minutos a um dia. Deve ser igual ou menor que o [intervalo de tempo de consulta](#query-time-range) para não perder registros de log.

Por exemplo, se você definir o período de tempo como 30 minutos e a frequência como 1 hora.  Se a consulta for executada às 00:00, ela retornará registros entre 23:30 e 00:00. A próxima vez que a consulta seria executada é 01:00 que retornaria registros entre 00:30 e 01:00. Todos os registros criados entre 00:00 e 00:30 nunca seriam avaliados.

### <a name="number-of-violations-to-trigger-alert"></a>Número de violações para disparar o alerta

Você pode especificar o período de avaliação do alerta e o número de falhas necessárias para disparar um alerta. Permitindo que você defina melhor um tempo de impacto para disparar um alerta. 

Por exemplo, se a [**granularidade de agregação**](#aggregation-granularity) de regra for definida como ' 5 minutos ', você poderá disparar um alerta somente se três falhas (15 minutos) da última hora tiverem ocorrido. Essa configuração é definida pela política de negócios do aplicativo.

## <a name="state-and-resolving-alerts"></a>Estado e resolução de alertas

Os alertas de log são sem estado. Os alertas são acionados sempre que a condição é atendida, mesmo se forem acionados anteriormente. Os alertas acionados não são resolvidos. Você pode [marcar o alerta como fechado](../alerts/alerts-managing-alert-states.md). Você também pode ativar mudo de ações para impedir que elas sejam disparadas por um período após uma regra de alerta ser disparada.

Em espaços de trabalho e Application Insights, ele é chamado de **suprimir alertas**. Em todos os outros tipos de recursos, ele é chamado de **ações de mudo**. 

Consulte este exemplo de avaliação de alerta:

| Hora    | Avaliação da condição de log | Resultado 
| ------- | ----------| ----------| ------- 
| 00:05 | FALSE | O alerta não é acionado. Nenhuma ação chamada.
| 00:10 | TRUE  | O alerta é acionado e os grupos de ação chamados. Novo estado de alerta ativo.
| 00:15 | TRUE  | O alerta é acionado e os grupos de ação chamados. Novo estado de alerta ativo.
| 00:20 | FALSE | O alerta não é acionado. Nenhuma ação chamada. O estado dos alertas do anterior permanece ativo.

## <a name="pricing-and-billing-of-log-alerts"></a>Preços e cobrança de alertas de log

As informações de preços estão localizadas na [página de preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Os alertas de log são listados em provedor `microsoft.insights/scheduledqueryrules` de recursos com:

- Alertas de log em Application Insights mostrados com o nome exato do recurso junto com as propriedades do grupo de recursos e do alerta.
- Alertas de log em Log Analytics mostrados com o nome exato do recurso junto com as propriedades do grupo de recursos e do alerta; Quando criado usando a [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules).
- Os alertas de log criados a partir da [API de log Analytics herdado](./api-alerts.md) não são acompanhados pelos [recursos do Azure](../../azure-resource-manager/management/overview.md) e não impõe nomes de recursos exclusivos. Esses alertas ainda são criados `microsoft.insights/scheduledqueryrules` como recursos ocultos, que têm essa estrutura de nomeação de recursos `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Os alertas de log na API herdada são mostrados com o nome do recurso oculto acima, juntamente com as propriedades do grupo de recursos e do alerta.

> [!NOTE]
> Os caracteres de recurso sem suporte, como `<, >, %, &, \, ?, /` são substituídos por `_` nos nomes de recursos ocultos, e isso também refletirá nas informações de cobrança.

> [!NOTE]
> Alertas de log para Log Analytics usados para serem gerenciados usando a [API de alerta log Analytics](./api-alerts.md) herdada e os modelos herdados de [log Analytics pesquisas e alertas salvos](../insights/solutions.md). [Saiba mais sobre como alternar para a API ScheduledQueryRules atual](../alerts/alerts-log-api-switch.md). Qualquer gerenciamento de regras de alerta deve ser feito usando a [API de log Analytics herdada](./api-alerts.md) até você decidir alternar e não pode usar os recursos ocultos.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [criação de alertas de log no Azure](./alerts-log.md).
* Entenda os [webhooks nos alertas de log no Azure](../alerts/alerts-log-webhook.md).
* Saiba mais sobre os [Alertas do Azure](./alerts-overview.md).
* Saiba mais sobre [log Analytics](../logs/log-query-overview.md).