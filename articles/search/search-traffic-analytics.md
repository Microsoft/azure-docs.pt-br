---
title: Telemetria para análise de tráfego de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite a análise de tráfego de pesquisa para Azure Cognitive Search, colete telemetria e eventos iniciados pelo usuário usando o Application Insights e, em seguida, analise as descobertas em um relatório Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/29/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 10bb0364bf7c54cd07d6dfa5725b3a626622d390
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097785"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Coletar dados telemétricos para análise de tráfego de pesquisa

A análise de tráfego de pesquisa é um padrão para coletar telemetria sobre interações do usuário com o aplicativo Azure Cognitive Search, como eventos de clique iniciado pelo usuário e entradas do teclado. Com essas informações você pode determinar a eficácia de sua solução de pesquisa, incluindo termos de pesquisa populares, taxa de cliques e quais entradas de consulta geram zero resultados.

Esse padrão depende do [Application Insights](../azure-monitor/app/app-insights-overview.md) (um recurso do [Azure Monitor](../azure-monitor/index.yml)) para coletar dados do usuário. É necessário adicionar instrumentação ao código do seu cliente, conforme descrito neste artigo. Por fim, você precisará de um mecanismo de geração de relatórios para analisar os dados. É recomendável Power BI, mas você pode usar o painel do aplicativo ou qualquer ferramenta que se conecte ao Application Insights.

