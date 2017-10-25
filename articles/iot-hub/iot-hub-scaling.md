---
title: Dimensionamento do Hub IoT do Azure | Microsoft Docs
description: "Como dimensionar o Hub IoT para dar suporte à taxa de transferência de mensagens esperada. Inclui um resumo das taxas de transferência com suporte para cada camada e opções de fragmentação."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2cb263103da05b10c24aab71d81c43eb25987565
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scale-your-iot-hub-solution"></a>Escalar sua solução do hub IoT
O Hub do IoT do Azure pode oferecer suporte a até um milhão de dispositivos conectados ao mesmo tempo. Para saber mais, confira [Preço do Hub IoT][lnk-pricing]. Cada unidade do Hub IoT permite uma determinada quantidade de mensagens diárias.

Para dimensionar corretamente sua solução, considere sua utilização específica do Hub IoT. Em particular, considere a taxa de transferência de pico necessária para as seguintes categorias de operações:

* Mensagens do dispositivo para a nuvem
* Mensagens da nuvem para o dispositivo
* Operações de registro de identidade

Além dessas informações sobre produtividade, confira as [Cotas e limites do Hub IoT][IoT Hub quotas and throttles] e crie sua solução segundo com tais informações.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Taxa de transferência de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo
A melhor maneira de dimensionar uma solução do Hub IoT é avaliar o tráfego de acordo com a unidade.

As mensagens do dispositivo para a nuvem seguem estas diretrizes de taxa de transferência sustentada.

| Camada | Taxa de transferência sustentada | Taxa de envio sustentada |
| --- | --- | --- |
| S1 |Até 1111 KB/minuto por unidade<br/>(1,5 GB/dia/unidade) |Média de 278 mensagens/minuto por unidade<br/>(400.000 mensagens/dia por unidade) |
| S2 |Até 16 MB/minuto por unidade<br/>(22,8 GB/dia/unidade) |Média de 4.167 mensagens/minuto por unidade<br/>(6 milhões de mensagens/dia por unidade) |
| S3 |Até 814 MB/minuto por unidade<br/>(1144,4 GB/dia/unidade) |Média de 208,333 mensagens/minuto por unidade<br/>(300 milhões de mensagens/dia por unidade) |

## <a name="identity-registry-operation-throughput"></a>Taxa de transferência de operações de registro de identidade
As operações de Registro de identidade do Hub IoT não devem ser operações em tempo de execução, pois estão relacionadas principalmente com provisionamento do dispositivo.

Para ver número de pico de desempenho específicos, consulte [Cotas e limites do Hub IoT][IoT Hub quotas and throttles].

## <a name="sharding"></a>Fragmentação
Enquanto um único Hub IoT pode ser dimensionado para milhões de dispositivos, às vezes sua solução exigirá características específicas de desempenho que um único Hub IoT não pode garantir. Nesse caso, é recomendável que você particione seus dispositivos em vários Hubs IoT. Vários hubs IoT suavizam picos de tráfego e obtêm a taxa de transferência ou as taxas de operação necessárias.

## <a name="next-steps"></a>Próximas etapas
Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Simulando um dispositivo com Azure IoT Edge][lnk-iotedge]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
