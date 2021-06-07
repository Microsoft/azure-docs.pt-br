---
title: Métricas padrão do Aplicativo Azure insights | Microsoft Docs
description: Este artigo lista Aplicativo Azure métricas de informações com agregações e dimensões com suporte.
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.subservice: application-insights
ms.openlocfilehash: 0a18088fa434efa76007607c067feec107bdae57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572363"
---
# <a name="application-insights-standard-metrics"></a>Application Insights métricas padrão

As métricas padrão são previamente agregadas durante a coleta, elas têm melhor desempenho no momento da consulta. Isso os torna a melhor opção para os painéis e alertas em tempo real.

## <a name="availability-metrics"></a>Métricas de disponibilidade

As métricas na categoria disponibilidade permitem que você veja a integridade do seu aplicativo Web, conforme observado em pontos em todo o mundo. [Configure os testes de disponibilidade](../app/monitor-web-app-availability.md) para começar a usar qualquer métrica dessa categoria.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Disponibilidade (availabilityResults/availabilityPercentage)
A métrica de *disponibilidade* mostra a porcentagem de execuções de teste na Web que não detectaram nenhum problema. O menor valor possível é 0, o que indica que todas as execuções de teste na Web falharam. O valor de 100 significa que todas as execuções de testes na Web passaram pelos critérios de validação.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|---|---|---|
|Percentual|Média|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>Duração do teste de disponibilidade (availabilityResults/Duration)

A métrica *duração do teste de disponibilidade* mostra quanto tempo levou para o teste na Web ser executado. Para os [testes da Web de várias etapas](../app/availability-multistep.md), a métrica reflete o tempo de execução total de todas as etapas.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|---|---|---|
|Milissegundos|Média, mín., máx.|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>Testes de disponibilidade (availabilityResults/Count)

A métrica de *testes de disponibilidade* reflete a contagem dos testes da Web executados por Azure monitor.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|---|---|---|
|Contagem|Contagem|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>Métricas do navegador

As métricas do navegador são coletadas pelo SDK Application Insights JavaScript de navegadores de usuários finais reais. Eles fornecem ótimos percepções sobre a experiência dos seus usuários com seu aplicativo Web. As métricas de navegador normalmente não são amostradas, o que significa que elas fornecem maior precisão dos números de uso em comparação com as métricas do lado do servidor que podem ser distorcidas por amostragem.

