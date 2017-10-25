---
title: "Análise de uso para aplicativos Web com o Azure Application Insights | Microsoft Docs"
description: "Compreenda seus usuários e o que eles fazem com seu aplicativo Web."
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: bwren
ms.openlocfilehash: edf15e72c822ea5e045895c6f03477c613c0a6c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="usage-analysis-for-web-applications-with-application-insights"></a>Análise de uso para aplicativos Web com o Application Insights

Quais recursos de seu aplicativo Web são mais populares? Os usuários atingem as metas deles com seu aplicativo? Eles deixam o aplicativo em determinados pontos? E retornam posteriormente?  O [Application Insights do Azure](app-insights-overview.md) ajuda você a ter insights profundos sobre como as pessoas usam seu aplicativo Web. Sempre que atualiza seu aplicativo, você pode avaliar como ele funciona para os usuários. Com esse conhecimento, você pode tomar decisões baseadas em dados sobre os próximos ciclos de desenvolvimento.

## <a name="send-telemetry-from-your-app"></a>Enviar telemetria de seu aplicativo

A melhor experiência é obtida, instalando o Application Insights tanto no código do servidor de aplicativos como nas suas páginas da Web. Os componentes do servidor e cliente do aplicativo retornam telemetria aoo portal Azure para análise.

1. **Código do servidor:** Instale o módulo apropriado para o [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md) ou [outro](app-insights-platforms.md) aplicativo.

    * *Não quer instalar o código do servidor? Apenas [crie um recurso do Azure Application Insights](app-insights-create-new-resource.md).*

