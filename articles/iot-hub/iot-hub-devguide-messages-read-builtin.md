---
title: Entenda o ponto de extremidade interno Azure Hub IoT | Microsoft Docs
description: Guia do Desenvolvedor – descreve como usar o ponto de extremidade compatível com hub de eventos interno para ler mensagens de dispositivo para nuvem.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom:
- amqp
- 'Role: Cloud Development'
ms.openlocfilehash: 4bb33721625f4fc752745ce2b43051c90b3aaa74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147678"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Ler mensagens de dispositivo para a nuvem do ponto de extremidade interno

Por padrão, as mensagens são roteadas para o ponto de extremidade voltado para o serviço interno (**mensagens/eventos**) compatíveis com [Hubs de Eventos](https://azure.microsoft.com/documentation/services/event-hubs/). Esse ponto de extremidade é atualmente apenas exposto usando o protocolo [AMQP](https://www.amqp.org/) na porta 5671. Um Hub IoT expõe as propriedades a seguir para permitir que você controle as **mensagens/eventos** do ponto de extremidade do sistema de mensagens interno compatível com o Hub de Eventos.

| Propriedade            | Descrição |
| ------------------- | ----------- |
| **Contagem de partição** | Defina essa propriedade no momento da criação para determinar o número de [partições](../event-hubs/event-hubs-features.md#partitions) para inclusão do evento de dispositivo para nuvem. |
| **Período de retenção**  | Esta propriedade especifica por quanto tempo, em dias, as mensagens são retidas pelo Hub IoT. O padrão é de um dia, mas pode ser aumentado para sete dias. |

O Hub IoT permite retenção de dados nos Hubs de Eventos internos por, no máximo, 7 dias. Você pode definir o tempo de retenção durante a criação do Hub IoT. O tempo de retenção de dados no Hub IoT depende da camada e do tipo de unidade do Hub IoT. Em termos de tamanho, os Hub de Eventos internos podem reter as mensagens do tamanho máximo da mensagem até pelo menos 24 horas de cota. Por exemplo, para uma unidade S1, o Hub IoT fornece armazenamento suficiente para reter pelo menos 400 mil mensagens de 4k cada. Se seus dispositivos enviarem mensagens menores, elas poderão ser retidas por mais tempo (até 7 dias), dependendo do consumo de armazenamento. Garantimos a retenção dos dados pelo tempo de retenção especificado como mínimo. As mensagens expirarão e não poderão ser acessadas depois que o tempo de retenção tiver passado. 

O Hub IoT também permite que você gerencie grupos de consumidores no ponto de extremidade de recebimento do dispositivo para a nuvem interno. Você pode ter até 20 grupos de consumidores para cada Hub IoT.

Se você estiver usando [roteamento de mensagens](iot-hub-devguide-messages-d2c.md) e a [rota de fallback](iot-hub-devguide-messages-d2c.md#fallback-route) estiver habilitada, todas as mensagens que não corresponderem a uma consulta em qualquer rota irão para o ponto de extremidade interno. Se você desabilitar esta rota de fallback, as mensagens que não corresponderem a nenhuma consulta serão descartadas.

Você pode modificar o tempo de retenção, seja de maneira programática usando as [APIs REST do provedor de recursos do Hub IoT](/rest/api/iothub/iothubresource) ou com o [portal do Azure](https://portal.azure.com).

O Hub IoT expõe um ponto de extremidade interno de **mensagens/eventos** para os serviços de back-end lerem as mensagens de dispositivo para nuvem recebidas por seu hub. Este ponto de extremidade é compatível com o Hub de Eventos, o que permite que você use qualquer um dos mecanismos para os quais o serviço Hubs de Eventos dá suporte para ler as mensagens.

## <a name="read-from-the-built-in-endpoint"></a>Leitura do ponto de extremidade interno

Algumas integrações do produto e SDKs dos Hubs de Eventos reconhecem o Hub IoT e permitem que você use a cadeia de conexão do serviço do Hub IoT para se conectar ao ponto de extremidade interno.

Ao usar os SDKs dos Hubs de Eventos ou as integrações do produto que não reconhecem o Hub IoT, você precisará recuperar um ponto de extremidade compatível com o hub de eventos e o nome compatível com o Hub de Eventos. Você pode recuperar esses valores do portal da seguinte maneira:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.

2. Clique em **Pontos de extremidade internos**.

3. A seção **Eventos** contém os valores a seguir: **Partições**, **Nome compatível com o hub de eventos**, **ponto de extremidade compatível com hub de eventos**, **Tempo de retenção** e **Grupos de consumidores**.

    ![Configurações de dispositivo para a nuvem](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

No portal, o campo do ponto de extremidade compatível com hub de eventos contém uma cadeia de conexão completa dos Hubs de Eventos parecida com esta: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. Se o SDK que você está usando exigir outros valores, eles devem ser:

| Nome | Valor |
| ---- | ----- |
| Ponto de extremidade | sb://abcd1234namespace.servicebus.windows.net/ |
| Nome do host | abcd1234namespace.servicebus.windows.net |
| Namespace | abcd1234namespace |

Em seguida, você pode escolher qualquer política de acesso compartilhado na lista suspensa, conforme mostrado na captura de tela acima. Ele mostra apenas as políticas que têm as permissões de **perconnect** para se conectar ao Hub de eventos especificado.

Os SDKs, que você pode usar para se conectar ao ponto de extremidade compatível com hub de eventos interno que o Hub IoT expõe, incluem:

| Linguagem | . | Exemplo |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [Início rápido](quickstart-send-telemetry-dotnet.md) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [Início rápido](quickstart-send-telemetry-java.md) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [Início rápido](quickstart-send-telemetry-node.md) |
| Python | https://pypi.org/project/azure-eventhub/ | [Início rápido](quickstart-send-telemetry-python.md) |

As integrações do produto, que você pode usar com o ponto de extremidade compatível com hub de eventos interno que o Hub IoT expõe, incluem:

* [Azure Functions](../azure-functions/index.yml). Consulte [Processar dados do Hub IoT com o Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](../stream-analytics/index.yml). Consulte [Transmitir dados como entrada no Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](../time-series-insights/index.yml). Consulte [Adicionar uma origem do evento do hub IoT ao seu ambiente do Time Series Insights](../time-series-insights/how-to-ingest-data-iot-hub.md).
* [Spout do Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Você pode exibir a [fonte do spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/spark/apache-spark-ipython-notebook-machine-learning.md).
* [Azure Databricks](/azure/azure-databricks/).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre pontos de extremidade do Hub IoT, consulte [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md).

* Os [Inícios Rápidos](quickstart-send-telemetry-node.md) mostram como enviar mensagens de dispositivo para nuvem de dispositivos simulados e ler as mensagens do ponto de extremidade interno. 

Para saber mais, confira o tutorial [Processar as mensagens de dispositivo para nuvem do Hub IoT usando rotas](tutorial-routing.md).

* Se você quiser encaminhar as mensagens de dispositivo para nuvem para pontos de extremidade personalizados, confira [Usar rotas de mensagens e pontos de extremidade personalizados para mensagens de dispositivo para nuvem](iot-hub-devguide-messages-read-custom.md).