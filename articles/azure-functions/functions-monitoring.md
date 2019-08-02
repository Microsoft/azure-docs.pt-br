---
title: Monitorar Azure Functions
description: Saiba como usar Aplicativo Azure insights com Azure Functions para monitorar a execução da função.
services: functions
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 15fd8593f950e0f553d1b7ca34ee785692043cad
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304352"
---
# <a name="monitor-azure-functions"></a>Monitorar Azure Functions

O [Azure Functions](functions-overview.md) oferece integração interna com [informações de aplicativo Azure](../azure-monitor/app/app-insights-overview.md) para monitorar funções. Este artigo mostra como configurar Azure Functions para enviar arquivos de log gerados pelo sistema para Application Insights.

É recomendável usar Application Insights porque coleta dados de log, desempenho e erro. Ele detecta automaticamente anomalias de desempenho e inclui poderosas ferramentas de análise para ajudá-lo a diagnosticar problemas e a entender como suas funções são usadas. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo. Você pode até mesmo usar Application Insights durante o desenvolvimento do projeto de aplicativo de função local. Para obter mais informações, consulte [o que é Application insights?](../azure-monitor/app/app-insights-overview.md).

Como a instrumentação de Application Insights necessária é incorporada ao Azure Functions, tudo o que você precisa é de uma chave de instrumentação válida para conectar seu aplicativo de funções a um recurso de Application Insights.

## <a name="application-insights-pricing-and-limits"></a>Preços e limites do Application Insights

