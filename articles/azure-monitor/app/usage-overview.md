---
title: Usar análise com o Azure Application Insights | Microsoft Docs
description: Compreenda seus usuários e o que eles fazem com o seu aplicativo.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 633d35ec16f5eb9de664421c38cd4c824dc240cf
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233841"
---
# <a name="usage-analysis-with-application-insights"></a>Análise de uso com o Application Insights

Quais recursos do seu aplicativo Web ou móvel são mais populares? Os usuários atingem as metas deles com seu aplicativo? Eles deixam o aplicativo em determinados pontos? E retornam posteriormente?  O [Application Insights do Azure](./app-insights-overview.md) ajuda você a ter insights profundos sobre como as pessoas usam seu aplicativo. Sempre que atualiza seu aplicativo, você pode avaliar como ele funciona para os usuários. Com esse conhecimento, você pode tomar decisões baseadas em dados sobre os próximos ciclos de desenvolvimento.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Enviar telemetria de seu aplicativo

A melhor experiência é obtida, instalando o Application Insights tanto no código do servidor de aplicativos como nas suas páginas da Web. Os componentes do servidor e cliente do aplicativo retornam telemetria aoo portal Azure para análise.

1. **Código do servidor:** Instale o módulo apropriado para o [ASP.NET](./asp-net.md), [Azure](./app-insights-overview.md), [Java](./java-get-started.md), [Node.js](./nodejs.md) ou [outro](./platforms.md) aplicativo.

    * *Não quer instalar o código do servidor? Apenas [crie um recurso do Azure Application Insights](./create-new-resource.md).*

2. **URL da página na web:** Adicione o seguinte script à página da Web antes do fechamento ``</head>``. Substitua a chave de instrumentação pelo valor apropriado para seu recurso do Application Insights:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Para saber sobre configurações mais avançadas para o monitoramento de sites, confira o [artigo de referência do SDK de JavaScript](./javascript.md).

3. **Código do aplicativo móvel:** Use o SDK do App Center para coletar eventos do seu aplicativo e, em seguida, envie cópias desses eventos ao Application Insights para análise [seguindo este guia](../learn/mobile-center-quickstart.md).

4. **Obter telemetria:** Execute seu projeto no modo de depuração por alguns minutos e, em seguida, procure resultados na folha Visão Geral em Application Insights.

    Publique seu aplicativo para monitorar o desempenho do aplicativo e descobrir o que seus usuários estão fazendo com o aplicativo.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Incluir a ID de usuário e de sessão em sua telemetria
Para controlar os usuários ao longo do tempo, o Application Insights requer um modo para identificá-los. A ferramenta Eventos é a única ferramenta de Uso que não requer uma ID de usuário ou uma ID de sessão.

Comece o envio de IDs de usuário e de sessão usando [este processo](./usage-send-user-context.md).

## <a name="explore-usage-demographics-and-statistics"></a>Explore as estatísticas e dados demográficos de uso
Descubra quando as pessoas usam seu aplicativo, em quais páginas elas estão mais interessadas, onde os usuários estão localizados e quais navegadores e sistemas operacionais eles usam. 

Os relatórios de Usuários e Sessões filtram seus dados por páginas ou eventos personalizados e os segmentam segundo as propriedades, como local, ambiente e página. Você também pode adicionar seus próprios filtros.

![Captura de tela mostra a página Visão geral dos usuários de uma empresa fictícia.](./media/usage-overview/users.png)  

Os insights à direita indicam padrões interessantes no conjunto de dados.  

* O relatório **Usuários** conta o número de usuários exclusivos que acessam suas páginas nos períodos escolhidos. Para aplicativos Web, os usuários são contados usando cookies. Se alguém acessar seu site usando computadores cliente ou navegadores diferentes ou limpar os cookies, essa pessoa será contada mais de uma vez.
* O relatório **Sessões** conta o número de sessões dos usuários que acessam seu site. Uma sessão é um período de atividade de um usuário, encerrada por um período de inatividade de mais de meia hora.

