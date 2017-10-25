---
title: Comparar o Hub IoT do Azure ao Hubs de Eventos do Azure | Microsoft Docs
description: "Uma comparação dos serviços do Hub IoT e de Eventos do Azure destacando diferenças funcionais e casos de uso. A comparação inclui protocolos com suporte, gerenciamento de dispositivos, monitoramento e uploads de arquivos."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: 44dc7b18d46d1e75e8b55b6e32a346272a06bda9
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Comparação do Hub IoT do Azure e Hubs de Eventos do Azure
Um dos principais casos de uso do Hub IoT é coletar a telemetria dos dispositivos. Por esse motivo, o Hub IoT muitas vezes é comparado aos [Hubs de Eventos do Azure][Azure Event Hubs]. Assim como o Hub IoT, o Hubs de Eventos do Azure é um serviço de processamento de eventos que permite a entrada em grande escala de telemetria e de eventos na nuvem, com baixa latência e alta confiabilidade.

Entretanto, os serviços têm várias diferenças, que são detalhadas na tabela a seguir:

| Área | Hub IoT | Hubs de Eventos |
| --- | --- | --- |
| Padrões de comunicação | Permite [comunicação do dispositivo com a nuvem][lnk-d2c-guidance] (mensagens, carregamentos de arquivos e propriedades reportadas) e [comunicação da nuvem com o dispositivo][lnk-c2d-guidance] (métodos diretos, propriedades desejadas, mensagens). |Permite somente a entrada de eventos (geralmente considerada para cenários do dispositivo para a nuvem). |
| Informações de estado do dispositivo | [Dispositivos gêmeos][lnk-twins] podem armazenar e consultar informações de estado do dispositivo. | Nenhuma informação de estado do dispositivo pode ser armazenada. |
| Suporte ao protocolo de dispositivo |Compatível com MQTT, MQTT por WebSockets, AMQP, AMQP por WebSockets e HTTPS. Além disso, o Hub IoT funciona com o [gateway de protocolo do IoT do Azure][lnk-azure-protocol-gateway], uma implementação de gateway de protocolo personalizável que permite protocolos personalizados. |Compatível com AMQP, AMQP sobre WebSockets e HTTPS. |
| Segurança |Fornece identidade por dispositivo e controle de acesso revogável. Confira a [seção Segurança do guia do desenvolvedor de Hub IoT]. |Fornece [políticas de acesso compartilhado][Event Hubs - security] em todos os Hubs de Eventos, com suporte limitado à revogação usando [políticas do editor][Event Hubs publisher policies]. As soluções IoT muitas vezes devem implementar uma solução personalizada para permitir credenciais e medidas contra falsificação por dispositivo. |
| Monitoramento de operações |Permite que as soluções IoT assinem um conjunto avançado de eventos de conectividade e gerenciamento de identidade de dispositivo, como erros de autenticação de dispositivos individuais, limitação e exceções de formato inválido. Esses eventos permitem identificar rapidamente problemas de conectividade no nível do dispositivo individual. |Expõe apenas as métricas de agregação. |
| Escala |É otimizada para dar suporte a milhões de dispositivos conectados simultaneamente. |Limita as conexões conforme as [cotas de Hubs de Eventos do Azure][Azure Event Hubs quotas]. Por outro lado, os Hubs de Eventos permitem que você especifique a partição de cada mensagem enviada. |
| SDKs de dispositivo |Fornece [SDKs de dispositivo][Azure IoT SDKs] para uma grande variedade de plataformas e linguagens, além de APIs diretas de MQTT, AMQP e HTTPS. |Tem suporte no .NET, Java e C, além de nas interfaces de envio AMQP e HTTPS. |
| Upload de arquivos |Permite que soluções IoT carreguem arquivos de dispositivos para a nuvem. Inclui um ponto de extremidade de notificação de arquivo para a integração de fluxo de trabalho e uma categoria de monitoramento de operações para o suporte à depuração. | Sem suporte. |
| Rotear mensagens para vários pontos de extremidade | Há suporte para até 10 pontos de extremidade personalizados. As regras determinam como as mensagens são roteadas para os pontos de extremidade personalizados. Para saber mais, confira [Enviar e receber mensagens com o Hub IoT][lnk-devguide-messaging]. | Exige a composição e hospedagem de um código adicional para despacho de mensagens. |

Em resumo, mesmo se o único caso de uso for a entrada de telemetria do dispositivo para nuvem, o Hub IoT fornecerá um serviço que foi desenvolvido para conectividade do dispositivo IoT. Ele continuará expandindo as propostas de valor para esses cenários com recursos específicos de IoT. Os Hubs de Eventos são projetados para a entrada de evento em grande escala, no contexto de cenários internos de datacenter e entre datacenters.

É comum usar o Hub IoT e os Hubs de Eventos na mesma solução. O Hub IoT trata da comunicação do dispositivo com a nuvem e os Hubs de Eventos tratam da entrada do evento em estágio posterior nos mecanismos de processamento em tempo real.

### <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como planejar sua implantação do Hub IoT, veja [Escalando, HA e DR][lnk-scaling].

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Simulando um dispositivo com IoT Edge][lnk-iotedge]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[seção Segurança do guia do desenvolvedor de Hub IoT]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-features.md#event-publishers
[Azure Event Hubs quotas]: ../event-hubs/event-hubs-quotas.md
[Azure IoT SDKs]: https://github.com/Azure/azure-iot-sdks
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
