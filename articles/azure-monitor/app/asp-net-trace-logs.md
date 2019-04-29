---
title: Explorar os logs de rastreamento do .NET no Application Insights
description: Pesquise logs gerados com Trace, NLog ou Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mbullwin
ms.openlocfilehash: 8c722eb0db3022620ba03e02dd2ae00f97a78f28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691148"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Explorar os logs de rastreamento do .NET/.NET Core no Application Insights

Se você usa ILogger, NLog, log4Net ou System.Diagnostics.Trace para o rastreamento de diagnóstico em seu aplicativo ASP.NET/ASP.NET Core, os logs podem ser enviados ao [Azure Application Insights][start], onde é possível explorá-los e pesquisá-los. Os logs serão mesclados à outra telemetria proveniente de seu aplicativo para que você possa identificar os rastreamentos associados ao atendimento de cada solicitação de usuário e correlacioná-los com outros relatórios de eventos e exceções.

> [!NOTE]
> Você precisa do módulo de captura de log? É um adaptador útil para agentes de terceiros, mas se você ainda não usa o NLog, log4Net ou System.Diagnostics.Trace, convém chamar apenas o [TrackTrace() do Application Insights](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) diretamente.
>
>

## <a name="install-logging-on-your-app"></a>Instalar o log no seu aplicativo
Instale a estrutura de registros escolhida no seu projeto. Isso deve resultar em uma entrada no app.config ou web.config.

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Configurar o Application Insights para coletar logs
**[Adicione o Application Insights ao seu projeto](../../azure-monitor/app/asp-net.md)** se ainda não tiver feito isso. Você verá uma opção para incluir o coletor de logs.

Ou então **Configure o Application Insights** clicando com o botão direito no seu projeto no Gerenciador de Soluções. Selecione a opção **Configurar a coleta de rastreamento**.

