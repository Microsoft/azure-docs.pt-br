---
title: Compreender os pontos de extremidade personalizados do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – usando regras de roteamento para encaminhar mensagens de dispositivo para nuvem a pontos de extremidade personalizados.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e5e92c40cef15e99431dc9652820c71e87935f67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61244337"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Usar rotas de mensagens e pontos de extremidade personalizados para mensagens de dispositivo para a nuvem

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O [roteamento de mensagens](iot-hub-devguide-routing-query-syntax.md) do Hub IoT permite que os usuários encaminhem mensagens de dispositivo para nuvem a pontos de extremidade voltados para o serviço. O roteamento também fornece uma funcionalidade de consulta para filtrar os dados antes de encaminhá-los aos pontos de extremidade. Cada regra de roteamento que você configura tem as seguintes propriedades:

| Propriedade      | DESCRIÇÃO |
| ------------- | ----------- |
| **Nome**      | O nome exclusivo que identifica a consulta. |
| **Fonte**    | A origem do fluxo de dados a ser afetado. Por exemplo, telemetria do dispositivo. |
| **Condição** | A expressão de consulta para a consulta de roteamento que é executada nas propriedades do aplicativo de mensagens, propriedades do sistema, no corpo da mensagem, nas marcas de dispositivo gêmeo e propriedades de dispositivo gêmeo para determinar se é uma correspondência do ponto de extremidade. Para obter mais informações de como construir uma consulta, confira a [sintaxe de consulta de roteamento da mensagem](iot-hub-devguide-routing-query-syntax.md) |
| **Ponto de extremidade**  | O nome do ponto de extremidade para o qual o Hub IoT envia as mensagens que correspondem à consulta. É recomendado que você escolha um ponto de extremidade na mesma região que o Hub IoT. |

Uma única mensagem pode corresponder à condição em várias consultas de roteamento e, nesse caso, o Hub IoT entrega a mensagem ao ponto de extremidade associado a cada regra correspondente. O Hub IoT também elimina a duplicação da entrega de mensagem automaticamente, portanto, se uma mensagem corresponder a várias regras com o mesmo destino, ela será gravada apenas uma vez no destino.

## <a name="endpoints-and-routing"></a>Pontos de extremidade e roteamento

Um hub IoT tem um [ponto de extremidade interno](iot-hub-devguide-messages-read-builtin.md) padrão. Você pode criar pontos de extremidade personalizados para direcionar mensagens ou vinculando outros serviços em sua assinatura ao hub. O Hub IoT atualmente dá suporte aos contêineres de Armazenamento do Microsoft Azure, Hubs de Eventos, filas do Barramento de Serviço e tópicos do Barramento de Serviço como pontos de extremidade personalizados.

Quando você usa e pontos de extremidade de roteamento e personalizados, as mensagens só são entregues ao ponto de extremidade interno quando não correspondem a nenhuma consulta. Para entregar mensagens ao ponto de extremidade interno e a um ponto de extremidade personalizado, adicione uma rota que envia mensagens ao ponto de extremidade **events** interno.

> [!NOTE]
> * O Hub IoT somente dá suporte à gravação de dados em contêineres de Armazenamento do Microsoft Azure como blobs.
> * As filas e os tópicos do Barramento de Serviço com **Sessões** ou **Detecção de Duplicidades** habilitadas não têm suporte como pontos de extremidade personalizados.

Para obter mais informações sobre a criação de pontos de extremidade personalizados no Hub IoT, consulte [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md).

Para saber mais sobre a leitura de pontos de extremidade personalizados, confira:

* Leitura de [contêineres de Armazenamento do Azure](../storage/blobs/storage-blobs-introduction.md).

* Leitura de [Hubs de Eventos](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Leitura de [Filas do Barramento de Serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Leitura de [tópicos do Barramento de Serviço](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre pontos de extremidade do Hub IoT, consulte [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md).

* Para obter mais informações sobre a linguagem de consulta usada para definir as consultas de roteamento, confira [sintaxe de consulta de roteamento de mensagem](iot-hub-devguide-routing-query-syntax.md).

* O tutorial [Processar mensagens de dispositivo para nuvem do Hub IoT usando rotas](tutorial-routing.md) mostra como usar consultas de roteamento e pontos de extremidade personalizados.