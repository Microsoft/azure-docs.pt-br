---
title: Usar análise com o Azure Application Insights | Microsoft Docs
description: Compreenda seus usuários e o que eles fazem com o seu aplicativo.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: f2539d5250ff436a720fe10f748f40db29b0ee25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783393"
---
# <a name="usage-analysis-with-application-insights"></a>Análise de uso com o Application Insights

Quais recursos do seu aplicativo Web ou móvel são mais populares? Os usuários atingem as metas deles com seu aplicativo? Eles deixam o aplicativo em determinados pontos? E retornam posteriormente?  O [Application Insights do Azure](../../azure-monitor/app/app-insights-overview.md) ajuda você a ter insights profundos sobre como as pessoas usam seu aplicativo. Sempre que atualiza seu aplicativo, você pode avaliar como ele funciona para os usuários. Com esse conhecimento, você pode tomar decisões baseadas em dados sobre os próximos ciclos de desenvolvimento.

## <a name="send-telemetry-from-your-app"></a>Enviar telemetria de seu aplicativo

A melhor experiência é obtida, instalando o Application Insights tanto no código do servidor de aplicativos como nas suas páginas da Web. Os componentes do servidor e cliente do aplicativo retornam telemetria aoo portal Azure para análise.

1. **Código do servidor:** Instale o módulo apropriado para o [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md) ou [outro](../../azure-monitor/app/platforms.md) aplicativo.

    * *Não quer instalar o código do servidor? Apenas [crie um recurso do Azure Application Insights](../../azure-monitor/app/create-new-resource.md ).*

2. **URL da página na web:** Adicione o seguinte script à página da Web antes do fechamento ``</head>``. Substitua a chave de instrumentação pelo valor apropriado para seu recurso do Application Insights:

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
    </script>
    ```
    Para saber sobre configurações mais avançadas para o monitoramento de sites, confira a [referência da API do SDK de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

3. **Código do aplicativo móvel:** Use o SDK do App Center para coletar eventos do seu aplicativo e, em seguida, envie cópias desses eventos ao Application Insights para análise [seguindo este guia](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Obter telemetria:** Execute seu projeto no modo de depuração por alguns minutos e, em seguida, procure resultados na folha Visão Geral em Application Insights.

    Publique seu aplicativo para monitorar o desempenho do aplicativo e descobrir o que seus usuários estão fazendo com o aplicativo.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Incluir a ID de usuário e de sessão em sua telemetria
Para controlar os usuários ao longo do tempo, o Application Insights requer um modo para identificá-los. A ferramenta Eventos é a única ferramenta de Uso que não requer uma ID de usuário ou uma ID de sessão.

Comece o envio de IDs de usuário e de sessão usando [este processo](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Explore as estatísticas e dados demográficos de uso
Descubra quando as pessoas usam seu aplicativo, em quais páginas elas estão mais interessadas, onde os usuários estão localizados e quais navegadores e sistemas operacionais eles usam. 

Os relatórios de Usuários e Sessões filtram seus dados por páginas ou eventos personalizados e os segmentam segundo as propriedades, como local, ambiente e página. Você também pode adicionar seus próprios filtros.

![Usuários](./media/usage-overview/users.png)  

Os insights à direita indicam padrões interessantes no conjunto de dados.  

* O relatório **Usuários** conta o número de usuários exclusivos que acessam suas páginas nos períodos escolhidos. Para aplicativos Web, os usuários são contados usando cookies. Se alguém acessar seu site usando computadores cliente ou navegadores diferentes ou limpar os cookies, essa pessoa será contada mais de uma vez.
* O relatório **Sessões** conta o número de sessões dos usuários que acessam seu site. Uma sessão é um período de atividade de um usuário, encerrada por um período de inatividade de mais de meia hora.

[Mais informações sobre as ferramentas Usuários, Sessões e Eventos](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Retenção – quantos usuários voltam?

A retenção o ajuda a entender com que frequência os usuários voltam para usar o aplicativo, com base em coortes de usuários que executaram alguma ação de negócios durante um determinado bucket de tempo. 

- Entenda quais recursos específicos fazem com que alguns usuários voltem mais que outros 
- Forme hipóteses com base em dados de usuários reais 
- Determine se a retenção é um problema para seu produto 

![Retenção](./media/usage-overview/retention.png) 

Os controles de retenção na parte superior permitem que você defina eventos específicos o intervalo de tempo para calcular a retenção. O grafo no meio fornece uma representação visual do percentual geral de retenção, segundo o intervalo de tempo especificado. O grafo na parte inferior representa a retenção individual em um determinado período. Esse nível de detalhamento permite entender o que os usuários estão fazendo e o que pode afetar usuários que retornam com uma granularidade mais detalhada.  

[Mais informações sobre a ferramenta de Retenção](usage-retention.md)

## <a name="custom-business-events"></a>Eventos de negócios personalizados

Para obter uma compreensão clara do que os usuários fazem com seu aplicativo, é útil inserir linhas de código para registrar eventos personalizados. Esses eventos podem controlar qualquer coisa, desde ações detalhadas do usuário, como clicar em botões específicos, até eventos de negócios mais significativos, como fazer uma compra ou vencer um jogo. 

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

Saiba mais sobre [eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) e [propriedades](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Fatiar e dividir eventos

Com as ferramentas de Usuários, Sessões e Eventos, você pode fatiar e dividir eventos personalizados segundo o usuário, o nome do evento e as propriedades.
![Usuários](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Design de telemetria com o aplicativo

Quando estiver criando cada recurso do aplicativo, considere como você vai medir seu sucesso com os usuários. Decida quais eventos de negócios você precisa registrar e codifique as chamadas de rastreamento para esses eventos em seu aplicativo desde o início.

## <a name="a--b-testing"></a>Testando A | B
Se você não souber qual variante de um recurso terá mais êxito, libere ambas, tornando cada uma acessível para diferentes usuários. Avalie o sucesso de cada uma e então parta para uma versão unificada.

Para essa técnica, você anexa valores de propriedade distintos para toda a telemetria que é enviada por cada versão do seu aplicativo. Você pode fazer isso definindo propriedades no TelemetryContext ativo. Essas propriedades padrão são adicionadas a todas as mensagens de telemetria que o aplicativo envia - não apenas a suas mensagens personalizadas, mas também à telemetria padrão.

No portal do Application Insights, filtre e divida seus dados segundo os valores da propriedade, de modo a comparar as diferentes versões.

Para fazer isso, [configure um inicializador de telemetria](../../azure-monitor/app/api-filtering-sampling.md##add-properties-itelemetryinitializer):

```csharp


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
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

Todos os novos TelemetryClients adicionam automaticamente o valor da propriedade que você especificar. Eventos de telemetria individuais podem substituir os valores padrão.

## <a name="next-steps"></a>Próximas etapas
   - [Usuários, Sessões, Eventos](usage-segmentation.md)
   - [Funis](usage-funnels.md)
   - [Retenção](usage-retention.md)
   - [Fluxos de Usuário](usage-flows.md)
   - [Pastas de trabalho](../../azure-monitor/app/usage-workbooks.md)
   - [Adicionar contexto de usuário](usage-send-user-context.md)
