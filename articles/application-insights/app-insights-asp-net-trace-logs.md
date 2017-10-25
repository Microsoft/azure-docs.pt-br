---
title: Explorar os logs de rastreamento do .NET no Application Insights
description: Pesquise logs gerados com Trace, NLog ou Log4Net.
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: bwren
ms.openlocfilehash: 21e4ae78653977efc7a47f984bc309afac870a59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="explore-net-trace-logs-in-application-insights"></a>Explorar os logs de rastreamento do .NET no Application Insights
Se você usar NLog, log4Net ou System.Diagnostics.Trace para o rastreamento de diagnóstico em seu aplicativo ASP.NET, os logs poderão ser enviados ao [Azure Application Insights][start], onde será possível explorá-los e pesquisá-los. Os logs serão mesclados à outra telemetria proveniente de seu aplicativo para que você possa identificar os rastreamentos associados ao atendimento de cada solicitação de usuário e correlacioná-los com outros relatórios de eventos e exceções.

> [!NOTE]
> Você precisa do módulo de captura de log? É um adaptador útil para agentes de terceiros, mas se você ainda não usa o NLog, log4Net ou System.Diagnostics.Trace, convém chamar apenas o [TrackTrace() do Application Insights](app-insights-api-custom-events-metrics.md#tracktrace) diretamente.
>
>

## <a name="install-logging-on-your-app"></a>Instalar o log no seu aplicativo
Instale a estrutura de registros escolhida no seu projeto. Isso deve resultar em uma entrada no app.config ou web.config.

Se você estiver usando System.Diagnostics.Trace, precisará adicionar uma entrada ao web.config:

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener"
             type="System.Diagnostics.TextWriterTraceListener"
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```
## <a name="configure-application-insights-to-collect-logs"></a>Configurar o Application Insights para coletar logs
**[Adicione o Application Insights ao seu projeto](app-insights-asp-net.md)** se ainda não tiver feito isso. Você verá uma opção para incluir o coletor de logs.

Ou então **Configure o Application Insights** clicando com o botão direito no seu projeto no Gerenciador de Soluções. Selecione a opção **Configurar a coleta de rastreamento**.

*Não consegue ver o menu do Application Insights nem a opção de coletor de logs?* Experimente [Solucionar problemas](#troubleshooting).

## <a name="manual-installation"></a>Instalação manual
Use este método se o tipo de projeto não tiver suporte no instalador do Application Insights (por exemplo, um projeto de Área de Trabalho do Windows).

1. Se você planeja usar o log4Net ou NLog, instale-o em seu projeto.
2. No Gerenciador de Soluções, clique com o botão direito do mouse no seu projeto e escolha **Gerenciar Pacotes NuGet**.
3. Pesquise “Application Insights”
4. Selecione o pacote apropriado entre:

   * Microsoft.ApplicationInsights.TraceListener (para capturar chamadas do System.Diagnostics.Trace)
   * Microsoft.ApplicationInsights.EventSourceListener (para capturar EventSource)
   * Microsoft.ApplicationInsights.EtwListener (para capturar eventos ETW)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

O pacote NuGet instala os assemblies necessários e também modifica o app.config ou web.config.

## <a name="insert-diagnostic-log-calls"></a>Inserir chamadas de log de diagnóstico
Se você usa System.Diagnostics.Trace, uma chamada típica é semelhante a:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se você preferir log4net ou NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>Usando eventos EventSource
É possível configurar eventos [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) para que eles sejam enviados para o Application Insights como rastreamentos. Primeiro, instale o pacote NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Depois, edite a seção `TelemetryModules` do arquivo [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

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
É possível configurar eventos [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) para que eles sejam enviados para o Application Insights como rastreamentos. Primeiro, instale o pacote NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Depois, edite a seção `TelemetryModules` do arquivo [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnsoticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada DiagnosticSource que você deseja rastrear, adicione uma entrada com o atributo `Name` definido como o nome do seu DiagnosticSource.

## <a name="using-etw-events"></a>Usando eventos ETW
É possível configurar os eventos ETW a serem enviados ao Application Insights como rastreamentos. Primeiro, instale o pacote NuGet `Microsoft.ApplicationInsights.EtwCollector`. Depois, edite a seção `TelemetryModules` do arquivo [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

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

![No Application Insights, escolha Pesquisar](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Pesquisa](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Por exemplo, você pode:

* Filtrar rastreamentos de log ou itens com propriedades específicas
* Inspecionar um item específico em detalhes.
* Localizar outra telemetria relacionada à mesma solicitação de usuário (ou seja, com o mesmo OperationId)
* Salvar a configuração dessa página como um favorito

> [!NOTE]
> **Amostragem.** Se o aplicativo enviar muitos dados e se você estiver usando o SDK do Application Insights para o ASP.NET versão 2.0.0-beta3 ou posterior, o recurso de amostragem adaptável poderá operar e enviar apenas uma porcentagem de sua telemetria. [Saiba mais sobre amostragem.](app-insights-sampling.md)
>
>

## <a name="next-steps"></a>Próximas etapas
[Diagnosticar falhas e exceções no ASP.NET][exceptions]

[Saiba mais sobre o Search][diagnostic].

## <a name="troubleshooting"></a>Solucionar problemas
### <a name="how-do-i-do-this-for-java"></a>Como faço isso no Java?
Use os [adaptadores de log Java](app-insights-java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Não há nenhuma opção do Application Insights no menu de contexto do projeto
* Verifique se as ferramentas do Application Insights estão instaladas neste computador de desenvolvimento. No menu Ferramentas do Visual Studio, em Extensões e Atualizações, procure pelas Ferramentas do Application Insights. Se não estiver na aba Instalado, abra a guia Online e instale-as.
* Este pode ser um tipo de projeto sem suporte pelas ferramentas do Application Insights. Use a [instalação manual](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Não há nenhuma opção de adaptador de log na ferramenta de configuração
* Você precisa instalar primeiro a estrutura de registros.
* Se estiver usando System.Diagnostics.Trace, verifique se você o [configurou no `web.config`](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Você tem a versão mais recente do Application Insights? No menu **Ferramentas** do Visual Studio, escolha **Extensões e Atualizações** e abra a guia **Atualizações**. Se as ferramentas de Análise do Desenvolvedor estiverem presentes, clique para atualizá-las.

### <a name="emptykey"></a>Recebo um erro "Chave de instrumentação não pode ser vazio"
Parece que você instalou o pacote de Nuget de adaptador para registro em log sem instalar o Application Insights.

No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Você obterá uma caixa de diálogo que solicita que você entre no Azure e crie um recurso de Application Insights, ou então reutilize um recurso existente. Isso deve corrigir o erro.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Posso ver rastreamentos na pesquisa de diagnóstico, mas não os outros eventos
Às vezes, pode levar algum tempo para que todos os eventos e solicitações percorram o pipeline.

### <a name="limits"></a>Que quantidade de dados é mantida?
Vários fatores afetam a quantidade de dados retidos. Veja a seção de [limites](app-insights-api-custom-events-metrics.md#limits) da página de métricas de eventos do cliente para obter mais informações. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Não estou vendo algumas das entradas de log que eu esperava
Se o aplicativo enviar muitos dados e se você estiver usando o SDK do Application Insights para o ASP.NET versão 2.0.0-beta3 ou posterior, o recurso de amostragem adaptável poderá operar e enviar apenas uma porcentagem de sua telemetria. [Saiba mais sobre amostragem.](app-insights-sampling.md)

## <a name="add"></a>Próximas etapas
* [Configurar testes de disponibilidade e capacidade de resposta][availability]
* [Solução de problemas][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
