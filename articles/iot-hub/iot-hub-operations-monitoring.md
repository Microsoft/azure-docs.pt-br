---
title: Monitoramento de operações do Hub IoT do Azure (preterido) | Microsoft Docs
description: Como usar o monitoramento das operações do Hub IoT do Azure para monitorar o status das operações no seu Hub IoT em tempo real.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: robinsh
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 045d5693c4388c6285bc6983ac2a385ceac9f6d0
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408117"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>Monitoramento de operações do Hub IoT (preterido)

O monitoramento das operações do Hub IoT permite monitorar o status das operações no seu Hub IoT em tempo real. O Hub IoT controla eventos em várias categorias de operações. Você pode aceitar o envio de eventos de uma ou mais categorias para um ponto de extremidade do seu Hub IoT para processamento. É possível monitorar os dados em busca de erros ou configurar processamento mais complexo com base nos padrões de dados.

>[!NOTE]
>O **monitoramento de operações do Hub IoT foi preterido e removido do Hub IoT em 10 de março de 2019**. Para monitorar as operações e a integridade do Hub IoT, consulte [monitorar o Hub IOT](monitor-iot-hub.md). Para saber mais sobre a linha do tempo de substituição, confira [Monitorar suas soluções de IoT do Azure com o Azure Monitor e o Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health).

O Hub IoT monitora seis categorias de eventos:

* Operações de identidade do dispositivo
* Telemetria de dispositivo
* Mensagens da nuvem para o dispositivo
* conexões
* Carregamentos de arquivos
* Roteamento de mensagem

> [!IMPORTANT]
> O monitoramento de operações do Hub IoT não garante a entrega confiável ou ordenada de eventos. Dependendo da infraestrutura subjacente do Hub IoT, alguns eventos podem ser perdidos ou entregues fora de ordem. Use o monitoramento de operações para gerar alertas com base nos sinais de erro, como tentativas de conexão com falha ou desconexões de alta frequência de dispositivos específicos. Você não deve confiar em eventos do monitoramento de operações para criar um repositório consistente para o estado do dispositivo, por exemplo, um repositório de controle do estado de um dispositivo conectado ou desconectado. 

## <a name="how-to-enable-operations-monitoring"></a>Como habilitar o monitoramento de operações

1. Crie um Hub IoT. Encontre instruções sobre como criar um hub IoT no guia de [Introdução](quickstart-send-telemetry-dotnet.md).

2. Abra a folha do seu Hub IoT. Nela, clique em **Monitoramento de operações**.

    ![Acessar a configuração do monitoramento de operações no portal](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Selecione as categorias de monitoramento que deseja monitorar e, em seguida, clique em **Salvar**. Os eventos estão disponíveis para leitura no ponto de extremidade compatível com o Hub de Eventos listado em **Configurações de monitoramento**. O ponto de extremidade do Hub IoT chama-se `messages/operationsmonitoringevents`.

    ![Configurar o monitoramento de operações no Hub IoT](./media/iot-hub-operations-monitoring/enable-OM-2.png)

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

### <a name="connections"></a>conexões

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

## <a name="connect-to-the-monitoring-endpoint"></a>Conectar-se ao ponto de extremidade de monitoramento

O ponto de extremidade de monitoramento em seu Hub IoT é um ponto de extremidade compatível com o Hub de Eventos. Você pode usar qualquer mecanismo que funciona com os Hubs de Eventos para ler mensagens de monitoramento desse ponto de extremidade. A amostra a seguir cria um leitor básico que não é adequado para uma implantação com alta taxa de transferência. Para obter mais informações sobre como processar as mensagens dos Hubs de Eventos, confira o tutorial [Introdução aos Hubs de Eventos](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) .

Para se conectar ao ponto de extremidade de monitoramento, você precisa de uma cadeia de conexão e do nome do ponto de extremidade. As etapas a seguir mostram como localizar os valores necessários no portal:

1. No portal, navegue até a folha de recursos do Hub IoT.

2. Escolha **Monitoramento de operações** e anote os valores do **Nome compatível com o Hub de Eventos** e do **Ponto de extremidade compatível com o Hub de Eventos** :

    ![Valores de ponto de extremidade compatível com o Hub de Eventos](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Escolha **Políticas de acesso compartilhado** e, em seguida, escolha **serviço**. Anote o valor da **Chave primária** :

    ![Chave primária de política de acesso compartilhado de serviço](./media/iot-hub-operations-monitoring/service-key.png)

O exemplo de código C# a seguir provém de um aplicativo de console C# da **Área de Trabalho Clássica do Windows** do Visual Studio. O projeto tem o pacote do NuGet **WindowsAzure.ServiceBus** instalado.

* Substitua o espaço reservado da cadeia de conexão por uma cadeia de conexão que usa os valores de **ponto de extremidade compatível com o Hub de Eventos** e **Chave primária** do serviço anotados anteriormente, conforme mostrado no exemplo a seguir:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Substitua o espaço reservado nome de ponto de extremidade de monitoramento pelo valor de **Nome compatível com o Hub de Eventos** que você anotou anteriormente.

```csharp
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

Para explorar ainda mais usando Azure Monitor para monitorar o Hub IoT, consulte:

* [Monitorar o Hub IoT](monitor-iot-hub.md)

* [Migrar do monitoramento de operações do Hub IoT para Azure Monitor](iot-hub-migrate-to-diagnostics-settings.md)