---
title: "Acompanhamento de dependência no Azure Application Insights | Microsoft Docs"
description: Analise o uso, disponibilidade e desempenho de seu local ou um aplicativo Web do Microsoft Azure com o Application Insights.
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: bwren
ms.openlocfilehash: 6e0b67ba98af27017901608dde4401600eb9957f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-application-insights-dependency-tracking"></a>Configurar o Application Insights: acompanhamento de dependências
Um *dependência* é um componente externo que é chamado por seu aplicativo. Normalmente, ele é um serviço chamado usando HTTP, um banco de dados ou um sistema de arquivos. O [Application Insights](app-insights-overview.md) mede por quanto tempo o aplicativo aguarda dependências e com que frequência uma chamada de dependência falha. Você pode investigar chamadas específicas e relacioná-las a solicitações e exceções.

![gráficos de exemplo](./media/app-insights-asp-net-dependencies/10-intro.png)

O monitor de dependência pronto para uso atualmente relata chamadas para esses tipos de dependências:

* Servidor
  * Bancos de dados SQL
  * Serviços Web ASP.NET e WCF que usam associações baseadas em HTTP
  * Chamadas HTTP locais ou remotas
  * Azure Cosmos DB, tabela, Armazenamento de Blobs e fila
* Páginas da Web
  * Chamadas AJAX

