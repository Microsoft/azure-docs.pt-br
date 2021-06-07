---
title: Acompanhamento de dependência no Azure Application Insights | Microsoft Docs
description: Monitore chamadas de dependência de seu aplicativo Web local ou Microsoft Azure com Application Insights.
ms.topic: conceptual
ms.date: 08/26/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 124b8d3de59d1645379d50360e69a5fdbd5587e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045285"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Rastreamento de dependência no insights Aplicativo Azure 

Uma *dependência* é um componente que é chamado pelo seu aplicativo. Normalmente, ele é um serviço chamado usando HTTP, um banco de dados ou um sistema de arquivos. [Application insights](./app-insights-overview.md) mede a duração de chamadas de dependência, se sua falha ou não, juntamente com informações adicionais, como nome de dependência e assim por diante. Você pode investigar chamadas de dependência específicas e correlacioná-las a solicitações e exceções.

## <a name="automatically-tracked-dependencies"></a>Dependências rastreadas automaticamente

Os SDKs do Application Insights para .NET e .NET Core são fornecidos com o `DependencyTrackingTelemetryModule` , que é um módulo de telemetria que coleta automaticamente as dependências. Essa coleção de dependências é habilitada automaticamente para aplicativos [ASP.net](./asp-net.md) e [ASP.NET Core](./asp-net-core.md) , quando configurada de acordo com os documentos oficiais vinculados. `DependencyTrackingTelemetryModule` é enviado como [este](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) pacote NuGet e é colocado automaticamente ao usar qualquer um dos pacotes NuGet `Microsoft.ApplicationInsights.Web` ou `Microsoft.ApplicationInsights.AspNetCore` .

 `DependencyTrackingTelemetryModule` atualmente rastreia as seguintes dependências automaticamente:

