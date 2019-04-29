---
title: Portas e protocolos de comunicação do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – descreve os protocolos de comunicação com suporte para comunicações de dispositivo para a nuvem e da nuvem para dispositivos e os números de porta que devem ser abertos.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 7082ebc4ca3066f84ca9790797cfa04e437f78a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626172"
---
# <a name="reference---choose-a-communication-protocol"></a>Referência – escolha um protocolo de comunicação

O Hub IoT permite que os dispositivos usem os seguintes protocolos para comunicação no lado do dispositivo:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT sobre WebSockets
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP sobre WebSockets
* HTTPS

Para saber mais sobre como esses protocolos dão suporte a recursos específicos do Hub IoT, confira [Orientação sobre comunicações de dispositivo para nuvem](iot-hub-devguide-d2c-guidance.md) e [Orientação sobre comunicações de nuvem para dispositivo](iot-hub-devguide-c2d-guidance.md).

A tabela a seguir fornece as recomendações de alto nível para sua escolha de protocolo:

| Protocolo | Quando você deve escolher este protocolo |
| --- | --- |
| MQTT <br> MQTT sobre WebSocket |Use em todos os dispositivos que não exigem conectar vários dispositivos (cada um com suas próprias credenciais por dispositivo) sobre a mesma conexão TLS. |
| AMQP <br> AMQP sobre WebSocket |Use em gateways de campo e de nuvem para tirar proveito da multiplexação de conexão entre dispositivos. |
| HTTPS |Use para dispositivos que não dão suporte a outros protocolos. |

Considere os seguintes pontos ao escolher seu protocolo de comunicação do lado do dispositivo:

* **Padrão da nuvem para o dispositivo**. O HTTPS não tem uma maneira eficiente de implementar o envio por push do servidor. Assim, ao usar HTTPS, os dispositivos sondam o Hub IoT em busca de mensagens da nuvem para o dispositivo. Essa abordagem é ineficiente para o dispositivo e para o Hub IoT. De acordo com as diretrizes atuais de HTTPS, cada dispositivo deve sondar mensagens a cada 25 minutos ou mais. MQTT e o AMQP dão suporte ao envio por push do servidor quando recebem mensagens de nuvem para o dispositivo. Eles permitem envios por push imediatos de mensagens do Hub IoT para o dispositivo. Se a latência de entrega for uma preocupação, o MQTT ou AMQP serão as melhores opções de protocolo. Para dispositivos conectados raramente, o HTTPS funciona também.

* **Gateways de campo**. Ao usar MQTT e HTTPS, você não pode conectar vários dispositivos (cada um com suas próprias credenciais por dispositivo) usando a mesma conexão TLS. Para [cenários de gateway de campo](iot-hub-devguide-endpoints.md#field-gateways), que exigem uma conexão TLS entre o gateway de campo e o Hub IoT para cada dispositivo conectado, esses protocolos ficam abaixo do ideal.

* **Dispositivos com poucos recursos**. As bibliotecas de MQTT e HTTPS têm uma superfície menor do que as bibliotecas de AMQP. Dessa forma, caso o dispositivo tenha recursos limitados (por exemplo, menos de 1 MB de RAM), esses protocolos poderão ser a única implementação de protocolo disponível.

* **Percurso da rede**. O protocolo AMQP padrão usa a porta 5671 e o MQTT escuta na porta 8883. O uso dessas portas pode causar problemas em redes que estão fechadas para protocolos não HTTPS. Use MQTT por WebSockets, AMQP, AMQP por WebSockets ou HTTPS nesse cenário.

* **Tamanho da carga**. O MQTT e o AMQP são protocolos binários, que resultam em cargas mais compactas que o HTTPS.

> [!WARNING]
> Ao usar o HTTPS, cada dispositivo deverá sondar se há mensagens da nuvem para o dispositivo a cada 25 minutos ou mais. No entanto, durante o desenvolvimento, é aceitável sondar com mais frequência do que a cada 25 minutos.

## <a name="port-numbers"></a>Números de porta

Os dispositivos podem se comunicar com o Hub IoT no Azure usando uma variedade de protocolos. Normalmente, a opção de protocolo é orientada por requisitos específicos da solução. A tabela a seguir lista as portas de saída que devem ser abertas para um dispositivo para poder usar um protocolo específico:

| Protocolo | Porta |
| --- | --- |
| MQTT |8883 |
| MQTT sobre WebSockets |443 |
| AMQP |5671 |
| AMQP sobre WebSockets |443 |
| HTTPS |443 |

Depois de criar um Hub IoT em uma região do Azure, o hub manterá o mesmo endereço IP durante a vida útil desse Hub IoT. No entanto, para manter a qualidade de serviço, se a Microsoft mover o Hub IoT para uma unidade de escala diferente, um novo endereço IP será atribuído a ele.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como o Hub IoT implementa o protocolo MQTT, confira [Comunicação com o hub IoT usando o protocolo MQTT](iot-hub-mqtt-support.md).
