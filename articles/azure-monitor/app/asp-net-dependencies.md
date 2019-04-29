---
title: Acompanhamento de dependência no Azure Application Insights | Microsoft Docs
description: Analise o uso, disponibilidade e desempenho de seu local ou um aplicativo de web do Microsoft Azure com o Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: c77b5810164aef7508f717a0f75d90cf6cba2089
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691353"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Configurar o Application Insights: acompanhamento de dependência
Um *dependência* é um componente externo que é chamado por seu aplicativo. Normalmente, ele é um serviço chamado usando HTTP, um banco de dados ou um sistema de arquivos. O [Application Insights](../../azure-monitor/app/app-insights-overview.md) mede por quanto tempo o aplicativo aguarda dependências e com que frequência uma chamada de dependência falha. Você pode investigar chamadas específicas e relacioná-las a solicitações e exceções.

O monitor de dependência pronto para uso atualmente relata chamadas para esses tipos de dependências:

* Servidor
  * Bancos de dados SQL
  * Serviços Web ASP.NET e WCF que usam associações baseadas em HTTP
  * Chamadas HTTP locais ou remotas
  * Azure Cosmos DB, tabela, Armazenamento de Blobs e fila 
* Páginas da Web
  * Chamadas AJAX

Monitoramento funciona usando [instrumentação de código de byte](https://msdn.microsoft.com/library/z9z62c29.aspx) em torno de métodos Select ou com base em retornos de chamada DiagnosticSource (nos SDKs mais recentes do .NET) do .NET Framework. A sobrecarga de desempenho é mínima.

Você também pode escrever suas próprias chamadas SDK para monitorar outras dependências, no código do cliente e servidor, usando o [API TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

> [!NOTE]
> O Azure Cosmos DB é acompanhado automaticamente somente se [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) é usado. O modo TCP não será capturado pelo Application Insights.

## <a name="set-up-dependency-monitoring"></a>Configurar o monitoramento de dependência
As informações de dependência parciais são coletadas automaticamente pelo [SDK do Application Insights](asp-net.md). Para obter dados completos, instale o agente apropriado para o servidor host.

| Plataforma | Instalar |
| --- | --- |
| Servidor IIS |Você pode [instalar o Status Monitor no servidor](../../azure-monitor/app/monitor-performance-live-website-now.md) ou [atualizar o aplicativo para .NET Framework 4.6 ou posterior](https://go.microsoft.com/fwlink/?LinkId=528259) e instalar o [SDK do Application Insights](asp-net.md) no aplicativo. |
| Aplicativo Web do Azure |No painel de controle do aplicativo Web, [abra a folha do Application Insights no seu painel de controle do aplicativo Web](../../azure-monitor/app/azure-web-apps.md) e escolha Instalar, se solicitado. |
| Serviço de Nuvem do Azure |[Usar tarefa de inicialização](../../azure-monitor/app/cloudservices.md) ou [Instalar o .NET Framework 4.6 +](../../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Onde encontrar dados de dependência
* O [Mapa do Aplicativo](#application-map) visualiza as dependências entre seu aplicativo e os componentes de vizinhança.
* As [folhas de desempenho, de navegador e de falha](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-performance) mostram dados de dependência de servidor.
* A [folha de navegadores](#ajax-calls) mostra chamadas AJAX de navegadores dos usuários.
* Clique pelas solicitações com falha ou lentas para verificar a dependência de chamadas.
* O [Analytics](#analytics) pode ser usado para consultar dados de dependência.

## <a name="application-map"></a>Mapa de aplicativo
O Mapa de aplicativo atua como uma ajuda visual para descobrir dependências entre os componentes do seu aplicativo. Ele é gerado automaticamente da telemetria do seu aplicativo. Este exemplo mostra chamadas AJAX de scripts de navegador e chamadas REST do aplicativo de servidor para os dois serviços externos.

![Mapa de aplicativo](./media/asp-net-dependencies/cloud-rolename.png)

* **Navegue das caixas** até a dependência relevante e outros gráficos.
* **Fixe o mapa** no [painel](../../azure-monitor/app/app-insights-dashboards.md), onde ele ficará totalmente funcional.

[Saiba mais](../../azure-monitor/app/app-map.md).

## <a name="performance-and-failure-blades"></a>Folhas de falha e de desempenho
A folha de desempenho mostra a duração das chamadas de dependência feitas pelo aplicativo de servidor.

As **contagens de falhas** são mostrados na folha **Falhas**. Uma falha é qualquer código de retorno que não esteja no intervalo 200-399, ou que seja desconhecido.

> [!NOTE]
> **Falhas de 100%?** - Isso provavelmente indica que você está apenas obtendo dados de dependência parcial. Você precisa [configurar o monitoramento de dependência apropriado para sua plataforma](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>Chamadas AJAX
A folha Navegadores mostra a taxa de falha e a duração de chamadas AJAX de [JavaScript nas páginas da Web](../../azure-monitor/app/javascript.md). Elas são mostradas como Dependências.

## <a name="diagnosis"></a> Diagnosticar solicitações lentas
Cada evento de solicitação está associado com as chamadas de dependência, exceções e outros eventos que são rastreados enquanto seu aplicativo está processando a solicitação. Então se algumas solicitações são com baixo desempenho, você pode descobrir seja devido à lentidão nas respostas de uma dependência.

### <a name="profile-your-live-site"></a>Perfil de seu site ativo

Não sabe para onde o tempo vai? O [Application Insights profiler](../../azure-monitor/app/profiler.md) rastreamentos HTTP chama para seu site ativo e mostra quais funções em seu código mais demoradas.

## <a name="analytics"></a>Análise
Você pode rastrear dependências na [linguagem de consulta Kusto](/azure/kusto/query/). Veja alguns exemplos.

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

Você pode escrever código que envia informações de dependência usando a mesma [API TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency) usada pelos módulos padrão.

Por exemplo, se você criar seu código com um assembly que não escreveu, poderá determinar o tempo de todas as chamadas nele, para descobrir qual sua contribuição aos tempos de resposta. Para que esses dados sejam exibidos nos gráficos de dependência no Application Insights, envie-os usando `TrackDependency`.

```csharp

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
                // The call above has been made obsolete in the latest SDK. The updated call follows this format:
                // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
            }
```

Se desejar desativar o módulo padrão de acompanhamento de dependência, remova a referência para DependencyTrackingTelemetryModule em [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>solução de problemas
*O sinalizador de êxito da dependência sempre mostra true ou false.*

*Consulta SQL não mostrada por completo.*

Veja a tabela a seguir e garanta que você escolheu a configuração correta para habilitar o monitoramento de dependência para o seu aplicativo.

| Plataforma | Instalar |
| --- | --- |
| Servidor IIS |Você pode [instalar o Status Monitor no seu servidor](../../azure-monitor/app/monitor-performance-live-website-now.md). Ou [atualizar o aplicativo para .NET Framework 4.6 ou posterior](https://go.microsoft.com/fwlink/?LinkId=528259) e instalar o [SDK do Application Insights](asp-net.md) no aplicativo. |
| IIS Express |Use o Servidor IIS em vez disso. |
| Aplicativo Web do Azure |No painel de controle do aplicativo Web, [abra a folha do Application Insights no seu painel de controle do aplicativo Web](../../azure-monitor/app/azure-web-apps.md) e escolha Instalar, se solicitado. |
| Serviço de Nuvem do Azure |[Usar tarefa de inicialização](../../azure-monitor/app/cloudservices.md) ou [Instalar o .NET Framework 4.6 +](../../cloud-services/cloud-services-dotnet-install-dotnet.md). |

## <a name="next-steps"></a>Próximas etapas
* [Exceções](../../azure-monitor/app/asp-net-exceptions.md)
* [Dados do usuário e da página](../../azure-monitor/app/javascript.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
