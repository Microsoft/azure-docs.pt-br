---
title: Métricas baseadas em log do Aplicativo Azure insights | Microsoft Docs
description: Este artigo lista Aplicativo Azure métricas de informações com agregações e dimensões com suporte. Os detalhes sobre as métricas baseadas em log incluem as instruções de consulta Kusto subjacentes.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: d4b7a214af23d69f1217d84e9401de230cd358b0
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877357"
---
# <a name="application-insights-log-based-metrics"></a>Application Insights métricas baseadas em log

Application Insights métricas baseadas em log permitem analisar a integridade de seus aplicativos monitorados, criar painéis avançados e configurar alertas. Há dois tipos de métricas:

* As [métricas baseadas em log](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) por trás da cena são convertidas em [consultas Kusto](https://docs.microsoft.com/azure/kusto/query/) de eventos armazenados.
* As [métricas padrão](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) são armazenadas como uma série de tempo agregado previamente.

Como as *métricas padrão* são previamente agregadas durante a coleta, elas têm melhor desempenho no momento da consulta. Isso os torna uma opção melhor para o painel e alertas em tempo real. As *métricas baseadas em log* têm mais dimensões, o que as torna a opção superior para análise de dados e diagnóstico ad hoc. Use o [seletor de namespace](metrics-getting-started.md#create-your-first-metric-chart) para alternar entre as métricas padrão e baseadas em log no [Metrics Explorer](metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Interpretar e usar consultas deste artigo

Este artigo lista as métricas com as dimensões e agregações com suporte. Os detalhes sobre as métricas baseadas em log incluem as instruções de consulta Kusto subjacentes. Para sua conveniência, cada consulta usa padrões para granularidade de tempo, tipo de gráfico e, às vezes, dividindo a dimensão, o que simplifica o uso da consulta em Log Analytics sem necessidade de modificação.

Quando você plota a mesma métrica no [Metrics Explorer](metrics-getting-started.md), não há padrões-a consulta é ajustada dinamicamente com base nas configurações do seu gráfico:

- O **intervalo de tempo** selecionado é convertido em uma cláusula WHERE de *carimbo de data/* hora adicional para selecionar apenas os eventos do intervalo de tempo selecionado. Por exemplo, um gráfico mostrando dados para as 24 horas mais recentes, a consulta inclui *| onde o carimbo de data/hora > atrás (24 h)* .

- A **granularidade de tempo** selecionada é colocada no resumo final *... na cláusula bin (timestamp, [time refinable])* .

- Todas as dimensões de **filtro** selecionadas são convertidas em cláusulas *Where* adicionais.

- A dimensão de **gráfico dividido** selecionada é convertida em uma propriedade resumida extra. Por exemplo, se você dividir o gráfico por *local*e plotar usando uma granularidade de tempo de 5 minutos, a  cláusula resumete será resumida *... por compartimento (carimbo de data/hora, 5 m), local*.

> [!NOTE]
> Se você for novo na linguagem de consulta do Kusto, comece copiando e colando instruções Kusto no painel de consulta Log Analytics sem fazer nenhuma modificação. Clique em **executar** para ver o gráfico básico. Ao começar a entender a sintaxe da linguagem de consulta, você pode começar a fazer pequenas modificações e ver o impacto de sua alteração. Explorar seus próprios dados é uma ótima maneira de começar a concretizar todo o poder da [log Analytics](../../azure-monitor/log-query/get-started-portal.md) e [Azure monitor](../../azure-monitor/overview.md).

## <a name="availability-metrics"></a>Métricas de disponibilidade

As métricas na categoria disponibilidade permitem que você veja a integridade do seu aplicativo Web, conforme observado em pontos em todo o mundo. [Configure os testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para começar a usar qualquer métrica dessa categoria.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Disponibilidade (availabilityResults/availabilityPercentage)
A métrica de *disponibilidade* mostra a porcentagem de execuções de teste na Web que não detectaram nenhum problema. O menor valor possível é 0, o que indica que todas as execuções de teste na Web falharam. O valor de 100 significa que todas as execuções de testes na Web passaram pelos critérios de validação.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|---|---|---|
|Percentual|Average|Local de execução, nome do teste|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Duração do teste de disponibilidade (availabilityResults/Duration)

A métrica *duração do teste de disponibilidade* mostra quanto tempo levou para o teste na Web ser executado. Para os [testes da Web de várias etapas](../../azure-monitor/app/availability-multistep.md), a métrica reflete o tempo de execução total de todas as etapas.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|---|---|---|
|Milissegundos|Média, mín., máx.|Local de execução, nome do teste, resultado do teste

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Testes de disponibilidade (availabilityResults/Count)

A métrica de *testes de disponibilidade* reflete a contagem dos testes da Web executados por Azure monitor.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|---|---|---|
|Contagem|Count|Local de execução, nome do teste, resultado do teste|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Métricas do navegador

As métricas do navegador são coletadas pelo SDK Application Insights JavaScript de navegadores de usuários finais reais. Eles fornecem ótimos percepções sobre a experiência dos seus usuários com seu aplicativo Web. As métricas de navegador normalmente não são amostradas, o que significa que elas fornecem maior precisão dos números de uso em comparação com as métricas do lado do servidor que podem ser distorcidas por amostragem.

> [!NOTE]
> Para coletar métricas do navegador, seu aplicativo deve ser instrumentado com o [trecho de Application insights SDK do JavaScript](../../azure-monitor/app/javascript.md#add-the-sdk-script-to-your-app-or-web-pages).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Tempo de carregamento de página do navegador (browserTimings/totalDuration)

|Unidade de medida|Agregações com suporte|Dimensões previamente agregadas|
|---|---|---|
|Milissegundos|Média, mín., máx.|Nenhum|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Tempo de processamento do cliente (browserTiming/processingDuration)

|Unidade de medida|Agregações com suporte|Dimensões previamente agregadas|
|---|---|---|
|Milissegundos|Média, mín., máx.|Nenhum|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Tempo de conexão de rede de carregamento de página (browserTimings/networkDuration)

|Unidade de medida|Agregações com suporte|Dimensões previamente agregadas|
|---|---|---|
|Milissegundos|Média, mín., máx.|Nenhum|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Recebendo tempo de resposta (browserTimings/receiveDuration)

|Unidade de medida|Agregações com suporte|Dimensões previamente agregadas|
|---|---|---|
|Milissegundos|Média, mín., máx.|Nenhum|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Enviar tempo de solicitação (browserTimings/sendDuration)

|Unidade de medida|Agregações com suporte|Dimensões previamente agregadas|
|---|---|---|
|Milissegundos|Média, mín., máx.|Nenhum|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Métricas de falha

As métricas em **falhas** mostram problemas com solicitações de processamento, chamadas de dependência e exceções geradas.

### <a name="browser-exceptions-exceptionsbrowser"></a>Exceções do navegador (exceções/navegador)

Essa métrica reflete o número de exceções geradas do código do aplicativo em execução no navegador. Somente as exceções rastreadas com uma ```trackException()``` chamada à API Application insights são incluídas na métrica.

|Unidade de medida|Agregações com suporte|Dimensões previamente agregadas|Observações|
|---|---|---|---|
|Count|Contagem|Nenhum|A versão baseada em log usa agregação **sum**|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Falhas de chamada de dependência (dependências/com falha)

O número de chamadas de dependência com falha.

|Unidade de medida|Agregações com suporte|Dimensões previamente agregadas|Observações|
|---|---|---|---|
|Contagem|Count|Nenhum|A versão baseada em log usa agregação **sum**|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Exceções (exceções/contagem)

Cada vez que você registra uma exceção em Application Insights, há uma chamada para o [método trackexception ()](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) do SDK. A métrica de exceções mostra o número de exceções registradas.

|Unidade de medida|Agregações com suporte|Dimensões previamente agregadas|Observações|
|---|---|---|---|
|Contagem|Count|Nome da função de nuvem, instância de função de nuvem, tipo de dispositivo|A versão baseada em log usa agregação **sum**|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Solicitações com falha (solicitações/falha)

A contagem de solicitações de servidor rastreadas que foram marcadas como *com falha*. Por padrão, o SDK do Application Insights marca automaticamente cada solicitação do servidor que retornou o código de resposta HTTP 5xx ou 4xx como uma solicitação com falha. Você pode personalizar essa lógica modificando a propriedade *Success* do item telemetria de solicitação em um inicializador de [telemetria personalizado](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer).

|Unidade de medida|Agregações com suporte|Dimensões previamente agregadas|Observações|
|---|---|---|---|
|Contagem|Count|Instância de função de nuvem, nome da função de nuvem, tráfego real ou sintético, desempenho da solicitação, código de resposta|A versão baseada em log usa agregação **sum**|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Exceções de servidor (exceções/servidor)

Essa métrica mostra o número de exceções de servidor.

|Unidade de medida|Agregações com suporte|Dimensões previamente agregadas|Observações|
|---|---|---|---|
|Count|Count|Nome da função de nuvem, instância de função de nuvem|A versão baseada em log usa agregação **sum**|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>contadores de desempenho

Use as métricas na categoria **contadores de desempenho** para acessar os contadores de desempenho do [sistema coletados pelo Application insights](../../azure-monitor/app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Memória disponível (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Taxa de exceção (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Tempo de execução da solicitação HTTP (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Taxa de solicitação HTTP (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Solicitações HTTP na fila de aplicativos (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CPU do processo (performanceCounters/processCpuPercentage)

A métrica mostra quanto da capacidade total do processador é consumida pelo processo que está hospedando seu aplicativo monitorado.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
|Percentual|Média, mín., máx.|Instância de função de nuvem

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Taxa de e/s de processo (performanceCounters/processIOBytesPerSecond)

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
|Bytes por segundo|Média, mín., máx.|Instância de função de nuvem

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Processar bytes privados (performanceCounters/processPrivateBytes)

Quantidade de memória não compartilhada que o processo monitorado alocou para seus dados.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
|Bytes|Média, mín., máx.|Instância de função de nuvem

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Tempo do processador (performanceCounters/processorCpuPercentage)

Consumo de CPU por *todos os* processos em execução na instância do servidor monitorado.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
|Percentual|Média, mín., máx.|Instância de função de nuvem

>[!NOTE]
> A métrica de tempo do processador não está disponível para os aplicativos hospedados nos serviços de Azure App. Use a métrica [processar CPU](#process-cpu-performancecountersprocesscpupercentage) para acompanhar a utilização da CPU dos aplicativos Web hospedados nos serviços de aplicativos.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Métricas do servidor

### <a name="dependency-calls-dependenciescount"></a>Chamadas de dependência (dependências/contagem)

Essa métrica está em relação ao número de chamadas de dependência.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Duração da dependência (dependências/duração)

Essa métrica refere-se à duração de chamadas de dependência.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Solicitações do servidor (solicitações/contagem)

Essa métrica reflete o número de solicitações de servidor de entrada que foram recebidas pelo seu aplicativo Web.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Tempo de resposta do servidor (solicitações/duração)

Essa métrica reflete o tempo necessário para que os servidores processem as solicitações de entrada.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Métricas de uso

### <a name="page-view-load-time-pageviewsduration"></a>Tempo de carregamento da exibição de página (pageViews/duração)

Essa métrica se refere à quantidade de tempo que levou para os eventos de PageView serem carregados.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Exibições de página (pageViews/Count)

A contagem de eventos PageView registrados com a API de Application Insights de TrackPageView ().

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sessões (sessões/contagem)

Essa métrica refere-se à contagem de IDs de sessão distintas.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Rastreamentos (rastreamentos/contagem)

A contagem de instruções de rastreamento registradas com o TrackTrace () Application Insights chamada à API.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Usuários (usuários/contagem)

O número de usuários distintos que acessaram seu aplicativo. A precisão dessa métrica pode ser significativamente afetada usando a filtragem e a amostragem da telemetria.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Usuários, autenticados (usuários/autenticados)

O número de usuários distintos que se autenticaram em seu aplicativo.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
