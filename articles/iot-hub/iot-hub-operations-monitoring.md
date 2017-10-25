---
title: "Monitoramento de operações de Hub IoT do Azure | Microsoft Docs"
description: "Como usar o monitoramento das operações do Hub IoT do Azure para monitorar o status das operações no seu Hub IoT em tempo real."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a299f3a5-b14d-4586-9c3b-44aea14ed013
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: nberdy
ms.openlocfilehash: 3eafa32907c8f68cfc44cb2771d625349ff42003
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-operations-monitoring"></a>Monitoramento de operações do Hub IoT

O monitoramento das operações do Hub IoT permite monitorar o status das operações no seu Hub IoT em tempo real. O Hub IoT controla eventos em várias categorias de operações. Você pode aceitar o envio de eventos de uma ou mais categorias para um ponto de extremidade do seu Hub IoT para processamento. É possível monitorar os dados em busca de erros ou configurar processamento mais complexo com base nos padrões de dados.

>[!NOTE]
>O monitoramento de operações do Hub IoT foi preterido e será removido do Hub IoT no futuro. Para monitorar as operações e a integridade do Hub IoT, consulte [Monitorar a integridade do Hub IoT do Azure e diagnosticar problemas rapidamente][lnk-monitor]. Para obter mais informações sobre a linha do tempo de substituição, consulte [Monitorar suas soluções de IoT do Azure com o Azure Monitor e o Azure Resource Health][lnk-blog-announcement].

O Hub IoT monitora seis categorias de eventos:

* Operações de identidade do dispositivo
* Telemetria de dispositivo
* Mensagens da nuvem para o dispositivo
* Conexões
* Carregamentos de arquivos
* Roteamento de mensagem

> [!IMPORTANT]
> O monitoramento de operações do Hub IoT não garante a entrega confiável ou ordenada de eventos. Dependendo da infraestrutura subjacente do Hub IoT, alguns eventos podem ser perdidos ou entregues fora de ordem. Use o monitoramento de operações para gerar alertas com base nos sinais de erro, como tentativas de conexão com falha ou desconexões de alta frequência de dispositivos específicos. Você não deve confiar em eventos do monitoramento de operações para criar um repositório consistente para o estado do dispositivo, por exemplo, um repositório de controle do estado de um dispositivo conectado ou desconectado. 

## <a name="how-to-enable-operations-monitoring"></a>Como habilitar o monitoramento de operações

1. Crie um Hub IoT. Você pode encontrar instruções sobre como criar um Hub IoT no guia de [Introdução][lnk-get-started].

1. Abra a folha do seu Hub IoT. Nela, clique em **Monitoramento de operações**.

    ![Acessar a configuração do monitoramento de operações no portal][1]

1. Selecione as categorias de monitoramento que deseja monitorar e, em seguida, clique em **Salvar**. Os eventos estão disponíveis para leitura no ponto de extremidade compatível com o Hub de Eventos listado em **Configurações de monitoramento**. O ponto de extremidade do Hub IoT chama-se `messages/operationsmonitoringevents`.

    ![Configurar o monitoramento de operações no Hub IoT][2]

> [!NOTE]
> A seleção do monitoramento **Detalhado** para a categoria **Conexões** faz com que o Hub IoT gere mensagens de diagnóstico adicionais. Para todas as outras categorias, a configuração **Detalhada** muda a quantidade de informações incluídas pelo Hub IoT em cada mensagem de erro.

## <a name="event-categories-and-how-to-use-them"></a>Categorias de evento e como usá-las

Cada categoria de monitoramento de operações rastreia um tipo diferente de interação com o Hub IoT e cada categoria de monitoramento tem um esquema que define como os eventos nessa categoria são estruturados.

### <a name="device-identity-operations"></a>Operações de identidade do dispositivo

A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando você tenta criar, atualizar ou excluir uma entrada no registro de identidade do seu Hub IoT. O rastreamento dessa categoria é útil em cenários de provisionamento.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Telemetria de dispositivo

A categoria de telemetria de dispositivo rastreia erros que ocorrem no Hub IoT e estão relacionados ao pipeline de telemetria. Essa categoria inclui erros que ocorrem no envio de eventos de telemetria (como limitação) e no recebimento de eventos de telemetria (como leitor não autorizado). Essa categoria não pode capturar erros causados pelo código em execução no próprio dispositivo.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Comandos da nuvem para o dispositivo

A categoria de comandos da nuvem para o dispositivo rastreia erros que ocorrem no Hub IoT e são relacionados ao pipeline de mensagem da nuvem para o dispositivo. Essa categoria inclui erros que ocorrem no envio (tal como remetente não autorizado) e recebimento (como contagem de entrega excedida) de mensagens da nuvem para o dispositivo e no recebimento de comentários de mensagens da nuvem para o dispositivo (como comentário expirado). Essa categoria não capturará erros de um dispositivo que manipula incorretamente uma mensagem da nuvem para o dispositivo se tal mensagem tiver sido entregue com êxito.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Conexões