> [!NOTE]
> Para coletar métricas do navegador, seu aplicativo deve ser instrumentado com o [SDK Application insights JavaScript](../app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Tempo de carregamento de página do navegador (browserTimings/totalDuration)

|Unidade de medida|Agregações com suporte| Dimensões com suporte|
|---|---|---|
|Milissegundos|Média, mín., máx.|Nenhum|

### <a name="client-processing-time-browsertimingprocessingduration"></a>Tempo de processamento do cliente (browserTiming/processingDuration)

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
|Milissegundos|Média, mín., máx.|Nenhum|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Tempo de conexão de rede de carregamento de página (browserTimings/networkDuration)

|Unidade de medida|Agregações com suporte| Dimensões com suporte|
|---|---|---|
|Milissegundos|Média, mín., máx.|Nenhum|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Recebendo tempo de resposta (browserTimings/receiveDuration)

|Unidade de medida|Agregações com suporte| Dimensões com suporte|
|---|---|---|
|Milissegundos|Média, mín., máx.|Nenhum|

### <a name="send-request-time-browsertimingssendduration"></a>Enviar tempo de solicitação (browserTimings/sendDuration)

|Unidade de medida|Agregações com suporte| Dimensões com suporte|
|---|---|---|
|Milissegundos|Média, mín., máx.|Nenhum|

## <a name="failure-metrics"></a>Métricas de falha

As métricas em **falhas** mostram problemas com solicitações de processamento, chamadas de dependência e exceções geradas.

### <a name="browser-exceptions-exceptionsbrowser"></a>Exceções do navegador (exceções/navegador)

Essa métrica reflete o número de exceções geradas do código do aplicativo em execução no navegador. Somente as exceções rastreadas com uma ```trackException()``` chamada à API Application insights são incluídas na métrica.

|Unidade de medida|Agregações com suporte | Dimensões com suporte|
|---|---|---|---|
| Contagem | Contagem | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>Falhas de chamada de dependência (dependências/com falha)

O número de chamadas de dependência com falha.

|Unidade de medida|Agregações com suporte | Dimensões com suporte |
|---|---|---|---|
|Contagem|Contagem| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>Exceções (exceções/contagem)

Cada vez que você registra uma exceção em Application Insights, há uma chamada para o [método trackexception ()](../app/api-custom-events-metrics.md#trackexception) do SDK. A métrica de exceções mostra o número de exceções registradas.

|Unidade de medida|Agregações com suporte | Dimensões com suporte |
|---|---|---|---|
|Contagem|Contagem|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>Solicitações com falha (solicitações/falha)

A contagem de solicitações de servidor rastreadas que foram marcadas como *com falha*. Por padrão, o SDK do Application Insights marca automaticamente cada solicitação do servidor que retornou o código de resposta HTTP 5xx ou 4xx como uma solicitação com falha. Você pode personalizar essa lógica modificando a propriedade  *Success* do item telemetria de solicitação em um [inicializador de telemetria personalizado](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).


|Unidade de medida|Agregações com suporte | Dimensões com suporte |
|---|---|---|---|
|Contagem|Contagem|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>Exceções de servidor (exceções/servidor)

Essa métrica mostra o número de exceções de servidor.

|Unidade de medida|Agregações com suporte | Dimensões com suporte |
|---|---|---|---|
|Contagem|Contagem|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>Contadores de desempenho

Use as métricas na categoria **contadores de desempenho** para acessar os contadores de desempenho do [sistema coletados pelo Application insights](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Memória disponível (performanceCounters/availableMemory)

|Unidade de medida|Agregações com suporte | Dimensões com suporte |
|---|---|---|---|
|Dependentes de dados: megabytes, gigabytes|Média, máx., mín.|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>Taxa de exceção (performanceCounters/exceptionRate)

|Unidade de medida|Agregações com suporte | Dimensões com suporte |
|---|---|---|---|
| Contagem | Média, máx., mín. | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Tempo de execução da solicitação HTTP (performanceCounters/requestExecutionTime)

|Unidade de medida|Agregações com suporte | Dimensões com suporte |
|---|---|---|---|
| Milissegundos | Média, máx., mín. | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Taxa de solicitação HTTP (performanceCounters/requestsPerSecond)

|Unidade de medida|Agregações com suporte | Dimensões com suporte |
|---|---|---|---|
| Solicitações por segundo | Média, máx., mín. | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Solicitações HTTP na fila de aplicativos (performanceCounters/requestsInQueue)

|Unidade de medida|Agregações com suporte | Dimensões com suporte |
|---|---|---|---|
| Contagem | Média, máx., mín. | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CPU do processo (performanceCounters/processCpuPercentage)

A métrica mostra quanto da capacidade total do processador é consumida pelo processo que está hospedando seu aplicativo monitorado.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
|Percentual|Média, máx., mín.| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Taxa de e/s de processo (performanceCounters/processIOBytesPerSecond)

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
|Bytes por segundo|Média, mín., máx.|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Processar bytes privados (performanceCounters/processPrivateBytes)

Quantidade de memória não compartilhada que o processo monitorado alocou para seus dados.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
|Bytes|Média, mín., máx.|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Tempo do processador (performanceCounters/processorCpuPercentage)

Consumo de CPU por *todos os* processos em execução na instância do servidor monitorado.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
|Percentual|Média, mín., máx.|`Cloud role instance` |

>[!NOTE]
> A métrica de tempo do processador não está disponível para os aplicativos hospedados nos serviços de Azure App. Use a métrica  [processar CPU](#process-cpu-performancecountersprocesscpupercentage) para acompanhar a utilização da CPU dos aplicativos Web hospedados nos serviços de aplicativos.

## <a name="server-metrics"></a>Métricas do servidor

### <a name="dependency-calls-dependenciescount"></a>Chamadas de dependência (dependências/contagem)

Essa métrica está em relação ao número de chamadas de dependência.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
| Contagem | Contagem | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>Duração da dependência (dependências/duração)

Essa métrica refere-se à duração de chamadas de dependência.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
| Hora | Média, mín., máx. | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>Taxa de solicitação do servidor (solicitações/contagem)

Essa métrica reflete o número de solicitações de servidor de entrada que foram recebidas pelo seu aplicativo Web.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
| Contagem | Média | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>Solicitações do servidor (solicitações/contagem)

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
| Contagem | Contagem | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>Tempo de resposta do servidor (solicitações/duração)

Essa métrica reflete o tempo necessário para que os servidores processem as solicitações de entrada.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
| Hora | Média, mín., máx. | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>Métricas de uso

### <a name="page-view-load-time-pageviewsduration"></a>Tempo de carregamento da exibição de página (pageViews/duração)

Essa métrica se refere à quantidade de tempo que levou para os eventos de PageView serem carregados.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
| Hora | Média, mín., máx. | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>Exibições de página (pageViews/Count)

A contagem de eventos PageView registrados com a API de Application Insights de TrackPageView ().

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
| Contagem | Contagem | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>Rastreamentos (rastreamentos/contagem)

A contagem de instruções de rastreamento registradas com o TrackTrace () Application Insights chamada à API.

|Unidade de medida|Agregações com suporte|Dimensões com suporte|
|---|---|---|
| Contagem | Contagem | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [métricas baseadas em log e previamente agregadas](./pre-aggregated-metrics-log-metrics.md).
* [Consultas e definições de métricas baseadas em log](../essentials/app-insights-metrics.md).