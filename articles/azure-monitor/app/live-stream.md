---
title: Live Metrics Stream com métricas personalizadas e diagnósticos no Azure Application Insights | Microsoft Docs
description: Monitore seu aplicativo Web em tempo real usando métrica personalizada e diagnostique problemas com um feed em tempo real de falhas, rastreamentos e eventos.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: f8203cade1d2e34a9852e945df03dc2fddc1fbe5
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359423"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: Monitorar e diagnosticar com latência de um segundo

Teste a pulsação do aplicativo Web ao vivo em produção usando o Live Metrics Stream do [Application Insights](../../azure-monitor/app/app-insights-overview.md). Selecione e filtre os contadores de desempenho e as métricas para observar em tempo real, sem qualquer perturbação para o serviço. Inspecione os rastreamentos de pilha de exceções e solicitações de amostra com falha. Junto com o [criador de perfil](../../azure-monitor/app/profiler.md), depurador de [instantâneos](../../azure-monitor/app/snapshot-debugger.md). O Live Metrics Stream fornece uma ferramenta de diagnóstico poderosa e não invasiva para seu site da Web em tempo real.

Com o Live Metrics Stream, você pode:

* Valide uma correção enquanto ela é liberado, observando as contagens de falha e desempenho.
* Observe o efeito de cargas de teste e diagnostique problemas em tempo real. 
* Concentre-se em sessões de teste específicas ou filtre os problemas conhecidos, selecionando e filtrando as métricas que você deseja inspecionar.
* Obter rastreamentos de exceção quando eles ocorrerem.
* Experimente os filtros para localizar os KPIs mais relevantes.
* Monitore qualquer contador de desempenho do Windows em tempo real.
* Identifique com facilidade um servidor que está apresentando problemas e filtre todo o KPI/feed em tempo real para apenas aquele servidor.