Você pode testar a integração do Application Insights aos Aplicativos de Funções gratuitamente. Há um limite diário para a quantidade de dados que pode ser processada gratuitamente. Você pode atingir esse limite durante o teste. O Azure fornece o portal e notificações por email quando está se aproximando do limite diário. Se você perder esses alertas e atingir o limite, novos logs não aparecerão em Application Insights consultas. Esteja ciente do limite para evitar o tempo de solução de problemas desnecessário. Para obter mais informações, consulte [Gerenciar o preço e o volume de dados no Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Habilitar a integração do Application Insights

Para um aplicativo de funções enviar dados ao Application Insights, ele precisa saber a chave de instrumentação de um recurso do Application Insights. A chave deve estar em uma configuração de aplicativo chamada **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Novo aplicativo de funções no portal

Quando você [cria seu aplicativo de funções no portal do Azure, a](functions-create-first-azure-function.md)integração Application insights é habilitada por padrão. O recurso Application Insights tem o mesmo nome que seu aplicativo de funções e é criado na mesma região ou na região mais próxima.

Para examinar a Application Insights recurso que está sendo criado, selecione-o para expandir a janela **Application insights** . Você pode alterar o **nome do novo recurso** ou escolher um **local** diferente em uma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde você deseja armazenar seus dados.

![Habilitar o Application Insights ao criar um aplicativo de funções](media/functions-monitoring/enable-ai-new-function-app.png)

Quando você escolhe **criar**, um recurso de Application insights é criado com seu aplicativo de funções, que `APPINSIGHTS_INSTRUMENTATIONKEY` tem o conjunto nas configurações do aplicativo. Tudo está pronto para começar.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Adicionar a um aplicativo de funções existente 

Ao criar um aplicativo de funções usando o [CLI do Azure](functions-create-first-azure-function-azure-cli.md), o [Visual Studio](functions-create-your-first-function-visual-studio.md)ou o [Visual Studio Code](functions-create-first-function-vs-code.md), você deve criar o recurso de Application insights. Em seguida, você pode adicionar a chave de instrumentação a partir desse recurso como uma configuração de aplicativo em seu aplicativo de funções.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

As versões anteriores das funções usavam o monitoramento interno, o que não é mais recomendado. Ao habilitar a integração de Application Insights para esse aplicativo de funções, você também deve [desabilitar o registro em log interno](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Exibir telemetria na guia Monitor

Com a [integração do Application insights habilitada](#enable-application-insights-integration), você pode exibir dados de telemetria na guia **monitorar** .

1. Na página do aplicativo de funções, selecione uma função que tenha sido executada pelo menos uma vez depois que Application Insights foi configurada. Em seguida, selecione a guia **Monitor** .

   ![Selecionar a guia Monitor](media/functions-monitoring/monitor-tab.png)

1. Selecione **Atualizar** periodicamente, até que a lista de invocações de função seja exibida.

   Pode levar até cinco minutos para que a lista seja exibida enquanto o cliente de telemetria batche dados para transmissão para o servidor. (O atraso não se aplica ao [Live Metrics Stream](../azure-monitor/app/live-stream.md). Esse serviço se conecta ao host de Funções quando você carrega a página, logo os logs são transmitidos diretamente para a página.)

   ![Lista de invocações](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Para ver os logs de uma invocação de função específica, selecione o link da coluna **Data** para a invocação.

   ![Link de detalhes da Invocação](media/functions-monitoring/invocation-details-link-ai.png)

   A saída de logs para essa invocação aparece em uma nova página.

   ![Detalhes da Invocação](media/functions-monitoring/invocation-details-ai.png)

Você pode ver que ambas as páginas têm uma **execução no Application insights** link para a consulta Application insights Analytics que recupera os dados.

![Executar no Application Insights](media/functions-monitoring/run-in-ai.png)

A consulta a seguir é exibida. Você pode ver que a lista de invocação está limitada aos últimos 30 dias. A lista mostra no máximo 20 linhas (`where timestamp > ago(30d) | take 20`). A lista de detalhes de invocação é para os últimos 30 dias sem nenhum limite.

![Lista de invocações de Análise do Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Para obter mais informações, consulte [Dados de telemetria da consulta](#query-telemetry-data) a seguir neste artigo.

## <a name="view-telemetry-in-application-insights"></a>Exibir a telemetria no Application Insights

Para abrir Application Insights de um aplicativo de funções no portal do Azure, vá para a página **visão geral** do aplicativo de funções. Em **recursos**configurados, selecione **Application insights**.

![Abrir Application Insights na página Visão geral do aplicativo de funções](media/functions-monitoring/ai-link.png)

Para obter informações sobre como usar o Application Insights, consulte a [documentação do o Application Insights](https://docs.microsoft.com/azure/application-insights/). Esta seção mostra alguns exemplos de como exibir dados no Application Insights. Se você já estiver familiarizado com o Application Insights, poderá ir diretamente para [as seções sobre como configurar e personalizar os dados de telemetria](#configure-categories-and-log-levels).

![Guia de visão geral do Application Insights](media/functions-monitoring/metrics-explorer.png)

As áreas de Application Insights a seguir podem ser úteis ao avaliar o comportamento, o desempenho e os erros em suas funções:

| Tabulação | DESCRIÇÃO |
| ---- | ----------- |
| **[Sucedi](../azure-monitor/app/asp-net-exceptions.md)** |  Crie gráficos e alertas com base em falhas de função e exceções de servidor. O **Nome da Operação** é o nome da função. Falhas em dependências não são mostradas a menos que você implemente telemetria personalizada para dependências. |
| **[Desempenho](../azure-monitor/app/performance-counters.md)** | Analisar problemas de desempenho. |
| **Servidores** | Exibir a utilização de recursos e a taxa de transferência por servidor. Esses dados podem ser úteis para cenários de depuração em que as funções estão travando seus recursos subjacentes. Servidores são chamados de **Instâncias de função de nuvem**. |
| **[Métrica](../azure-monitor/app/metrics-explorer.md)** | Crie gráficos e alertas baseados em métricas. As métricas incluem o número de invocações de função, tempo de execução e taxas de sucesso. |
| **[Live Metrics Stream](../azure-monitor/app/live-stream.md)** | Exiba os dados de métricas conforme eles são criados em tempo real. |

## <a name="query-telemetry-data"></a>Dados de telemetria da consulta

[Application insights Analytics](../azure-monitor/app/analytics.md) fornece acesso a todos os dados de telemetria na forma de tabelas em um banco de dados. A Análise fornece uma linguagem de consulta para extrair, manipular e visualizar os dados.

![Selecionar análise](media/functions-monitoring/select-analytics.png)

![Exemplo de análise](media/functions-monitoring/analytics-traces.png)

Veja um exemplo de consulta que mostra a distribuição de solicitações por trabalhador nos últimos 30 minutos.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

As tabelas disponíveis são mostradas na guia **esquema** à esquerda. Você pode encontrar os dados gerados por invocações de função nas tabelas a seguir:

| Tabela | DESCRIÇÃO |
| ----- | ----------- |
| **rastreamentos** | Logs criados pelo tempo de execução e por código de função. |
| **requests** | Uma solicitação para cada invocação de função. |
| **exceptions** | Todas as exceções geradas pelo tempo de execução. |
| **customMetrics** | A contagem de invocações bem-sucedidas e com falha, taxa de êxito e duração. |
| **customEvents** | Eventos rastreados pelo tempo de execução, por exemplo: Solicitações HTTP que disparam uma função. |
| **performanceCounters** | Informações sobre o desempenho dos servidores em que as funções estão sendo executadas. |

As outras tabelas são para testes de disponibilidade e telemetria de cliente e navegador. Você pode implementar a telemetria personalizada para adicionar dados a ela.

Dentro de cada tabela, alguns dos dados específicos do Functions estão em um campo `customDimensions`.  Por exemplo, a consulta a seguir recupera todos os rastreamentos que têm o nível de log `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

O tempo de execução `customDimensions.LogLevel` fornece `customDimensions.Category` os campos e. Você pode fornecer campos adicionais nos logs que você escreve em seu código de função. Consulte [Registro em log estruturado](#structured-logging) mais adiante neste artigo.

## <a name="configure-categories-and-log-levels"></a>Configurar categorias e níveis de log

Você pode usar Application Insights sem nenhuma configuração personalizada. A configuração padrão pode resultar em grandes volumes de dados. Se você estiver usando uma assinatura do Azure do Visual Studio, poderá ter atingido o limite de dados para o Application Insights. Posteriormente neste artigo, você aprenderá a configurar e personalizar os dados que suas funções enviam para Application Insights. Para um aplicativo de funções, o registro em log é configurado no arquivo [host. JSON] .

### <a name="categories"></a>Categorias

O agente do Azure Functions inclui uma *categoria* para cada log. A categoria indica qual parte do código de tempo de execução ou do seu código de função gravou o log. 

O tempo de execução do Functions cria logs com uma categoria que começa com "host". Os logs "função iniciada," "função executada" e "função concluída" têm a categoria "host. executor". 

Se você gravar logs em seu código de função, sua categoria será "função".

### <a name="log-levels"></a>Níveis de log

O agente de Azure Functions também inclui um *nível de log* com cada log. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) é uma enumeração e o código inteiro indica a importância relativa:

|LogLevel    |Código|
|------------|---|
|Rastreamento       | 0 |
|Depurar       | 1 |
|Information | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhum        | 6 |

Nível de log `None` é explicado na próxima seção. 

### <a name="log-configuration-in-hostjson"></a>Configuração de log em host. JSON

O arquivo [host. JSON] configura quanto registro em log um aplicativo de função envia ao Application Insights. Para cada categoria, você deve indicar o nível de log mínimo para enviar. Há dois exemplos: o primeiro exemplo tem como alvo o [tempo de execução das funções versão 2. x](functions-versions.md#version-2x) (.NET Core) e o segundo exemplo é para o tempo de execução da versão 1. x.

### <a name="version-2x"></a>Versão 2.x

O tempo de execução v2.x usa a [hierarquia de filtros de registro da .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Versão 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

Este exemplo configura as seguintes regras:

* Para logs com categoria `Host.Results` ou `Function`, envie somente `Error` o nível e acima para Application insights. Os logs para o nível `Warning` e abaixo são ignorados.
* Para logs com a categoria `Host.Aggregator`, enviar todos os logs para o Application Insights. O `Trace` nível de log é o mesmo que o que chamo de alguns agentes `Verbose`, mas usar `Trace` no [host. JSON] arquivo.
* Para todos os outros logs, envie somente o nível `Information` e acima ao Application Insights.

O valor de categoria em [host. JSON] controla o registro em log para todas as categorias que começam com o mesmo valor. `Host`no [host. JSON] controla o registro `Host.General`em `Host.Executor`log `Host.Results`para,, e assim por diante.

Se [host. JSON] incluir várias categorias que comecem com a mesma cadeia de caracteres, será feito primeiro a correspondência com as mais longas. Suponha que você deseja tudo do tempo de `Host.Aggregator` execução, exceto `Error` para fazer logon no nível `Host.Aggregator` , mas `Information` deseja fazer logon no nível:

### <a name="version-2x"></a>Versão 2.x 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Versão 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Para suprimir todos os logs para uma categoria, você pode usar o nível de log `None`. Nenhum log é gravado com essa categoria e não há nenhum nível de registro acima dele.

As seções a seguir descrevem as principais categorias de logs que o tempo de execução cria. 

### <a name="category-hostresults"></a>Categoria Host.Results

Esses logs aparecem como "solicitações" no Application Insights. Elas indicam o sucesso ou a falha de uma função.

![Gráfico de solicitações](media/functions-monitoring/requests-chart.png)

Todos esses logs são gravados no `Information` nível. Se você filtrar em `Warning` ou acima, não verá nenhum desses dados.

### <a name="category-hostaggregator"></a>Categoria Host.Aggregator

Esses logs fornecem contagens e médias de chamadas de função sobre um período [configurável](#configure-the-aggregator). O período padrão é de 30 segundos ou 1.000 resultados, o que ocorrer primeiro. 

Os logs estão disponíveis na tabela **customMetrics** no Application Insights. Os exemplos são o número de execuções, a taxa de sucesso e a duração.

![consulta de customMetrics](media/functions-monitoring/custom-metrics-query.png)

Todos esses logs são gravados no `Information` nível. Se você filtrar em `Warning` ou acima, não verá nenhum desses dados.

### <a name="other-categories"></a>Outras categorias

Todos os logs para categorias diferentes daquelas já listadas estão disponíveis na tabela **traces** no Application Insights.

![consulta de rastreamentos](media/functions-monitoring/analytics-traces.png)

Todos os logs com categorias que começam `Host` com são gravados pelo tempo de execução do functions. Os logs "função iniciada" e "função concluída" têm `Host.Executor`categoria. Para execuções bem-sucedidas, esses logs `Information` são de nível. As exceções são registradas no `Error` nível. O tempo de execução também cria logs de nível `Warning`, por exemplo: mensagens de fila enviadas para a fila de suspeita.

Os logs gravados pelo seu código de `Function` função têm categoria e podem ser qualquer nível de log.

## <a name="configure-the-aggregator"></a>Configurar o agregador

Conforme observado na seção anterior, o tempo de execução agrega dados sobre as execuções de função em um período. O período padrão é de 30 segundos ou 1.000 execuções, o que ocorrer primeiro. Você pode definir essa configuração no arquivo [host. JSON].  Veja um exemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurar a amostragem

O Application Insights tem um recurso de [amostragem](../azure-monitor/app/sampling.md) que pode protegê-lo de produzir dados de telemetria demais em execuções concluídas em momentos de pico de carga. Quando a taxa de execuções de entrada excede um limite especificado, Application Insights começa a ignorar aleatoriamente algumas das execuções de entrada. A configuração padrão para o número máximo de execuções por segundo é 20 (cinco na versão 1. x). Você pode configurar a amostragem em [host. JSON].  Veja um exemplo:

### <a name="version-2x"></a>Versão 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Versão 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [Amostragem](../azure-monitor/app/sampling.md) é habilitado por padrão. Se parecer que faltam dados, talvez seja necessário ajustar as configurações de amostragem para que se ajustem ao seu cenário de monitoramento específico.

## <a name="write-logs-in-c-functions"></a>Gravar logs em funções C#

Você pode gravar logs no seu código de função que apareçam como rastreamentos no Application Insights.

### <a name="ilogger"></a>ILogger

Use um parâmetro [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) em funções, em vez de um parâmetro `TraceWriter`. Logs criados usando `TraceWriter` o go to Application insights, mas `ILogger` permite que você faça o [registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Com um objeto `ILogger`, você chama os `Log<level>` [ métodos de extensão no ILogger ](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) para criar logs. O código a seguir `Information` grava logs com a categoria "função".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Registro em log estruturado

A ordem dos espaços reservados, não seus nomes, determina quais parâmetros são usados na mensagem de log. Suponha que você tenha o seguinte código:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Se você mantiver a mesma cadeia de caracteres de mensagem e inverter a ordem dos parâmetros, o texto da mensagem resultante terá os valores nos locais errados.

Os espaços reservados são tratados dessa forma para que você possa fazer registro em log estruturado. Application Insights armazena os pares nome-valor do parâmetro e a cadeia de caracteres da mensagem. O resultado é que os argumentos da mensagem tornam-se campos que você pode consultar.

Se a chamada do método de agente for parecida com o exemplo anterior, você poderá `customDimensions.prop__rowKey`consultar o campo. O `prop__` prefixo é adicionado para garantir que não haja colisões entre os campos que o tempo de execução adiciona e os campos que o código de função adiciona.

Você também pode consultar a cadeia de caracteres da mensagem original referenciando o campo `customDimensions.prop__{OriginalFormat}`.  

Aqui está uma representação JSON de exemplo de dados `customDimensions`:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Log de métricas personalizadas

Em funções de script C#, você pode usar o método de extensão `LogMetric` em `ILogger` para criar métricas personalizadas no Application Insights. Aqui está um exemplo de chamada de método:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Esse código é uma alternativa para chamar `TrackMetric` usando a API de Application insights para .net.

## <a name="write-logs-in-javascript-functions"></a>Gravar logs em funções de JavaScript

Em funções do Node.js, use `context.log` para gravar logs. O log estruturado não está habilitado.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Log de métricas personalizadas

Quando você está executando na [versão 1. x](functions-versions.md#creating-1x-apps) do tempo de execução do functions, as funções do Node `context.log.metric` . js podem usar o método para criar métricas personalizadas no Application insights. Atualmente, não há suporte para esse método na versão 2. x. Aqui está um exemplo de chamada de método:

```javascript
context.log.metric("TestMetric", 1234);
```

Esse código é uma alternativa para chamar `trackMetric` usando o SDK do node. js para Application insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Registrar telemetria personalizada C# em funções

Você pode usar o pacote do NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) para enviar dados de telemetria personalizados ao Application Insights. O exemplo C# a seguir usa a [API de telemetria personalizada](../azure-monitor/app/api-custom-events-metrics.md). O exemplo é para uma biblioteca de classes do .NET, mas o código do Application Insights é o mesmo para o script C#.

### <a name="version-2x"></a>Versão 2.x

A versão de tempo de execução 2.x usa recursos mais recentes no Application Insights para correlacionar automaticamente a telemetria com a operação atual. Não é necessário definir manualmente os campos de operação `Id`, `ParentId`ou `Name` .

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>Versão 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Não chame `TrackRequest` ou `StartOperation<RequestTelemetry>` porque você verá solicitações duplicadas para uma invocação de função.  O tempo de execução do Functions controla automaticamente as solicitações.

Não definir `telemetryClient.Context.Operation.Id`. Essa configuração global causa correlação incorreta quando muitas funções são executadas simultaneamente. Em vez disso, crie uma nova instância de telemetria (`DependencyTelemetry`, `EventTelemetry`) e modifique a propriedade `Context`. Em seguida, passe a instância de telemetria para o método `Track` correspondente em `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Esse método garante que a telemetria tenha os detalhes de correlação corretos para a invocação de função atual.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Registrar telemetria personalizada em funções JavaScript

Aqui está um trecho de código de exemplo que envia telemetria personalizada com o [Application insights SDK do node. js](https://github.com/microsoft/applicationinsights-node.js):

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

O `tagOverrides` parâmetro define o `operation_Id` para a ID de invocação da função. Essa configuração permite que você correlacione toda a telemetria gerada automaticamente e a telemetria personalizada para uma dada invocação de função.

## <a name="dependencies"></a>Dependências

As funções v2 coletam automaticamente as dependências para solicitações HTTP, ServiceBus e SQL.

Você pode escrever código personalizado para mostrar as dependências. Para obter exemplos, consulte o código de exemplo na [ C# seção telemetria personalizada](#log-custom-telemetry-in-c-functions). O código de exemplo resulta em um *mapa de aplicativo* em Application insights semelhante à imagem a seguir:

![Mapa do aplicativo](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Relatar problemas

Para relatar um problema com a integração do Application Insights em Functions ou para fazer sugestões ou uma solicitação, [crie um problema no GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Logs de streaming

Ao desenvolver um aplicativo, é sempre útil visualizar informações de registro em log realizado em tempo quase real. Você pode exibir um fluxo de arquivos de log que estão sendo gerados por suas funções no portal do Azure ou em uma sessão de linha de comando no computador local.

Isso é equivalente à saída vista quando você depura suas funções durante o [desenvolvimento local](functions-develop-local.md). Para obter mais informações, consulte [Como transmitir logs](../app-service/troubleshoot-diagnostic-logs.md#streamlogs).

> [!NOTE]
> Os logs de streaming dão suporte a apenas uma única instância do host do functions. Quando a função é dimensionada para várias instâncias, os dados de outras instâncias não são mostrados no fluxo de log. O [Live Metrics Stream](../azure-monitor/app/live-stream.md) no Application insights oferece suporte a várias instâncias. Embora também quase em tempo real, a análise de streaming também se baseia em [dados de amostra](#configure-sampling).

### <a name="portal"></a>Portal

Para exibir os logs de streaming no portal, selecione a guia **recursos da plataforma** em seu aplicativo de funções. Em seguida, em **monitoramento**, escolha **streaming de log**.

![Habilitar logs de streaming no portal](./media/functions-monitoring/enable-streaming-logs-portal.png)

Isso conecta seu aplicativo ao serviço de streaming de log e os logs de aplicativo são exibidos na janela. Você pode alternar entre **logs do aplicativo** e **logs do servidor Web**.  

![Exibir logs de streaming no portal](./media/functions-monitoring/streaming-logs-window.png)

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="azure-cli"></a>CLI do Azure

Você pode habilitar os logs de streaming usando o [CLI do Azure](/cli/azure/install-azure-cli). Use os comandos a seguir para entrar, escolher sua assinatura e transmitir arquivos de log:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Você pode habilitar os logs de streaming usando [Azure PowerShell](/powershell/azure/overview). Para o PowerShell, use os seguintes comandos para adicionar sua conta do Azure, escolha sua assinatura e transmita os arquivos de log:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Desabilitar o registro em log interno

Ao habilitar Application Insights, desabilite o log interno que usa o armazenamento do Azure. O registro em log interno é útil para testes com cargas de trabalho leves, mas não se destina ao uso de produção de alta carga. Para o monitoramento de produção, recomendamos Application Insights. Se o registro em log interno for usado na produção, o registro de log poderá estar incompleto devido à limitação no armazenamento do Azure.

Para desabilitar o registro em log interno, exclua a configuração de aplicativo `AzureWebJobsDashboard`. Para obter informações sobre como excluir configurações do aplicativo no portal do Azure, consulte a seção **Configurações do aplicativo** em [Como gerenciar um aplicativo de funções](functions-how-to-use-azure-function-app-settings.md#settings). Antes de excluir a configuração do aplicativo, certifique-se de que nenhuma função existente no mesmo aplicativo de funções use a configuração para gatilhos ou associações do armazenamento do Azure.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* [Application Insights](/azure/application-insights/)
* [Registro em log de ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host. JSON]: functions-host-json.md