2.  **Código de página da Web:** Abra o [Portal do Azure](https://portal.azure.com), abra o recurso do Application Insights para seu aplicativo e abra o **Introdução > Monitorar e diagnosticar aplicativos do lado do cliente**. 

    ![Copie o script no cabeçalho da página da web mestra.](./media/app-insights-usage-overview/02-monitor-web-page.png)


3. **Obter telemetria:** Execute seu projeto no modo de depuração por alguns minutos e, em seguida, procure resultados na folha Visão Geral em Application Insights.

    Publique seu aplicativo para monitorar o desempenho do aplicativo e descobrir o que seus usuários estão fazendo com o aplicativo.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Incluir a ID de usuário e de sessão em sua telemetria
Para controlar os usuários ao longo do tempo, o Application Insights requer um modo para identificá-los. A ferramenta Eventos é a única ferramenta de Uso que não requer uma ID de usuário ou uma ID de sessão.

Comece o envio de IDs de usuário e de sessão usando [este processo](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Explore as estatísticas e dados demográficos de uso
Descubra quando as pessoas usam seu aplicativo, em quais páginas elas estão mais interessadas, onde os usuários estão localizados e quais navegadores e sistemas operacionais eles usam. 

Os relatórios de Usuários e Sessões filtram seus dados por páginas ou eventos personalizados e os segmentam segundo as propriedades, como local, ambiente e página. Você também pode adicionar seus próprios filtros.

![Usuários](./media/app-insights-usage-overview/users.png)  

Os insights à direita indicam padrões interessantes no conjunto de dados.  

* O relatório **Usuários** conta o número de usuários exclusivos que acessam suas páginas nos períodos escolhidos. (Os usuários são contados usando cookies. Se alguém acessar seu site usando computadores cliente ou navegadores diferentes ou limpar os cookies, essa pessoa será contada mais de uma vez.)
* O relatório **Sessões** conta o número de sessões dos usuários que acessam seu site. Uma sessão é um período de atividade de um usuário, encerrada por um período de inatividade de mais de meia hora.

[Mais informações sobre as ferramentas Usuários, Sessões e Eventos](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>Visualizações de página

Na folha de Uso, clique no bloco de Visualizações de Página para obter uma análise das suas páginas mais populares:

![Na folha Visão Geral, clicar no gráfico Visualizações de página](./media/app-insights-usage-overview/05-games.png)

O exemplo acima é de um site da Web. A partir dos gráficos, imediatamente podemos ver:

* O uso não melhorou na última semana. Talvez devamos pensar em otimização do mecanismo de pesquisa?
* Tênis é a página de jogo mais popular. Vamos manter o foco em melhorias adicionais para esta página.
* Em média, os usuários visitam a página Tênis aproximadamente três vezes por semana. (Há cerca de três vezes mais sessões do que usuários.)
* A maioria dos usuários visita o site durante a semana de trabalho dos EUA e, em horário de trabalho. Talvez devêssemos fornecer um botão "ocultar rápido" na página da Web.
* As [anotações](app-insights-annotations.md) no gráfico mostram quando novas versões do site foram implantadas. Nenhuma das implantações recentes teve um efeito notável no uso.

E se você quiser investigar o tráfego em seu site com mais detalhes, por exemplo, dividindo segundo uma propriedade personalizada que o site envia em sua telemetria de exibições de página?

1. Abra a ferramenta **Eventos** no menu de recursos do Application Insights. Essa ferramenta permite analisar quantas exibições de página e eventos personalizados foram enviados de seu aplicativo, com base em uma variedade de opções de filtragem, divisão em coortes e segmentação.
2. No menu suspenso "Quem usou", selecione "Qualquer exibição de página".
3. No menu suspenso "Dividir por", selecione uma propriedade segundo a qual deseja dividir sua telemetria de exibição de página.

## <a name="retention---how-many-users-come-back"></a>Retenção – quantos usuários voltam?

A retenção o ajuda a entender com que frequência os usuários voltam para usar o aplicativo, com base em coortes de usuários que executaram alguma ação de negócios durante um determinado bucket de tempo. 

- Entenda quais recursos específicos fazem com que alguns usuários voltem mais que outros 
- Forme hipóteses com base em dados de usuários reais 
- Determine se a retenção é um problema para seu produto 

![Retenção](./media/app-insights-usage-overview/retention.png) 

Os controles de retenção na parte superior permitem que você defina eventos específicos o intervalo de tempo para calcular a retenção. O gráfico no meio fornece uma representação visual do percentual geral de retenção, segundo o intervalo de tempo especificado. O gráfico na parte inferior representa a retenção individual em um determinado período. Esse nível de detalhamento permite entender o que os usuários estão fazendo e o que pode afetar usuários que retornam com uma granularidade mais detalhada.  

[Mais informações sobre a ferramenta de Retenção](app-insights-usage-retention.md)

## <a name="custom-business-events"></a>Eventos de negócios personalizados

Para ter uma compreensão clara do que os usuários fazem com seu aplicativo Web, é útil inserir linhas de código para registrar eventos personalizados. Esses eventos podem controlar qualquer coisa, desde ações detalhadas do usuário, como clicar em botões específicos, até eventos de negócios mais significativos, como fazer uma compra ou vencer um jogo. 

Embora em alguns casos as exibições de página possam representar eventos úteis, de modo geral não é esse o caso. Um usuário pode abrir uma página de produto sem adquirir o produto. 

Com eventos de negócios específicos, você pode traçar um gráfico do progresso do dos usuários em seu site. Você pode descobrir as preferências deles com relação a diferentes opções e quando eles desistem ou têm dificuldades. Com esse conhecimento, é possível tomar decisões informadas sobre as prioridades em sua lista de pendências de desenvolvimento.

Eventos podem ser registrados na página da Web:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Ou no lado do servidor do aplicativo Web:

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Você pode anexar valores de propriedade a esses eventos, para que possa filtrar ou dividir os eventos quando inspecioná-los no portal. Além disso, um conjunto padrão de propriedades é anexado a cada evento, como a ID de usuário anônimo, que permite rastrear a sequência de atividades de um usuário individual.

Saiba mais sobre [eventos personalizados](app-insights-api-custom-events-metrics.md#trackevent) e [propriedades](app-insights-api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Fatiar e dividir eventos

Com as ferramentas de Usuários, Sessões e Eventos, você pode fatiar e dividir eventos personalizados segundo o usuário, o nome do evento e as propriedades.
![Usuários](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Design de telemetria com o aplicativo

Quando estiver criando cada recurso do aplicativo, considere como você vai medir seu sucesso com os usuários. Decida quais eventos de negócios você precisa registrar e codifique as chamadas de rastreamento para esses eventos em seu aplicativo desde o início.

## <a name="a--b-testing"></a>Testando A | B
Se você não souber qual variante de um recurso terá mais êxito, libere ambas, tornando cada uma acessível para diferentes usuários. Avalie o sucesso de cada uma e então parta para uma versão unificada.

Para essa técnica, você anexa valores de propriedade distintos para toda a telemetria que é enviada por cada versão do seu aplicativo. Você pode fazer isso definindo propriedades no TelemetryContext ativo. Essas propriedades padrão são adicionadas a todas as mensagens de telemetria que o aplicativo envia - não apenas a suas mensagens personalizadas, mas também à telemetria padrão.

No portal do Application Insights, filtre e divida seus dados segundo os valores da propriedade, de modo a comparar as diferentes versões.

Para fazer isso, [configure um inicializador de telemetria](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer):

```C#


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

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Todos os novos TelemetryClients adicionam automaticamente o valor da propriedade que você especificar. Eventos de telemetria individuais podem substituir os valores padrão.

## <a name="next-steps"></a>Próximas etapas
   - [Usuários, Sessões, Eventos](app-insights-usage-segmentation.md)
   - [Funis](usage-funnels.md)
   - [Retenção](app-insights-usage-retention.md)
   - [Fluxos de Usuário](app-insights-usage-flows.md)
   - [Pastas de trabalho](app-insights-usage-workbooks.md)
   - [Adicionar contexto de usuário](app-insights-usage-send-user-context.md)
