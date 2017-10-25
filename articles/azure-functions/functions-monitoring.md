---
title: Monitorar Azure Functions
description: "Saiba como usar o Azure Application Insights com o Azure Functions para monitorar a execução da função."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: 355cb2cef52b5dfecddae228d0cc24a069d3b695
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-functions"></a>Monitorar Azure Functions

## <a name="overview"></a>Visão geral 

O [Azure Functions](functions-overview.md) oferece integração interna com o [Azure Application Insights](../application-insights/app-insights-overview.md) para funções de monitoramento. Este artigo mostra como configurar o Functions para enviar dados de telemetria ao Application Insights.

![Metrics Explorer do Application Insights](media/functions-monitoring/metrics-explorer.png)

O Functions também tem monitoramento interno que não usa o Application Insights. Recomendamos o Application Insights porque ele oferece mais dados e maneiras melhores de analisar os dados. Para obter informações sobre o monitoramento interno, consulte a [última seção deste artigo](#monitoring-without-application-insights).

## <a name="enable-application-insights-integration"></a>Habilitar a integração do Application Insights

Para um aplicativo de funções enviar dados ao Application Insights, ele precisa saber a chave de instrumentação de uma instância do Application Insights. Há duas maneiras de fazer essa conexão no [portal do Azure](https://portal.azure.com):

* [Criar uma instância conectada do Application Insights ao criar o aplicativo de funções](#new-function-app).
* [Conectar uma instância do Application Insights a um aplicativo de funções existente](#existing-function-app).
 
### <a name="new-function-app"></a>Novo aplicativo de funções

Habilitar o Application Insights na página **Criar** do Aplicativo Functions:

1. Definir a opção **Application Insights** como **Ativada**.

2. Selecionar um **Local do Application Insights**.

   ![Habilitar o Application Insights ao criar um aplicativo de funções](media/functions-monitoring/enable-ai-new-function-app.png)

### <a name="existing-function-app"></a>Aplicativo de funções existente

Obter uma chave de instrumentação e salvá-la em um aplicativo de funções:

1. Criar a instância do Application Insights. Definir o tipo de aplicativo como **Geral**.

   ![Uma instância do Application Insights, tipo Geral](media/functions-monitoring/ai-general.png)

2. Copie a chave de instrumentação da sua página **Essenciais** da instância do Application Insights. Focalize o fim do valor da chave exibido para obter um botão **Clique para copiar**.

   ![Copie a chave de instrução do Application Insights](media/functions-monitoring/copy-ai-key.png)

1. Na página de **configurações do Aplicativo** do aplicativo de funções, [adicione uma configuração de aplicativo](functions-how-to-use-azure-function-app-settings.md#settings) chamado APPINSIGHTS_INSTRUMENTATIONKEY e cole a chave de instrumentação.

   ![Adicione a chave de instrumentação às configurações do aplicativo](media/functions-monitoring/add-ai-key.png)

1. Clique em **Salvar**.

## <a name="view-telemetry-data"></a>Exibir dados de telemetria

Para navegar ao Application Insights de um aplicativo de funções no portal, selecione o link **Application Insights** na página **Visão geral** do aplicativo de funções.

Para obter informações sobre como usar o Application Insights, consulte a [documentação do o Application Insights](https://docs.microsoft.com/azure/application-insights/). Esta seção mostra alguns exemplos de como exibir dados no Application Insights. Se você já estiver familiarizado com o Application Insights, poderá ir diretamente para [as seções sobre como configurar e personalizar os dados de telemetria](#configure-categories-and-log-levels).

Em [Metrics Explorer](../application-insights/app-insights-metrics-explorer.md), você pode criar gráficos e alertas com base em métricas como o número de invocações de função, tempo de execução e taxa de sucesso.

![Metrics Explorer](media/functions-monitoring/metrics-explorer.png)

Na guia [Falha](../application-insights/app-insights-asp-net-exceptions.md), você pode criar gráficos e alertas com base em falhas de função e de exceções do servidor. O **Nome da Operação** é o nome da função. Falhas nas dependências não são mostradas, a menos que você implemente [telemetria personalizada](#custom-telemetry-in-c-functions) para dependências.

![Falhas](media/functions-monitoring/failures.png)

Na guia [Desempenho](../application-insights/app-insights-performance-counters.md), você pode analisar problemas de desempenho.

![Desempenho](media/functions-monitoring/performance.png)

A guia **Servidores** mostra a utilização de recursos e a taxa de transferência por servidor. Esses dados podem ser úteis para cenários de depuração em que as funções estão travando seus recursos subjacentes. Servidores são chamados de *Instâncias de função de nuvem*. 

![Servidores](media/functions-monitoring/servers.png)

A guia [Live Metrics Stream](../application-insights/app-insights-live-stream.md) mostra dados de métricas conforme eles são criados em tempo real.

![Fluxo ao vivo](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Dados de telemetria da consulta

A [Análise do Application Insights](../application-insights/app-insights-analytics.md) oferece acesso a todos os dados de telemetria na forma de tabelas em um banco de dados. A Análise fornece uma linguagem de consulta para extrair e manipular os dados.

![Selecionar análise](media/functions-monitoring/select-analytics.png)

![Exemplo de análise](media/functions-monitoring/analytics-traces.png)

Aqui está um exemplo de consulta. Este mostra a distribuição de solicitações por trabalho nos últimos 30 minutos.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

As tabelas disponíveis são mostradas na guia **Esquema** do painel esquerdo. Você pode encontrar os dados gerados por invocações de função nas tabelas a seguir:

* **rastreamentos** – logs criados pelo tempo de execução e por código de função.
* **solicitações** – uma para cada invocação de função.
* **exceções** – quaisquer exceções geradas pelo tempo de execução.
* **customMetrics** – contagem de invocações bem-sucedidas e com falha, taxa de sucesso e duração.
* **customEvents** – eventos rastreados pelo tempo de execução, por exemplo: solicitações HTTP que disparam uma função.
* **performanceCounters** – informações sobre o desempenho dos servidores em que as funções estão sendo executadas.

As outras tabelas são para testes de disponibilidade e telemetria do cliente/navegador. Você pode implementar a telemetria personalizada para adicionar dados a ela.

Dentro de cada tabela, alguns dos dados específicos do Functions estão em um campo `customDimensions`.  Por exemplo, a consulta a seguir recupera todos os rastreamentos que têm o nível de log `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

O tempo de execução fornece `customDimensions.LogLevel` e `customDimensions.Category`. Você pode fornecer campos adicionais nos logs que você grava em seu código de função. Consulte [Registro em log estruturado](#structured-logging) mais adiante neste artigo.

## <a name="configure-categories-and-log-levels"></a>Configurar categorias e níveis de log

Você pode usar o Application Insights sem qualquer configuração personalizada, mas a configuração padrão pode resultar em altos volumes de dados. Se você estiver usando uma assinatura do Azure do Visual Studio, poderá atingido o limite de dados para o Application Insights. O restante deste artigo mostra como configurar e personalizar os dados que suas funções enviam ao Application Insights.

### <a name="categories"></a>Categorias

O agente do Azure Functions inclui uma *categoria* para cada log. A categoria indica qual parte do código de tempo de execução ou do seu código de função gravou o log. 

O tempo de execução do Functions cria logs que têm uma categoria que começa com "Host". Por exemplo, os logs "função iniciada," "função executada" e "função concluída" têm a categoria "Host.Executor". 

Se você gravar logs no seu código de função, sua categoria será "Função".

### <a name="log-levels"></a>Níveis de log

O agente de funções do Azure também inclui um *nível de log* com cada log. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) é uma enumeração e o código inteiro indica a importância relativa:

|LogLevel    |Código|
|------------|---|
|Rastreamento       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhum        | 6 |

Nível de log `None` é explicado na próxima seção. 

### <a name="configure-logging-in-hostjson"></a>Configurar o registro em log no host.json

O arquivo *host.json* configura quanto registro em log um aplicativo de função envia ao Application Insights. Para cada categoria, você deve indicar o nível de log mínimo para enviar. Aqui está um exemplo:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Este exemplo configura as seguintes regras:

1. Para logs com a categoria "Host.Results" ou "Função", envie apenas o nível `Error` e acima ao Application Insights. Os logs para o nível `Information` e abaixo são ignorados.
2. Para logs com a categoria de Host. Agregador, envie somente o nível `Information` e acima ao Application Insights. Os logs para o nível `Debug` e abaixo são ignorados.
3. Para todos os outros logs, envie somente o nível `Information` e acima ao Application Insights.

O valor de categoria em *host.json* controla o registro em log para todas as categorias que começam com o mesmo valor. Por exemplo, "Host" em *host.json* controla o registro em log para "Host.General", "Host.Executor", "Host.Results" e assim por diante.

Se *host.json* incluir várias categorias que comecem com a mesma cadeia de caracteres, será feito primeiro a correspondência com as mais longas. Por exemplo, suponha que você queira que tudo do tempo de execução, exceto "Host.Aggregator", seja registrado em log no nível `Error`, enquanto os "Host.Aggregator" é registrado em log no nível `Information`:

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

Para suprimir todos os logs para uma categoria, você pode usar o nível de log `None`. Nenhum log é gravado com essa categoria e não há nenhum nível de log acima dela.

As seções a seguir descrevem as principais categorias de logs que o tempo de execução cria. 

### <a name="category-hostresults"></a>Categoria Host.Results

Esses logs aparecem como "solicitações" no Application Insights. Elas indicam o sucesso ou a falha de uma função.

![Gráfico de solicitações](media/functions-monitoring/requests-chart.png)

Todos esses logs são gravados no nível de `Information`, portanto, se você filtrar em `Warning` ou superior, não verá nenhum desses dados.

### <a name="category-hostaggregator"></a>Categoria Host.Aggregator

Esses logs fornecem contagens e médias de chamadas de função sobre um período [configurável](#configure-the-aggregator). O período padrão é de 30 segundos ou 1.000 resultados, o que ocorrer primeiro. 

Os logs aparecem como "customMetrics" no Application Insights. Exemplos são número de execuções, taxa de sucesso e duração.

![consulta de customMetrics](media/functions-monitoring/custom-metrics-query.png)

Todos esses logs são gravados no nível de `Information`, portanto, se você filtrar em `Warning` ou superior, não verá nenhum desses dados.

### <a name="other-categories"></a>Outras categorias

Todos os logs para categorias diferentes daquelas já listadas aparecem como "rastreamentos" no Application Insights.

![consulta de rastreamentos](media/functions-monitoring/analytics-traces.png)

Todos os logs com categorias que começam com "Host" são gravados pelo tempo de execução do Functions. Os logs "Função iniciada," "Função executada" e "Função concluída" têm a categoria "Host.Executor". Para execuções bem-sucedidas, esses logs são de nível `Information`; as exceções são registradas no nível `Error`. O tempo de execução também cria logs de nível `Warning`, por exemplo: mensagens de fila enviadas para a fila de suspeita.

Logs gravados pelo seu código de função têm a categoria "Função" e podem ser de qualquer nível de log.

## <a name="configure-the-aggregator"></a>Configurar o agregador

Conforme observado na seção anterior, o tempo de execução agrega dados sobre as execuções de função em um período. O período padrão é de 30 segundos ou 1.000 execuções, o que ocorrer primeiro. Você pode definir essa configuração no arquivo *host.json*.  Aqui está um exemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurar a amostragem

O Application Insights tem um recurso de [amostragem](../application-insights/app-insights-sampling.md) que pode protegê-lo contra a produção de excesso de dados de telemetria em horários de pico de carregamento. Quando o número de itens de telemetria excede uma taxa especificada, o Application Insights começa a ignorar aleatoriamente alguns dos itens de entrada. Você pode configurar a amostragem em *host.json*.  Aqui está um exemplo:

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

## <a name="write-logs-in-c-functions"></a>Gravar logs em funções C#

Você pode gravar logs no seu código de função que apareçam como rastreamentos no Application Insights.

### <a name="ilogger"></a>ILogger

Use um parâmetro [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) em funções, em vez de um parâmetro `TraceWriter`. Logs criados usando `TraceWriter` vão para o Application Insights, mas o `ILogger` permite que você faça [registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Com um objeto `ILogger`, chame os `Log<level>` [métodos de extensão no ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_) para criar logs. Por exemplo, o código a seguir grava `Information` logs com a categoria "Função".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Registro em log estruturado

A ordem dos espaços reservados, não seus nomes, determina quais parâmetros são usados na mensagem de log. Por exemplo, suponha que você tenha o seguinte código:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Se você mantiver a mesma cadeia de caracteres de mensagem e inverter a ordem dos parâmetros, o texto da mensagem resultante terá os valores nos locais errados.

Os espaços reservados são tratados dessa forma para que você possa fazer registro em log estruturado. O Application Insights armazena os pares de nome-valor do parâmetro além de cadeia de caracteres de mensagem. O resultado é que os argumentos da mensagem tornam-se campos que você pode consultar.

Por exemplo, se sua chamada do método do agente for semelhante ao exemplo anterior, você poderá consultar o campo `customDimensions.prop__rowKey`. O prefixo é adicionado para garantir que não haja colisões entre campos que o tempo de execução adiciona e que o código de função adiciona.

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

### <a name="logging-custom-metrics"></a>Como registrar métricas personalizadas em log  

Em funções de script C#, você pode usar o método de extensão `LogMetric` em `ILogger` para criar métricas personalizadas no Application Insights. Aqui está um exemplo de chamada de método:

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Esse código é uma alternativa a chamar `TrackMetric` usando [a API do Application Insights para .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Gravar logs em funções de JavaScript

Em funções do Node.js, use `context.log` para gravar logs. Registro em log estruturado não está habilitado.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Como registrar métricas personalizadas em log  

Em funções do Node.js, você pode usar o método `context.log.metric` para criar métricas personalizadas no Application Insights. Aqui está um exemplo de chamada de método:

```javascript
context.log.metric("TestMetric", 1234); 
```

Esse código é uma alternativa a chamar `trackMetric` usando [SDK do Node.js para Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>Telemetria personalizada em funções C#

Você pode usar o pacote do NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) para enviar dados de telemetria personalizados ao Application Insights.

Aqui está um exemplo de código C# que usa a [API de telemetria personalizada](../application-insights/app-insights-api-custom-events-metrics.md). O exemplo é para uma biblioteca de classes do .NET, mas o código do Application Insights é o mesmo para o script C#.

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
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

        private static TelemetryClient telemetry = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;

            telemetry.Context.Operation.Id = context.InvocationId.ToString();
            telemetry.Context.Operation.Name = "cs-http";
            if (!String.IsNullOrEmpty(name))
            {
                telemetry.Context.User.Id = name;
            }
            telemetry.TrackEvent("Function called");
            telemetry.TrackMetric("Test Metric", DateTime.Now.Millisecond);
            telemetry.TrackDependency("Test Dependency", 
                "swapi.co/api/planets/1/", 
                start, DateTime.UtcNow - start, true);

            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
    }
}
```

Não chame `TrackRequest` nem `StartOperation<RequestTelemetry>`, pois você verá solicitações duplicadas de uma invocação de função.  O tempo de execução do Functions controla automaticamente as solicitações.

Defina `telemetry.Context.Operation.Id` para a ID de invocação sempre que sua função for iniciada. Isso torna possível correlacionar todos os itens de telemetria para uma dada invocação de função.

```cs
telemetry.Context.Operation.Id = context.InvocationId.ToString();
```

## <a name="custom-telemetry-in-javascript-functions"></a>Telemetria personalizada em funções JavaScript

O [SDK do Node.js do Application Insights](https://www.npmjs.com/package/applicationinsights) está em beta no momento. Aqui está um exemplo de código que envia a telemetria personalizada ao Application Insights:

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

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

O parâmetro `tagOverrides` define `operation_Id` para a ID de invocação de função. Essa configuração permite que você correlacione toda a telemetria gerada automaticamente e a telemetria personalizada para uma dada invocação de função.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="dependencies"></a>Dependências

Dependências não são exibidas automaticamente, mas você pode escrever código personalizado para mostrar as dependências. O código de exemplo da [seção de telemetria personalizada C#](#custom-telemetry-in-c-functions) mostra como. O código de exemplo resulta em um *mapa de aplicativo* no Application Insights que tem esta aparência:

![Mapa do aplicativo](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Relatar problemas

Para relatar um problema com a integração do Application Insights em Functions ou para fazer sugestões ou uma solicitação, [crie um problema no GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Monitorando sem o Application Insights

Recomendamos o Application Insights para monitorar funções porque ele oferece mais dados e maneiras melhores de analisar os dados. Porém, você também pode encontrar telemetria e dados de registro em log nas páginas do portal do Azure para um aplicativo Functions. 

Selecione a guia **Monitor** para uma função e obtenha uma lista de execuções de função. Selecione uma execução de função para examinar a duração, os dados de entrada, os erros e os arquivos de log associados.

> [!IMPORTANT]
> Ao usar o [Plano de hospedagem de consumo](functions-overview.md#pricing) do Azure Functions, o bloco **Monitoramento** no Aplicativo de Funções não mostrará dados. Isso ocorre porque a plataforma gerenciar e dimensionar dinamicamente instâncias de computação para você. Essas métricas não são significativas em um plano de consumo.

### <a name="real-time-monitoring"></a>Monitoramento em tempo real

Monitoramento em tempo real está disponível clicando em **transmissão de evento ao vivo** na guia **Monitor** da função. A transmissão de evento ao vivo é exibida em um gráfico em uma nova guia no navegador

> [!NOTE]
> Há um problema conhecido que pode causar falha na população dos dados. Talvez seja preciso fechar a guia do navegador que contém a transmissão de evento ao vivo e clicar em **transmissão de evento ao vivo** novamente para permitir que os dados da transmissão do evento sejam preenchidos corretamente. 

Essas estatísticas são em tempo real, mas o gráfico real dos dados de execução pode ter aproximadamente 10 segundos de latência.

### <a name="monitor-log-files-from-a-command-line"></a>Monitorar arquivos de log de uma linha de comando

Você pode transmitir arquivos de log para uma sessão de linha de comando em uma estação de trabalho local usando a CLI (Interface de Linha de Comando) 1.0 do Azure ou o PowerShell.

### <a name="monitor-function-app-log-files-with-the-azure-cli-10"></a>Monitorar arquivos de log do aplicativo de funções com a CLI do Azure 1.0

Para começar, [instale a CLI do Azure 1.0](../cli-install-nodejs.md) e [entre no Azure](../xplat-cli-connect.md).

Use os comandos a seguir para habilitar o modo clássico do Gerenciamento de Serviço, escolha a sua assinatura e transmita arquivos de log:

```
azure config mode asm
azure account list
azure account set <subscriptionNameOrId>
azure site log tail -v <function app name>
```

### <a name="monitor-function-app-log-files-with-powershell"></a>Monitorar arquivos de log do aplicativo de funções com o PowerShell

Para começar, [instale e configure o Azure PowerShell](/powershell/azure/overview).

Use os comandos a seguir para adicionar sua conta do Azure, escolha sua assinatura e transmita os arquivos de log:

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail
```

Para obter mais informações, consulte [Como transmitir logs para aplicativos Web](../app-service/web-sites-enable-diagnostic-log.md#streamlogs). 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o Application Insights](https://docs.microsoft.com/azure/application-insights/)

> [!div class="nextstepaction"]
> [Saiba mais sobre a estrutura de registro em log que o Functions usa](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)
