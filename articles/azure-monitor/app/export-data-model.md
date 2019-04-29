---
title: Modelo de Dados do Azure Application Insights | Microsoft Docs
description: Descreve as propriedades exportadas de exportação contínua em JSON e usados como filtros.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: mbullwin
ms.openlocfilehash: 12025dfb93bbcfc86ae301f8fb63e7ac74697cf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898909"
---
# <a name="application-insights-export-data-model"></a>Modelo de dados de exportação do Application Insights
Esta tabela lista as propriedades de telemetria enviadas dos SDKs do [Application Insights](../../azure-monitor/app/app-insights-overview.md) para o portal.
Você verá essas propriedades na saída de dados de [Exportação Contínua](export-telemetry.md).
Elas também aparecerão nos filtros da propriedade no [Explorador de Métrica](../../azure-monitor/app/metrics-explorer.md) e na [Pesquisa de Diagnóstico](../../azure-monitor/app/diagnostic-search.md).

Pontos a serem observados:

* `[0]` nessas tabelas indica um ponto no caminho no qual você precisa inserir um índice; mas nem sempre é 0.
* A duração é em décimos de microssegundo, portanto, 10000000 = = 1 segundo.
* Datas e horas estão em UTC e são fornecidas no formato ISO `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Exemplo
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Contexto
Todos os tipos de telemetria são acompanhados por uma seção de contexto. Nem todos esses campos são transmitidos com cada ponto de dados.

| Caminho | Type | Observações |
| --- | --- | --- |
| context.custom.dimensions [0] |object [ ] |Pares de cadeira de caractere chave-valor definidos pelo parâmetro das propriedades personalizadas. Comprimento máximo da chave 100, comprimento máximo dos valores 1024. Mais de 100 valores exclusivos, é possível pesquisar na propriedade, mas não usá-la para segmentação. Máximo de 200 chaves por ikey. |
| context.custom.metrics [0] |object [ ] |Pares de chave-valor definidos pelo parâmetro de medidas personalizadas e por TrackMetrics. Comprimento máximo da chave 100, os valores podem ser numéricos. |
| context.data.eventTime |string |UTC |
| context.data.isSynthetic |boolean |A solicitação parece ser proveniente de um teste na Web ou de um bot. |
| context.data.samplingRate |número |Porcentagem de telemetria gerada pelo SDK enviado ao portal. Intervalo 0.0-100.0. |
| context.device |objeto |Dispositivo de cliente |
| context.device.browser |string |IE, Chrome, ... |
| context.device.browserVersion |string |Chrome 48.0, ... |
| context.device.deviceModel |string | |
| context.device.deviceName |string | |
| context.device.id |string | |
| context.device.locale |string |en-GB, de-DE, ... |
| context.device.network |string | |
| context.device.oemName |string | |
| context.device.os |string | |
| context.device.osVersion |string |SO host |
| context.device.roleInstance |string |ID do host do servidor |
| context.device.roleName |string | |
| context.device.screenResolution |string | |
| context.device.type |string |PC, navegador,... |
| context.location |objeto |Derivado de clientip. |
| context.location.city |string |Derivado de clientip, se conhecido |
| context.location.clientip |string |Último octógono tornado anônimo pelo valor 0. |
| context.location.continent |string | |
| context.location.country |string | |
| context.location.province |string |Estado ou província |
| context.operation.id |string |Itens que têm a mesma ID de operação são mostrados como Itens Relacionados no portal. Normalmente a ID da solicitação. |
| context.operation.name |string |nome solicitação ou url |
| context.operation.parentId |string |Permite itens relacionados aninhados. |
| context.session.id |string |ID de um grupo de operações da mesma fonte. Um período de 30 minutos sem uma operação sinaliza o término de uma sessão. |
| context.session.isFirst |boolean | |
| context.user.accountAcquisitionDate |string | |
| context.user.accountId |string | |
| context.user.anonAcquisitionDate |string | |
| context.user.anonId |string | |
| context.user.authAcquisitionDate |string |[Usuário autenticado](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |string | |
| context.user.isAuthenticated |boolean | |
| context.user.storeRegion |string | |
| internal.data.documentVersion |string | |
| internal.data.id |string | ID exclusiva atribuída quando um item é ingerido no Application Insights |

## <a name="events"></a>Eventos
Eventos personalizados gerados por [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Caminho | Type | Observações |
| --- | --- | --- |
| event [0] count |inteiro |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo, 4 =&gt; 25%. |
| event [0] name |string |Nome do evento.  Comprimento máximo 250. |
| event [0] url |string | |
| event [0] urlData.base |string | |
| event [0] urlData.host |string | |

## <a name="exceptions"></a>Exceções
[Exceções](../../azure-monitor/app/asp-net-exceptions.md) do relatório no servidor e no navegador.

| Caminho | Type | Observações |
| --- | --- | --- |
| basicException [0] assembly |string | |
| basicException [0] count |inteiro |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo, 4 =&gt; 25%. |
| basicException [0] exceptionGroup |string | |
| basicException [0] exceptionType |string | |
| basicException [0] failedUserCodeMethod |string | |
| basicException [0] failedUserCodeAssembly |string | |
| basicException [0] handledAt |string | |
| basicException [0] hasFullStack |boolean | |
| basicException [0] id |string | |
| basicException [0] method |string | |
| basicException [0] message |string |Mensagem de exceção. Comprimento máximo 10k. |
| basicException [0] outerExceptionMessage |string | |
| basicException [0] outerExceptionThrownAtAssembly |string | |
| basicException [0] outerExceptionThrownAtMethod |string | |
| basicException [0] outerExceptionType |string | |
| basicException [0] outerId |string | |
| basicException [0] parsedStack [0] assembly |string | |
| basicException [0] parsedStack [0] fileName |string | |
| basicException [0] parsedStack [0] level |inteiro | |
| basicException [0] parsedStack [0] line |inteiro | |
| basicException [0] parsedStack [0] method |string | |
| basicException [0] stack |string |Comprimento máximo 10k |
| basicException [0] typeName |string | |

## <a name="trace-messages"></a>Mensagens de rastreamento
Enviado por [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) e pelos [adaptadores de log](../../azure-monitor/app/asp-net-trace-logs.md).

| Caminho | Type | Observações |
| --- | --- | --- |
| message [0] loggerName |string | |
| message [0] parameters |string | |
| message [0] raw |string |A mensagem de log, comprimento máximo de 10 mil. |
| message [0] severityLevel |string | |

## <a name="remote-dependency"></a>Dependência remota
Enviado por TrackDependency. Usado para indicar o desempenho e o uso das [chamadas para dependências](../../azure-monitor/app/asp-net-dependencies.md) no servidor, e chamadas do AJAX no navegador.

| Caminho | Type | Observações |
| --- | --- | --- |
| remoteDependency [0] async |boolean | |
| remoteDependency [0] baseName |string | |
| remoteDependency [0] commandName |string |Por exemplo, "home/index" |
| remoteDependency [0] count |inteiro |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo, 4 =&gt; 25%. |
| remoteDependency [0] dependencyTypeName |string |HTTP, SQL, ... |
| remoteDependency [0] durationMetric.value |número |Tempo desde a chamada até a conclusão da resposta por dependência |
| remoteDependency [0] id |string | |
| remoteDependency [0] name |string |Url. Comprimento máximo 250. |
| remoteDependency [0] resultCode |string |da dependência de HTTP |
| remoteDependency [0] success |boolean | |
| remoteDependency [0] type |string |Http, Sql,... |
| remoteDependency [0] url |string |Comprimento máximo 2000 |
| remoteDependency [0] urlData.base |string |Comprimento máximo 2000 |
| remoteDependency [0] urlData.hashTag |string | |
| remoteDependency [0] urlData.host |string |Comprimento máximo 200 |

## <a name="requests"></a>Requests
Enviado por [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Os módulos padrão usam isso para indicar o tempo de resposta do servidor, medido no servidor.

| Caminho | Type | Observações |
| --- | --- | --- |
| request [0] count |inteiro |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo:  4 =&gt; 25%. |
| request [0] durationMetric.value |número |Tempo de chegada da solicitação até a resposta. 1e7 == 1s |
| request [0] id |string |ID da operação |
| request [0] name |string |GET/POST + url base.  Comprimento máximo 250 |
| request [0] responseCode |inteiro |Resposta HTTP enviada ao cliente |
| request [0] success |boolean |Padrão == (responseCode &lt; 400) |
| request [0] url |string |Não incluindo o host |
| request [0] urlData.base |string | |
| request [0] urlData.hashTag |string | |
| request [0] urlData.host |string | |

## <a name="page-view-performance"></a>Desempenho de exibição da página
Enviado pelo navegador. Mede o tempo de processamento de uma página, desde o início da solicitação do usuário até a exibição completa (excluindo as chamadas do AJAX assíncronas).

Os valores de contexto mostram a versão do navegador e do sistema operacional cliente.

| Caminho | Type | Observações |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |inteiro |Tempo desde o término do recebimento do HTML até a exibição da página. |
| clientPerformance [0] name |string | |
| clientPerformance [0] networkConnection.value |inteiro |Tempo necessário para estabelecer uma conexão de rede. |
| clientPerformance [0] receiveRequest.value |inteiro |Tempo desde o término do envio da solicitação até o recebimento do HTML na resposta. |
| clientPerformance [0] sendRequest.value |inteiro |Tempo necessário para enviar a solicitação HTTP. |
| clientPerformance [0] total.value |inteiro |Tempo desde o início até o envio da solicitação para exibição da página. |
| clientPerformance [0] url |string |URL dessa solicitação |
| clientPerformance [0] urlData.base |string | |
| clientPerformance [0] urlData.hashTag |string | |
| clientPerformance [0] urlData.host |string | |
| clientPerformance [0] urlData.protocol |string | |

## <a name="page-views"></a>Visualizações de página
Enviado por trackPageView() ou [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Caminho | Type | Observações |
| --- | --- | --- |
| view [0] count |inteiro |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo, 4 =&gt; 25%. |
| view [0] durationMetric.value |inteiro |Valor definido opcionalmente em trackPageView() ou por startTrackPage() - stopTrackPage(). Não é igual aos valores de clientPerformance. |
| view [0] name |string |Título da página.  Comprimento máximo 250 |
| view [0] url |string | |
| view [0] urlData.base |string | |
| view [0] urlData.hashTag |string | |
| view [0] urlData.host |string | |

## <a name="availability"></a>Disponibilidade
Relata os [testes de disponibilidade na Web](../../azure-monitor/app/monitor-web-app-availability.md).

| Caminho | Type | Observações |
| --- | --- | --- |
| availability [0] availabilityMetric.name |string |Disponibilidade |
| availability [0] availabilityMetric.value |número |1.0 ou 0.0 |
| availability [0] count |inteiro |100/(taxa de[amostragem](../../azure-monitor/app/sampling.md) ). Por exemplo, 4 =&gt; 25%. |
| availability [0] dataSizeMetric.name |string | |
| availability [0] dataSizeMetric.value |inteiro | |
| availability [0] durationMetric.name |string | |
| availability [0] durationMetric.value |número |Duração do teste. 1e7==1s |
| availability [0] message |string |Diagnóstico de falha |
| availability [0] result |string |Aprovado/Reprovado |
| availability [0] runLocation |string |Fonte geográfica de solicitações http |
| availability [0] testName |string | |
| availability [0] testRunId |string | |
| availability [0] testTimestamp |string | |

## <a name="metrics"></a>Métricas
Gerado por TrackMetric().

O valor da métrica é encontrado em context.custom.metrics[0]

Por exemplo: 

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Sobre valores de métricas
Valores de métricas, tanto em relatórios de métrica quanto em outros locais, são relatados com uma estrutura de objeto padrão. Por exemplo: 

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Atualmente, embora isso possa mudar no futuro, em todos os valores relatados pelos módulos padrão do SDK, `count==1` e apenas os campos `name` e `value` são úteis. O único caso em que eles poderiam ser diferentes seria se você escrevesse suas próprias chamadas TrackMetric, nas quais definiria os outros parâmetros.

A finalidade dos outros campos é permitir que as métricas sejam agregadas ao SDK, a fim de reduzir o tráfego no portal. Por exemplo, você pode realizar várias leituras sucessivas antes de enviar cada relatório de métricas. Depois, você deve calcular o desvio mín., máx. e padrão e o valor agregado (soma ou média), e definir a contagem como o número de leituras representado pelo relatório.

Nas tabelas acima, omitimos os campos min, max, stdDev e sampledValue, que são usados raramente.

Em vez de agregar previamente as métricas, você pode usar a [amostragem](../../azure-monitor/app/sampling.md) se precisar reduzir o volume de telemetria.

### <a name="durations"></a>Durações
Exceto quando indicado o contrário, as durações são representadas em décimos de microssegundo, de modo que 10000000.0 significa 1 segundo.

## <a name="see-also"></a>Consulte também
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Exportação Contínua](export-telemetry.md)
* [Exemplos de código](export-telemetry.md#code-samples)