[![Vídeo do Live Metrics Stream](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Atualmente, há suporte para métricas em tempo real para aplicativos ASP.NET, ASP.NET Core, Azure Functions, Java e node. js.

## <a name="get-started"></a>Introdução

1. Se você ainda precisa [instalar o Application Insights](../../azure-monitor/azure-monitor-app-hub.md) em seu aplicativo web, faça isso agora.
2. Além dos pacotes padrão do Application Insights, [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) é necessário para habilitar o Live Metrics Stream.
3. **Atualização para a última versão** do pacote do Application Insights. No Visual Studio, clique com o botão direito do mouse em seu projeto e escolha **Gerenciar pacotes Nuget**. Abra a guia **Atualizações** e selecione todos os pacotes Microsoft.ApplicationInsights.*.

    Reimplante o aplicativo.

3. No [Portal do Azure](https://portal.azure.com), abra o recurso Application Insights para o aplicativo e abra o Live Stream.

4. [Proteja o canal de controle](#secure-the-control-channel) se você puder usar dados confidenciais, como nomes de clientes, em seus filtros.

### <a name="nodejs"></a>Node.js

Para usar métricas em tempo real com node. js, você deve atualizar para a versão 1,30 ou superior do SDK. Por padrão, as métricas ao vivo são desabilitadas no SDK do node. js. Para habilitar as métricas em `setSendLiveMetrics(true)` tempo real, adicione aos seus [métodos de configuração](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) à medida que você inicializar o SDK.

### <a name="no-data-check-your-server-firewall"></a>Não há dados? Verificar o firewall de servidor

Verifique se as [portas de saída para o Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) estão abertas no firewall dos servidores. 

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Como o Live Metrics Stream difere do Metrics Explorer e Analytics?

| |Live Stream | Metrics Explorer e Analytics |
|---|---|---|
|Latência|Dados exibidos em um segundo|Agregado ao longo de minutos|
|Nenhuma retenção|Os dados persistem enquanto estão no gráfico e depois são descartados|[Dados retidos por 90 dias](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|Sob demanda|Os dados são transmitidos enquanto você abre o Live Metrics|Os dados são enviados sempre que o SDK está instalado e habilitado|
|Gratuito|Não há nenhum custo para dados do Live Stream|Sujeito a [preços](../../azure-monitor/app/pricing.md)
|amostragem|Todas as métricas e os contadores selecionados são transmitidos. Há amostras de falhas e rastreamentos de pilha. TelemetryProcessors não são aplicados.|Os eventos podem ter [amostras](../../azure-monitor/app/api-filtering-sampling.md)|
|Canal de controle|Os sinais de controle de filtro são enviados ao SDK. Recomendamos que você proteja este canal.|A comunicação é unidirecional para o portal|

## <a name="select-and-filter-your-metrics"></a>Selecionar e filtrar suas métricas

(Disponível com o ASP.NET, ASP.NET Core e Azure Functions (v2).)

Você pode monitorar o KPI personalizado em tempo real aplicando filtros arbitrários a qualquer Application Insights Telemetry no portal. Clique no controle de filtro que é exibido quando você passa o mouse sobre qualquer um dos gráficos. O gráfico a seguir plota um KPI personalizado de contagem de solicitações com filtros nos atributos de URL e a duração. Valide seus filtros com a seção Versão Prévia do Fluxo, que mostra um feed em tempo real da telemetria que corresponde aos critérios que você especificou em qualquer ponto no tempo. 

![KPI de solicitação personalizado](./media/live-stream/live-stream-filteredMetric.png)

Você pode monitorar um valor diferente da Contagem. As opções dependem do tipo de fluxo, que poderia ser qualquer Application Insights Telemetry: solicitações, dependências, exceções, rastreamentos, eventos ou métricas. Ela pode ser sua própria [medida personalizada](../../azure-monitor/app/api-custom-events-metrics.md#properties):

![Opções de valor](./media/live-stream/live-stream-valueoptions.png)

Além da Application Insights Telemetry, você também pode monitorar qualquer contador de desempenho do Windows selecionando entre as opções de fluxo e fornecendo o nome do contador de desempenho.

Métricas em tempo real são agregadas em dois pontos: localmente em cada servidor e, em seguida, em todos os servidores. Você pode alterar o padrão de ambos selecionando outras opções nos respectivos menus suspensos.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetria de Exemplo: Eventos de Diagnóstico do Azure em Tempo Real Personalizado
Por padrão, o feed de eventos em tempo real mostra exemplos de solicitações com falha e chamadas de dependência, exceções, eventos e rastreamentos. Clique no ícone do filtro para ver os critérios aplicados em qualquer ponto no tempo. 

![Feed em tempo real padrão](./media/live-stream/live-stream-eventsdefault.png)

Assim como acontece com as métricas, você pode especificar qualquer critério arbitrário para qualquer um dos tipos de Application Insights Telemetry. Neste exemplo, estamos selecionando falhas, rastreamentos e eventos de solicitação específicos. Também estamos selecionando todas as exceções e falhas de dependência.

![Feed em tempo real personalizado](./media/live-stream/live-stream-events.png)

Observação: No momento, para critérios baseados em mensagens de exceção, use a mensagem de exceção mais externa. No exemplo anterior, para filtrar a exceção benigna com a mensagem de exceção interna (segue o delimitador "< –") "O cliente se desconectou.", use um critério de que a mensagem não contém "Erro ao ler o conteúdo da solicitação".

Consulte os detalhes de um item no feed em tempo real clicando nele. Você pode pausar o feed clicando em **Pausar** ou simplesmente rolando para baixo ou clicando em um item. O feed em tempo real será retomado quando você rolar de volta para o início ou clicando no contador de itens coletados enquanto ele estava em pausa.

![Amostra de falhas em tempo real](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrar por instância do servidor

Se você quiser monitorar uma instância de função de servidor específico, filtre por servidor.

![Amostra de falhas em tempo real](./media/live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Requisitos do SDK
O Fluxo de métricas em tempo real personalizado está disponível com a versão 2.4.0-beta2 ou mais recente do [SDK do Application Insights para Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Lembre-se de selecionar a opção "Incluir pré-lançamento" no gerenciador de pacotes do NuGet.

## <a name="secure-the-control-channel"></a>Proteger o canal de controle
Os critérios de filtro personalizados especificados são enviados para o componente de Métricas em tempo real no SDK do Application Insights. Os filtros podem conter informações confidenciais, como customerIDs. Você pode proteger o canal com uma chave de API secreta além da chave de instrumentação.
### <a name="create-an-api-key"></a>Criar uma chave de API

![Criar chave de API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Adicionar chave de API à configuração

### <a name="classic-aspnet"></a>ASP.NET clássico

No arquivo applicationinsights.config, adicione AuthenticationApiKey a QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Ou, no código, defina-a no QuickPulseTelemetryModule:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Aplicativos de Funções do Azure

Para que os aplicativos de função do Azure (v2) protejam o canal com uma API chave, pode-se fazer isso com uma variável de ambiente. 

Crie uma chave de API de dentro de seu recurso do Application Insights e vá para **Configurações do aplicativo** de seu Aplicativo de funções. Selecione **adicionar nova configuração** e insira um nome de `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` e um valor que corresponda à sua chave de API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (requer o Application Insights ASP.NET Core SDK 2.3.0-beta ou superior)

Modifique o arquivo startup.cs da seguinte forma:

Primeiro adicione

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

No método ConfigureServices, adicione:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

No entanto, caso reconheça e confie em todos os servidores conectados, você pode testar os filtros personalizados sem o canal autenticado. Essa opção está disponível por seis meses. Essa substituição é necessária uma vez a cada nova sessão ou quando um novo servidor ficar online.

![Opções de autenticação das métricas em tempo real](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>É altamente recomendável que você configure o canal autenticado antes de inserir informações potencialmente confidenciais, como CustomerIDs, nos critérios de filtro.
>

## <a name="troubleshooting"></a>Solução de problemas

Não há dados? Se seu aplicativo estiver em uma rede protegida: O Live Metrics Stream usa um endereço IP diferente do usado pela telemetria do Application Insights. Certifique-se de que [esses endereços IP](../../azure-monitor/app/ip-addresses.md) estejam abertos em seu firewall.



## <a name="next-steps"></a>Próximas etapas
* [Monitorando o uso com o Application Insights](../../azure-monitor/app/usage-overview.md)
* [Usando a Pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Criador de perfil](../../azure-monitor/app/profiler.md)
* [Depurador instantâneo](../../azure-monitor/app/snapshot-debugger.md)
