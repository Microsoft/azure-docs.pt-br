---
title: Entenda o ponto de extremidade interno Azure Hub IoT | Microsoft Docs
description: "Guia do desenvolvedor - descreve como usar o ponto de extremidade compatível com evento interno Hub para ler mensagens de dispositivo para nuvem."
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
ms.date: 05/25/2017
ms.author: dobett
ms.openlocfilehash: f17f3084138d667b2584142ed90ecc8fc1586189
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Ler mensagens de dispositivo para a nuvem do ponto de extremidade interno

Por padrão, as mensagens são roteadas para o ponto de extremidade voltado para o serviço interno (**mensagens/eventos**) compatíveis com [Hubs de Eventos][lnk-event-hubs]. Esse ponto de extremidade é atualmente apenas exposto usando o protocolo [AMQP][lnk-amqp] na porta 5671. Um Hub IoT expõe as propriedades a seguir para permitir que você controle as **mensagens/eventos** do ponto de extremidade do sistema de mensagens interno compatível com o Hub de Eventos.

| Propriedade            | Descrição |
| ------------------- | ----------- |
| **Contagem de partição** | Defina essa propriedade no momento da criação e define o número de [partições][lnk-event-hub-partitions] para inclusão do evento do dispositivo para a nuvem. |
| **Período de retenção**  | Esta propriedade especifica por quanto tempo, em dias, as mensagens são retidas pelo Hub IoT. O padrão é de um dia, mas pode ser aumentado para sete dias. |

O Hub IoT também permite que você gerencie grupos de consumidores no ponto de extremidade de recebimento do dispositivo para a nuvem interno.

Por padrão, todas as mensagens que não correspondem explicitamente a uma regra de roteamento de mensagem são gravadas no ponto de extremidade interno. Se você desabilitar esta rota de fallback, as mensagens que não corresponderão explicitamente a nenhuma regra de roteamento de mensagem são descartadas.

Você pode modificar o tempo de retenção, seja de maneira programática por meio das [APIs REST do provedor de recursos do Hub IoT][lnk-resource-provider-apis] ou usando o [portal do Azure][lnk-management-portal].

O Hub IoT expõe um ponto de extremidade interno de **mensagens/eventos** para os serviços de back-end lerem as mensagens de dispositivo para nuvem recebidas por seu hub. Este ponto de extremidade é compatível com o Hub de Eventos, o que permite que você use qualquer um dos mecanismos para os quais o serviço Hubs de Eventos dá suporte para ler as mensagens.

## <a name="read-from-the-built-in-endpoint"></a>Leitura do ponto de extremidade interno

Ao usar o [SDK do Barramento de Serviço do Azure para .NET][lnk-servicebus-sdk] ou [Hubs de Eventos – Host Processador de Evento][lnk-eventprocessorhost], você pode usar qualquer cadeia de conexão do Hub IoT com as permissões corretas. Em seguida, use **mensagens/eventos** como o nome do Hub de Eventos.

Ao usar os SDKs (ou integrações de produtos) que não reconhecem o Hub IoT, será necessário recuperar um ponto de extremidade compatível com os Hubs de Eventos e o nome do Hub de Eventos das configurações do Hub IoT no [Portal do Azure][lnk-management-portal]:

1. Na folha de Hub IoT, clique em **Pontos de Extremidade**.
1. Na seção **Pontos de extremidade internos**, clique em **Eventos**. A folha contém os seguintes valores: **Ponto de extremidade compatível com o Hub de Eventos**, **Nome compatível com o Hub de Eventos**, **Partições**, **Ponto de retenção** e **Grupos de consumidores**.

    ![Configurações de dispositivo para a nuvem][img-eventhubcompatible]

O SDK do Hub IoT requer o nome de ponto de extremidade do Hub IoT, que é **mensagens/eventos** conforme mostrado na folha **Pontos de extremidade**.

Se o SDK sendo usado exigir um valor de **Nome do host** ou **Namespace**, remova o esquema do **Ponto de extremidade compatível com o Hub de Eventos**. Por exemplo, se o ponto de extremidade compatível com o Hub de Eventos fosse **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, o **Nome do host** seria **iothub-ns-myiothub-1234.servicebus.windows.net** e o **Namespace** seria **iothub-ns-myiothub-1234**.

Dessa forma, você poderá usar qualquer política de acesso compartilhado com permissões **ServiceConnect** para se conectar ao Hub de Eventos especificado.

Caso você precise criar uma cadeia de conexão do Hub de Eventos usando as informações anteriores, use o seguinte padrão:

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

Os SDKs e integrações que você pode usar com os pontos de extremidade compatíveis com o Hub de Eventos expostos pelo Hub IoT incluem os itens a seguir na lista:

* [Cliente Java dos Hubs de Eventos](https://github.com/Azure/azure-event-hubs-java).
* [Spout do Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Você pode exibir a [fonte do spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre pontos de extremidade do Hub IoT, consulte [Pontos de extremidade do Hub IoT][lnk-endpoints].

Os tutoriais [Comece][lnk-get-started] mostram como enviar mensagens de dispositivo para nuvem de dispositivos simulados e ler as mensagens do ponto de extremidade interno. Para obter mais informações, confira o tutorial [Como processar as mensagens entre o dispositivo e a nuvem do Hub IoT usando rotas][lnk-d2c-tutorial].

Se você quiser encaminhar as mensagens de dispositivo para nuvem para pontos de extremidade personalizados, consulte [Usar rotas de mensagens e pontos de extremidade personalizados para mensagens de dispositivo para nuvem][lnk-custom].

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-amqp]: https://www.amqp.org/
