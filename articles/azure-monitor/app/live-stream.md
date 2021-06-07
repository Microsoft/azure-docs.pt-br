---
title: Diagnosticar com informações de Aplicativo Azure de Live Metrics Stream
description: Monitore seu aplicativo Web em tempo real usando métrica personalizada e diagnostique problemas com um feed em tempo real de falhas, rastreamentos e eventos.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 865de94f1d9b4012a908643bbf87f38aeb8594a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98679459"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: monitorar e diagnosticar com latência de um segundo

Monitore seu aplicativo Web em produção em tempo real usando Live Metrics Stream (também conhecido como QuickPulse) de [Application insights](./app-insights-overview.md). Selecione e filtre os contadores de desempenho e as métricas para observar em tempo real, sem qualquer perturbação para o serviço. Inspecione os rastreamentos de pilha de exceções e solicitações de amostra com falha. Junto com o [criador de perfil](./profiler.md) e o depurador de [instantâneos](./snapshot-debugger.md), Live Metrics Stream fornece uma ferramenta de diagnóstico poderosa e não invasiva para seu site ativo.

Com o Live Metrics Stream, você pode:

* Valide uma correção enquanto ela é liberado, observando as contagens de falha e desempenho.
* Observe o efeito de cargas de teste e diagnostique problemas em tempo real.
* Concentre-se em sessões de teste específicas ou filtre os problemas conhecidos, selecionando e filtrando as métricas que você deseja inspecionar.
* Obter rastreamentos de exceção quando eles ocorrerem.
* Experimente os filtros para localizar os KPIs mais relevantes.
* Monitore qualquer contador de desempenho do Windows em tempo real.
* Identifique com facilidade um servidor que está apresentando problemas e filtre todo o KPI/feed em tempo real para apenas aquele servidor.

![Guia de métricas ao vivo](./media/live-stream/live-metric.png)

Atualmente, há suporte para métricas em tempo real para aplicativos ASP.NET, ASP.NET Core, Azure Functions, Java e Node.js.

## <a name="get-started"></a>Introdução

