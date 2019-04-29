---
title: Implementar análise de tráfego de pesquisa — Azure Search
description: Habilite a análise de tráfego de pesquisa do Azure Search para adicionar a telemetria e eventos iniciados pelo usuário em arquivos de log.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c30c8bae3e76778a31cdd0695acde52b5b1c6b02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749543"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>Implementar análise de tráfego de pesquisa no Azure Search
Análise de tráfego de pesquisa é um padrão de implementação de um loop de comentários para seu serviço de pesquisa. Esse padrão descreve os dados necessários e como coletá-los usando o Application Insights, líder no setor de serviços de monitoramento em várias plataformas.

A análise de tráfego de pesquisa permite que você ganhe visibilidade em seu serviço de pesquisa e tenha ideias sobre os usuários e seus comportamentos. A coleta de dados sobre as escolhas dos usuários permite que você tome decisões que melhoram ainda mais sua experiência de pesquisa e recue quando os resultados não forem o esperado.

O Azure Search oferece uma solução de telemetria que integra o Azure Application Insights e o Power BI para fornecer monitoramento e rastreamento detalhado. Como a interação com o Azure Search ocorre apenas por meio de APIs, a telemetria deve ser implementada pelos desenvolvedores usando a pesquisa, seguindo as instruções nesta página.

## <a name="identify-relevant-search-data"></a>Identificar dados de pesquisa relevantes

Para ter métricas de pesquisa úteis, é necessário registrar alguns sinais dos usuários do aplicativo de pesquisa. Esses sinais indicam o conteúdo no qual os usuários estão interessados e que eles consideraram relevantes para suas necessidades.

Há dois sinais necessários para a Análise do Tráfego de Pesquisa:

1. Eventos de pesquisa gerados pelo usuário: somente as consultas de pesquisa iniciadas por um usuário são interessantes. Solicitações de pesquisa usadas para preencher facetas, conteúdo adicional ou qualquer informação interna, não são importantes e distorcem e influenciam seus resultados.

2. Eventos de clique gerados pelo usuário: Neste documento, cliques se referem a um usuário que seleciona um resultado de pesquisa específico retornado de uma consulta de pesquisa. Um clique geralmente significa que um documento é um resultado relevante para uma consulta de pesquisa específica.

Ao vincular eventos de pesquisa e de clique a uma ID de correlação, é possível analisar os comportamentos dos usuários em seu aplicativo. Essas informações de pesquisa são impossíveis de obter apenas com os logs de tráfego de pesquisa.

## <a name="add-search-traffic-analytics"></a>Adicionar análise de tráfego de pesquisa

Os sinais mencionados na seção anterior devem ser coletados do aplicativo de pesquisa à medida que o usuário interage com ele. O Application Insights é uma solução de monitoramento extensível, disponível para várias plataformas e com opções flexíveis de instrumentação. O uso do Application Insights permite que você aproveite os relatórios de pesquisa do Power BI criados pelo Azure Search para facilitar a análise de dados.

