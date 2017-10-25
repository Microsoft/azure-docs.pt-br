---
title: "Usar métricas para monitorar o IoT Hub do Azure | Microsoft Docs"
description: "Como usar as métricas do Hub IoT do Azure para avaliar e monitorar a integridade geral dos seus hubs IoT."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e850370faf2d271b4adad1af48c1ead7b316fa67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-iot-hub-metrics"></a>Entender as métricas de Hub IoT
As métricas do Hub IoT fornecem dados melhores sobre o estado dos recursos IoT do Azure na sua assinatura do Azure. As métricas Hub IoT permitem avaliar a integridade geral do serviço do Hub IoT e dos dispositivos conectados a ele. As estatísticas voltadas para o usuário são importantes porque elas ajudam você a ver o que está acontecendo com o Hub IoT e com os problemas de causa raiz sem precisar contatar o suporte do Azure.

As métricas são habilitadas por padrão. Você pode exibir as métricas do Hub IoT no portal do Azure.

## <a name="how-to-view-iot-hub-metrics"></a>Como exibir métricas de IoT Hub
1. Crie um Hub IoT. Você pode encontrar instruções sobre como criar um Hub IoT no guia de [Introdução][lnk-get-started].
2. Abra a folha do seu Hub IoT. A partir daí, clique em **métricas**.
   
    ![][1]
