---
title: Monitorar tópicos e assinaturas de evento-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Monitorar tópicos e assinaturas de evento
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d230be4f74abd61ad7b7f0fdb3facb32ee63da73
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171526"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Monitorar tópicos e assinaturas de evento

A grade de eventos no Edge expõe um número de métricas para tópicos e assinaturas de evento no [formato Prometheus exposição](https://prometheus.io/docs/instrumenting/exposition_formats/). Este artigo descreve as métricas disponíveis e como habilitá-las.

## <a name="enable-metrics"></a>Habilitar a métrica

Configure o módulo para emitir métricas definindo a `metrics__reporterType` variável de ambiente como `prometheus` nas opções de criação de contêiner:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

As métricas estarão disponíveis em `5888/metrics` seu módulo para http e `4438/metrics` https. Por exemplo, `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` para http. Neste ponto, um módulo de métricas pode sondar o ponto de extremidade para coletar métricas como nesta [arquitetura de exemplo](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Métricas disponíveis

Os tópicos e as assinaturas de evento emitem as métricas para fornecer informações sobre a entrega de eventos e o desempenho do módulo.

### <a name="topic-metrics"></a>Métricas de tópico

| Métrica | Descrição |
| ------ | ----------- |
| EventsReceived | Número de eventos publicados no tópico
| UnmatchedEvents | Número de eventos publicados no tópico que não correspondem a uma assinatura de evento e são removidos
| SuccessRequests | Número de solicitações de publicação de entrada recebidas pelo tópico
| SystemErrorRequests | Número de solicitações de publicação de entrada com falha devido a um erro interno do sistema
| UserErrorRequests | Número de solicitações de publicação de entrada com falha devido a um erro do usuário, como JSON malformado
| SuccessRequestLatencyMs | Latência de resposta de solicitação de publicação em milissegundos


### <a name="event-subscription-metrics"></a>Métricas de assinatura de evento

| Métrica | Descrição |
| ------ | ----------- |
| DeliverySuccessCounts | Número de eventos entregues com êxito ao ponto de extremidade configurado
| DeliveryFailureCounts | Número de eventos que falharam ao serem entregues ao ponto de extremidade configurado
| DeliverySuccessLatencyMs | Latência de eventos entregues com êxito em milissegundos
| DeliveryFailureLatencyMs | Latência de falhas de entrega de eventos em milissegundos
| SystemDelayForFirstAttemptMs | Atraso do sistema de eventos antes da primeira tentativa de entrega em milissegundos
| DeliveryAttemptsCount | Número de tentativas de entrega de eventos-êxito e falha
| ExpiredCounts | Número de eventos que expiraram e não foram entregues ao ponto de extremidade configurado