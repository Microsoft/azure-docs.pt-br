---
title: Usar análise com o Azure Application Insights | Microsoft Docs
description: Compreenda seus usuários e o que eles fazem com o seu aplicativo.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: d9de1e10363f2100b9dfe557dc12e0be951ce6b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489031"
---
# <a name="usage-analysis-with-application-insights"></a>Análise de uso com o Application Insights

Quais recursos do seu aplicativo Web ou móvel são mais populares? Os usuários atingem as metas deles com seu aplicativo? Eles deixam o aplicativo em determinados pontos? E retornam posteriormente?  O [Application Insights do Azure](./app-insights-overview.md) ajuda você a ter insights profundos sobre como as pessoas usam seu aplicativo. Sempre que atualiza seu aplicativo, você pode avaliar como ele funciona para os usuários. Com esse conhecimento, você pode tomar decisões baseadas em dados sobre os próximos ciclos de desenvolvimento.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Enviar telemetria de seu aplicativo

A melhor experiência é obtida, instalando o Application Insights tanto no código do servidor de aplicativos como nas suas páginas da Web. Os componentes do servidor e cliente do aplicativo retornam telemetria aoo portal Azure para análise.

1. **Código do servidor:** Instale o módulo apropriado para o [ASP.NET](./asp-net.md), [Azure](./app-insights-overview.md), [Java](./java-get-started.md), [Node.js](./nodejs.md) ou [outro](./platforms.md) aplicativo.

    * *Não quer instalar o código do servidor? Apenas [crie um recurso do Azure Application Insights](./create-new-resource.md).*

2. **URL da página na web:** Adicione o seguinte script à página da Web antes do fechamento ``</head>``. Substitua a chave de instrumentação pelo valor apropriado para seu recurso do Application Insights:
    
    O trecho atual (listado abaixo) é a versão "5", a versão é codificada no trecho de código como VA: "#" e a [versão atual também está disponível no GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

    ```html
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
    // name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    // ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    // useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
    // onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
    cfg: { // Application Insights Configuration
      instrumentationKey:"INSTRUMENTATION_KEY"
    }});
    </script>
    ```

    Para saber sobre configurações mais avançadas para o monitoramento de sites, confira o [artigo de referência do SDK de JavaScript](./javascript.md).

3. **Código do aplicativo móvel:** Use o SDK do App Center para coletar eventos do seu aplicativo e, em seguida, envie cópias desses eventos ao Application Insights para análise [seguindo este guia](../app/mobile-center-quickstart.md).

4. **Obter telemetria:** Execute seu projeto no modo de depuração por alguns minutos e, em seguida, procure resultados na folha Visão Geral em Application Insights.

    Publique seu aplicativo para monitorar o desempenho do aplicativo e descobrir o que seus usuários estão fazendo com o aplicativo.

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
   - [Pastas de trabalho](../visualize/workbooks-overview.md)
