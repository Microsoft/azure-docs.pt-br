---
title: Amostragem de telemetria no Azure Application Insights | Microsoft Docs
description: Como manter o volume de telemetria sob controle.
services: application-insights
documentationcenter: windows
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.author: cithomas
ms.openlocfilehash: b35b0c66c29805d9cd7ecd00ffaad4fc1cfe253b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653783"
---
# <a name="sampling-in-application-insights"></a>Amostragem no Application Insights

A amostragem é um recurso do [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). É a maneira recomendada para reduzir o tráfego de telemetria e de armazenamento, preservando uma análise estatística correta de dados do aplicativo. O filtro seleciona itens relacionados para que você possa navegar entre os itens quando você estiver realizando investigações de diagnóstico.
Quando as contagens de métrica são apresentadas no portal, elas são normalizadas novamente para levar em consideração a amostragem. Fazer isso minimiza qualquer efeito sobre as estatísticas.

A amostragem reduz os custos de tráfego e de dados e ajuda a evitar a limitação.

## <a name="in-brief"></a>Em resumo:

* A amostragem retém 1 em registros *n* e descarta o resto. Por exemplo, ela pode reter uma em cinco eventos, uma taxa de amostragem de 20%. 
* Amostragem adaptável está habilitada por padrão em todas as a versão mais recente do ASP.NET e ASP.NET Core Software Development Kits (SDKs).
* Você também pode definir a amostragem manualmente. Isso pode ser configurado no portal sobre o *página uso e custos estimados*, no SDK do ASP.NET no arquivo applicationinsights. config, no SDK do ASP.NET Core por meio de código ou no SDK do Java no applicationinsights. XML do arquivo.
* Se você registrar eventos personalizados e necessidade de garantir que um conjunto de eventos é retido ou descartado em conjunto, os eventos devem ter o mesmo valor de OperationId.
* O divisor de amostragem *n`itemCount` é relatado em cada registro na propriedade* , que, na Pesquisa, aparece sob o nome amigável "contagem de solicitação" ou "contagem de eventos". `itemCount==1`Quando amostragem não está em operação.
* Se você escrever consultas de Análise, deverá [levar em conta a amostragem](../../azure-monitor/log-query/aggregations.md). Em particular, em vez de simplesmente contar registros, você deve usar `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Tipos de amostragem

Há três módulos de amostragem alternativos:

* **Amostragem adaptável** ajusta automaticamente o volume de telemetria enviado do SDK em seu aplicativo ASP.NET/ASP.NET Core. Essa é a amostragem padrão do SDK Web do ASP.NET v 2.0.0-Beta3 em diante e 2.2.0-beta1 de v aspnetcore SDK em diante.  A amostragem adaptável está disponível atualmente somente para telemetria ASP.NET do lado do servidor.

* **Amostragem de taxa fixa** reduz o volume de telemetria enviado do seu servidor ASP.NET ou ASP.NET Core ou Java e de navegadores dos usuários. Você define a taxa. O cliente e o servidor sincronizarão suas amostragens para que, na Pesquisa, você possa navegar entre exibições de página e solicitações relacionadas.

* **Amostragem de ingestão** funciona no portal do Azure. Ela descarta parte da telemetria que chega em seu aplicativo, na taxa de amostragem que você definir. Ela não reduz o tráfego de telemetria enviado do seu aplicativo, mas ajuda você a se manter em sua cota mensal. A principal vantagem da amostragem de ingestão é que você pode definir a taxa de amostragem sem reimplantar o aplicativo. Amostragem de ingestão funciona uniformemente para todos os servidores e clientes.

Se a amostragem de taxa Adaptável ou Fixa estiver em operação, a amostragem de Ingestão estará desabilitada.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Amostragem adaptável em seus aplicativos de Web Core ASP.NET/ASP.NET

Amostragem adaptável está disponível para o SDK do Application Insights para ASP.NET v 2.0.0-Beta3 e posterior, o SDK de aspnetcore v 2.2.0-beta1 e versões posteriores e é habilitado por padrão.

A amostragem adaptável afeta o volume de telemetria enviado do seu aplicativo de servidor Web para o end-point do serviço Application Insights. O volume é ajustado automaticamente para se manter em uma taxa máxima especificada de tráfego e é controlado por meio da configuração `MaxTelemetryItemsPerSecond`. Se o aplicativo produz uma quantidade pequena de telemetria, como durante a depuração ou devido ao baixo uso, os itens não seja amostradas desde que o volume estiver abaixo de `MaxTelemetryItemsPerSecond`. Como o volume de telemetria aumenta, a taxa de amostragem é ajustada para atingir o volume de destino.

Para atingir o volume de destino, parte da telemetria gerada é descartada. Assim como os outros tipos de amostragem, o algoritmo retém os itens de telemetria relacionados. Por exemplo, quando você estiver analisando a telemetria na Pesquisa, poderá localizar a solicitação relacionada a uma exceção específica.

As contagens de métrica, como a taxa de solicitações e a taxa de exceções são ajustadas para compensar a taxa de amostragem, para que mostrem valores aproximadamente corretos no Gerenciador de Métricas.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Configure a amostragem adaptável para aplicativos ASP.NET

[Saiba mais](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) sobre como configurar a amostragem adaptável para aplicativos do ASP.NET Core. 

Em [ApplicationInsights.config`AdaptiveSamplingTelemetryProcessor`, é possível ajustar diversos parâmetros no nó ](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Os números mostrados são os valores padrão:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    A taxa de destino desejada pelo algoritmo adaptável **em cada host de servidor**. Se o seu aplicativo Web for executado em muitos hosts, reduza esse valor para permanecer dentro de sua taxa de destino de tráfego no portal do Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    O intervalo no qual a taxa atual de telemetria é reavaliada. Avaliação é executada como uma média móvel. Talvez você queira reduzir esse intervalo se a sua telemetria estiver sujeita a picos repentinos.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Quando o valor da porcentagem de amostragem muda, quanto tempo depois temos permissão para diminuir a porcentagem de amostragem novamente a fim de capturar menos dados.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Quando o valor da porcentagem de amostragem muda, quanto tempo depois temos permissão para aumentar a porcentagem de amostragem novamente a fim de capturar mais dados.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    À medida que a porcentagem de amostragem varia, qual é o valor mínimo que podemos definir.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    À medida que a porcentagem de amostragem varia, qual é o valor máximo que podemos definir.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    No cálculo da média móvel, o peso atribuído ao valor mais recente. Use um valor menor ou igual a 1. Valores menores tornam o algoritmo menos reativo a mudanças repentinas.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    O valor atribuído quando o aplicativo acabou de ser iniciado. Não reduza o valor durante a depuração.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Uma lista delimitada por ponto e vírgula dos tipos que você não deseja usar como amostra. Tipos reconhecidos são: Dependência, Evento, Exceção, 	Exibição da Página, Solicitação, Rastreamento. Todas as instâncias dos tipos especificados são transmitidas; os tipos que não especificados são usados como amostra.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Uma lista delimitada por ponto e vírgula dos tipos que você deseja usar como amostra. Tipos reconhecidos são: Dependência, Evento, Exceção, 	Exibição de Página, Solicitação, Rastreamento. Os tipos especificados são usados como amostra; todas as instâncias dos outros tipos serão sempre transmitidas.


**Para desativar** adaptável de amostragem, remova o nó AdaptiveSamplingTelemetryProcessor do applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativa: configurar amostragem adaptável no código

Em vez de definir o parâmetro de amostragem no arquivo .config, você pode definir esses valores de forma programática.

1. Remova todo o `AdaptiveSamplingTelemetryProcessor` nó (s) do arquivo. config.
2. Use o trecho a seguir para configurar a amostragem adaptável.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Saiba mais sobre os processadores de telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Você também pode ajustar a taxa de amostragem para cada tipo de telemetria individualmente, ou pode até mesmo excluir determinados tipos de amostra em todos os. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Configure a amostragem adaptável para aplicativos ASP.NET Core.

Não há nenhum `ApplicationInsights.Config` para aplicativos do ASP.NET Core, portanto, cada configuração é feita por meio de código.
A amostragem adaptável está habilitada por padrão para todos os aplicativos ASP.NET Core. É possível desabilitar ou personalizar o comportamento de amostragem.

### <a name="turning-off-adaptive-sampling"></a>Desligar a amostragem adaptável

O recurso de amostragem padrão pode ser desabilitado durante a adição de serviço do Application Insights, no método ```ConfigureServices```, usando ```ApplicationInsightsServiceOptions``` dentro de `Startup.cs` arquivo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
    //...
}
```