1. Siga as diretrizes específicas do idioma para habilitar as métricas em tempo real.
   * As métricas [ASP.net](./asp-net.md) -Live são habilitadas por padrão.
   * As métricas de [ASP.NET Core](./asp-net-core.md)ao vivo são habilitadas por padrão.
   * [.Net/.NET Core console/Worker](./worker-service.md)-as métricas ao vivo estão habilitadas por padrão.
   * [Aplicativos .net – habilite usando código](#enable-livemetrics-using-code-for-any-net-application).
    * As métricas [Java](./java-in-process-agent.md) -Live são habilitadas por padrão.
   * [Node.js](./nodejs.md#live-metrics)

2. No [Portal do Azure](https://portal.azure.com), abra o recurso Application Insights para o aplicativo e abra o Live Stream.

3. [Proteja o canal de controle](#secure-the-control-channel) se você puder usar dados confidenciais, como nomes de clientes, em seus filtros.

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>Habilitar LiveMetrics usando código para qualquer aplicativo .NET

Embora o LiveMetrics esteja habilitado por padrão ao realizar a integração usando instruções recomendadas para aplicativos .NET, o seguinte mostra como configurar manualmente as métricas ao vivo.

1. Instale o pacote NuGet [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)
2. O exemplo de código de aplicativo de console a seguir mostra a configuração de métricas dinâmicas.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

Embora o exemplo acima seja para um aplicativo de console, o mesmo código pode ser usado em qualquer aplicativo .NET. Se qualquer outro TelemetryModules estiver habilitado, o que coleta automaticamente a telemetria, é importante garantir que a mesma configuração usada para inicializar esses módulos também seja usada para o módulo de métricas ao vivo.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Como o Live Metrics Stream difere do Metrics Explorer e Analytics?

| |Live Stream | Metrics Explorer e Analytics |
|---|---|---|
|**Latência**|Dados exibidos em um segundo|Agregado ao longo de minutos|
|**Nenhuma retenção**|Os dados persistem enquanto estão no gráfico e depois são descartados|[Dados retidos por 90 dias](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**Sob Demanda**|Os dados só são transmitidos enquanto o painel de métricas ao vivo está aberto |Os dados são enviados sempre que o SDK está instalado e habilitado|
|**Gratuito**|Não há nenhum custo para dados do Live Stream|Sujeito a [preços](./pricing.md)
|**Amostragem**|Todas as métricas e os contadores selecionados são transmitidos. Há amostras de falhas e rastreamentos de pilha. |Os eventos podem ter [amostras](./api-filtering-sampling.md)|
|**Canal de controle**|Os sinais de controle de filtro são enviados ao SDK. Recomendamos que você proteja este canal.|A comunicação é de uma maneira para o portal|

## <a name="select-and-filter-your-metrics"></a>Selecionar e filtrar suas métricas

(Disponível com o ASP.NET, ASP.NET Core e Azure Functions (v2).)

Você pode monitorar o KPI personalizado em tempo real aplicando filtros arbitrários a qualquer Application Insights Telemetry no portal. Clique no controle de filtro que é exibido quando você passa o mouse sobre qualquer um dos gráficos. O gráfico a seguir plota um KPI personalizado de contagem de solicitações com filtros nos atributos de URL e a duração. Valide seus filtros com a seção Versão Prévia do Fluxo, que mostra um feed em tempo real da telemetria que corresponde aos critérios que você especificou em qualquer ponto no tempo.

![Taxa de solicitação de filtro](./media/live-stream/filter-request.png)

Você pode monitorar um valor diferente da Contagem. As opções dependem do tipo de fluxo, que poderia ser qualquer Application Insights Telemetry: solicitações, dependências, exceções, rastreamentos, eventos ou métricas. Ela pode ser sua própria [medida personalizada](./api-custom-events-metrics.md#properties):

![Construtor de consultas na taxa de solicitação com métrica personalizada](./media/live-stream/query-builder-request.png)

Além da Application Insights Telemetry, você também pode monitorar qualquer contador de desempenho do Windows selecionando entre as opções de fluxo e fornecendo o nome do contador de desempenho.

Métricas em tempo real são agregadas em dois pontos: localmente em cada servidor e, em seguida, em todos os servidores. Você pode alterar o padrão de ambos selecionando outras opções nos respectivos menus suspensos.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetria de exemplo: eventos de diagnóstico em tempo real personalizados
Por padrão, o feed de eventos em tempo real mostra exemplos de solicitações com falha e chamadas de dependência, exceções, eventos e rastreamentos. Clique no ícone do filtro para ver os critérios aplicados em qualquer ponto no tempo.

![Botão Filtrar](./media/live-stream/filter.png)

Assim como acontece com as métricas, você pode especificar qualquer critério arbitrário para qualquer um dos tipos de Application Insights Telemetry. Neste exemplo, estamos selecionando falhas de solicitação específicas e eventos.

![Construtor de Consultas](./media/live-stream/query-builder.png)

> [!NOTE]
> No momento, para critérios baseados em mensagens de exceção, use a mensagem de exceção mais externa. No exemplo anterior, para filtrar a exceção benigna com a mensagem de exceção interna (segue o delimitador "< –") "O cliente se desconectou.", use um critério de que a mensagem não contém "Erro ao ler o conteúdo da solicitação".

Consulte os detalhes de um item no feed em tempo real clicando nele. Você pode pausar o feed clicando em **Pausar** ou simplesmente rolando para baixo ou clicando em um item. O feed em tempo real será retomado quando você rolar de volta para o início ou clicando no contador de itens coletados enquanto ele estava em pausa.

![Captura de tela mostra a janela de telemetria de exemplo com uma exceção selecionada e os detalhes da exceção exibidos na parte inferior da janela.](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Filtrar por instância do servidor

Se você quiser monitorar uma instância de função de servidor específico, filtre por servidor. Para filtrar, selecione o nome do servidor em *servidores*.

![Amostra de falhas em tempo real](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Proteger o canal de controle

> [!NOTE]
> Atualmente, você só pode configurar um canal autenticado usando o monitoramento baseado em código e não pode autenticar servidores usando a anexação sem código.

Os critérios de filtros personalizados que você especificar no portal de métricas dinâmicas são enviados de volta para o componente de métricas em tempo real no SDK do Application Insights. Os filtros podem conter informações confidenciais, como customerIDs. Você pode proteger o canal com uma chave de API secreta além da chave de instrumentação.

### <a name="create-an-api-key"></a>Criar uma chave de API

![Chave de API > criar chave de API ](./media/live-stream/api-key.png)
 ![ criar guia de chave de API. Selecione "autenticar canal de controle SDK" e "gerar chave"](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>Adicionar chave de API à configuração

### <a name="aspnet"></a>ASP.NET

No arquivo applicationinsights.config, adicione AuthenticationApiKey a QuickPulseTelemetryModule:

```XML
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

Para [ASP.NET Core](./asp-net-core.md) aplicativos, siga as instruções abaixo.

Modifique `ConfigureServices` o arquivo Startup. cs da seguinte maneira:

Adicione o namespace a seguir.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Em seguida, modifique `ConfigureServices` o método como mostrado abaixo.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

Mais informações sobre como configurar aplicativos ASP.NET Core podem ser encontradas em nossas diretrizes sobre a [configuração de módulos de telemetria no ASP.NET Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules).

### <a name="workerservice"></a>WorkerService

Para aplicativos [WorkerService](./worker-service.md) , siga as instruções abaixo.

Adicione o namespace a seguir.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Em seguida, adicione a linha a seguir antes da chamada `services.AddApplicationInsightsTelemetryWorkerService` .

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Mais informações sobre como configurar aplicativos WorkerService podem ser encontradas em nossas diretrizes sobre a [configuração de módulos de telemetria em workerservices](./worker-service.md#configuring-or-removing-default-telemetrymodules).

### <a name="azure-function-apps"></a>Aplicativos de Funções do Azure

Para os aplicativos de funções do Azure (v2), a proteção do canal com uma chave de API pode ser realizada com uma variável de ambiente.

Crie uma chave de API de dentro de seu Application Insights recurso e acesse **configurações > configuração** para seu aplicativo de funções. Selecione **nova configuração de aplicativo** e insira um nome `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` e um valor que corresponda à sua chave de API.

No entanto, caso reconheça e confie em todos os servidores conectados, você pode testar os filtros personalizados sem o canal autenticado. Essa opção está disponível por seis meses. Essa substituição é necessária uma vez a cada nova sessão ou quando um novo servidor ficar online.

![Opções de autenticação das métricas em tempo real](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>É altamente recomendável que você configure o canal autenticado antes de inserir informações potencialmente confidenciais, como CustomerIDs, nos critérios de filtro.
>

## <a name="supported-features-table"></a>Tabela de recursos com suporte

| Idioma                         | Métricas básicas       | Métricas de desempenho | Filtragem personalizada    | Telemetria de exemplo    | Divisão de CPU por processo |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET Framework                   | Com suporte (V 2.7.2 +) | Com suporte (V 2.7.2 +) | Com suporte (V 2.7.2 +) | Com suporte (V 2.7.2 +) | Com suporte (V 2.7.2 +)  |
| .NET Core (Target =. NET Framework)| Com suporte (V 2.4.1 +) | Com suporte (V 2.4.1 +) | Com suporte (V 2.4.1 +) | Com suporte (V 2.4.1 +) | Com suporte (V 2.4.1 +)  |
| .NET Core (Target =. NET Core)     | Com suporte (V 2.4.1 +) | Com suporte*          | Com suporte (V 2.4.1 +) | Com suporte (V 2.4.1 +) | **Sem suporte**    |
| Azure Functions v2               | Com suporte           | Com suporte           | Com suporte           | Com suporte           | **Sem suporte**    |
| Java                             | Com suporte (V 2.0.0 +) | Com suporte (V 2.0.0 +) | **Sem suporte**   | **Sem suporte**   | **Sem suporte**    |
| Node.js                          | Com suporte (V 1.3.0 +) | Com suporte (V 1.3.0 +) | **Sem suporte**   | Com suporte (V 1.3.0 +) | **Sem suporte**    |

As métricas básicas incluem solicitação, dependência e taxa de exceção. As métricas de desempenho (contadores de desempenho) incluem memória e CPU. A telemetria de exemplo mostra um fluxo de informações detalhadas para solicitações e dependências com falha, exceções, eventos e rastreamentos.

 \* O suporte a PerfCounters varia ligeiramente entre as versões do .NET Core que não têm como alvo o .NET Framework:

- Há suporte para métricas PerfCounters ao executar no serviço Azure App para Windows. (AspNetCore SDK versão 2.4.1 ou superior)
- PerfCounters têm suporte quando o aplicativo está em execução em qualquer computador Windows (VM ou serviço de nuvem ou local, etc.) (AspNetCore SDK versão 2.7.1 ou superior), mas para aplicativos destinados ao .NET Core 2,0 ou superior.
- Há suporte para PerfCounters quando o aplicativo está em execução em qualquer lugar (Linux, Windows, serviço de aplicativo para Linux, contêineres, etc.) nas versões mais recentes (ou seja, AspNetCore SDK versão 2.8.0 ou superior), mas somente para aplicativos destinados ao .NET Core 2,0 ou superior.

## <a name="troubleshooting"></a>Solução de problemas

Live Metrics Stream usa endereços IP diferentes dos outros Application Insights telemetria. Certifique-se de que [esses endereços IP](./ip-addresses.md) estejam abertos em seu firewall. Verifique também se as [portas de saída para Live Metrics Stream](./ip-addresses.md#outgoing-ports) estão abertas no firewall dos servidores.

## <a name="next-steps"></a>Próximas etapas

* [Monitorando o uso com o Application Insights](./usage-overview.md)
* [Usando a Pesquisa de diagnóstico](./diagnostic-search.md)
* [Profiler](./profiler.md)
* [Depurador instantâneo](./snapshot-debugger.md)