---
title: "Application Insights para aplicativos Web Java que já estão em modo dinâmico"
description: "Comece a monitorar um aplicativo Web que já esteja em execução no servidor"
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: bwren
ms.openlocfilehash: a2731e3e44f8f3d104d8abc7dbe71fe3a4c3a690
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Application Insights para aplicativos Web Java que já estão em modo dinâmico


Se tiver um aplicativo Web já em execução no servidor J2EE, você poderá começar a monitorá-lo com o [Application Insights](app-insights-overview.md) sem a necessidade de fazer alterações de código ou recompilar o projeto. Com essa opção, você obtém informações sobre solicitações HTTP enviadas ao seu servidor, exceções sem tratamento e contadores de desempenho.

Você precisará de uma assinatura do [Microsoft Azure](https://azure.com).

> [!NOTE]
> O procedimento nesta página adiciona o SDK ao seu aplicativo Web no tempo de execução. Essa instrumentação de tempo de execução será útil se você não quiser atualizar nem recompilar o código-fonte. Mas, se possível, recomendamos que você [adicione o SDK para o código-fonte](app-insights-java-get-started.md) em vez disso. Isso dá mais opções, como escrever o código para rastrear a atividade do usuário.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Obter uma chave de instrumentação do Application Insights
1. Entre no [Portal do Microsoft Azure](https://portal.azure.com)
2. Crie um novo recurso do Application Insights e defina o tipo de aplicativo para aplicativo Web Java.
   
    ![Preencha um nome, escolha o aplicativo Java da Web e clique em Criar](./media/app-insights-java-live/02-create.png)

    O recurso é criado em alguns segundos.

4. Abr o novo recurso e obtenha sua chave de instrumentação. Você precisará colar essa chave no código de seu projeto em breve.
   
    ![Na visão geral do novo recurso, clique em Propriedades e copie a chave de instrumentação](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Baixar o SDK
1. Baixe o [SDK do Application Insights para Java](https://aka.ms/aijavasdk). 
2. No servidor, extraia o conteúdo do SDK para o diretório por meio do qual os binários do projeto são carregados. Se você estiver usando o Tomcat, esse diretório normalmente estará em `webapps/<your_app_name>/WEB-INF/lib`

Observe que você precisa repetir isso em cada instância do servidor e para cada aplicativo.

## <a name="3-add-an-application-insights-xml-file"></a>3. Adicione um arquivo xml do Application Insights
Crie o ApplicationInsights.xml na pasta em que você adicionou o SDK. Coloque dentro dela o XML a seguir.

Substitua a chave de instrumentação que você obteve no Portal do Azure.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

* A chave de instrumentação é enviada junto com todos os itens de telemetria e orienta o Application Insights a exibi-los em seu recurso.
* O componente de solicitação HTTP é opcional. Ele envia automaticamente a telemetria sobre solicitações e tempos de resposta para o portal.
* A correlação de eventos é uma adição ao componente de solicitação HTTP. Ele atribui um identificador a cada solicitação recebida pelo servidor e adiciona esse identificador como uma propriedade para cada item de telemetria, como a propriedade “Operation.Id”. Ele permite que você correlacione a telemetria associada com cada solicitação, definindo um filtro na [pesquisa de diagnóstico](app-insights-diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. Adicionar um filtro HTTP
Localize e abra o arquivo web.xml em seu projeto, então mescle o trecho de código a seguir sob o nó do aplicativo Web, no qual seus filtros de aplicativo estão configurados.

Para obter os resultados mais precisos, o filtro deve ser mapeado antes de todos os outros filtros.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## <a name="5-check-firewall-exceptions"></a>5. Verificar exceções do firewall
Talvez você precise [definir exceções de firewall para enviar dados de saída](app-insights-ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. Reiniciar seu aplicativo Web
## <a name="7-view-your-telemetry-in-application-insights"></a>7. Exibir sua telemetria no Application Insights
Retorne para seu recurso do Application Insights no [Portal do Microsoft Azure](https://portal.azure.com).

A telemetria de solicitações HTTP é exibida na folha de visão geral. (Se não estiverem lá, aguarde alguns segundos e, em seguida, clique em Atualizar.)

![dados de exemplo](./media/app-insights-java-live/5-results.png)

Clique em qualquer gráfico para ver métricas mais detalhadas. 

![](./media/app-insights-java-live/6-barchart.png)

Ao exibir as propriedades de uma solicitação, você pode ver os eventos de telemetria associados a ela, como solicitações e exceções.

![](./media/app-insights-java-live/7-instance.png)

[Saiba mais sobre métricas.](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>Próximas etapas
* [Adicione telemetria às suas páginas da Web](app-insights-javascript.md) para monitorar exibições de página e métricas de usuário.
* [Configure os testes da Web](app-insights-monitor-web-app-availability.md) para certificar-se de manter seu aplicativo operante e responsivo.
* [Capturar rastreamentos de log](app-insights-java-trace-logs.md)
* [Pesquise eventos e logs](app-insights-diagnostic-search.md) para ajudar a diagnosticar problemas.