O código acima desabilitará o recurso de amostragem. Siga as etapas abaixo para adicionar a amostragem com mais opções de personalização.

### <a name="configure-sampling-settings"></a>Definir configurações de amostragem

Use os métodos de extensão de ```TelemetryProcessorChainBuilder``` conforme mostrado abaixo para personalizar o comportamento de amostragem.

> [!IMPORTANT]
> Se usar esse método para configurar a amostragem, não deixe de usar a configuração aiOptions.EnableAdaptiveSampling = false; com AddApplicationInsightsTelemetry().

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

    var builder = configuration .TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**Se usar o método acima para configurar a amostragem, certifique-se de usar ```aiOptions.EnableAdaptiveSampling = false;``` configurações com AddApplicationInsightsTelemetry().**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites"></a>Amostragem de taxa fixa para sites do ASP.NET, ASP.NET Core e Java

A amostragem de taxa fixa reduz o tráfego enviado do seu servidor Web e de navegadores da Web. Ao contrário da amostragem adaptável, ela reduz a telemetria a uma taxa fixa decidida por você. Ela também sincroniza a amostragem de servidor e de cliente para que os itens relacionados sejam mantidos, por exemplo, quando você examinar um modo de exibição de página na Pesquisa, poderá localizar a solicitação relacionada.

