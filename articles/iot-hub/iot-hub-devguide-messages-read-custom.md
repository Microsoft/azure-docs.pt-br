---
title: Compreender os pontos de extremidade personalizados do Hub IoT do Azure | Microsoft Docs
description: "Guia do desenvolvedor – regras de direcionamento para direcionar mensagens de dispositivo para a nuvem para pontos de extremidade personalizados."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: dobett
ms.openlocfilehash: a499783fc02e1371562edd41b827758e19fbd823
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Usar rotas de mensagens e pontos de extremidade personalizados para mensagens de dispositivo para a nuvem

O Hub IoT habilita o direcionamento de [mensagens de dispositivo para a nuvem][lnk-device-to-cloud] para pontos de extremidade voltados para o serviço do Hub IoT com base nas propriedades da mensagem. As regras de roteamento oferecem a flexibilidade para enviar mensagens para onde elas precisam ir sem a necessidade de serviços adicionais para processar mensagens ou escrever código adicional. Cada regra de roteamento que você configurar tem as seguintes propriedades:

| Propriedade      | Descrição |
| ------------- | ----------- |
| **Nome**      | O nome exclusivo que identifica a regra. |
| **Fonte**    | A origem do fluxo de dados a ser afetado. Por exemplo, telemetria do dispositivo. |
| **Condição** | A expressão de consulta para a regra de roteamento que é executada em relação ao cabeçalho e ao corpo da mensagem e usada para determinar se ela é uma correspondência para o ponto de extremidade. Para obter mais informações sobre como construir uma condição de rota, consulte o [Referência – linguagem de consulta para dispositivos gêmeos e trabalhos][lnk-devguide-query-language]. |
| **Ponto de extremidade**  | O nome do ponto de extremidade em que Hub IoT envia as mensagens que correspondem à condição. Os pontos de extremidade devem estar na mesma região que o Hub IoT, caso contrário, você pode ser cobrado por gravações entre regiões. |

Uma única mensagem pode corresponder à condição em várias regras de roteamentos, caso em que o Hub IoT entrega a mensagem para o ponto de extremidade associado a cada regra correspondente. O Hub IoT também elimina a duplicação da entrega de mensagem automaticamente, portanto, se uma mensagem corresponder a várias regras que têm o mesmo destino, ela será gravada apenas uma vez no destino.

Um hub IoT tem um [ponto de extremidade interno][lnk-built-in] padrão. Você pode criar pontos de extremidade personalizados para direcionar mensagens ou vinculando outros serviços em sua assinatura ao hub. O Hub IoT atualmente dá suporte aos contêineres de Armazenamento do Microsoft Azure, Hubs de Eventos, filas do Barramento de Serviço e tópicos do Barramento de Serviço como pontos de extremidade personalizados.

> [!NOTE]
> O Hub IoT somente dá suporte à gravação de dados em contêineres de Armazenamento do Microsoft Azure como blobs.

> [!WARNING]
> As filas e os tópicos do Barramento de Serviço com **Sessões** ou **Detecção de Duplicidades** habilitadas não têm suporte como pontos de extremidade personalizados.

Para obter mais informações sobre a criação de pontos de extremidade personalizados no Hub IoT, consulte [Pontos de extremidade do Hub IoT][lnk-devguide-endpoints].

Para saber mais sobre a leitura de pontos de extremidade personalizados, confira:

* Leitura de [contêineres do Microsoft Azure][lnk-getstarted-storage].
* Leitura de [Hubs de Eventos][lnk-getstarted-eh].
* Leitura de [filas do Barramento de Serviço][lnk-getstarted-queue].
* Leitura de [tópicos do Barramento de Serviço][lnk-getstarted-topic].

### <a name="next-steps"></a>Próximas etapas

Para saber mais sobre pontos de extremidade do Hub IoT, confira [Pontos de extremidade do Hub IoT][lnk-devguide-endpoints].

Para saber mais sobre a linguagem de consulta que você usa para definir regras de direcionamento, confira [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e direcionamento de mensagens][lnk-devguide-query-language].

O tutorial [Processar mensagens de dispositivo para a nuvem do Hub IoT usando rotas][lnk-d2c-tutorial] mostra como usar regras de direcionamento e pontos de extremidade personalizados.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
