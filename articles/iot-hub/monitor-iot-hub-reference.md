---
title: Monitorando a referência de dados do Hub IoT do Azure
description: Material de referência importante necessário ao monitorar o Hub IoT do Azure
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/22/2020
ms.openlocfilehash: f198b59f106b7d2a29e35d77b54274328be6fa93
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581594"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Monitorando a referência de dados do Hub IoT do Azure

Consulte [monitorar o Hub IOT do Azure](monitor-iot-hub.md) para obter detalhes sobre como coletar e analisar dados de monitoramento para o Hub IOT do Azure.

## <a name="metrics"></a>Métricas

Esta seção lista todas as métricas de plataforma coletadas automaticamente para o Hub IoT do Azure. O namespace do provedor de recursos para métricas do Hub IoT é **Microsoft. Devices** e o namespace do tipo é **IoTHubs**.

As subseções a seguir dividem as métricas da plataforma do Hub IoT por categoria geral e as listam pelo nome de exibição que elas aparecem no portal do Azure com. As informações também são fornecidas relevantes para as métricas que aparecem em cada subseção.

Você também pode encontrar uma única tabela que lista todas as métricas da plataforma do Hub IoT por nome da métrica em [Microsoft. Devices/IotHubs](../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs) na documentação do Azure monitor. Lembre-se de que essa tabela não fornece algumas das informações, como [agregações com suporte](#supported-aggregations) para algumas métricas, disponíveis neste artigo.

Para saber mais sobre as métricas com suporte em outros serviços do Azure, consulte [métricas com suporte com Azure monitor](../azure-monitor/platform/metrics-supported.md).

**Tópicos nesta seção**

- [Agregações com suporte](#supported-aggregations)
- [Métricas de comando da nuvem para o dispositivo](#cloud-to-device-command-metrics)
- [Métricas de métodos diretos de nuvem para dispositivo](#cloud-to-device-direct-methods-metrics)
- [Métricas de operações de entrelaçamento de nuvem para dispositivo](#cloud-to-device-twin-operations-metrics)
- [Métricas de configuração](#configurations-metrics)
- [Métricas de cota diária](#daily-quota-metrics)
- [Métricas do dispositivo](#device-metrics)
- [Métricas de telemetria do dispositivo](#device-telemetry-metrics)
- [Métricas de operações do dispositivo para a nuvem no Cloud](#device-to-cloud-twin-operations-metrics)
- [Métricas da grade de eventos](#event-grid-metrics)
- [Métricas de trabalhos](#jobs-metrics)
- [Métricas de roteamento](#routing-metrics)
- [Métricas de consulta de entrelaçamento](#twin-query-metrics)

### <a name="supported-aggregations"></a>Agregações com suporte

A coluna de **tipo de agregação** em cada tabela corresponde à agregação padrão usada quando a métrica é selecionada para um gráfico ou alerta.

   ![Captura de tela mostrando a agregação para métricas](./media/monitor-iot-hub-reference/aggregation-type.png)

Para a maioria das métricas, todos os tipos de agregação são válidos; no entanto, para métricas de contagem, aquelas com um valor de coluna de **unidade** de **contagem**, apenas algumas agregações são válidas. As métricas de contagem podem ser de um dos dois tipos:

* Para métricas de contagem de **ponto único** , o Hub IOT registra um único ponto de dados--essencialmente um 1--cada vez que a operação medida ocorre. Azure Monitor soma esses pontos de dados sobre a granularidade especificada. Exemplos de métricas de **ponto único** são *mensagens de telemetria enviadas* e *entregas de mensagens C2D concluídas*. Para essas métricas, o único tipo de agregação relevante é total (Sum). O portal permite que você escolha mínimo, máximo e média; no entanto, esses valores serão sempre 1.

* Para métricas de contagem de **instantâneos** , o Hub IOT registra uma contagem total quando ocorre a operação medida. Atualmente, há três métricas de **instantâneo** emitidas pelo Hub IOT: *número total de mensagens usadas*, *total de dispositivos (versão prévia)* e *dispositivos conectados (versão prévia)*. Como essas métricas apresentam uma quantidade "total" sempre que são emitidas, a soma delas na granularidade especificada não faz sentido. Azure Monitor limita a seleção de média, mínimo e máximo para o tipo de agregação para essas métricas.

### <a name="cloud-to-device-command-metrics"></a>Métricas de comando da nuvem para o dispositivo

|Nome de exibição da métrica|Métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Mensagens C2D expiradas (versão prévia)|C2DMessagesExpired|Contagem|Total|Número de mensagens de nuvem para o dispositivo expiradas|Nenhuma|
|Entregas de mensagem C2D concluídas|c2d.commands.egress.complete.success|Contagem|Total|Número de entregas de mensagem da nuvem para o dispositivo concluídas com êxito pelo dispositivo|Nenhuma|
|Mensagens C2D abandonadas|c2d.commands.egress.abandon.success|Contagem|Total|Número de mensagens da nuvem para o dispositivo abandonadas pelo dispositivo|Nenhuma|
|Mensagens C2D rejeitadas|c2d.commands.egress.reject.success|Contagem|Total|Número de mensagens da nuvem para o dispositivo rejeitadas pelo dispositivo|Nenhuma|

Para métricas com um valor de **unidade** de **contagem**, somente a agregação total (Sum) é válida. As agregações mínima, máxima e média sempre retornam 1. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

### <a name="cloud-to-device-direct-methods-metrics"></a>Métricas de métodos diretos de nuvem para dispositivo

|Nome de exibição da métrica|Métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Invocações de método diretas com falhas|c2d.methods.failure|Contagem|Total|A contagem de todas as chamadas de método diretas com falhas.|Nenhuma|
|Tamanho da solicitação das invocações de método diretas|c2d.methods.requestSize|Bytes|Média|A contagem de todas as solicitações de método diretas bem-sucedidas.|Nenhuma|
|Tamanho da resposta das invocações de método diretas|c2d.methods.responseSize|Bytes|Média|A contagem de todas as respostas de método diretas bem-sucedidas.|Nenhuma|
|Invocações de método diretas bem-sucedidas|c2d.methods.success|Contagem|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Nenhuma|

Para métricas com um valor de **unidade** de agregação de **contagem** total (Sum) é válido. As agregações mínima, máxima e média sempre retornam 1. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

### <a name="cloud-to-device-twin-operations-metrics"></a>Métricas de operações de entrelaçamento de nuvem para dispositivo

|Nome de exibição da métrica|Métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Leituras de gêmeos de back-end com falhas|c2d.twin.read.failure|Contagem|Total|A contagem de todas as falhas de leituras de gêmeos iniciadas pelo back-end.|Nenhuma|
|Atualizações de gêmeos de back-end com falhas|c2d.twin.update.failure|Contagem|Total|A contagem de todas as falhas de atualizações de gêmeos iniciadas pelo back-end.|Nenhuma|
|Tamanho da resposta das leituras de gêmeos de back-end|c2d.twin.read.size|Bytes|Média|A contagem de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Nenhuma|
|Tamanho das atualizações de gêmeos de back-end|c2d.twin.update.size|Bytes|Média|O tamanho total de todas as atualizações de cópia do back-end iniciadas com êxito.|Nenhuma|
|Leituras de gêmeos de back-end bem-sucedidas|c2d.twin.read.success|Contagem|Total|A contagem de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Nenhuma|
|Atualizações de gêmeos de back-end bem-sucedidas|c2d.twin.update.success|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Nenhuma|

Para métricas com um valor de **unidade** de **contagem**, somente a agregação total (Sum) é válida. As agregações mínima, máxima e média sempre retornam 1. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

### <a name="configurations-metrics"></a>Métricas de configuração

|Nome de exibição da métrica|Métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Métricas de configuração|configurações|Contagem|Total|Número total de operações CRUD executadas para a configuração do dispositivo e implantação de IoT Edge em um conjunto de dispositivos de destino. Isso também inclui o número de operações que modificam o dispositivo FileMover ou o módulo n por causa dessas configurações.|Nenhuma|

Para métricas com um valor de **unidade** de **contagem**, somente a agregação total (Sum) é válida. As agregações mínima, máxima e média sempre retornam 1. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

### <a name="daily-quota-metrics"></a>Métricas de cota diária

|Nome de exibição da métrica|Métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Uso total de dados do dispositivo|deviceDataUsage|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Nenhuma|
|Uso total de dados de dispositivos (versão prévia)|deviceDataUsageV2|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Nenhuma|
|Número total de mensagens usadas|dailyMessageQuotaUsed|Contagem|Média|Número total de mensagens utilizadas hoje. Este é um valor cumulativo que é reiniciado para zero em 00:00 UTC diariamente.|Nenhuma|

Para o *número total de mensagens usadas*, apenas as agregações mínima, máxima e média têm suporte. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

### <a name="device-metrics"></a>Métricas do dispositivo

|Nome de exibição da métrica|Métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Total de dispositivos (preterido)|devices.totalDevices|Contagem|Total|Número de dispositivos registrados para o seu hub IoT|Nenhuma|
|Dispositivos conectados (preteridos) |devices.connectedDevices.allProtocol|Contagem|Total|Número de dispositivos registrados ao seu hub IoT|Nenhuma|
|Total de dispositivos (versão prévia)|totalDeviceCount|Contagem|Média|Número de dispositivos registrados para o seu hub IoT|Nenhuma|
|Dispositivos conectados (versão prévia)|connectedDeviceCount|Contagem|Média|Número de dispositivos registrados ao seu hub IoT|Nenhuma|

Para *dispositivos totais (preteridos)* e *dispositivos conectados (preterido)*, somente a agregação total (Sum) é válida. As agregações mínima, máxima e média sempre retornam 1. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

Para *dispositivos totais (visualização)* e *dispositivos conectados (visualização)*, somente as agregações mínima, máxima e média são válidas. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

*Dispositivos conectados (visualização)* e *total de dispositivos (visualização)* não são exportáveis por meio de configurações de diagnóstico.

### <a name="device-telemetry-metrics"></a>Métricas de telemetria do dispositivo

|Nome de exibição da métrica|Métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Número de erros de limitação|d2c.telemetry.ingress.sendThrottle|Contagem|Total|Número de erros de limitação devido a limitações da taxa de transferência do dispositivo|Nenhuma|
|Tentativas de envio de mensagem de telemetria|d2c.telemetry.ingress.allProtocol|Contagem|Total|Número de tentativas de envio de mensagens de telemetria do dispositivo para nuvem para o hub IoT|Nenhuma|
|Mensagens de telemetria enviadas|d2c.telemetry.ingress.success|Contagem|Total|Número de mensagens de telemetria do dispositivo para nuvem enviadas com êxito para o hub IoT|Nenhuma|

Para métricas com um valor de **unidade** de **contagem**, somente a agregação total (Sum) é válida. As agregações mínima, máxima e média sempre retornam 1. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

### <a name="device-to-cloud-twin-operations-metrics"></a>Métricas de operações do dispositivo para a nuvem no Cloud

|Nome de exibição da métrica|Métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Leituras de gêmeos dos dispositivos com falhas|d2c.twin.read.failure|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo com falhas.|Nenhuma|
|Atualizações de gêmeos dos dispositivos com falhas|d2c.twin.update.failure|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo.|Nenhuma|
|Tamanho da resposta das leituras de gêmeos dos dispositivos|d2c.twin.read.size|Bytes|Média|O número de todas as leituras de entrelaçadas iniciadas pelo dispositivo bem-sucedidas.|Nenhuma|
|Tamanho das atualizações de gêmeos dos dispositivos|d2c.twin.update.size|Bytes|Média|O tamanho total de todas as atualizações de atualização do dispositivo iniciadas com êxito.|Nenhuma|
|Leituras de gêmeos dos dispositivos bem-sucedidas|d2c.twin.read.success|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Nenhuma|
|Atualizações de gêmeos dos dispositivos bem-sucedidas|d2c.twin.update.success|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Nenhuma|

Para métricas com um valor de **unidade** de **contagem**, somente a agregação total (Sum) é válida. As agregações mínima, máxima e média sempre retornam 1. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

### <a name="event-grid-metrics"></a>Métricas da grade de eventos

|Nome de exibição da métrica|Métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Entregas da grade de eventos (visualização)|EventGridDeliveries|Contagem|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento ( https://aka.ms/ioteventgrid) .|Resultado,<br/>EventType<br>*Para obter mais informações, consulte [dimensões de métrica](#metric-dimensions)*.|
|Latência da Grade de Eventos (versão prévia)|EventGridLatency|Milissegundos|Média|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|EventType<br>*Para obter mais informações, consulte [dimensões de métrica](#metric-dimensions)*.|

Para métricas com um valor de **unidade** de **contagem**, somente a agregação total (Sum) é válida. As agregações mínima, máxima e média sempre retornam 1. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

### <a name="jobs-metrics"></a>Métricas de trabalhos

|Nome de exibição da métrica|Métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Trabalhos concluídos|jobs.completed|Contagem|Total|A contagem de todos os trabalhos concluídos.|Nenhuma|
|Chamadas para listar trabalhos com falhas|jobs.listJobs.failure|Contagem|Total|A contagem de todas as chamadas para listar trabalhos com falhas.|Nenhuma|
|Criações de trabalhos de invocação de método com falhas|jobs.createDirectMethodJob.failure|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto com falhas.|Nenhuma|
|Criações de trabalhos de atualização de gêmeos com falhas|jobs.createTwinUpdateJob.failure|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos com falhas.|Nenhuma|
|Cancelamentos de trabalho com falhas|jobs.cancelJob.failure|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho com falhas.|Nenhuma|
|Consultas de trabalho com falhas|jobs.queryJobs.failure|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho com falhas.|Nenhuma|
|Trabalhos com falha|jobs.failed|Contagem|Total|A contagem de todos os trabalhos com falha.|Nenhuma|
|Chamadas para listar trabalhos bem-sucedidas|jobs.listJobs.success|Contagem|Total|A contagem de todas as chamadas para listar trabalhos bem-sucedidas.|Nenhuma|
|Criações de trabalhos de invocação de método bem-sucedidas|jobs.createDirectMethodJob.success|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto bem-sucedidas.|Nenhuma|
|Criações de trabalhos de atualização de gêmeos bem-sucedidas|Jobs. createTwinUpdateJob.<br>sucesso|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos bem-sucedidos.|Nenhuma|
|Cancelamentos de trabalho bem-sucedidos|jobs.cancelJob.success|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho bem-sucedidas.|Nenhuma|
|Consultas de trabalho bem-sucedidas|jobs.queryJobs.success|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho bem-sucedidas.|Nenhuma|

Para métricas com um valor de **unidade** de **contagem**, somente a agregação total (Sum) é válida. As agregações mínima, máxima e média sempre retornam 1. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

### <a name="routing-metrics"></a>Métricas de roteamento

|Nome de exibição da métrica|Métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
| Roteamento de tentativas de entrega (visualização) |RoutingDeliveries | Contagem | Total |Essa é a métrica de entrega de roteamento. Use as dimensões para identificar o status de entrega para um ponto de extremidade específico ou para uma fonte de roteamento específica.| Resultado,<br>Roteamento,<br>EndpointType<br>FailureReasonCategory,<br>EndpointName<br>*Para obter mais informações, consulte [dimensões de métrica](#metric-dimensions)*. |
| Tamanho dos dados de entrega de roteamento em bytes (versão prévia)|RoutingDataSizeInBytesDelivered| Bytes | Total |O número total de bytes roteados pelo Hub IoT para o ponto de extremidade personalizado e o ponto de extremidade interno. Use as dimensões para identificar o tamanho dos dados roteados para um ponto de extremidade específico ou para uma fonte de roteamento específica.| Roteamento,<br>EndpointType<br>EndpointName<br>*Para obter mais informações, consulte [dimensões de métrica](#metric-dimensions)*.|
| Latência de roteamento (versão prévia) |RoutingDeliveryLatency| Milissegundos | Média |Essa é a métrica de latência de entrega de roteamento. Use as dimensões para identificar a latência de um ponto de extremidade específico ou de uma fonte de roteamento específica.| Roteamento,<br>EndpointType<br>EndpointName<br>*Para obter mais informações, consulte [dimensões de métrica](#metric-dimensions)*.|
|Roteamento: blobs entregues ao armazenamento|d2c.endpoints.egress.storage.blobs|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou blobs aos pontos de extremidade de armazenamento.|Nenhuma|
|Roteamento: dados entregues ao armazenamento|d2c.endpoints.egress.storage.bytes|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregue aos pontos de extremidade de armazenamento.|Nenhuma|
|Roteamento: latência de mensagem para o Hub de Eventos|d2c.endpoints.latency.eventHubs|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem em pontos de extremidade personalizados do tipo Hub de eventos. Isso não inclui rotas de mensagens para ponto de extremidade interno (eventos).|Nenhuma|
|Roteamento: latência de mensagem para a Fila do Barramento de Serviço|d2c.endpoints.latency.serviceBusQueues|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem em um ponto de extremidade da fila do barramento de serviço.|Nenhuma|
|Roteamento: latência de mensagem para o Tópico do Barramento de Serviço|d2c.endpoints.latency.serviceBusTopics|Milissegundos|Média|A latência média (milissegundos) entre a entrada de mensagem para o Hub IoT e a entrada de mensagem em um ponto de extremidade de tópico do barramento de serviço.|Nenhuma|
|Roteamento: latência de mensagem para mensagens/eventos|d2c.Endpoints.Latency.builtIn.Events|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem no ponto de extremidade interno (mensagens/eventos) e na rota de fallback.|Nenhuma|
|Roteamento: latência de mensagem para armazenamento|d2c.endpoints.latency.storage|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem em um ponto de extremidade de armazenamento.|Nenhuma|
|Roteamento: mensagens entregues ao Hub de Eventos|d2c.endpoints.egress.eventHubs|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para pontos de extremidade personalizados do tipo Hub de eventos. Isso não inclui rotas de mensagens para ponto de extremidade interno (eventos).|Nenhuma|
|Roteamento: mensagens entregues à Fila do Barramento de Serviço|d2c.endpoints.egress.serviceBusQueues|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade da fila do Barramento de Serviço.|Nenhuma|
|Roteamento: mensagens entregues ao Tópico do Barramento de Serviço|d2c.endpoints.egress.serviceBusTopics|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do tópico do Barramento de Serviço.|Nenhuma|
|Roteamento: mensagens entregues ao fallback|d2c.telemetry.egress.fallback|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou mensagens ao ponto de extremidade associado à rota de fallback.|Nenhuma|
|Roteamento: mensagens entregues a mensagens/eventos|d2c.endpoints.egress.builtIn.events|Contagem|Total|O número de vezes que o roteamento do Hub IoT forneceu com êxito mensagens para o ponto de extremidade interno (mensagens/eventos) e a rota de fallback.|Nenhuma|
|Roteamento: mensagens entregues ao armazenamento|d2c.endpoints.egress.storage|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade de armazenamento.|Nenhuma|
|Roteamento: mensagens de telemetria entregues|d2c.telemetry.egress.success|Contagem|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem é encaminhada para vários pontos de extremidade, esse valor aumenta em um para cada entrega bem-sucedida. Se uma mensagem é entregue ao mesmo ponto de extremidade várias vezes, esse valor aumenta em um para cada entrega bem-sucedida.|Nenhuma|
|Roteamento: mensagens de telemetria removidas |d2c.telemetry.egress.dropped|Contagem|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido à inatividade dos pontos de extremidade. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens removidas não são entregues.|Nenhuma|
|Roteamento: mensagens de telemetria incompatíveis|d2c.telemetry.egress.invalid|Contagem|Total|O número de vezes que o roteamento do Hub IoT não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Uma mensagem é incompatível com um ponto de extremidade quando o Hub IOT tenta entregar a mensagem a um ponto de extremidade e falha com um erro não transitório. Mensagens inválidas não são repetidas. Esse valor não inclui novas tentativas.|Nenhuma|
|Roteamento: mensagens de telemetria órfãs |d2c.telemetry.egress.orphaned|Contagem|Total|O número de vezes que as mensagens ficaram órfãs pelo roteamento do Hub IoT porque não corresponderam a nenhuma consulta de roteamento, quando a rota de fallback está desabilitada.|Nenhuma|

Para métricas com um valor de **unidade** de **contagem**, somente a agregação total (Sum) é válida. As agregações mínima, máxima e média sempre retornam 1. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

### <a name="twin-query-metrics"></a>Métricas de consulta de entrelaçamento

|Nome de exibição da métrica|Métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Consultas de gêmeos com falhas|twinQueries.failure|Contagem|Total|A contagem de todas as consultas de gêmeos com falhas.|Nenhuma|
|Consultas de gêmeos bem-sucedidas|twinQueries.success|Contagem|Total|A contagem de todas as consultas de gêmeos bem-sucedidas.|Nenhuma|
|Tamanho do resultado das consultas de gêmeos|twinQueries.resultSize|Bytes|Média|O total do tamanho do resultado de todas as consultas de myup com êxito.|Nenhuma|

Para métricas com um valor de **unidade** de **contagem**, somente a agregação total (Sum) é válida. As agregações mínima, máxima e média sempre retornam 1. Para obter mais informações, consulte [agregações com suporte](#supported-aggregations).

## <a name="metric-dimensions"></a>Dimensões de métrica

O Hub IoT do Azure tem as seguintes dimensões associadas a algumas das suas métricas de roteamento e de grade de eventos.

|Nome da dimensão | Descrição|
|---|---|
||
|**EndpointName**| O nome do ponto de extremidade.|
|**EndpointType**|Um dos seguintes: **eventHubs**, **serviceBusQueues**, **cosmosDB**, **serviceBusTopics**. **BuiltIn** ou **blobStorage**.|
|**EventType**| Um dos seguintes tipos de evento da grade de eventos: **Microsoft. Devices. DeviceCreated**. **Microsoft. Devices. DeviceDeleted**, **Microsoft. Devices. DeviceConnected**, **Microsoft. Devices. DeviceDisconnected** ou **Microsoft. Devices. DeviceTelemetry**. Para obter mais informações, consulte [tipos de evento](iot-hub-event-grid.md#event-types).|
|**FailureReasonCategory**| Um dos seguintes: **inválido**, **Descartado**, **órfão** ou **nulo**.|
|**Resultado**| **Êxito** ou **falha**.|
|**Roteamento**| Mensagens do dispositivo<br>Eventos de alteração de entrelaçamento<br>Eventos de ciclo de vida do dispositivo|

Para saber mais sobre dimensões métricas, consulte [métricas multidimensionais](../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics).

## <a name="resource-logs"></a>Logs de recursos

Esta seção lista todos os tipos de categoria de log de recursos e os esquemas coletados para o Hub IoT do Azure. O provedor de recursos e o tipo para todos os logs do Hub IoT são [Microsoft. Devices/IotHubs](../azure-monitor/platform/resource-logs-categories.md#microsoftdevicesiothubs).

**Tópicos nesta seção**

- [Conexões](#connections)
- [Telemetria do Dispositivo](#device-telemetry)
- [Comandos da nuvem para o dispositivo](#cloud-to-device-commands)
- [Operações de identidade do dispositivo](#device-identity-operations)
- [Operações de upload de arquivo](#file-upload-operations)
- [Rotas](#routes)
- [Operações de nuvem gêmea para dispositivo](#device-to-cloud-twin-operations)
- [Operações de dispositivo gêmeo para nuvem](#cloud-to-device-twin-operations)
- [Consultas de gêmeos](#twin-queries)
- [Operações de trabalhos](#jobs-operations)
- [Métodos diretos](#direct-methods)
- [Rastreamento Distribuído (Versão Prévia)](#distributed-tracing-preview)
  - [Logs D2C do Hub IoT (dispositivo para nuvem)](#iot-hub-d2c-device-to-cloud-logs)
  - [Logs de entrada do Hub IoT](#iot-hub-ingress-logs)
  - [Logs de saída do Hub IoT](#iot-hub-egress-logs)
- [Configurações](#configurations)
- [Fluxos de dispositivo (visualização)](#device-streams-preview)

### <a name="connections"></a>conexões

O dispositivo de faixas de categoria conexões conecta e desconecta eventos um hub IoT, bem como erros. Esta categoria é útil para identificar tentativas de conexão não autorizadas e/ou alertas ao perder a conexão aos dispositivos.

> [!NOTE]
> Para obter um status de conexão confiável dos dispositivos, marque [Pulsação do dispositivo](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>Telemetria de dispositivo

A categoria de telemetria de dispositivo rastreia erros que ocorrem no Hub IoT e estão relacionados ao pipeline de telemetria. Essa categoria inclui erros que ocorrem no envio de eventos de telemetria (como limitação) e no recebimento de eventos de telemetria (como leitor não autorizado). Essa categoria não pode capturar erros causados pelo código em execução no próprio dispositivo.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>Comandos da nuvem para o dispositivo

A categoria de comandos da nuvem para o dispositivo rastreia erros que ocorrem no Hub IoT e são relacionados ao pipeline de mensagem da nuvem para o dispositivo. Essa categoria inclui erros que ocorrem ao:

* Enviar mensagens de nuvem para dispositivo (como erros de remetente não autorizado),
* Receber mensagens da nuvem para dispositivo (como erros de contagem de entrega excedida), e
* Receber comentários de mensagem da nuvem para dispositivo (como erros de comentário expirados).

Essa categoria não capturará erros quando a mensagem da nuvem para dispositivo for entregue com êxito, mas for manipulada incorretamente pelo dispositivo.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>Operações de identidade do dispositivo

A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando você tenta criar, atualizar ou excluir uma entrada no registro de identidade do seu Hub IoT. O rastreamento dessa categoria é útil em cenários de provisionamento.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>Operações de upload de arquivo

A categoria de carregamento de arquivo rastreia erros que ocorrem no Hub IoT e estão relacionados à funcionalidade de carregamento de arquivo. Essa categoria inclui:

* Erros que ocorrem com o URI de SAS, como quando ele expirar antes que um dispositivo notifique o hub de um carregamento concluído.

* Carregamentos com falha relatados pelo dispositivo.

* Erros que ocorrem quando um arquivo não for encontrado no armazenamento durante a criação de mensagem de notificação do Hub IoT.

Essa categoria não pode capturar erros que ocorrem diretamente enquanto o dispositivo está carregando um arquivo no armazenamento.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>Rotas

A categoria [Roteamento de mensagens](./iot-hub-devguide-messages-d2c.md) rastreia erros que ocorrem durante a avaliação de rota de mensagem e integridade do ponto de extremidade, conforme percebido pelo Hub IOT. Essa categoria inclui eventos, como:

* Uma regra é avaliada como "não definida";
* O Hub IoT marca um ponto de extremidade como inativo; ou
* Quaisquer erros recebidos de um ponto de extremidade.

Essa categoria não inclui erros específicos sobre as próprias mensagens (como erros de limitação de dispositivo), que são relatados na categoria "telemetria de dispositivo".

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Aqui estão mais detalhes sobre os logs de recursos de roteamento:

* [Lista de códigos de erro de log de recursos de roteamento](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Lista de OperationNames dos logs de recursos de roteamento](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>Operações de nuvem gêmea para dispositivo

A categoria de operações de gêmeos de dispositivo para nuvem rastreia eventos iniciados pelo dispositivo em dispositivos gêmeos. Essas operações podem incluir gêmeos, atualizar as propriedades relatadas e assinar as propriedades desejadas.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>Operações de dispositivo gêmeo para nuvem

A categoria de operações de gêmeos de nuvem para dispositivo rastreia eventos iniciados pelo serviço em dispositivos gêmeos. Essas operações podem incluir obter gêmeo, atualizar ou substituir marcas e atualizar ou substituir propriedades desejadas.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>Consultas de gêmeos

A categoria de consultas de gêmeos relata sobre solicitações de consulta para dispositivos gêmeos que são iniciados na nuvem.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>Operações de trabalhos

A categoria de operações de trabalhos relata sobre solicitações de trabalho para atualizar dispositivos gêmeos ou invocar métodos diretos em vários dispositivos. Essas solicitações são iniciadas na nuvem.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>Métodos diretos

A categoria de métodos diretos rastreia as interações solicitação-resposta enviadas a dispositivos individuais. Essas solicitações são iniciadas na nuvem.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>Rastreamento Distribuído (Versão Prévia)

A categoria de rastreamento distribuído rastreia as IDs de correlação para mensagens que carregam o cabeçalho de contexto de rastreamento. Para habilitar esses logs totalmente, o código do lado do cliente deve ser atualizado ao seguir [analisar e diagnosticar aplicativos IOT de ponta a ponta com o rastreamento distribuído do Hub IOT (versão prévia)](iot-hub-distributed-tracing.md).

Observe que `correlationId` está em conformidade com a proposta de [contexto de rastreamento W3C](https://github.com/w3c/trace-context) , em que ele contém um, `trace-id` bem como um `span-id` .

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Logs D2C do Hub IoT (dispositivo para nuvem)

O Hub IoT registra esse log quando uma mensagem que contém propriedades de rastreamento válidas chega ao Hub IoT.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Aqui, `durationMs` não é calculado, uma vez que o relógio do Hub IoT não pode ser sincronizado com o relógio do dispositivo e, portanto, um cálculo de duração pode ser enganoso. Recomendamos gravar escrever lógica usando carimbos de data/hora na seção `properties` para capturar os picos na latência de dispositivo para nuvem.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | O tamanho da mensagem de dispositivo para nuvem em bytes |
| **deviceId** | Cadeia de caracteres alfanumérica ASCII de 7 bits | A identidade do dispositivo |
| **callerLocalTimeUtc** | Carimbo de data/hora UTC | A hora de criação da mensagem conforme relatada pelo relógio local do dispositivo |
| **calleeLocalTimeUtc** | Carimbo de data/hora UTC | A hora de chegada da mensagem no gateway do Hub IoT conforme relatado pelo relógio do lado do serviço de Hub IoT |

#### <a name="iot-hub-ingress-logs"></a>Logs de entrada do Hub IoT

O Hub IoT registra esse log quando a mensagem que contém as propriedades de rastreamento válido grava no Hub de Eventos interno ou integrado.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

Na `properties` seção, esse log contém informações adicionais sobre a entrada da mensagem.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | Verdadeiro ou falso, indica se o roteamento de mensagens está ou não habilitado no Hub IoT |
| **parentSpanId** | String | A [ID do span](https://w3c.github.io/trace-context/#parent-id) da mensagem pai, que seria, neste caso, o rastreamento de mensagens D2C |

#### <a name="iot-hub-egress-logs"></a>Logs de saída do Hub IoT

O Hub IoT registra esse log quando [roteamento](iot-hub-devguide-messages-d2c.md) está habilitado e a mensagem é gravada para um [ponto de extremidade](iot-hub-devguide-endpoints.md). Se o roteamento não estiver habilitado, o Hub IoT não registrará esse log.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

Na `properties` seção, esse log contém informações adicionais sobre a entrada da mensagem.

| Propriedade | Tipo | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | O nome do ponto de extremidade de roteamento |
| **endpointType** | String | O tipo de roteamento o ponto de extremidade |
| **parentSpanId** | String | A [ID do span](https://w3c.github.io/trace-context/#parent-id) da mensagem pai, que seria, neste caso, o rastreamento de mensagens de entrada do Hub IoT |

### <a name="configurations"></a>Configurações

Os logs de configuração do Hub IoT rastreiam eventos e erros para o conjunto de recursos de gerenciamento automático de dispositivos.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Fluxos de dispositivo (visualização)

A categoria fluxos de dispositivo rastreia as interações de solicitação-resposta enviadas a dispositivos individuais.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Tabelas de Logs do Azure Monitor
<!-- REQUIRED. Please keep heading in this order -->

Esta seção refere-se a todos os Azure Monitor logs Kusto tabelas relevantes ao Hub IoT do Azure e disponíveis para consulta por Log Analytics. Para obter uma lista dessas tabelas e links para obter mais informações sobre o tipo de recurso do Hub IoT, consulte [Hub IOT](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub) na referência da tabela Logs de Azure monitor.

Para obter uma referência de todas as tabelas de Logs do Azure Monitor/Log Analytics, confira a [Referência da tabela de log do Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="see-also"></a>Consulte Também

* Consulte [monitorar o Hub IOT do Azure](monitor-iot-hub.md) para obter uma descrição do monitoramento do Hub IOT do Azure.
* Confira [Como monitorar os recursos do Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre o monitoramento de recursos do Azure.