3. Folha de métricas, você pode exibir as métricas para o hub IoT e criar exibições personalizadas de suas métricas. Você pode optar por enviar seus dados de métricas para um ponto de extremidade de Hubs de eventos ou uma conta de armazenamento do Azure clicando em **configurações de diagnóstico**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métricas do Hub IoT e como usá-las
O Hub IoT fornece várias métricas que dão uma visão geral da integridade do hub e o número total de dispositivos conectados. É possível combinar informações de várias métricas para ter uma ideia mais ampla do estado do Hub IoT. A tabela a seguir descreve as métricas que cada Hub IoT rastreia e como cada métrica se relaciona com o status geral do Hub IoT.

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de tentativas de envio de mensagens de telemetria do dispositivo para nuvem para o hub IoT|
|d2c.telemetry.ingress.success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para nuvem enviadas com êxito para o hub IoT|
|c2d.commands.egress.complete.success|Comandos concluídos|Contagem|Total|Número de comandos da nuvem para o dispositivo concluídos com sucesso pelo dispositivo|
|c2d.commands.egress.abandon.success|Comandos abandonados|Contagem|Total|Número de comandos da nuvem para o dispositivo abandonados pelo dispositivo|
|c2d.commands.egress.reject.success|Comandos rejeitados|Contagem|Total|Número de comandos da nuvem para o dispositivo rejeitados pelo dispositivo|
|devices.totalDevices|Total de dispositivos|Contagem|Total|Número de dispositivos registrados para o seu hub IoT|
|devices.connectedDevices.allProtocol|Dispositivos conectados|Contagem|Total|Número de dispositivos registrados ao seu hub IoT|
|d2c.telemetry.egress.success|Mensagens de telemetria entregues|Contagem|Total|Número de vezes que as mensagens foram gravadas com êxito nos pontos de extremidade (total)|
|d2c.telemetry.egress.dropped|Mensagens descartadas|Contagem|Total|Número de mensagens descartadas por não corresponderem a nenhuma rota e a rota de fallback foi desabilitada|
|d2c.telemetry.egress.orphaned|Mensagens órfãs|Contagem|Total|A contagem de mensagens que não correspondem a nenhuma rota, incluindo a rota de fallback|
|d2c.telemetry.egress.invalid|Mensagens inválidas|Contagem|Total|A contagem de mensagens não entregues devido à incompatibilidade com o ponto de extremidade|
|d2c.telemetry.egress.fallback|Mensagens que correspondem à condição de fallback|Contagem|Total|Número de mensagens gravadas para o ponto de extremidade de fallback|
|d2c.endpoints.egress.eventHubs|Mensagens entregues aos pontos de extremidade do Hub de Evento|Contagem|Total|Número de vezes que as mensagens foram gravadas com êxito nos pontos de extremidade do Hub de Evento|
|d2c.endpoints.latency.eventHubs|Latência de mensagem para pontos de extremidade do Hub de Eventos|Milissegundos|Média|A latência média entre a entrada da mensagem no hub IoT e a entrada da mensagem em um ponto de extremidade do Hub de Eventos, em milissegundos|
|d2c.endpoints.egress.serviceBusQueues|Mensagens entregues aos pontos de extremidade da Fila do Barramento de Serviço|Contagem|Total|Número de vezes que as mensagens foram gravadas com êxito nos pontos de extremidade da Fila do Barramento de Serviço|
|d2c.endpoints.latency.serviceBusQueues|Latência de mensagem para pontos de extremidade da Fila do Barramento de Serviço|Milissegundos|Média|A latência média, mínima e máxima entre a entrada da mensagem no hub IoT e a entrada da mensagem em um ponto de extremidade de Fila do Barramento de Serviço, em milissegundos|
|d2c.endpoints.egress.serviceBusTopics|Mensagens entregues aos pontos de extremidade do Tópico do Barramento de Serviço|Contagem|Total|Número de vezes que as mensagens foram gravadas com êxito nos pontos de extremidade do Tópico do Barramento de Serviço|
|d2c.endpoints.latency.serviceBusTopics|Latência de mensagem para pontos de extremidade de Tópico do Barramento de Serviço|Milissegundos|Média|A latência média, mínima e máxima entre a entrada da mensagem no hub IoT e a entrada da mensagem em um ponto de extremidade de Tópico do Barramento de Serviço, em milissegundos|
|d2c.endpoints.egress.builtIn.events|Mensagens entregues ao ponto de extremidade interno (mensagens/eventos)|Contagem|Total|Número de vezes que as mensagens foram gravadas com êxito no ponto de extremidade interno (mensagens/eventos)|
|d2c.Endpoints.Latency.builtIn.Events|Latência de mensagem para o ponto de extremidade interno (mensagens/eventos)|Milissegundos|Média|A latência média, mínima e máxima entre a entrada da mensagem no hub IoT e a entrada da mensagem no ponto de extremidade interno (mensagens/eventos), em milissegundos |
|d2c.twin.read.success|Leituras de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|
|d2c.twin.read.failure|Leituras de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo com falhas.|
|d2c.twin.read.size|Tamanho da resposta das leituras de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|
|d2c.twin.update.success|Atualizações de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|
|d2c.twin.update.failure|Atualizações de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo.|
|d2c.twin.update.size|Tamanho das atualizações de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|
|c2d.methods.success|Invocações de método diretas bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|
|c2d.methods.failure|Invocações de método diretas com falhas|Contagem|Total|A contagem de todas as chamadas de método diretas com falhas.|
|c2d.methods.requestSize|Tamanho da solicitação das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|
|c2d.methods.responseSize|Tamanho da resposta das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|
|c2d.twin.read.success|Leituras de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|
|c2d.twin.read.failure|Leituras de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de leituras de gêmeos iniciadas pelo back-end.|
|c2d.twin.read.size|Tamanho da resposta das leituras de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|
|c2d.twin.update.success|Atualizações de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|
|c2d.twin.update.failure|Atualizações de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de atualizações de gêmeos iniciadas pelo back-end.|
|c2d.twin.update.size|Tamanho das atualizações de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|
|twinQueries.success|Consultas de gêmeos bem-sucedidas|Contagem|Total|A contagem de todas as consultas de gêmeos bem-sucedidas.|
|twinQueries.failure|Consultas de gêmeos com falhas|Contagem|Total|A contagem de todas as consultas de gêmeos com falhas.|
|twinQueries.resultSize|Tamanho do resultado das consultas de gêmeos|Bytes|Média|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de gêmeos bem-sucedidas.|
|jobs.createTwinUpdateJob.success|Criações de trabalhos de atualização de gêmeos bem-sucedidas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos bem-sucedidos.|
|jobs.createTwinUpdateJob.failure|Criações de trabalhos de atualização de gêmeos com falhas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos com falhas.|
|jobs.createDirectMethodJob.success|Criações de trabalhos de invocação de método bem-sucedidas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto bem-sucedidas.|
|jobs.createDirectMethodJob.failure|Criações de trabalhos de invocação de método com falhas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto com falhas.|
|jobs.listJobs.success|Chamadas para listar trabalhos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos bem-sucedidas.|
|jobs.listJobs.failure|Chamadas para listar trabalhos com falhas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos com falhas.|
|jobs.cancelJob.success|Cancelamentos de trabalho bem-sucedidos|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho bem-sucedidas.|
|jobs.cancelJob.failure|Cancelamentos de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho com falhas.|
|jobs.queryJobs.success|Consultas de trabalho bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho bem-sucedidas.|
|jobs.queryJobs.failure|Consultas de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho com falhas.|
|jobs.completed|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|
|jobs.failed|Trabalhos com falha|Contagem|Total|A contagem de todos os trabalhos com falha.|

## <a name="next-steps"></a>Próximas etapas
Depois da visão geral das métricas do Hub IoT, acesse este link para saber mais sobre o gerenciamento do Hub IoT do Azure:

* [Monitoramento de operações][lnk-monitor]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Simulando um dispositivo com Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