> [!NOTE]
> O padrão descrito neste artigo é para cenários avançados e dados de fluxo de cliques gerados pelo código adicionado ao seu cliente. Por outro lado, os logs de serviço são fáceis de configurar, fornecem uma variedade de métricas e podem ser feitos no portal sem a necessidade de código. O registro em log é recomendado para todos os cenários. Para obter mais informações, consulte [Coletar e analisar dados de logs](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identificar dados de pesquisa relevantes

Para ter métricas úteis para análise de tráfego de pesquisa, é necessário registrar alguns sinais dos usuários do aplicativo de pesquisa. Esses sinais indicam o conteúdo no qual os usuários estão interessados e o que eles consideraram relevantes. Para análise do tráfego de pesquisa, estão incluídos:

+ Eventos de pesquisa gerados pelo usuário: Apenas consultas de pesquisa iniciadas por um usuário são interessantes. Outras solicitações de pesquisa, como as usadas para preencher facetas ou recuperar informações internas, não são importantes. Certifique-se de instrumentar apenas eventos iniciados pelo usuário para evitar a distorção ou tendência nos resultados.

+ Eventos de clique gerados pelo usuário: Em uma página de resultados de pesquisa, um clique geralmente significa que um documento é um resultado relevante para uma consulta de pesquisa específica.

Ao vincular os eventos de pesquisa e de clique com uma ID de correlação, você terá uma melhor compreensão do desempenho da funcionalidade de pesquisa do aplicativo.

## <a name="add-search-traffic-analytics"></a>Adicionar análise de tráfego de pesquisa

Na página do [portal](https://portal.azure.com) do serviço de pesquisa cognitiva do Azure, abra a página Pesquisar análise de tráfego para acessar uma folha de consulta para seguir este padrão de telemetria. Nesta página, você pode selecionar ou criar um recurso do Application Insights, obter a chave de instrumentação, copiar trechos de código que podem ser adaptados para sua solução e baixar um relatório de Power BI criado sobre o esquema refletido no padrão.

![Pesquisar a página de Análise de Tráfego no portal](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Pesquisar a página de Análise de Tráfego no portal")

## <a name="1---set-up-application-insights"></a>1 - Configurar o Application Insights

Selecione um recurso existente do Application Insights ou [crie um ](../azure-monitor/app/create-new-resource.md) caso você não tenha um. Se usar a página Pesquisar Análise de Tráfego, você poderá copiar a chave de instrumentação que seu aplicativo precisa para se conectar ao Application Insights.

Após ter um recurso do Application Insights, você pode seguir as [instruções para idiomas e plataformas compatíveis](../azure-monitor/app/platforms.md) para registrar seu aplicativo. O registro é simplesmente adicionar a chave de instrumentação do Application Insights ao seu código, o que configura a associação. A chave pode ser encontrada no portal ou na página Pesquisar Análise de Tráfego ao selecionar um recurso existente.

Um atalho que funciona para alguns tipos de projeto do Visual Studio é refletido nas etapas a seguir. Ele cria um recurso e registra seu aplicativo em apenas alguns cliques.

1. Abra sua solução e selecione **Projeto** > **Adicionar o Application Insights Telemetry** para desenvolver o Visual Studio e o ASP.NET.

1. Clique em **Começar a usar**.

1. Registre seu aplicativo fornecendo uma conta Microsoft, assinatura do Azure e um recurso do Application Insights (um novo recurso é o padrão). Clique em **Registrar**.

Neste momento, seu aplicativo está configurado para monitoramento de aplicativos, o que significa que todos os carregamentos de páginas são rastreados com métricas padrão. Para obter mais informações sobre as etapas anteriores, consulte [Habilitar a telemetria do lado do servidor do Application Insights](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 - Adicionar instrumentação

É nessa etapa que você instrumenta seu próprio aplicativo de pesquisa, usando o recurso Application Insights criado na etapa anterior. Há quatro etapas para esse processo, começando com a criação de um cliente de telemetria.

### <a name="step-1-create-a-telemetry-client"></a>Etapa 1: Criar um cliente de telemetria

Crie um objeto que envie eventos para o Application Insights. Você pode adicionar instrumentação ao código do seu aplicativo do lado do servidor ou ao código do lado do cliente em execução em um navegador, expressos aqui como variantes de C# e do JavaScript (para outras linguagens, consulte a lista completa de [plataformas e estruturas compatíveis](../azure-monitor/app/platforms.md). Escolha a abordagem que fornece a profundidade desejada das informações.

A telemetria do lado do servidor captura métricas na camada de aplicativos; por exemplo, em aplicativos executados como um serviço Web na nuvem ou como um aplicativo local em uma rede corporativa. A telemetria do lado do servidor captura eventos de pesquisa e de clique, a posição de um documento nos resultados e as informações de consulta, mas sua coleta de dados terá o escopo definido para qualquer informação disponível nessa camada.

No cliente, você pode ter um código adicional que manipula entradas de consulta, adiciona navegação ou inclui contexto (por exemplo, consultas iniciadas a partir de uma home page em vez de uma página de produto). Se isso descreve sua solução, você poderá optar pela instrumentação do lado do cliente para que sua telemetria reflita os detalhes adicionais. O modo como esses detalhes adicionais são coletados vai além do escopo desse padrão, mas você pode analisar [Application Insights para páginas da Web](../azure-monitor/app/javascript.md#explore-browserclient-side-data) para obter mais instruções. 

**Use o C#**

Para o C#, o **InstrumentationKey** deve ser definido em sua configuração de aplicativo, como appsettings.jsem se o seu projeto for ASP.net. Se não tiver certeza da localização da chave, consulte as instruções de registro.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**Usar JavaScript**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>Etapa 2: Solicitar uma ID de pesquisa para correlação

Para correlacionar solicitações de pesquisa com cliques, é necessário ter uma ID de correlação que relacione esses dois eventos distintos. A Pesquisa do Azure Cognitive Search fornece uma ID de pesquisa quando você a solicita com um cabeçalho HTTP.

A ID de pesquisa permite a correlação das métricas emitidas pela Pesquisa do Azure Cognitive Search para a solicitação em si, com as métricas personalizadas que você está registrando no Application Insights.

**Usar C# (SDK do v11 mais recente)**

O SDK mais recente requer o uso de um pipeline http para definir o cabeçalho, conforme detalhado neste [exemplo](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Pipeline.md#implementing-a-syncronous-policy).

```csharp
// Create a custom policy to add the correct headers
public class SearchIdPipelinePolicy : HttpPipelineSynchronousPolicy
{
    public override void OnSendingRequest(HttpMessage message)
    {
        message.Request.Headers.SetValue("x-ms-azs-return-searchid", "true");
    }
}
```

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Azure.Search.Documents

SearchClientOptions clientOptions = new SearchClientOptions();
clientOptions.AddPolicy(new SearchIdPipelinePolicy(), HttpPipelinePosition.PerCall);

var client = new SearchClient("<SearchServiceName>", "<IndexName>", new AzureKeyCredential("<QueryKey>"), options: clientOptions);

Response<SearchResults<SearchDocument>> response = await client.SearchAsync<SearchDocument>(searchText: searchText, searchOptions: options);
string searchId = string.Empty;
if (response.GetRawResponse().Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Usar C# (SDK do V10 mais antigo)**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>));

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Usar JavaScript (chamando APIs REST)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Etapa 3: Eventos de Pesquisa de logs

Sempre que uma solicitação de pesquisa é emitida por um usuário, você deve registrá-la como um evento de pesquisa com o esquema a seguir em um evento personalizado do Application Insights. Lembre-se de registrar apenas consultas de pesquisa geradas pelo usuário.

+ **SearchServiceName**: (cadeia de caracteres) nome do serviço de pesquisa
+ **SearchId**: (guid) identificador exclusivo da consulta de pesquisa (vem na resposta da pesquisa)
+ **IndexName**: (cadeia de caracteres) índice do serviço de pesquisa a ser consultado
+ **QueryTerms**: (cadeia de caracteres) termos de pesquisa inseridos pelo usuário
+ **ResultCount**: (int) número de documentos retornados (vem na resposta da pesquisa)
+ **ScoringProfile**: (cadeia de caracteres) nome do perfil de pontuação usado, se houver

> [!NOTE]
> Solicite a contagem de consultas geradas pelo usuário adicionando $count=true à consulta de pesquisa. Para obter mais informações, consulte [Pesquisar documentos (REST)](/rest/api/searchservice/search-documents#counttrue--false).
>

**Use o C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
};
_telemetryClient.TrackEvent("Search", properties);
```

**Usar JavaScript**

```javascript
appInsights.trackEvent("Search", {
  SearchServiceName: <service name>,
  SearchId: <search id>,
  IndexName: <index name>,
  QueryTerms: <search terms>,
  ResultCount: <results count>,
  ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Etapa 4: Eventos de Cliques de log

Sempre que um usuário clica em um documento, esse é um sinal que deve ser registrado para fins de análise de pesquisa. Use os eventos personalizados do Application Insights para registrar esses eventos com o esquema a seguir:

+ **ServiceName**: (cadeia de caracteres) nome do serviço de pesquisa
+ **SearchId**: (guid) identificador exclusivo da consulta de pesquisa relacionada
+ **DocId**: (cadeia de caracteres) identificador do documento
+ **Position**: (int) classificação do documento na página de resultados da pesquisa

> [!NOTE]
> Position refere-se à ordem cardinal em seu aplicativo. Você tem a liberdade para definir esse número, desde que seja sempre o mesmo, para permitir a comparação.
>

**Use o C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
};
_telemetryClient.TrackEvent("Click", properties);
```

**Usar JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - Analisar no Power BI

Após instrumentar o aplicativo e verificar se o aplicativo está conectado corretamente ao Application Insights, você baixa um modelo de relatório predefinido para analisar os dados na área de trabalho do Power BI. O relatório contém gráficos e tabelas predefinidos úteis para analisar os dados adicionais capturados para análise de tráfego de pesquisa.

1. No painel de navegação esquerdo do painel do Azure Cognitive Search, em **Configurações**, clique em **Análise do tráfego de pesquisa**.

1. Na página **Análise de tráfego de pesquisa**, na etapa 3, clique em **Obter o Power BI Desktop** para instalar o Power BI.

   ![Obter relatórios do Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Obter relatórios do Power BI")

1. Na mesma página, clique em **Download do relatório do Power BI**.

1. O relatório é aberto no Power BI Desktop e é pedido que você se conecte ao Application Insights e forneça credenciais. Você pode encontrar as informações de conexão nas páginas do portal do Azure para o recurso do Application Insights. Para as credenciais, forneça o mesmo nome de usuário e senha que você usa para entrar no portal.

   ![Conecte-se ao Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Conecte-se ao Application Insights")

1. Clique em **Carregar**.

O relatório contém gráficos e tabelas que ajudam você a tomar decisões mais inteligentes a fim de melhorar o desempenho e a relevância da pesquisa.

As métricas incluem os seguintes itens:

+ Volume de pesquisa e pares de documentos/termos mais populares: termos que resultam no mesmo documento clicado, ordenados por cliques.
+ Pesquisa sem cliques: termos das principais consultas que não registraram nenhum clique

A captura de tela abaixo mostra a aparência de um relatório interno se você tiver usado todos os elementos do esquema.

![Painel do Power BI para o Azure Cognitive Search](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Painel do Power BI para o Azure Cognitive Search")

## <a name="next-steps"></a>Próximas etapas

Instrumente seu aplicativo de pesquisa para obter dados informativos e avançados sobre o serviço de pesquisa.

Você pode encontrar mais informações sobre o [Application Insights](../azure-monitor/app/app-insights-overview.md) e visitar a [página de preços](https://azure.microsoft.com/pricing/details/application-insights/) para saber mais sobre suas diferentes camadas de serviço.

Saiba mais sobre como criar relatórios incríveis. Consulte [Introdução ao Power BI Desktop](/power-bi/fundamentals/desktop-getting-started) para obter detalhes.