A categoria de conexões rastreia erros que ocorrem quando os dispositivos se conectam a um Hub IoT ou se desconectam dele. Rastrear essa categoria é útil para identificar tentativas de conexão não autorizada e para saber quando dispositivos em áreas de conectividade ruim perdem conexão.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Carregamentos de arquivos

A categoria de carregamento de arquivo rastreia erros que ocorrem no Hub IoT e estão relacionados à funcionalidade de carregamento de arquivo. Essa categoria inclui:

* Erros que ocorrem com o URI de SAS, como quando ele expirar antes que um dispositivo notifique o hub de um carregamento concluído.
* Carregamentos com falha relatados pelo dispositivo.
* Erros que ocorrem quando um arquivo não for encontrado no armazenamento durante a criação de mensagem de notificação do Hub IoT.

Essa categoria não pode capturar erros que ocorrem diretamente enquanto o dispositivo está carregando um arquivo no armazenamento.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Roteamento de mensagem

A categoria de roteamento de mensagem acompanha os erros que ocorrem durante a avaliação da rota da mensagem e da integridade do ponto de extremidade, conforme visto pelo Hub IoT. Essa categoria inclui eventos, por exemplo, quando uma regra é avaliada como "não definida", quando o Hub IoT marca um ponto de extremidade como inativo e todos os outros erros são recebidos de um ponto de extremidade. Essa categoria não inclui erros específicos sobre as próprias mensagens (como erros de limitação de dispositivo), que são relatados na categoria "telemetria de dispositivo".

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="view-events"></a>Exibir eventos

Você pode usar a ferramenta *iothub explorer* para testar rapidamente se o Hub IoT está gerando eventos de monitoramento. Para instalar a ferramenta, consulte as instruções no repositório do GitHub [iothub-explorer][lnk-iothub-explorer].

1. Certifique-se de que a categoria de monitoramento **Conexões** esteja definida como **Detalhadas** no portal.

1. No prompt de comando, execute o seguinte comando para ler do ponto de extremidade de monitoramento:

    ```
    iothub-explorer monitor-ops --login {your iothubowner connection string}
    ```

1. Em outro prompt de comando, execute o seguinte comando para simular um dispositivo enviando mensagens do dispositivo para a nuvem:

    ```
    iothub-explorer simulate-device {your device name} --send "My test message" --login {your iothubowner connection string}
    ```

1. O primeiro prompt de comando mostra os eventos de monitoramento conforme o dispositivo simulado se conecta ao seu Hub IoT.

## <a name="connect-to-the-monitoring-endpoint"></a>Conectar-se ao ponto de extremidade de monitoramento

O ponto de extremidade de monitoramento em seu Hub IoT é um ponto de extremidade compatível com o Hub de Eventos. Você pode usar qualquer mecanismo que funciona com os Hubs de Eventos para ler mensagens de monitoramento desse ponto de extremidade. A amostra a seguir cria um leitor básico que não é adequado para uma implantação com alta taxa de transferência. Para obter mais informações sobre como processar as mensagens dos Hubs de Eventos, confira o tutorial [Introdução aos Hubs de Eventos][lnk-eventhubs-tutorial].

Para se conectar ao ponto de extremidade de monitoramento, você precisa de uma cadeia de conexão e do nome do ponto de extremidade. As etapas a seguir mostram como localizar os valores necessários no portal:

1. No portal, navegue até a folha de recursos do Hub IoT.

1. Escolha **Monitoramento de operações** e anote os valores do **Nome compatível com o Hub de Eventos** e do **Ponto de extremidade compatível com o Hub de Eventos**:

    ![Valores de ponto de extremidade compatível com o Hub de Eventos][img-endpoints]

1. Escolha **Políticas de acesso compartilhado** e, em seguida, escolha **serviço**. Anote o valor da **Chave primária**:

    ![Chave primária de política de acesso compartilhado de serviço][img-service-key]

O exemplo de código C# a seguir provém de um aplicativo de console C# da **Área de Trabalho Clássica do Windows** do Visual Studio. O projeto tem o pacote do NuGet **WindowsAzure.ServiceBus** instalado.

* Substitua o espaço reservado da cadeia de conexão por uma cadeia de conexão que usa os valores de **ponto de extremidade compatível com o Hub de Eventos** e **Chave primária** do serviço anotados anteriormente, conforme mostrado no exemplo a seguir:

    ```cs
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Substitua o espaço reservado nome de ponto de extremidade de monitoramento pelo valor de **Nome compatível com o Hub de Eventos** que você anotou anteriormente.

```cs
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();

        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }

        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }

            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));

            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Simulando um dispositivo com Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png
[img-endpoints]: media/iot-hub-operations-monitoring/monitoring-endpoint.png
[img-service-key]: media/iot-hub-operations-monitoring/service-key.png

[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