|Dependências |Detalhes|
|---------------|-------|
|Http/https | Chamadas http/https locais ou remotas |
|Chamadas do WCF| Rastreado automaticamente se as associações baseadas em http forem usadas.|
|SQL | Chamadas feitas com `SqlClient` . Consulte [isto](#advanced-sql-tracking-to-get-full-sql-query) para capturar a consulta SQL.  |
|[Armazenamento do Azure (BLOB, tabela, fila)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chamadas feitas com o cliente de armazenamento do Azure. |
|[SDK do cliente do EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versão 1.1.0 e posterior. |
|[SDK do Cliente do Barramento de Serviço](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versão 3.0.0 e posterior. |
|Azure Cosmos DB | Rastreado automaticamente se HTTP/HTTPS for usado. O modo TCP não será capturado pelo Application Insights. |

Se você não tiver uma dependência ou se estiver usando um SDK diferente, verifique se ele está na lista de [dependências coletadas automaticamente](./auto-collect-dependencies.md). Se a dependência não for coletada automaticamente, você ainda poderá rastreá-la manualmente com uma [chamada de dependência de faixa](./api-custom-events-metrics.md#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Configurar o rastreamento automático de dependência em aplicativos de console

Para rastrear automaticamente as dependências de aplicativos de console do .NET, instale o pacote NuGet `Microsoft.ApplicationInsights.DependencyCollector` e inicialize da `DependencyTrackingTelemetryModule` seguinte maneira:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Para aplicativos de console do .NET Core, TelemetryConfiguration. Active é obsoleto. Consulte as diretrizes na documentação do [serviço de trabalho](./worker-service.md) e a [documentação de monitoramento de ASP.NET Core](./asp-net-core.md)

### <a name="how-automatic-dependency-monitoring-works"></a>Como funciona o monitoramento de dependência automática?

As dependências são coletadas automaticamente usando uma das seguintes técnicas:

* Usando a instrumentação de código de byte em volta dos métodos Select. (InstrumentationEngine de StatusMonitor ou extensão de aplicativo Web do Azure)
* Retornos de chamada EventSource
* Retornos de chamada de diagnosticm (nos SDKs .NET/.NET Core mais recentes)

## <a name="manually-tracking-dependencies"></a>Controlando dependências manualmente

Veja a seguir alguns exemplos de dependências, que não são coletadas automaticamente e, portanto, exigem acompanhamento manual.

* O Azure Cosmos DB é acompanhado automaticamente somente se [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) é usado. O modo TCP não será capturado pelo Application Insights.
* Redis

Para essas dependências não coletadas automaticamente pelo SDK, você pode rastreá-las manualmente usando a [API do TrackDependency](api-custom-events-metrics.md#trackdependency) que é usada pelos módulos de coleção automática padrão.

Por exemplo, se você criar seu código com um assembly que não escreveu, poderá determinar o tempo de todas as chamadas nele, para descobrir qual sua contribuição aos tempos de resposta. Para que esses dados sejam exibidos nos gráficos de dependência no Application Insights, envie-os usando `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Como alternativa, `TelemetryClient` fornece métodos `StartOperation` de extensão e `StopOperation` que podem ser usados para controlar manualmente as dependências, como mostrado [aqui](custom-operations-tracking.md#outgoing-dependencies-tracking)

Se você quiser desativar o módulo de rastreamento de dependência padrão, remova a referência a DependencyTrackingTelemetryModule em [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para aplicativos ASP.net. Para ASP.NET Core aplicativos, siga as instruções [aqui](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Controlando chamadas AJAX de páginas da Web

Para páginas da Web, Application Insights SDK do JavaScript coleta automaticamente chamadas AJAX como dependências.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Acompanhamento de SQL avançado para obter a consulta SQL completa

> [!NOTE]
> Azure Functions requer configurações separadas para habilitar a coleta de texto SQL, consulte [Configurar monitoramento para Azure Functions](../../azure-functions/configure-monitoring.md) para saber mais.

Para chamadas SQL, o nome do servidor e do banco de dados é sempre coletado e armazenado como o nome do coletado `DependencyTelemetry` . Há um campo adicional chamado ' data ', que pode conter o texto completo da consulta SQL.

Para aplicativos ASP.NET Core, agora é necessário aceitar a coleta de texto SQL usando
```csharp
services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) => { module. EnableSqlCommandTextInstrumentation = true; });
```

Para aplicativos ASP.NET, o texto completo da consulta SQL é coletado com a ajuda da instrumentação de código de byte, que requer o uso do mecanismo de instrumentação ou usando o pacote NuGet [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) em vez da biblioteca System. Data. SqlClient. As etapas específicas da plataforma para habilitar a coleta de consulta SQL completa são descritas abaixo:

| Plataforma | Etapas necessárias para obter a consulta SQL completa |
| --- | --- |
| Aplicativo Web do Azure |No painel de controle do aplicativo Web, [abra a folha Application insights](../../azure-monitor/app/azure-web-apps.md) e habilite os comandos SQL no .net |
| Servidor IIS (VM do Azure, local e assim por diante). | Use o pacote NuGet [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) ou use o módulo status monitor PowerShell para [instalar o mecanismo de instrumentação](../../azure-monitor/app/status-monitor-v2-api-reference.md#enable-instrumentationengine) e reiniciar o IIS. |
| Serviço de Nuvem do Azure | Adicionar [tarefa de inicialização para instalar o StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Seu aplicativo deve ser integrado ao SDK do ApplicationInsights no momento da compilação Instalando pacotes NuGet para aplicativos [ASP.net](./asp-net.md) ou [ASP.NET Core](./asp-net-core.md) |
| IIS Express | Use o pacote NuGet [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) .
| Trabalhos Web do Azure | Use o pacote NuGet [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) .

Além das etapas específicas da plataforma acima, você **também deve optar explicitamente por habilitar a coleção de comandos SQL** modificando o arquivo de applicationInsights.config com o seguinte:

```xml
<TelemetryModules>
  <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
    <EnableSqlCommandTextInstrumentation>true</EnableSqlCommandTextInstrumentation>
  </Add>
```

Nos casos acima, a maneira correta de validar esse mecanismo de instrumentação está instalada corretamente é Validando que a versão do SDK coletada `DependencyTelemetry` é ' rddp '. ' rdddsd ' ou ' rddf ' indica que as dependências são coletadas por meio de retornos de chamada de Diagnostic ou EventSource e, portanto, a consulta SQL completa não será capturada.

## <a name="where-to-find-dependency-data"></a>Onde encontrar dados de dependência

* O [Mapa do Aplicativo](app-map.md) visualiza as dependências entre seu aplicativo e os componentes de vizinhança.
* O [diagnóstico de transação](transaction-diagnostics.md) mostra dados unificados e correlacionados do servidor.
* [Guia navegadores](javascript.md) mostra chamadas AJAX dos navegadores dos seus usuários.
* Clique pelas solicitações com falha ou lentas para verificar a dependência de chamadas.
* O [Analytics](#logs-analytics) pode ser usado para consultar dados de dependência.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> Diagnosticar solicitações lentas

Cada evento de solicitação é associado às chamadas de dependência, exceções e outros eventos que são rastreados enquanto seu aplicativo está processando a solicitação. Portanto, se algumas solicitações estiverem fazendo mal, você poderá descobrir se é devido a respostas lentas de uma dependência.

### <a name="tracing-from-requests-to-dependencies"></a>Rastreamento de solicitações de dependências

Abra a guia **desempenho** e navegue até a guia **dependências** na parte superior ao lado de operações.

Clique em um **nome de dependência** em geral. Depois de selecionar uma dependência, um grafo da distribuição de durações da dependência será exibido à direita.

![Na guia desempenho, clique na guia dependência na parte superior e, em seguida, um nome de dependência no gráfico](./media/asp-net-dependencies/2-perf-dependencies.png)

Clique no botão **amostras** azuis na parte inferior direita e, em seguida, em um exemplo para ver os detalhes da transação de ponta a ponta.

![Clique em uma amostra para ver os detalhes da transação de ponta a ponta](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Perfil de seu site ativo

Não sabe para onde o tempo vai? O [criador de perfil Application insights](../../azure-monitor/app/profiler.md) RASTREIA chamadas http para seu site ativo e mostra as funções em seu código que levaram a hora mais longa.

## <a name="failed-requests"></a>Solicitações com falha

As solicitações com falha também podem ser associadas a chamadas com falha para as dependências.

Podemos ir até a guia **falhas** à esquerda e, em seguida, clicar na guia **dependências** na parte superior.

![Clique no gráfico de solicitações com falha](./media/asp-net-dependencies/4-fail.png)

Aqui você poderá ver a contagem de dependências com falha. Para obter mais detalhes sobre uma ocorrência com falha ao tentar clicar em um nome de dependência na tabela inferior. Você pode clicar no botão de **dependências** azuis na parte inferior direita para obter os detalhes da transação de ponta a ponta.

## <a name="logs-analytics"></a>Logs (Análise)

Você pode rastrear dependências na [linguagem de consulta Kusto](/azure/kusto/query/). Aqui estão alguns exemplos.

* Localize todas as chamadas com falha de dependência:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Localize as chamadas AJAX:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Localize as chamadas de dependência associadas a solicitações:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Localize as chamadas do AJAX associadas a exibições de página:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Como o coletor de dependências automáticas relatam chamadas com falha para dependências?*

* As chamadas de dependência com falha terão o campo ' êxito ' definido como falso. `DependencyTrackingTelemetryModule` não relata `ExceptionTelemetry` . O modelo de dados completo para dependência é descrito [aqui](data-model-dependency-telemetry.md).

### <a name="how-do-i-calculate-ingestion-latency-for-my-dependency-telemetry"></a>*Como fazer calcular a latência de ingestão para minha telemetria de dependência?*

```kusto
dependencies
| extend E2EIngestionLatency = ingestion_time() - timestamp 
| extend TimeIngested = ingestion_time()
```

### <a name="how-do-i-determine-the-time-the-dependency-call-was-initiated"></a>*Como fazer determinar a hora em que a chamada de dependência foi iniciada?*

No modo de exibição de consulta Log Analytics `timestamp` representa o momento em que a chamada TrackDependency () foi iniciada, o que ocorre imediatamente após o recebimento da resposta da chamada de dependência. Para calcular a hora em que a chamada de dependência começou, você pegaria `timestamp` e subtrairia o registro `duration` da chamada de dependência.

## <a name="open-source-sdk"></a>SDK do código-fonte aberto
Assim como todos os Application Insights SDK, o módulo de coleta de dependência também é de código-fonte aberto. Leia e contribua com o código ou relate problemas no [repositório do GitHub oficial](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Próximas etapas

* [Exceções](./asp-net-exceptions.md)
* [Dados do usuário e da página](./javascript.md)
* [Disponibilidade](./monitor-web-app-availability.md)