[Mais informações sobre as ferramentas Usuários, Sessões e Eventos](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Retenção – quantos usuários voltam?

A retenção o ajuda a entender com que frequência os usuários voltam para usar o aplicativo, com base em coortes de usuários que executaram alguma ação de negócios durante um determinado bucket de tempo. 

- Entenda quais recursos específicos fazem com que alguns usuários voltem mais que outros 
- Forme hipóteses com base em dados de usuários reais 
- Determine se a retenção é um problema para seu produto 

![Captura de tela mostra a página Visão geral de retenção que exibe informações sobre a frequência com que os usuários retornam para usar seu aplicativo.](./media/usage-overview/retention.png) 

Os controles de retenção na parte superior permitem que você defina eventos específicos o intervalo de tempo para calcular a retenção. O grafo no meio fornece uma representação visual do percentual geral de retenção, segundo o intervalo de tempo especificado. O grafo na parte inferior representa a retenção individual em um determinado período. Esse nível de detalhamento permite entender o que os usuários estão fazendo e o que pode afetar usuários que retornam com uma granularidade mais detalhada.  

[Mais informações sobre a ferramenta de Retenção](usage-retention.md)

## <a name="custom-business-events"></a>Eventos de negócios personalizados

Para obter uma compreensão clara do que os usuários fazem com seu aplicativo, é útil inserir linhas de código para registrar eventos personalizados. Esses eventos podem controlar qualquer coisa, desde ações detalhadas do usuário, como clicar em botões específicos, até eventos de negócios mais significativos, como fazer uma compra ou vencer um jogo.

Você também pode usar o [plug-in de análise automática do clique](javascript-click-analytics-plugin.md) para coletar eventos personalizados.

Embora em alguns casos as exibições de página possam representar eventos úteis, de modo geral não é esse o caso. Um usuário pode abrir uma página de produto sem adquirir o produto. 

Com eventos de negócios específicos, você pode traçar um gráfico do progresso do dos usuários em seu site. Você pode descobrir as preferências deles com relação a diferentes opções e quando eles desistem ou têm dificuldades. Com esse conhecimento, é possível tomar decisões informadas sobre as prioridades em sua lista de pendências de desenvolvimento.

Eventos podem ser registrados em log no lado do cliente do aplicativo:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Ou no lado do servidor:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Você pode anexar valores de propriedade a esses eventos, para que possa filtrar ou dividir os eventos quando inspecioná-los no portal. Além disso, um conjunto padrão de propriedades é anexado a cada evento, como a ID de usuário anônimo, que permite rastrear a sequência de atividades de um usuário individual.

Saiba mais sobre [eventos personalizados](./api-custom-events-metrics.md#trackevent) e [propriedades](./api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Fatiar e dividir eventos

Com as ferramentas de Usuários, Sessões e Eventos, você pode fatiar e dividir eventos personalizados segundo o usuário, o nome do evento e as propriedades.
![Captura de tela mostra a página Visão geral dos usuários de uma empresa fictícia.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Design de telemetria com o aplicativo

Quando estiver criando cada recurso do aplicativo, considere como você vai medir seu sucesso com os usuários. Decida quais eventos de negócios você precisa registrar e codifique as chamadas de rastreamento para esses eventos em seu aplicativo desde o início.

## <a name="a--b-testing"></a>Testando A | B
Se você não souber qual variante de um recurso terá mais êxito, libere ambas, tornando cada uma acessível para diferentes usuários. Avalie o sucesso de cada uma e então parta para uma versão unificada.

Para essa técnica, você anexa valores de propriedade distintos para toda a telemetria que é enviada por cada versão do seu aplicativo. Você pode fazer isso definindo propriedades no TelemetryContext ativo. Essas propriedades padrão são adicionadas a todas as mensagens de telemetria que o aplicativo envia - não apenas a suas mensagens personalizadas, mas também à telemetria padrão.

No portal do Application Insights, filtre e divida seus dados segundo os valores da propriedade, de modo a comparar as diferentes versões.

Para fazer isso, [configure um inicializador de telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer):

**Aplicativos ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

No inicializador do aplicativo Web, como Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**Aplicativos ASP.NET Core**

> [!NOTE]
> Adicionar inicializador usando `ApplicationInsights.config` ou usando `TelemetryConfiguration.Active` não é válido para aplicativos ASP.NET Core. 

Para [aplicativos ASP.NET Core](asp-net-core.md#adding-telemetryinitializers), adicionar um novo `TelemetryInitializer` é feito adicionando-o ao contêiner de injeção de dependência, conforme mostrado abaixo. Isso é feito no método `ConfigureServices` de sua classe `Startup.cs`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Todos os novos TelemetryClients adicionam automaticamente o valor da propriedade que você especificar. Eventos de telemetria individuais podem substituir os valores padrão.

## <a name="next-steps"></a>Próximas etapas
   - [Usuários, Sessões, Eventos](usage-segmentation.md)
   - [Funis](usage-funnels.md)
   - [Retenção](usage-retention.md)
   - [Fluxos de Usuário](usage-flows.md)
   - [Pastas de trabalho](../platform/workbooks-overview.md)
   - [Adicionar contexto de usuário](usage-send-user-context.md)