Assim como outras técnicas de amostragem, isso também retém os itens relacionados. Para cada evento de solicitação HTTP, a solicitação e seus eventos relacionados são descartados ou transmitidos juntos.

No Metrics Explorer, as taxas como as contagens de solicitações e de exceções são multiplicadas por um fator para compensar a taxa de amostragem, para que elas sejam aproximadamente corretas.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Configure a amostragem de taxa fixa no ASP.NET

1. **Desabilitar amostragem adaptável**: Em [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), remova ou comente o nó `AdaptiveSamplingTelemetryProcessor`.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Habilitar o módulo de amostragem de taxa fixa.** Adicione esse snippet de código a [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternativa: habilite a amostragem de taxa fixa no código do servidor
    
    Em vez de definir o parâmetro de amostragem no arquivo .config, você pode definir esses valores de forma programática. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([Saiba mais sobre os processadores de telemetria](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Configure a amostragem de taxa fixa no ASP.NET Core

1. **Desabilitar amostragem adaptável**:  Podem ser feitas alterações no método ```ConfigureServices```, usando ```ApplicationInsightsServiceOptions```:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    //...
    }
    ```

2. **Habilitar o módulo de amostragem de taxa fixa.** Podem ser feitas alterações no método ```Configure``` conforme mostrado no trecho abaixo de:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Configure a amostragem de taxa fixa no JAVA ###

1. Fazer o download e configurar seu aplicativo da web com a versão mais recente [SDK de java do Application Insights](../../azure-monitor/app/java-get-started.md)

2. **Habilita o módulo de taxa fixa de amostragem** adicionando o snippet a seguir ao arquivo ApplicationInsights.xml.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        <TelemetryProcessors/>
    ```

3. Você pode incluir ou excluir tipos específicos de telemetria da Amostragem usando as seguintes marcas dentro da marca do processador "FixedRateSamplingTelemetryProcessor"
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

Os tipos de telemetria que podem ser incluídos ou excluídos da amostragem são: Dependência, evento, exceção, PageView, solicitação e rastreamento.

> [!NOTE]
> Para o percentual de amostragem, escolha um percentual que esteja próximo a 100/N, em que N é um inteiro.  Atualmente, a amostragem não dá suporte a outros valores.
> 
> 

<a name="other-web-pages"></a>



## <a name="ingestion-sampling"></a>amostragem de ingestão

Essa forma de amostragem opera no ponto em que a telemetria de seu servidor Web, seus navegadores e seus dispositivos atinge o ponto de extremidade do serviço Application Insights. Embora ela não reduza o tráfego de telemetria enviado do seu aplicativo, reduz a quantidade processada e mantida (e cobrada) pelo Application Insights.

Use esse tipo de amostragem se seu aplicativo geralmente ultrapassar a cota mensal e se você não tiver a opção de usar os tipos de amostragem baseados em SDK. 

Defina a taxa de amostragem na página Uso e custos estimados:

![Na folha Visão Geral do aplicativo, clique em Configurações, Cota, Amostras, escolha uma taxa de amostragem e clique em Atualizar.](./media/sampling/04.png)

Assim como outros tipos de amostragem, o algoritmo retém os itens relacionados de telemetria. Por exemplo, quando você estiver analisando a telemetria na Pesquisa, poderá localizar a solicitação relacionada a uma exceção específica. As contagens de métrica como a taxa de solicitação e a taxa de exceções são mantidas corretamente.

Os pontos de dados que são descartados pela amostragem não estão disponíveis em nenhum recurso do Application Insights como [Exportação Contínua](../../azure-monitor/app/export-telemetry.md).

A amostragem de ingestão não funciona enquanto a amostragem adaptável ou de taxa fixa com base no SDK estiver em operação. Amostragem adaptável está habilitada por padrão quando ASP.NET/ASP.NET Core SDK está habilitado no Visual Studio ou habilitado nas extensões de aplicativo Web do Azure ou usando o Monitor de Status e a amostragem de ingestão está desabilitada. Se a taxa de amostragem no SDK for menor que 100% (ou seja itens que estão sendo amostradas) a taxa de amostragem de ingestão que você definir será ignorada.

> [!WARNING]
> O valor mostrado no bloco indica o valor definido para amostragem de ingestão. Ele não representará a taxa de amostragem real se a amostragem do SDK estiver em operação.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>Amostragem para áginas da Web com JavaScript
Você pode configurar as páginas da Web para amostragem de taxa fixa de qualquer servidor. 

Ao [configurar as páginas da Web para o Application Insights](../../azure-monitor/app/javascript.md), modifique o snippet de código JavaScript que você receber do portal do Application Insights. (Em aplicativos ASP.NET, o snippet de código geralmente vai em _Layout.cshtml.)  Insira uma linha como `samplingPercentage: 10,` antes da chave de instrumentação:

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

Para o percentual de amostragem, escolha um percentual que esteja próximo a 100/N, em que N é um inteiro.  Atualmente, a amostragem não dá suporte a outros valores.

Se você habilitar também a amostragem de taxa fixa no servidor, o cliente e o servidor serão sincronizados para que, na Pesquisa, você possa navegar entre exibições de página e solicitações relacionadas.

## <a name="when-to-use-sampling"></a>Quando usar a amostragem?

Amostragem adaptável é habilitada automaticamente em .NET e os SDKs do .NET Core mais recentes. Independentemente de qual versão do SDK você usar, você pode habilitar a amostragem de ingestão para permitir que o Application Insights realize uma amostragem dos dados coletados.

Por padrão a nenhuma amostragem está habilitada na SDK de Java. Atualmente, suporta apenas amostragem de taxa fixa. Não há suporte para amostragem adaptável no SDK de Java.

Em geral, para a maioria dos aplicativos de pequeno e médio porte, você não precisa de amostragem. As informações de diagnóstico mais úteis e as estatísticas mais precisas são obtidas por meio da coleta de dados em todas as atividades de usuário. 

As principais vantagens da amostragem são:

* O serviço Application Insights remove (“limita”) os pontos de dados quando o aplicativo envia uma taxa muito alta de telemetria em um curto intervalo de tempo. 
* Permanecer dentro da [cota](../../azure-monitor/app/pricing.md) de pontos de dados para o seu tipo de preço. 
* Para reduzir o tráfego de rede da coleção de telemetria. 

### <a name="which-type-of-sampling-should-i-use"></a>Que tipo de amostragem eu devo usar?

**Use a amostragem de ingestão se:**

* Você geralmente ultrapassa sua cota mensal de telemetria.
* Você estiver usando uma versão do SDK que não oferece suporte à amostragem. Por exemplo, o ASP.NET anteriores à 2.
* Você está obtendo muita telemetria de navegadores da web dos usuários.

**Use a amostragem de taxa fixa se:**

* Você estiver usando o SDK do Application Insights para os serviços Web do ASP.NET versão 2.0.0 ou posterior ou Java SDK v2.0.1 ou postrior, e
* Você quiser uma amostragem sincronizada entre cliente e servidor, para que quando estiver investigando eventos na [Pesquisa](../../azure-monitor/app/diagnostic-search.md), você possa navegar entre os eventos relacionados no cliente e no servidor, por exemplo, exibições de página e solicitações http.
* Você tiver certeza sobre a porcentagem de amostragem apropriada para seu aplicativo. Ela deve ser alta o suficiente para obter métricas precisas, mas abaixo da taxa que excede sua cota de preços e limitações. 

**Use a amostragem adaptável:**

Se as condições para usar outras formas de amostragem não se aplicarem, é recomendável a amostragem adaptável. Essa configuração é habilitada por padrão no SDK do servidor ASP.NET/ASP.NET Core. Ela não reduzirá o tráfego até que uma determinada taxa mínima seja atingida, portanto, os sites de baixo uso não serão afetados.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Como saber se a amostragem está em operação?

Para descobrir a taxa de amostragem real, independentemente de onde ela tiver sido aplicada, use uma [consulta do Analytics](../../azure-monitor/app/analytics.md) como esta:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Se RetainedPercentage para qualquer tipo é menor que 100, esse item está sendo amostrado.

**Application Insights não oferece amostragem de sessão, métricas e contadores de desempenho de tipos de telemetria em qualquer técnicas de amostragem descritos acima. Esses tipos são sempre excluídos da amostragem como redução na precisão pode ser muito ruim para esses tipos de telemetria**

## <a name="how-does-sampling-work"></a>Como funciona a amostragem?

Recurso da taxa de amostragem fixa do SDK em versões do ASP.NET de 2.0.0 e Java SDK versão 2.0.1 e em diante. As amostragens adaptáveis são um recurso do SDK nas versões do ASP.NET a partir da 2.0.0. A amostragem de ingestão é um recurso do serviço Application Insights e poderá estar em operação se o SDK não estiver executando a amostragem.

O algoritmo de amostragem decide quais itens de telemetria descartar e quais manter (se estiver no SDK ou no serviço Application Insights). A decisão de amostragem baseia-se em várias regras que visam preservar todos os pontos de dados inter-relacionados intactos, mantendo uma experiência de diagnóstico no Application Insights que seja acionável e confiável mesmo com um conjunto reduzido de dados. Por exemplo, se para uma solicitação com falha seu aplicativo enviar itens de telemetria adicionais (como exceção e rastreamentos registrados desta solicitação), a amostragem não dividirá essa solicitação e outra telemetria. Ela mantém ou remove todos juntos. Como resultado, quando você examinar os detalhes da solicitação no Application Insights, é possível ver sempre a solicitação junto com seus itens de telemetria associados. 

A decisão de amostragem baseia-se na ID de operação da solicitação, o que significa que todos os itens de telemetria que pertencem a uma determinada operação é preservada ou removida. Para os itens de telemetria que não têm operação ID conjunto (por exemplo, itens telemetria relatados dos threads assíncronos sem contexto http) amostragem apenas captura um percentual de itens de telemetria de cada tipo. Antes de 2.5.0-beta2 do SDK do .NET e 2.2.0-beta3 do SDK do ASP.NET Core, a decisão de amostragem foi baseada no hash da ID de usuário para aplicativos que definem o "usuário" (ou seja, aplicativos web mais típicos). Para os tipos de aplicativos que não definem usuários (como serviços da web) a decisão de amostragem foi com base na ID de operação da solicitação.

Ao apresentar a telemetria de volta para você, o serviço Application Insights ajusta as métricas pelo mesmo percentual de amostragem usado no momento da coleta, para compensar os pontos de dados ausentes. Portanto, ao examinar a telemetria no Application Insights, os usuários veem aproximações estatisticamente corretas que estão muito próximas aos números reais.

A precisão da aproximação depende principalmente do percentual de amostragem configurado. Além disso, a precisão aumenta para os aplicativos que lidam com um grande volume de solicitações geralmente semelhantes de uma grande quantidade de usuários. Por outro lado, para aplicativos que não funcionam com uma carga significativa, a amostragem não é necessária, pois esses aplicativos geralmente podem enviar toda a sua telemetria, ao mesmo tempo que permanecem dentro da cota, sem causar perda de dados da limitação. 

> [!WARNING]
> O Application Insights não oferece amostragem de métricas e tipos de telemetria de sessões. A falta de precisão pode ser muito ruim para esses tipos de telemetria.
> 

### <a name="adaptive-sampling"></a>amostragem adaptável

A amostragem adaptável adiciona um componente que monitora a taxa atual de transmissão do SDK e ajusta a porcentagem de amostragem a fim de tentar permanecer dentro da taxa máxima desejada. O ajuste é recalculado em intervalos regulares e tem base em uma média móvel da taxa de transmissão de saída.

## <a name="sampling-and-the-javascript-sdk"></a>Amostragem e o SDK do JavaScript

O SDK do lado do cliente (JavaScript) participa da amostragem de taxa fixa em conjunto com o SDK do lado do servidor. As páginas instrumentadas enviarão apenas a telemetria do lado do cliente dos mesmos usuários para os quais o lado do servidor tomou sua decisão de “amostragem”. Essa lógica é projetada para manter a integridade da sessão do usuário nos lados do cliente e do servidor. Como resultado, em qualquer item de telemetria específico no Application Insights é possível encontrar todos os outros itens de telemetria para esse usuário ou sessão. 

*Minha telemetria do lado do cliente e do servidor não mostra exemplos coordenados como descrito acima.*

* Verifique se você habilitou a amostragem de taxa fixa tanto no servidor quanto no cliente.
* Certifique-se de que a versão do SDK é 2.0 ou superior.
* Verifique se você definiu o mesmo percentual de amostragem no cliente e no servidor.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

*O que é o comportamento de amostragem padrão no ASP.NET e o SDK do ASP.NET Core?*

* Se você estiver usando uma das versões mais recentes do SDK acima, a amostragem adaptável está habilitada por padrão com cinco itens de telemetria por segundo.
  Há 2 AdaptiveSamplingTelemetryProcessors adicionados por padrão e uma inclui o tipo de evento em amostragem e o outro exclui o tipo de evento de amostragem. Essa configuração significa que o SDK tentará limitar itens de telemetria cinco itens de telemetria de tipos de eventos e cinco itens de telemetria de todos os outros tipos combinados, garantindo assim que os eventos são amostrados separadamente de outros tipos de telemetria. Eventos geralmente são usados para telemetria de negócios e provavelmente não serão afetados por volumes de telemetria de diagnóstico.
  
  A seguir mostra o arquivo applicationinsights. config do padrão gerado. Conforme descrito, há dois nós separados de AdaptiveSamplingTelemetryProcessor adicionado, uma exclusão de tipos de eventos e outro incluí-lo. No ASP.NET Core, exata mesmo comportamento padrão está habilitado no código. Use os exemplos na seção anterior do documento para alterar esse comportamento padrão.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*Telemetria pode ser exemplificada mais de uma vez?*

* Não. SamplingTelemetryProcessors Ignorar itens da amostragem considerações, se o item já é obtida como amostra. O mesmo é verdadeiro para amostragem de ingestão como bem, que aplicará a amostragem a esses itens já obtida como amostras no SDK em si.'

*Por que a amostragem não se trata apenas de “coletar X% de cada tipo de telemetria”?*

* Embora essa abordagem de amostragem forneceria um alto nível de precisão em aproximações de métrica, ela interromperia a capacidade de correlacionar dados de diagnóstico por usuário, sessão e solicitação, que é essencial para o diagnóstico. Portanto, a amostragem funciona melhor com a lógica “coletar todos os itens de telemetria para X% de usuários do aplicativo” ou “coletar toda a telemetria para X% das solicitações do aplicativo”. Para os itens de telemetria não associados às solicitações (como o processamento assíncrono em segundo plano), o fallback é "coletar X % de todos os itens para cada tipo de telemetria." 

*O percentual de amostragem pode ser alterado com o tempo?*

* Sim, a amostragem adaptável altera gradualmente a porcentagem de amostragem com base no volume atualmente observado da telemetria.

*Se eu usar a amostragem de taxa fixa, como saber qual percentual de amostragem funcionará melhor para o meu aplicativo?*

* Uma maneira é iniciar com a amostragem adaptável, descobrir qual taxa se adequa (consulte a pergunta anterior) e, em seguida, alternar para a amostragem de taxa fixa usando essa taxa. 
  
    Caso contrário, é preciso adivinhar. Analise o seu uso atual da telemetria na em Application Insights, observe qualquer limitação que esteja ocorrendo e estime o volume da telemetria coletada. Essas três entradas, junto com seu tipo de preço selecionado, sugere o quanto você talvez queira reduzir o volume da telemetria coletada. No entanto, um aumento no número de usuários ou alguma outra mudança no volume de telemetria pode invalidar sua estimativa.

*O que acontece se eu configurar o percentual de amostragem com um valor muito baixo?*

* Um percentual de amostragem excessivamente baixo (amostragem superagressiva) reduz a precisão das aproximações quando o Application Insights tenta compensar a visualização dos dados para a redução do volume de dados. Além disso, a experiência de diagnóstico pode ser afetada negativamente, já que algumas das solicitações lentas ou raramente com falhas podem ser amostradas.

*O que acontece se eu configurar o percentual de amostragem com um valor muito alto?*

* Configurar um percentual de amostragem muito alto (não agressivo o suficiente) resultará em uma redução insuficiente no volume da telemetria coletada. Ainda pode ocorrer perda de dados de telemetria relacionada à limitação, e o custo do uso do Application Insights pode ser maior do que o planejado devido a encargos excedentes.

*Em quais plataformas posso usar a amostragem?*

* A amostragem de ingestão pode ocorrer automaticamente para qualquer telemetria acima de um determinado volume, se o SDK não estiver executando a amostragem. Essa configuração funcionaria, por exemplo, se você estiver usando uma versão mais antiga do ASP.NET SDK ou versão anterior do Java SDK(1.0.10 or before).
* Se você estiver usando o SDK do ASP.NET versão 2.0.0 e acima ou o SDK do ASP.NET CORE versão 2.2.0 e acima (hospedado no Azure ou em seu próprio servidor), você obtém amostragem adaptável por padrão, mas você pode alternar para a taxa fixa, conforme descrito acima. Com a amostragem de taxa fixa, o SDK do navegador sincroniza automaticamente os eventos relacionados ao exemplo. 
* Se você estiver usando o SDK de Java versão 2.0.1 ou superior, você pode configurar applicationinsights. XML para ativar a amostragem de taxa fixa. A amostragem é desativada por padrão. Com a amostragem de taxa fixa, o SDK do navegador sincroniza automaticamente os eventos relacionados ao exemplo.

*Há alguns eventos raros que sempre desejo ver. Como posso fazê-los passar pelo módulo de amostragem?*

* A melhor maneira de conseguir isso é escrever um personalizado [TelemetryProcessor](../../azure-monitor/app/api-filtering-sampling.md#filtering), que define o `SamplingPercentage` a 100 no item de telemetria desejado retido, conforme mostrado abaixo. Isso garante que todas as técnicas de amostragem irão ignorar este item de quaisquer considerações de amostragem.

```csharp
    if(somecondition)
    {
        ((ISupportSampling)item).SamplingPercentage = 100;
    }
```

## <a name="next-steps"></a>Próximas etapas

* [filtragem](../../azure-monitor/app/api-filtering-sampling.md) pode fornecer um controle mais restrito do que o SDK envia.