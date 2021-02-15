---
title: Solução de problemas do Hub IoT do Azure erro 403004 DeviceMaximumQueueDepthExceeded
description: Entenda como corrigir o erro 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 421066ef30e23a79b26f97939cdfffb5be83afb5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148234"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

Este artigo descreve as causas e soluções para erros de **403004 DeviceMaximumQueueDepthExceeded** .

## <a name="symptoms"></a>Sintomas

Ao tentar enviar uma mensagem da nuvem para o dispositivo, a solicitação falha com o erro **403004** ou **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Causa

A causa subjacente é que o número de mensagens enfileiradas para o dispositivo excede o [limite da fila (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

O motivo mais provável que você está executando nesse limite é porque você está usando HTTPS para receber a mensagem, o que leva à sondagem contínua usando `ReceiveAsync` , resultando na limitação do Hub IOT à solicitação.

## <a name="solution"></a>Solução

O padrão com suporte para mensagens da nuvem para o dispositivo com o HTTPS são dispositivos conectados intermitentemente que verificam mensagens com pouca frequência (menos do que a cada 25 minutos). Para reduzir a probabilidade de execução no limite da fila, alterne para AMQP ou MQTT para mensagens da nuvem para o dispositivo.

Como alternativa, aprimore a lógica do lado do dispositivo para concluir, rejeitar ou abandonar mensagens em fila rapidamente, encurtar o tempo de vida ou considere enviar menos mensagens. Consulte [vida útil da mensagem C2D](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Por fim, considere usar a [API de limpeza da fila](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice) para limpar periodicamente as mensagens pendentes antes que o limite seja atingido.