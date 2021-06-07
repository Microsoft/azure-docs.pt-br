---
title: Analisar logs e métricas no Azure Spring Cloud | Microsoft Docs
description: Saiba como analisar dados de diagnóstico no Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: c354435f689f5182dd368fd53fdab28f1ab63d48
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218784"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analisar logs e métricas com configurações de diagnóstico

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

Usando a funcionalidade de diagnóstico do Azure Spring Cloud, você pode analisar os logs e as métricas com qualquer um dos seguintes serviços:

* Use o Azure Log Analytics, onde os dados são gravados no armazenamento do Azure. Há um atraso ao exportar logs para Log Analytics.
* Salve os logs em uma conta de armazenamento para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias).
* Transmita logs para o Hub de eventos para ingestão por um serviço de terceiros ou uma solução de análise personalizada.

Escolha a categoria de log e a categoria de métrica que você deseja monitorar.

> [!TIP]
> Deseja apenas transmitir seus logs? Confira este [CLI do Azure comando](/cli/azure/ext/spring-cloud/spring-cloud/app#ext-spring-cloud-az-spring-cloud-app-logs)!

## <a name="logs"></a>Logs

|Log | Descrição |
|----|----|
| **ApplicationConsole** | Log do console de todos os aplicativos do cliente. |
| **SystemLogs** | Atualmente, somente os logs do [servidor de configuração do Spring Cloud](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) nesta categoria. |

## <a name="metrics"></a>Métricas

Para obter uma lista completa de métricas, consulte [métricas de nuvem Spring](./spring-cloud-concept-metrics.md#user-metrics-options).

Para começar, habilite um desses serviços para receber os dados. Para saber mais sobre como configurar Log Analytics, consulte [introdução ao log Analytics no Azure monitor](../azure-monitor/logs/log-analytics-tutorial.md).

## <a name="configure-diagnostics-settings"></a>Definir configurações de diagnóstico

1. Na portal do Azure, acesse sua instância do Azure Spring Cloud.
1. Selecione a opção **configurações de diagnóstico** e, em seguida, selecione **Adicionar configuração de diagnóstico**.
1. Insira um nome para a configuração e escolha para onde deseja enviar os logs. Você pode selecionar qualquer combinação das três opções a seguir:
    * **Arquivar em uma conta de armazenamento**
    * **Transmitir por streaming para um hub de eventos**
    * **Enviar para o Log Analytics**

1. Escolha qual categoria de log e categoria de métrica você deseja monitorar e, em seguida, especifique o tempo de retenção (em dias). O tempo de retenção se aplica somente à conta de armazenamento.
1. Clique em **Salvar**.

> [!NOTE]
> 1. Pode haver um intervalo de até 15 minutos entre o momento em que os logs ou as métricas são emitidos e quando aparecem na sua conta de armazenamento, no Hub de eventos ou Log Analytics.
> 1. Se a instância do Azure Spring Cloud for excluída ou movida, a operação não será colocada em cascata para os recursos de **configurações de diagnóstico** . Os recursos de **configurações de diagnóstico** precisam ser excluídos manualmente antes da operação em relação ao seu pai, ou seja, a instância do Azure Spring Cloud. Caso contrário, se uma nova instância do Azure Spring Cloud for provisionada com a mesma ID de recurso que a excluída, ou se a instância do Azure Spring Cloud for movida de volta, os recursos anteriores de **configurações de diagnóstico** continuarão a estendê-la.

## <a name="view-the-logs-and-metrics"></a>Exibir os logs e as métricas
Há vários métodos para exibir os logs e as métricas, conforme descrito nos cabeçalhos a seguir.

### <a name="use-the-logs-blade"></a>Usar a folha logs

1. Na portal do Azure, acesse sua instância do Azure Spring Cloud.
1. Para abrir o painel **pesquisa de logs** , selecione **logs**.
1. Na caixa de pesquisa **tabelas**
   * Para exibir os logs, insira uma consulta simples, como:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Para exibir as métricas, insira uma consulta simples, como:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Para exibir o resultado da pesquisa, selecione **executar**.

### <a name="use-log-analytics"></a>Usar o Log Analytics

1. No portal do Azure, no painel esquerdo, selecione **log Analytics**.
1. Selecione o espaço de trabalho Log Analytics que você escolheu ao adicionar as configurações de diagnóstico.
1. Para abrir o painel **pesquisa de logs** , selecione **logs**.
1. Na caixa de pesquisa **tabelas** ,
   * para exibir os logs, insira uma consulta simples, como:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * para exibir as métricas, insira uma consulta simples, como:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Para exibir o resultado da pesquisa, selecione **executar**.
1. Você pode pesquisar os logs do aplicativo ou da instância específica definindo uma condição de filtro:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` diferencia maiúsculas de minúsculas, mas `=~` não é.

Para saber mais sobre a linguagem de consulta usada no Log Analytics, consulte [Azure monitor consultas de log](/azure/data-explorer/kusto/query/).

### <a name="use-your-storage-account"></a>Usar sua conta de armazenamento

1. No portal do Azure, localize **contas de armazenamento** no painel de navegação esquerdo ou na caixa de pesquisa.
1. Selecione a conta de armazenamento que você escolheu ao adicionar as configurações de diagnóstico.
1. Para abrir o painel **contêiner de blob** , selecione **BLOBs**.
1. Para examinar os logs de aplicativo, procure um contêiner chamado **insights-logs-applicationconsole**.
1. Para revisar as métricas do aplicativo, procure um contêiner chamado **insights-métricas-PT1M**.

Para saber mais sobre como enviar informações de diagnóstico para uma conta de armazenamento, consulte [armazenar e exibir dados de diagnóstico no armazenamento do Azure](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Usar seu hub de eventos

1. No portal do Azure, localize **hubs de eventos** no painel de navegação esquerdo ou na caixa de pesquisa.

1. Pesquise e selecione o Hub de eventos que você escolheu quando adicionou as configurações de diagnóstico.
1. Para abrir o painel **lista de Hub de eventos** , selecione hubs de **eventos**.
1. Para examinar os logs de aplicativo, procure um hub de eventos chamado **insights-logs-applicationconsole**.
1. Para revisar as métricas do aplicativo, procure um hub de eventos chamado **insights-métricas-PT1M**.

Para saber mais sobre como enviar informações de diagnóstico para um hub de eventos, confira [Streaming diagnóstico do Azure dados no Hot Path usando os hubs de eventos](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md).

## <a name="analyze-the-logs"></a>Analisar os logs

O Log Analytics do Azure está em execução com um mecanismo de Kusto para que você possa consultar seus logs para análise. Para obter uma breve introdução à consulta de logs usando o Kusto, consulte o [tutorial de log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).

Os logs de aplicativo fornecem informações críticas e logs detalhados sobre a integridade, o desempenho e muito mais do aplicativo. Nas próximas seções estão algumas consultas simples para ajudá-lo a entender os Estados atuais e antigos do seu aplicativo.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Mostrar logs de aplicativo do Azure Spring Cloud

Para examinar uma lista de logs de aplicativo do Azure Spring Cloud, classificados por tempo com os logs mais recentes mostrados primeiro, execute a seguinte consulta:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Mostrar entradas de logs contendo erros ou exceções

Para examinar as entradas de log não classificadas que mencionam um erro ou uma exceção, execute a seguinte consulta:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Use essa consulta para localizar erros ou modificar os termos da consulta para localizar códigos de erro ou exceções específicas.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Mostrar o número de erros e exceções relatados pelo seu aplicativo na última hora

Para criar um gráfico de pizza que exibe o número de erros e exceções registrados pelo seu aplicativo na última hora, execute a seguinte consulta:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Saiba mais sobre como consultar logs de aplicativos

O Azure Monitor fornece amplo suporte para consultar logs de aplicativos usando Log Analytics. Para saber mais sobre esse serviço, confira [introdução às consultas de log em Azure monitor](../azure-monitor/logs/get-started-queries.md). Para obter mais informações sobre como criar consultas para analisar os logs do aplicativo, consulte [visão geral das consultas de log no Azure monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="frequently-asked-questions-faq"></a>Perguntas frequentes

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Como converter rastreamentos de pilha Java de várias linhas em uma única linha?

Há uma solução alternativa para converter os rastreamentos de pilha de várias linhas em uma única linha. Você pode modificar a saída de log do Java para reformatar mensagens de rastreamento de pilha, substituindo caracteres de nova linha com um token. Se você usar a biblioteca Java Logback, poderá reformatar as mensagens de rastreamento de pilha adicionando `%replace(%ex){'[\r\n]+', '\\n'}%nopex` da seguinte maneira:

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
E, em seguida, você pode substituir o token por caracteres de nova linha novamente em Log Analytics como abaixo:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Talvez você possa usar a mesma estratégia para outras bibliotecas de log Java.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Implantar seu primeiro aplicativo do Azure Spring Cloud](spring-cloud-quickstart.md)