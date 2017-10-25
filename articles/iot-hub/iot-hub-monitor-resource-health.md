---
title: Monitorar a integridade de seu Hub IoT do Azure | Microsoft Docs
description: Use o Azure Monitor e o Azure Resource Health para monitorar seu Hub IoT Hub e diagnosticar problemas rapidamente
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: 3051af03d0c1433db98bcc674a072188e7ce80e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorar a integridade do Hub IoT do Azure e diagnosticar problemas rapidamente

As empresas que implementam o Hub IoT do Azure esperam um desempenho confiável de seus recursos. Para ajudá-lo a manter um monitoramento em suas operações, o Hub IoT é totalmente integrado ao [Azure Monitor][lnk-AM] e o [Azure Resource Health] [ lnk-ARH]. Esses dois serviços funcionam em conjunto para fornecer os dados de que você precisa manter suas soluções IoT em execução em um estado íntegro. 

O Azure Monitor é uma fonte única de monitoramento e registro em log para todos os serviços do Azure. Você pode enviar os logs gerados pelo Azure Monitor para o OMS Log Analytics, Hubs de eventos ou Armazenamento do Microsoft Azure para processamento personalizado. As configurações de diagnóstico e métricas do Azure Monitor oferecem a visibilidade em tempo real sobre o desempenho de seus recursos. Continue lendo este artigo para saber como [Usar o Azure Monitor](#use-azure-monitor) com o Hub IoT. 

O Azure Resource Health ajuda você a diagnosticar e a obter suporte quando um problema com o Azure afeta seus recursos. Um painel personalizado fornece o status de integridade atual e anterior de seus Hubs IoT. Continue lendo este artigo para saber como [Usar o Azure Resource Health](#use-azure-resource-health) com o Hub IoT. 

Além da integração com esses dois serviços, o Hub IoT também fornece suas próprias métricas que você pode usar para entender o estado de seus recursos de IoT. Para obter mais informações, consulte [Entender as métricas do Hub IoT][lnk-metrics].

## <a name="use-azure-monitor"></a>Usar o Azure Monitor

O Azure Monitor fornece informações de diagnóstico de nível de recurso, o que significa que você pode monitorar as operações que são executadas em seu Hub IoT. 

As configurações de diagnóstico do Azure Monitor substituem o monitor de operações do Hub IoT. Se você usa atualmente o monitoramento de operações, será necessário migrar seus fluxos de trabalho. Para obter mais informações, consulte [Migrar do monitoramento de operações para as configurações de diagnóstico][lnk-migrate].

Para saber mais sobre as métricas e os eventos que o Azure Monitor inspeciona, consulte [Métricas com suporte com o Azure Monitor][lnk-AM-metrics] e [Serviços, esquemas e categorias com suporte para Logs de Diagnóstico do Azure][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Compreender os logs

O Azure Monitor controla diferentes operações que ocorrem no Hub IoT. Cada categoria tem um esquema que define como os eventos nessa categoria são relatados. 

#### <a name="connections"></a>Conexões

A categoria de conexões rastreia erros que ocorrem quando os dispositivos se conectam a um Hub IoT ou se desconectam dele. Rastrear essa categoria é útil para identificar tentativas de conexão não autorizada e para saber quando dispositivos em áreas de conectividade ruim perdem conexão.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>Comandos da nuvem para o dispositivo

A categoria de comandos da nuvem para o dispositivo rastreia erros que ocorrem no Hub IoT e são relacionados ao pipeline de mensagem da nuvem para o dispositivo. Essa categoria inclui erros que ocorrem no envio (tal como remetente não autorizado) e recebimento (como contagem de entrega excedida) de mensagens da nuvem para o dispositivo e no recebimento de comentários de mensagens da nuvem para o dispositivo (como comentário expirado). Essa categoria não capturará erros de um dispositivo que manipula incorretamente uma mensagem da nuvem para o dispositivo se tal mensagem tiver sido entregue com êxito.

```json
{
    "time": " UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "messageExpired",
    "category": "C2DCommands",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="device-identity-operations"></a>Operações de identidade do dispositivo

A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando você tenta criar, atualizar ou excluir uma entrada no registro de identidade do seu Hub IoT. O rastreamento dessa categoria é útil em cenários de provisionamento.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "get",
    "category": "DeviceIdentityOperations",
    "level": "Error",    
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="routes"></a>Rotas

A categoria de roteamento de mensagem acompanha os erros que ocorrem durante a avaliação da rota da mensagem e da integridade do ponto de extremidade, conforme visto pelo Hub IoT. Essa categoria inclui eventos, por exemplo, quando uma regra é avaliada como "não definida", quando o Hub IoT marca um ponto de extremidade como inativo e todos os outros erros são recebidos de um ponto de extremidade. Essa categoria não inclui erros específicos sobre as próprias mensagens (como erros de limitação de dispositivo), que são relatados na categoria "telemetria de dispositivo".

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>Telemetria de dispositivo

A categoria de telemetria de dispositivo rastreia erros que ocorrem no Hub IoT e estão relacionados ao pipeline de telemetria. Essa categoria inclui erros que ocorrem no envio de eventos de telemetria (como limitação) e no recebimento de eventos de telemetria (como leitor não autorizado). Essa categoria não pode capturar erros causados pelo código em execução no próprio dispositivo.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
    "location": "Resource location"
}
```

#### <a name="file-upload-operations"></a>Operações de upload de arquivo

A categoria de carregamento de arquivo rastreia erros que ocorrem no Hub IoT e estão relacionados à funcionalidade de carregamento de arquivo. Essa categoria inclui:

* Erros que ocorrem com o URI de SAS, como quando ele expirar antes que um dispositivo notifique o hub de um carregamento concluído.
* Carregamentos com falha relatados pelo dispositivo.
* Erros que ocorrem quando um arquivo não for encontrado no armazenamento durante a criação de mensagem de notificação do Hub IoT.

Essa categoria não pode capturar erros que ocorrem diretamente enquanto o dispositivo está carregando um arquivo no armazenamento.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "FileUploadOperations",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-twin-operations"></a>Operações de gêmeos de nuvem para dispositivo

A categoria de operações de gêmeos de nuvem para dispositivo rastreia eventos iniciados pelo serviço em dispositivos gêmeos. Essas operações podem incluir obter gêmeo, atualizar propriedades relatadas e inscrever em propriedades desejadas

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "read",
    "category": "C2DTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="device-to-cloud-twin-operations"></a>Operações de gêmeos de dispositivo para nuvem

A categoria de operações de gêmeos de dispositivo para nuvem rastreia eventos iniciados pelo dispositivo em dispositivos gêmeos. Essas operações podem incluir obter gêmeo, atualizar ou substituir marcas e atualizar ou substituir propriedades desejadas. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "update",
    "category": "D2CTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}", 
    "location": "Resource location"
}
```

#### <a name="twin-queries"></a>Consultas de gêmeos

A categoria de consultas de gêmeos relata sobre solicitações de consulta para dispositivos gêmeos que são iniciados na nuvem. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "query",
    "category": "TwinQueries",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="jobs-operations"></a>Operações de trabalhos

A categoria de operações de trabalhos relata sobre solicitações de trabalho para atualizar dispositivos gêmeos ou invocar métodos diretos em vários dispositivos. Essas solicitações são iniciadas na nuvem. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "jobCompleted",
    "category": "JobsOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}", 
    "location": "Resource location"
}
```

#### <a name="direct-methods"></a>Métodos diretos

A categoria de métodos diretos rastreia as interações de resposta de solicitações enviadas aos dispositivos individuais. Essas solicitações são iniciadas na nuvem. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "send",
    "category": "DirectMethods",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
    "location": "Resource location"
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Ler logs de Hubs de Eventos do Azure

Depois de configurar o log de eventos por meio das configurações de diagnóstico, é possível criar aplicativos que leem logs de forma que você possa tomar uma ação com base nas informações neles. Esse código de exemplo recupera os logs de um hub de eventos:

```
class Program 
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}"; 
    static string monitoringEndpointName = "{your AMS event hub endpoint name}"; 
    static EventHubClient eventHubClient; 
//This is the Diagnostic Settings schema 
    class AzureMonitorDiagnosticLog 
    { 
        string time { get; set; } 
        string resourceId { get; set; } 
        string operationName { get; set; } 
        string category { get; set; } 
        string level { get; set; } 
        string resultType { get; set; } 
        string resultDescription { get; set; } 
        string durationMs { get; set; } 
        string callerIpAddress { get; set; } 
        string correlationId { get; set; } 
        string identity { get; set; } 
        string location { get; set; } 
        Dictionary<string, string> properties { get; set; } 
    }; 
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
                var deserializer = new JavaScriptSerializer(); 
                //deserialize json data to azure monitor object 
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result); 
 
            } 
        } 
    } 
} 
```

## <a name="use-azure-resource-health"></a>Usar o Azure Resource Health

Use o Azure Resource Health para monitorar se o Hub IoT está em execução. Você também pode saber se uma interrupção regional está afetando a integridade do seu Hub IoT. Para entender os detalhes específicos sobre o estado de integridade de seu Hub IoT do Azure, recomendamos que você [Use o Azure Monitor](#use-azure-monitor). 

O Hub IoT do Azure indica a integridade em um nível regional. Se houver uma interrupção regional afetando seu Hub IoT, o status de integridade mostrará como **Desconhecido**. Para saber mais sobre as verificações de integridade específicas que o Azure Resource Health realiza, consulte [Tipos de recursos e verificações de integridade do Azure Resource Health][lnk-ARH-checks].

Para verificar a integridade de seus Hubs IoT, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Vá até **Integridade do Serviço** > **Integridade de Recursos**.
1. Selecione sua assinatura e **Hub IoT** nas caixas suspensas.

Para saber mais sobre como interpretar os dados de integridade, consulte [Visão geral do Azure Resource Health][lnk-ARH]

## <a name="next-steps"></a>Próximas etapas

- [Entender as métricas de Hub IoT][lnk-metrics]
- [Monitoramento remoto IoT e notificações com Aplicativos Lógicos do Azure conectando o Hub IoT e a caixa de correio][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md