O monitoramento funciona com o uso da [instrumentação de código de byte](https://msdn.microsoft.com/library/z9z62c29.aspx) nos métodos selecionados. A sobrecarga de desempenho é mínima.

Você também pode escrever suas próprias chamadas SDK para monitorar outras dependências, no código do cliente e servidor, usando o [API TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency).

## <a name="set-up-dependency-monitoring"></a>Configurar o monitoramento de dependência
As informações de dependência parciais são coletadas automaticamente pelo [SDK do Application Insights](app-insights-asp-net.md). Para obter dados completos, instale o agente apropriado para o servidor host.

| Plataforma | Instalar |
| --- | --- |
| Servidor IIS |Você pode [instalar o Status Monitor no servidor](app-insights-monitor-performance-live-website-now.md) ou [atualizar o aplicativo para .NET Framework 4.6 ou posterior](http://go.microsoft.com/fwlink/?LinkId=528259) e instalar o [SDK do Application Insights](app-insights-asp-net.md) no aplicativo. |
| Aplicativo Web do Azure |No painel de controle do aplicativo Web, [abra a folha do Application Insights no seu painel de controle do aplicativo Web](app-insights-azure-web-apps.md) e escolha Instalar, se solicitado. |
| Serviço de Nuvem do Azure |[Usar tarefa de inicialização](app-insights-cloudservices.md) ou [Instalar o .NET Framework 4.6 +](../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Onde encontrar dados de dependência
* O [Mapa de Aplicativo](#application-map) visualiza as dependências entre seu aplicativo e os componentes de vizinhança.
* As [folhas de desempenho, de navegador e de falha](#performance-and-blades) mostram dados de dependência de servidor.
* A [folha de navegadores](#ajax-calls) mostra chamadas AJAX de navegadores dos usuários.
* [Clickthrough de solicitações com falha ou lentas](#diagnose-slow-requests) para verificar a dependência de chamadas.
* O [Analytics](#analytics) pode ser usado para consultar dados de dependência.

## <a name="application-map"></a>Mapa de aplicativo
O Mapa de aplicativo atua como uma ajuda visual para descobrir dependências entre os componentes do seu aplicativo. Ele é gerado automaticamente da telemetria do seu aplicativo. Este exemplo mostra chamadas AJAX de scripts de navegador e chamadas REST do aplicativo de servidor para os dois serviços externos.

![Mapa de aplicativo](./media/app-insights-asp-net-dependencies/08.png)

* **Navegue das caixas** até a dependência relevante e outros gráficos.
* **Fixe o mapa** no [painel](app-insights-dashboards.md), onde ele ficará totalmente funcional.

[Saiba mais](app-insights-app-map.md).

## <a name="performance-and-failure-blades"></a>Folhas de falha e de desempenho
A folha de desempenho mostra a duração das chamadas de dependência feitas pelo aplicativo de servidor. Há um gráfico de resumo e uma tabela segmentadas por chamada.

![Gráficos de dependência de folha de desempenho](./media/app-insights-asp-net-dependencies/dependencies-in-performance-blade.png)

Clickthrough nos gráficos de resumo ou os itens de tabela para pesquisar ocorrências brutas dessas chamadas.

![Instâncias de chamada de dependência](./media/app-insights-asp-net-dependencies/dependency-call-instance.png)

As **contagens de falhas** são mostrados na folha **Falhas**. Uma falha é qualquer código de retorno que não esteja no intervalo 200-399, ou que seja desconhecido.

> [!NOTE]
> **Falhas de 100%?** - Isso provavelmente indica que você está apenas obtendo dados de dependência parcial. Você precisa [configurar o monitoramento de dependência apropriado para sua plataforma](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Chamadas AJAX
A folha Navegadores mostra a taxa de falha e a duração de chamadas AJAX de [JavaScript nas páginas da Web](app-insights-javascript.md). Elas são mostradas como Dependências.

## <a name="diagnosis"></a> Diagnosticar solicitações lentas
Cada evento de solicitação está associado às chamadas de dependência, exceções e outros eventos que são rastreados enquanto seu aplicativo está processando a solicitação. Então se algumas solicitações são com baixo desempenho, você pode descobrir seja devido à lentidão nas respostas de uma dependência.

Vamos examinar um exemplo disso.

### <a name="tracing-from-requests-to-dependencies"></a>Rastreamento de solicitações de dependências
Abra a folha Desempenho e examine a grade de solicitações:

![Lista de solicitações com contagens e médias](./media/app-insights-asp-net-dependencies/02-reqs.png)

A solicitação superior está demorando muito. Vamos ver se conseguimos descobrir onde o tempo é gasto.

Clique nesta linha para ver os eventos de solicitação individuais:

![Lista de ocorrências de solicitação](./media/app-insights-asp-net-dependencies/03-instances.png)

Clique em qualquer instância de execução longa para inspecioná-la ainda mais e role para baixo até as chamadas de dependência remotas relacionadas a essa solicitação:

![Localizar as chamadas para dependências remotas, identificar duração incomum](./media/app-insights-asp-net-dependencies/04-dependencies.png)

Parece a maior parte do tempo atendendo a solicitação foi gasto em uma chamada para um serviço local.

Selecione a linha para obter mais informações:

![Clique nessa dependência remota para identificar o culpado](./media/app-insights-asp-net-dependencies/05-detail.png)

Parece que o problema está aí. Nós já identificamos o problema, então agora simplesmente precisamos descobrir por que essa chamada está demorando tanto.

### <a name="request-timeline"></a>Linha do tempo da solicitação
Em outro caso, não há nenhuma chamada de dependência que seja tão longa. Mas, ao alternar para o modo de exibição de linha do tempo, podemos ver onde está o atraso durante nosso processamento interno:

![Localizar as chamadas para dependências remotas, identificar duração incomum](./media/app-insights-asp-net-dependencies/04-1.png)

Parece haver uma grande lacuna após a primeira chamada de dependência e, portanto, devemos examinar nosso código para ver o motivo disso.

### <a name="profile-your-live-site"></a>Perfil de seu site ativo

Não sabe para onde o tempo vai? O [Application Insights Profiler](app-insights-profiler.md) rastreia chamadas HTTP para seu site ativo e mostra quais são as funções mais demoradas em seu código.

## <a name="failed-requests"></a>Solicitações falhas
As solicitações com falha também podem ser associadas a chamadas com falha para as dependências. Novamente, podemos fazer um clickthrough para rastrear o problema.

![Clique no gráfico de solicitações com falha](./media/app-insights-asp-net-dependencies/06-fail.png)

Clique para uma ocorrência de uma solicitação com falha e examine os eventos associados.

![Clique em um tipo de solicitação e na instância para obter uma exibição diferente da mesma instância, clique nele para obter detalhes da exceção.](./media/app-insights-asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Análise
Você pode rastrear dependências na [linguagem de consulta do Log Analytics](https://docs.loganalytics.io/). Veja alguns exemplos.

* Localize todas as chamadas com falha de dependência:

```

    dependencies | where success != "True" | take 10
```

* Localize as chamadas AJAX:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Localize as chamadas de dependência associadas a solicitações:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Localize as chamadas do AJAX associadas a exibições de página:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>Acompanhamento de dependência personalizado
O módulo padrão de acompanhamento de dependência descobre automaticamente dependências externas, como bancos de dados e APIs REST. Mas, talvez você queira que alguns componentes adicionais sejam tratados da mesma forma.

Você pode escrever código que envia informações de dependência usando a mesma [API TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency) usada pelos módulos padrão.

Por exemplo, se você criar seu código com um assembly que não escreveu, poderá determinar o tempo de todas as chamadas nele, para descobrir qual sua contribuição aos tempos de resposta. Para que esses dados sejam exibidos nos gráficos de dependência no Application Insights, envie-os usando `TrackDependency`.

```C#

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Se desejar desativar o módulo padrão de acompanhamento de dependência, remova a referência para DependencyTrackingTelemetryModule em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Solucionar problemas
*O sinalizador de êxito da dependência sempre mostra true ou false.*

*Consulta SQL não mostrada por completo.*

* Atualize para a versão mais recente do SDK. Se sua versão do .NET for inferior à 4.6:
  * Host IIS: instale o [Application Insights Agent](app-insights-monitor-performance-live-website-now.md) nos servidores de host.
  * Aplicativo Web do Azure: abra a guia Application Insights no painel de controle do aplicativo Web e instale o Application Insights.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Próximas etapas
* [Exceções](app-insights-asp-net-exceptions.md)
* [Dados do usuário e da página](app-insights-javascript.md)
* [Disponibilidade](app-insights-monitor-web-app-availability.md)