*Não consegue ver o menu do Application Insights nem a opção de coletor de logs?* Experimente [Solucionar problemas](#troubleshooting).

## <a name="manual-installation"></a>Instalação manual
Use este método se o tipo de projeto não tiver suporte no instalador do Application Insights (por exemplo, um projeto de Área de Trabalho do Windows).

1. Se você planeja usar o log4Net ou NLog, instale-o em seu projeto.
2. No Gerenciador de Soluções, clique com o botão direito do mouse no seu projeto e escolha **Gerenciar Pacotes NuGet**.
3. Pesquise "Application Insights"
4. Selecione um dos seguintes pacotes:

   - Para o ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Para o NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Para o Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Para o System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

O pacote NuGet instala os assemblies necessários e, quando aplicável, modifica o web.config ou o app.config.

## <a name="ilogger"></a>ILogger

Para obter exemplos de como usar a implementação de ILogger do Application Insights com aplicativos Console e ASP.NET Core, consulte este [artigo](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Inserir chamadas de log de diagnóstico
Se você usa System.Diagnostics.Trace, uma chamada típica é semelhante a:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se você preferir log4net ou NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>Usando eventos EventSource
É possível configurar eventos [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) para que eles sejam enviados para o Application Insights como rastreamentos. Primeiro, instale o pacote NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Depois, edite a seção `TelemetryModules` do arquivo [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, você pode definir os seguintes parâmetros:
 * `Name` especifica o nome do EventSource a ser coletado.
 * `Level` especifica o nível de registro em log a ser coletado. Pode ser `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose` ou `Warning`.
 * `Keywords` (opcional) especifica o valor inteiro das combinações de palavras-chave a serem usadas.

## <a name="using-diagnosticsource-events"></a>Usando eventos DiagnosticSource
É possível configurar eventos [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) para que eles sejam enviados para o Application Insights como rastreamentos. Primeiro, instale o pacote NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Depois, edite a seção `TelemetryModules` do arquivo [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada DiagnosticSource que você deseja rastrear, adicione uma entrada com o atributo `Name` definido como o nome do seu DiagnosticSource.

## <a name="using-etw-events"></a>Usando eventos ETW
É possível configurar os eventos ETW a serem enviados ao Application Insights como rastreamentos. Primeiro, instale o pacote NuGet `Microsoft.ApplicationInsights.EtwCollector`. Depois, edite a seção `TelemetryModules` do arquivo [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

> [!NOTE] 
> Eventos ETW só podem ser coletados se o processo que hospeda o SDK estiver em execução em uma identidade que é membro dos administradores ou "Usuários de log de desempenho".

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, você pode definir os seguintes parâmetros:
 * `ProviderName` é o nome do provedor de ETW a ser coletado.
 * `ProviderGuid` especifica o GUID do provedor de ETW a ser coletado, pode ser usado em vez de `ProviderName`.
 * `Level` define o nível de registro em log a ser coletado. Pode ser `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose` ou `Warning`.
 * `Keywords` (opcional) define o valor inteiro das combinações de palavras-chave a serem usadas.

## <a name="using-the-trace-api-directly"></a>Usando a API de rastreamento diretamente
Você pode chamar a API de rastreamento do Application Insights diretamente. Os adaptadores de log usam essa API.

Por exemplo: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Uma vantagem de TrackTrace é que você pode colocar dados relativamente compridos na mensagem. Por exemplo, você pode codificar dados POST.

Além disso, você pode adicionar um nível de severidade à mensagem. E, como ocorre com outros casos de telemetria, você pode adicionar valores de propriedade que podem ser usados para ajudar a filtrar ou a pesquisar diferentes conjuntos de rastreamentos. Por exemplo: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Isso permite que você filtre facilmente todas as mensagens de um nível de severidade específico relacionadas a determinado banco de dados na [Pesquisa][diagnostic].

## <a name="explore-your-logs"></a>Explorar seus logs
Execute o aplicativo no modo de depuração ou implante-o dinamicamente.

Na folha de visão geral do aplicativo, no [portal do Application Insights][portal], escolha [Search][diagnostic].

Por exemplo, você pode:

* Filtrar rastreamentos de log ou itens com propriedades específicas
* Inspecionar um item específico em detalhes.
* Localizar outra telemetria relacionada à mesma solicitação de usuário (ou seja, com o mesmo OperationId)
* Salvar a configuração dessa página como um favorito

> [!NOTE]
> **Amostragem.** Se o aplicativo enviar muitos dados e se você estiver usando o SDK do Application Insights para o ASP.NET versão 2.0.0-beta3 ou posterior, o recurso de amostragem adaptável poderá operar e enviar apenas uma porcentagem de sua telemetria. [Saiba mais sobre amostragem.](../../azure-monitor/app/sampling.md)
>
>

## <a name="next-steps"></a>Próximas etapas
[Diagnosticar falhas e exceções no ASP.NET][exceptions]

[Saiba mais sobre o Search][diagnostic].

## <a name="troubleshooting"></a>solução de problemas
### <a name="how-do-i-do-this-for-java"></a>Como faço isso no Java?
Use os [adaptadores de log Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Não há nenhuma opção do Application Insights no menu de contexto do projeto
* Verifique se as ferramentas do Application Insights estão instaladas neste computador de desenvolvimento. No menu Ferramentas do Visual Studio, em Extensões e Atualizações, procure pelas Ferramentas do Application Insights. Se não estiver na aba Instalado, abra a guia Online e instale-as.
* Este pode ser um tipo de projeto sem suporte pelas ferramentas do Application Insights. Use a [instalação manual](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Não há nenhuma opção de adaptador de log na ferramenta de configuração
* Você precisa instalar primeiro a estrutura de registros.
* Se estiver usando System.Diagnostics.Trace, verifique se você o [configurou no `web.config`](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Você tem a versão mais recente do Application Insights? No menu **Ferramentas** do Visual Studio, escolha **Extensões e Atualizações** e abra a guia **Atualizações**. Se as ferramentas de Análise do Desenvolvedor estiverem presentes, clique para atualizá-las.

### <a name="emptykey"></a>Recebo um erro "Chave de instrumentação não pode ser vazio"
Parece que você instalou o pacote de Nuget de adaptador para registro em log sem instalar o Application Insights.

No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Será exibida uma caixa de diálogo que solicita que você entre no Azure e crie um recurso do Application Insights, ou então reutilize um recurso existente. Isso deve corrigir o erro.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Posso ver rastreamentos na pesquisa de diagnóstico, mas não os outros eventos
Às vezes, pode levar algum tempo para que todos os eventos e solicitações percorram o pipeline.

### <a name="limits"></a>Que quantidade de dados é mantida?
Vários fatores afetam a quantidade de dados retidos. Veja a seção de [limites](../../azure-monitor/app/api-custom-events-metrics.md#limits) da página de métricas de eventos do cliente para obter mais informações. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Não estou vendo algumas das entradas de log que eu esperava
Se o aplicativo enviar muitos dados e se você estiver usando o SDK do Application Insights para o ASP.NET versão 2.0.0-beta3 ou posterior, o recurso de amostragem adaptável poderá operar e enviar apenas uma porcentagem de sua telemetria. [Saiba mais sobre amostragem.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Próximas etapas
* [Configurar testes de disponibilidade e capacidade de resposta][availability]
* [Solução de problemas][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