Na página do [Portal](https://portal.azure.com) de seu serviço Azure Search, a folha Análise de Tráfego de Pesquisa contém uma folha de consulta para seguir este padrão de telemetria. Também é possível selecionar ou criar um recurso do Application Insights e ver os dados necessários, tudo em um só lugar.

![Instruções da Análise do Tráfego de Pesquisa][1]

## <a name="1---select-a-resource"></a>1 - Selecionar um recurso

Você precisa selecionar um recurso do Application Insights para uso ou criar um, caso você não tenha um. Você pode usar um recurso que já está sendo usado para registrar os eventos personalizados necessários.

Ao criar um novo recurso do Application Insights, todos os tipos de aplicativo são válidos para esse cenário. Selecione aquele que melhor se adapta a plataforma que você está usando.

Você precisa da chave de instrumentação para criar o cliente de telemetria para seu aplicativo. Você pode obtê-la no painel do portal do Application Insights, ou você pode obtê-la na página da Análise do Tráfego de Pesquisa, selecionando a instância que você quer usar.

## <a name="2---add-instrumentation"></a>2 - Adicionar instrumentação

É nessa fase que você instrumenta seu próprio aplicativo de pesquisa, usando o recurso Application Insights criado na etapa anterior. Há quatro etapas nesse processo:

**Etapa 1: Criar um cliente de telemetria** Esse é o objeto que envia eventos ao recurso do Application Insights.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

Para outras plataformas e linguagens, consulte a [lista completa](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**Etapa 2: Solicitar uma ID de Pesquisa para correlação** Para correlacionar as solicitações de pesquisa com cliques, é necessário ter uma ID de correlação que relacione esses dois eventos distintos. O Azure Search fornece uma ID de Pesquisa quando você a solicita com um cabeçalho:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<ServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**Etapa 3: Registrar eventos de pesquisa**

Sempre que uma solicitação de pesquisa é emitida por um usuário, você deve registrá-la como um evento de pesquisa com o esquema a seguir em um evento personalizado do Application Insights:

**ServiceName**: (cadeia de caracteres) nome do serviço de pesquisa **SearchId**: (guid) identificador exclusivo da consulta de pesquisa (aparece na resposta da pesquisa) **IndexName**: (cadeia de caracteres) índice do serviço de pesquisa a ser consultado **QueryTerms**: (cadeia de caracteres) termos de pesquisa inseridos pelo usuário **ResultCount**: (int) número de documentos retornados (aparece na resposta da pesquisa) **ScoringProfile**: (cadeia de caracteres) nome do perfil de pontuação usado, se houver algum

> [!NOTE]
> Solicite a contagem de consultas geradas pelo usuário adicionando $count=true à consulta de pesquisa. Confira mais informações [aqui](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Lembre-se de registrar apenas as consultas de pesquisa geradas pelos usuários.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**Etapa 4: Registrar eventos de clique**

Sempre que um usuário clica em um documento, esse é um sinal que deve ser registrado para fins de análise de pesquisa. Use os eventos personalizados do Application Insights para registrar esses eventos com o esquema a seguir:

**ServiceName**: (cadeia de caracteres) nome do serviço de pesquisa **SearchId**: (guid) identificador exclusivo da consulta de pesquisa relacionada **DocId**: (cadeia de caracteres) identificador do documento **Position**: (int) classificação do documento na página de resultados da pesquisa

> [!NOTE]
> Position refere-se à ordem cardinal em seu aplicativo. Você tem a liberdade para definir esse número, desde que seja sempre o mesmo, para permitir a comparação.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3 - Analisar no Power BI

Depois de instrumentar seu aplicativo e verificar se ele está conectado corretamente ao Application Insights, use um modelo predefinido criado pelo Azure Search para a área de trabalho do Power BI. 

O Azure Search fornece um [Pacote de Conteúdo do Power BI](https://app.powerbi.com/getdata/services/azure-search) para monitoramento, para que seja possível analisar os dados do log. O pacote de conteúdo inclui gráficos e tabelas predefinidos úteis para analisar os dados adicionais capturados para análise do tráfego de pesquisa. Para obter mais informações, consulte a [página de ajuda do pacote de conteúdo](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. No painel de navegação esquerdo do painel do Azure Search, em **Configurações**, clique em **Análise do tráfego de pesquisa**.

2. Na página **Análise de tráfego de pesquisa**, na etapa 3, clique em **Obter o Power BI Desktop** para instalar o Power BI.

   ![Obter relatórios do Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Obter relatórios do Power BI")

2. Na mesma página, clique em **Fazer o download do relatório do Power BI**.

3. O relatório é aberto no Power BI Desktop, e você é solicitado a se conectar ao Application Insights. Você pode encontrar essas informações nas páginas do portal do Azure correspondentes ao seu recurso do Application Insights.

   ![Conectar-se ao Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Conectar-se ao Application Insights")

4. Clique em **Carregar**.

O relatório contém gráficos e tabelas que ajudam você a tomar decisões mais inteligentes a fim de melhorar o desempenho e a relevância da pesquisa.

As métricas incluem os seguintes itens:

* Taxa de cliques (CTR): proporção de usuários que clicam em um documento específico com relação ao número total de pesquisas.
* Pesquisa sem cliques: termos das principais consultas que não registraram nenhum clique
* Documentos mais clicados: documentos mais clicados por ID nas últimas 24 horas, sete dias e 30 dias.
* Pares de documento/termo populares: termos que resultam no mesmo documento clicado, ordenados por cliques.
* Hora do clique: cliques classificados por tempo desde a consulta de pesquisa

A captura de tela a seguir mostra os relatórios e gráficos internos para análise de tráfego de pesquisa.

![Painel do Power BI para o Azure Search](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "Painel do Power BI para o Azure Search")

## <a name="next-steps"></a>Próximas etapas
Instrumente seu aplicativo de pesquisa para obter dados informativos e avançados sobre o serviço de pesquisa.

Você pode encontrar mais informações sobre o [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) e visitar a [página de preços](https://azure.microsoft.com/pricing/details/application-insights/) para saber mais sobre suas diferentes camadas de serviço.

Saiba mais sobre como criar relatórios incríveis. Confira [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) para obter detalhes

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
