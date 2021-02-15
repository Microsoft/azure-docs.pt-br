---
title: Explorar os logs de rastreamento do .NET no Application Insights
description: Pesquise logs gerados por Trace, NLog ou Log4Net.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/08/2019
ms.openlocfilehash: 083ddbd06561550f89e414d6c679cdc6433fa338
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937563"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Explorar os logs de rastreamento do .NET/.NET Core e do Python no Application Insights

Envie logs de rastreamento de diagnóstico referentes ao seu aplicativo ASP.NET/ASP.NET Core do ILogger, NLog, log4Net ou System.Diagnostics.Trace para o [Azure Application Insights][start]. Para aplicativos em Python, envie logs de rastreamento de diagnóstico usando o AzureLogHandler em OpenCensus Python para o Azure Monitor. Em seguida, você pode explorá-los e pesquisá-los. Esses logs são mesclados a outros arquivos de log do seu aplicativo, de modo que você pode identificar os rastreamentos associados a cada solicitação de usuário e correlacioná-los com outros relatórios de eventos e exceções.

> [!NOTE]
> Você precisa do módulo de captura de log? Ele é um adaptador útil para agentes de terceiros. No entanto, se você ainda não usa o NLog, o log4Net nem o System.Diagnostics.Trace, convém chamar apenas o [**Application Insights TrackTrace()**](./api-custom-events-metrics.md#tracktrace) diretamente.
>
>
## <a name="install-logging-on-your-app"></a>Instalar o log no seu aplicativo
Instale sua estrutura de registros escolhida em seu projeto, o que deve resultar em uma entrada em app.config ou web.config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Configurar o Application Insights para coletar logs
[Adicione o Application Insights ao seu projeto](./asp-net.md) se ainda não tiver feito isso. Você verá uma opção para incluir o coletor de logs.

Ou então, clique com o botão direito do mouse no seu projeto no Gerenciador de Soluções para **Configurar o Application Insights**. Selecione a opção **Configurar a coleta de rastreamento**.

> [!NOTE]
> Não consegue ver o menu do Application Insights nem a opção de coletor de logs? Experimente [Solucionar problemas](#troubleshooting).

## <a name="manual-installation"></a>Instalação manual
Use este método se o tipo de projeto não tiver suporte no instalador do Application Insights (por exemplo, um projeto de Área de Trabalho do Windows).

1. Se você planeja usar o log4Net ou NLog, instale-o em seu projeto.
2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet**.
3. Pesquise por "Application Insights".
4. Selecione um dos seguintes pacotes:

   - Para ILogger: [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/) 
 [ ![ NuGet ILogger banner](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Para NLog: [Microsoft. ApplicationInsights. NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/) 
 [ ![ NuGet NLog banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Para log4net: [Microsoft. ApplicationInsights. Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/) 
 [ ![ NuGet log4net banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Para System. Diagnostics: [Microsoft. ApplicationInsights. TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) 
 [ ![ NuGet System. Diagnostic banner](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft. ApplicationInsights. DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) 
 [ ![ Faixa de ouvinte de origem de diagnóstico do NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) 
 [ ![ Faixa do coletor ETW do NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) 
 [ ![ Faixa de ouvinte de origem do evento NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

O pacote NuGet instala os assemblies necessários e, quando aplicável, modifica o web.config ou o app.config.

## <a name="ilogger"></a>ILogger

Para obter exemplos de como usar a implementação de ILogger do Application Insights com aplicativos Console e ASP.NET Core, confira [ApplicationInsightsLoggerProvider para logs do ILogger do .NET Core](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Inserir chamadas de log de diagnóstico
Se você usa System.Diagnostics.Trace, uma chamada típica é semelhante a:

```csharp
System.Diagnostics.Trace.TraceWarning("Slow response - database01");
```

Se você preferir log4net ou NLog, use:

```csharp
    logger.Warn("Slow response - database01");
```

## <a name="use-eventsource-events"></a>Usar eventos EventSource
É possível configurar eventos [System.Diagnostics.Tracing.EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) para que eles sejam enviados para o Application Insights como rastreamentos. Primeiro, instale o pacote NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Depois, edite a seção `TelemetryModules` do arquivo [ApplicationInsights.config](./configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, você pode definir os seguintes parâmetros:
 * **Nome** especifica o nome do EventSource a ser coletado.
 * **Nível** especifica o nível de registros em log a ser coletado: *Crítico*, *Erro*, *Informativo*, *LogAlways*, *Detalhado* ou *Aviso*.
 * **Palavras-chave** (opcional) especificam o valor inteiro das combinações de palavras-chave a serem usadas.

## <a name="use-diagnosticsource-events"></a>Usar eventos DiagnosticSource
É possível configurar eventos [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) para que eles sejam enviados para o Application Insights como rastreamentos. Primeiro, instale o pacote NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Em seguida, edite a seção "TelemetryModules" do arquivo [ApplicationInsights.config](./configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada DiagnosticSource que você deseja rastrear, adicione uma entrada com o atributo **Nome** definido como o nome do seu DiagnosticSource.

## <a name="use-etw-events"></a>Usar eventos ETW
É possível configurar os eventos ETW (Rastreamento de Eventos para Windows) a serem enviados ao Application Insights como rastreamentos. Primeiro, instale o pacote NuGet `Microsoft.ApplicationInsights.EtwCollector`. Em seguida, edite a seção "TelemetryModules" do arquivo [ApplicationInsights.config](./configuration-with-applicationinsights-config.md).

> [!NOTE] 
> Eventos ETW só poderão ser coletados se o processo que hospeda o SDK estiver em execução em uma identidade que seja membro dos administradores ou usuários de log de desempenho.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, você pode definir os seguintes parâmetros:
 * **ProviderName** é o nome do provedor de ETW a ser coletado.
 * **ProviderGuid** especifica o GUID do provedor de ETW a ser coletado. Ele poderá ser usado em lugar de `ProviderName`.
 * **Nível** define o nível de registros em log a ser coletado. Ele pode ser *Crítico*, *Erro*, *Informativo*, *LogAlways*, *Detalhado* ou *Aviso*.
 * **Palavras-chave** (opcional) definem o valor inteiro das combinações de palavras-chave a serem usadas.

## <a name="use-the-trace-api-directly"></a>Usar a API de rastreamento diretamente
Você pode chamar a API de rastreamento do Application Insights diretamente. Os adaptadores de log usam essa API.

Por exemplo:

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
var telemetryClient = new TelemetryClient(configuration);
telemetry.TrackTrace("Slow response - database01");
```

Uma vantagem de TrackTrace é que você pode colocar dados relativamente compridos na mensagem. Por exemplo, você pode codificar dados POST.

Você também pode adicionar um nível de severidade à mensagem. E, como ocorre com outros casos de telemetria, você pode adicionar valores de propriedade para ajudar a filtrar ou a pesquisar diferentes conjuntos de rastreamentos. Por exemplo:

  ```csharp
  TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
  var telemetryClient = new TelemetryClient(configuration);
  telemetryClient.TrackTrace("Slow database response",
                              SeverityLevel.Warning,
                              new Dictionary<string, string> { { "database", "db.ID" } });
  ```

Isso permite que você filtre facilmente, em [Pesquisa][diagnostic], todas as mensagens de um nível de severidade específico relacionadas a um determinado banco de dados.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler para OpenCensus Python
O Manipulador de Log do Azure Monitor permite exportar logs do Python para o Azure Monitor.

Instrumente o aplicativo com o [SDK do OpenCensus Python](./opencensus-python.md) para o Azure Monitor.

Este exemplo mostra como enviar um log de nível de aviso para Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Explorar seus logs
Execute o aplicativo no modo de depuração ou implante-o em tempo real.

No painel de visão geral do aplicativo, no [portal do Application Insights][portal], escolha [Pesquisa][diagnostic].

Por exemplo, você pode:

* Filtre rastreamentos de log ou itens com propriedades específicas.
* Inspecionar um item específico em detalhes.
* Encontre outros dados de log do sistema relacionados à mesma solicitação de usuário (tem a mesma OperationId).
* Salve a configuração de uma página como um favorito.

> [!NOTE]
>Se o aplicativo enviar muitos dados e se você estiver usando o SDK do Application Insights para o ASP.NET versão 2.0.0-beta3 ou posterior, o recurso de *amostragem adaptável* poderá operar e enviar apenas uma parte de sua telemetria. [Saiba mais sobre amostragem.](./sampling.md)
>

## <a name="troubleshooting"></a>Solução de problemas
### <a name="how-do-i-do-this-for-java"></a>Como faço isso no Java?
Em instrumentação sem código Java (recomendado), os logs são coletados prontos para o uso. Use o [agente do Java 3.0](./java-in-process-agent.md).

Se você estiver usando o SDK do Java, use os [adaptadores de log Java](./java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Não há nenhuma opção do Application Insights no menu de contexto do projeto
* Verifique se o Developer Analytics Tools está instalado no computador de desenvolvimento. Em **Ferramentas** > **Extensões e Atualizações** no Visual Studio, procure o **Developer Analytics Tools**. Se ele não estiver na aba **Instalado**, abra a guia **Online** e instale-o.
* Esse pode ser um tipo de projeto não compatível com o Developer Analytics Tools. Use a [instalação manual](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Não há nenhuma opção de adaptador de log na ferramenta de configuração
* Instale primeiro a estrutura de registros.
* Se estiver usando System.Diagnostics.Trace, verifique se você o [configurou no *web.config*](/dotnet/api/system.diagnostics.eventlogtracelistener).
* Verifique se você tem a versão mais recente do Application Insights. No Visual Studio, acesse **Ferramentas** > **Extensões e Atualizações** e abra a guia **Atualizações**. Se o **Developer Analytics Tools** estiver presente, selecione-o para atualizá-lo.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Recebo a mensagem de erro "A chave de instrumentação não pode estar vazia"
Você provavelmente instalou o pacote NuGet do adaptador de log sem instalar Application Insights. No Gerenciador de Soluções, clique com o botão direito do mouse em *ApplicationInsights.config* e selecione **Atualizar Application Insights**. Você receberá uma solicitação para entrar no Azure e criar um recurso do Application Insights ou, alternativamente, reutilizar um recurso existente. Isso deve corrigir o problema.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Posso ver rastreamentos na pesquisa de diagnóstico, mas não os outros eventos
Pode levar algum tempo para que todos os eventos e solicitações percorram o pipeline.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Que quantidade de dados é mantida?
Vários fatores afetam a quantidade dos dados retidos. Para obter mais informações, confira a seção de [limites](./api-custom-events-metrics.md#limits) da página de métricas de eventos do cliente.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Não vejo algumas entradas de log que eu esperava
Se o aplicativo enviar um volume muito grande de dados e se você estiver usando o SDK do Application Insights para o ASP.NET versão 2.0.0-beta3 ou posterior, o recurso de amostragem adaptável poderá operar e enviar apenas uma parte de sua telemetria. [Saiba mais sobre amostragem.](./sampling.md)

## <a name="next-steps"></a><a name="add"></a>Próximas etapas

* [Diagnosticar falhas e exceções no ASP.NET][exceptions]
* [Saber mais sobre o Search][diagnostic]
* [Configurar testes de disponibilidade e capacidade de resposta][availability]
* [Solução de problemas][qna]

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[start]: ./app-insights-overview.md

