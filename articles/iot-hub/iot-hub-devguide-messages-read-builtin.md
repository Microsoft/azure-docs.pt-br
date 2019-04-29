---
title: Entenda o ponto de extremidade interno Azure Hub IoT | Microsoft Docs
description: Guia do Desenvolvedor – descreve como usar o ponto de extremidade compatível com hub de eventos interno para ler mensagens de dispositivo para nuvem.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 827d7d9a3d584342703a84dd2a42e5cda9b3a656
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363953"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Ler mensagens de dispositivo para a nuvem do ponto de extremidade interno

Por padrão, as mensagens são roteadas para o ponto de extremidade voltado para o serviço interno (**mensagens/eventos**) compatíveis com [Hubs de Eventos](https://azure.microsoft.com/documentation/services/event-hubs/). Esse ponto de extremidade é atualmente apenas exposto usando o protocolo [AMQP](https://www.amqp.org/) na porta 5671. Um Hub IoT expõe as propriedades a seguir para permitir que você controle as **mensagens/eventos** do ponto de extremidade do sistema de mensagens interno compatível com o Hub de Eventos.

| Propriedade            | DESCRIÇÃO |
| ------------------- | ----------- |
| **Contagem de partição** | Defina essa propriedade no momento da criação para determinar o número de [partições](../event-hubs/event-hubs-features.md#partitions) para inclusão do evento de dispositivo para nuvem. |
| **Período de retenção**  | Esta propriedade especifica por quanto tempo, em dias, as mensagens são retidas pelo Hub IoT. O padrão é de um dia, mas pode ser aumentado para sete dias. |

O IoT Hub permite a retenção de dados em Hubs de eventos interno para um máximo de 7 dias. Você pode definir o tempo de retenção durante a criação do IoT Hub. Tempo de retenção de dados no IoT Hub depende de sua camada do hub IoT e o tipo de unidade. Em termos de tamanho, os Hubs de eventos interno pode manter as mensagens do tamanho máximo da mensagem até pelo menos 24 horas da cota. Por exemplo, por 1 unidade de S1 que do IoT Hub fornece armazenamento suficiente para manter pelo menos 400 mil mensagens de 4k de tamanho cada. Se seu dispositivos estiver enviando mensagens menores, eles podem ser mantidos para maior (até 7 dias), dependendo da quantidade de armazenamento é consumido. Garantimos que retém os dados para o tempo de retenção especificado, no mínimo.

O Hub IoT também permite que você gerencie grupos de consumidores no ponto de extremidade de recebimento do dispositivo para a nuvem interno.

Se você estiver usando [roteamento de mensagens](iot-hub-devguide-messages-d2c.md) e o [rota de fallback](iot-hub-devguide-messages-d2c.md#fallback-route) é habilitada, todas as mensagens que não correspondem a uma consulta em qualquer rota ir para o ponto de extremidade interno. Se você desabilitar esta rota de fallback, as mensagens que não correspondem a qualquer consulta são descartadas.

Você pode modificar o tempo de retenção, seja de maneira programática usando as [APIs REST do provedor de recursos do Hub IoT](/rest/api/iothub/iothubresource) ou com o [portal do Azure](https://portal.azure.com).

O Hub IoT expõe um ponto de extremidade interno de **mensagens/eventos** para os serviços de back-end lerem as mensagens de dispositivo para nuvem recebidas por seu hub. Este ponto de extremidade é compatível com o Hub de Eventos, o que permite que você use qualquer um dos mecanismos para os quais o serviço Hubs de Eventos dá suporte para ler as mensagens.

## <a name="read-from-the-built-in-endpoint"></a>Leitura do ponto de extremidade interno

Algumas integrações de produtos e SDKs dos Hubs de evento reconhecem o IoT Hub e permitem que você use sua cadeia de conexão de serviço do hub IoT para se conectar ao ponto de extremidade interno.

Quando você usar SDKs dos Hubs de eventos ou integrações de produtos que não reconhecem o IoT Hub, você precisa de um ponto de extremidade do Hub de eventos compatível e o nome do Hub de eventos compatível. Você pode recuperar esses valores do portal da seguinte maneira:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.

2. Clique em **Pontos de extremidade internos**.

3. O **eventos** seção contém os seguintes valores: **Partições**, **nome do Hub de eventos compatível**, **ponto de extremidade do Hub de eventos compatível**, **tempo de retenção**, e **gruposdeconsumidores**.

    ![Configurações de dispositivo para a nuvem](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

No portal, o campo de ponto de extremidade compatível com Hub de eventos contém uma cadeia de caracteres de conexão dos Hubs de eventos completa que se parece com: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. Se o SDK que você está usando requer outros valores, em seguida, eles seria:

| NOME | Value |
| ---- | ----- |
| Ponto de extremidade | sb://abcd1234namespace.servicebus.windows.net/ |
| Nome do host | abcd1234namespace.servicebus.windows.net |
| Namespace | abcd1234namespace |

Dessa forma, você poderá usar qualquer política de acesso compartilhado com permissões **ServiceConnect** para se conectar ao Hub de Eventos especificado.

Os SDKs que você pode usar para conectar-se para o ponto de extremidade compatível com Hub de eventos internos expostos pelo IoT Hub incluem:

| Linguagem | . | Exemplo | Observações |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Início rápido](quickstart-send-telemetry-dotnet.md) | Usa as informações de evento Hubs compatível |
 Java | https://github.com/Azure/azure-event-hubs-java | [Início rápido](quickstart-send-telemetry-java.md) | Usa as informações de evento Hubs compatível |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Início rápido](quickstart-send-telemetry-node.md) | Usa a cadeia de conexão do IoT Hub |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Usa a cadeia de conexão do IoT Hub |

Integrações de produtos que você pode usar com o ponto de extremidade compatível com Hub de eventos internos expostos pelo IoT Hub incluem:

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Ver [processamento de dados do IoT Hub com o Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Ver [Stream dados como entrada no Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/). Ver [adicionar uma origem de evento do hub IoT ao seu ambiente do Time Series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Spout do Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Você pode exibir a [fonte do spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre pontos de extremidade do Hub IoT, consulte [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md).

* Os [Inícios Rápidos](quickstart-send-telemetry-node.md) mostram como enviar mensagens de dispositivo para nuvem de dispositivos simulados e ler as mensagens do ponto de extremidade interno. 

Para saber mais, confira o tutorial [Processar as mensagens de dispositivo para nuvem do Hub IoT usando rotas](tutorial-routing.md).

* Se você quiser encaminhar as mensagens de dispositivo para nuvem para pontos de extremidade personalizados, confira [Usar rotas de mensagens e pontos de extremidade personalizados para mensagens de dispositivo para nuvem](iot-hub-devguide-messages-read-custom.md).
