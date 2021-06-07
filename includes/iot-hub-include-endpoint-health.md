---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 24a07109fc8f4d6ebd283dee7ee00107f0eb49b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95560821"
---
Você pode usar a integridade do [ponto de extremidade Get](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) da API REST para obter o status de integridade dos pontos de extremidade. É recomendável usar as [métricas de roteamento do Hub IOT](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) relacionadas à latência da mensagem de roteamento para identificar e depurar erros quando a integridade do ponto de extremidade está inativa ou não íntegra, pois esperamos que a latência seja maior quando o ponto de extremidade estiver em um desses Estados. Para saber mais sobre como usar as métricas do Hub IoT, confira [monitorar o Hub IOT](../articles/iot-hub/monitor-iot-hub.md).

|Status de integridade|Descrição|
|---|---|
|Healthy|O ponto de extremidade está aceitando mensagens conforme o esperado.|
|não íntegro|O ponto de extremidade não está aceitando mensagens e o Hub IoT está tentando novamente enviar mensagens para esse ponto de extremidade.|
|unknown|O Hub IoT não tentou entregar mensagens a este ponto de extremidade.|
|degradado|O ponto de extremidade está aceitando mensagens mais lentas do que o esperado ou está se recuperando de um estado não íntegro.|
|mortos|O Hub IoT não está mais entregando mensagens para este ponto de extremidade. Falha ao tentar enviar mensagens para este ponto de extremidade.|