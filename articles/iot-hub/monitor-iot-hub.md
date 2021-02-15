---
title: Monitorando o Hub IoT do Azure
description: Comece aqui para saber como monitorar o Hub IoT do Azure
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 11/06/2020
ms.openlocfilehash: db85594ad4fef37ec7411f7d5791fdb5279ec504
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581645"
---
# <a name="monitoring-azure-iot-hub"></a>Monitorando o Hub IoT do Azure

Quando você tem aplicativos e processos de negócios críticos que dependem de recursos do Azure, recomendamos monitorar a disponibilidade, o desempenho e a operação desses recursos. Este artigo descreve os dados de monitoramento gerados pelo Hub IoT do Azure e como você pode usar os recursos do Azure Monitor para analisar e alertar sobre esses dados.

## <a name="monitor-overview"></a>Visão geral do Monitor

A página de **visão geral** no portal do Azure para cada Hub IOT inclui gráficos que fornecem algumas métricas de uso, como o número de mensagens usadas e o número de dispositivos conectados ao Hub IOT.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="Gráficos de métrica padrão na página de visão geral do Hub IoT.":::

Lembre-se de que o valor da contagem de mensagens pode ser atrasado em 1 minuto e isso, por motivos que têm a ver com a infraestrutura de serviço do Hub IoT, às vezes, o valor pode saltar entre valores mais altos e mais baixos na atualização. Esse contador só deve estar incorreto para valores acumulados no último minuto.

As informações apresentadas no painel Visão geral são úteis, mas representam apenas uma pequena quantidade de dados de monitoramento que estão disponíveis para um hub IoT. Alguns dados de monitoramento são coletados automaticamente e ficam disponíveis para análise assim que você cria o Hub IoT. Você pode habilitar tipos adicionais de coleta de dados com algumas configurações.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

O Hub IoT do Azure cria dados de monitoramento usando [Azure monitor](../azure-monitor/overview.md), que é um serviço de monitoramento de pilha completo no Azure que fornece um conjunto completo de recursos para monitorar seus recursos do Azure, além de recursos em outras nuvens e locais.

Comece com o artigo [Como monitorar os recursos do Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md), que descreve os seguintes conceitos:

- O que é o Azure Monitor?
- Custos associados ao monitoramento
- Monitoramento de dados coletados no Azure
- Configuração de coleta de dados
- Ferramentas padrão no Azure para analisar e alertar sobre dados de monitoramento

As seções a seguir se baseiam neste artigo descrevendo os dados específicos coletados para o Hub IoT do Azure e fornecendo exemplos para configurar a coleta de dados e analisar esses dados com as ferramentas do Azure.

## <a name="monitoring-data"></a>Dados de monitoramento

O Hub IoT do Azure coleta os mesmos tipos de dados de monitoramento que outros recursos do Azure descritos em [monitoramento de dados de recursos do Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data).

Consulte [monitorando a referência de dados do Hub IOT do Azure](monitor-iot-hub-reference.md) para obter informações detalhadas sobre as métricas e os logs criados pelo Hub IOT do Azure.

> [!IMPORTANT]
> Os eventos emitidos pelo serviço de Hub IoT usando Azure Monitor logs de recursos não têm garantia de serem confiáveis ou ordenados. Alguns eventos podem ser perdidos ou entregues fora de ordem. Os logs de recursos também não devem ser em tempo real e podem levar vários minutos para que os eventos sejam registrados em sua escolha de destino.

## <a name="collection-and-routing"></a>Coleta e roteamento

As métricas de plataforma e o Log de atividades são coletados e armazenados automaticamente, mas podem ser roteados para outras localizações usando uma configuração de diagnóstico.

Os logs de recursos não são coletados e armazenados até você criar uma configuração de diagnóstico e roteá-los para um ou mais locais.

As métricas e os logs podem ser roteados para vários locais, incluindo:
- O repositório de logs de Azure Monitor por meio de um espaço de trabalho Log Analytics associado. Lá, eles podem ser analisados usando Log Analytics.
- Armazenamento do Azure para arquivamento e análise offline 
- Um ponto de extremidade de hubs de eventos onde eles podem ser lidos por aplicativos externos, por exemplo, ferramentas SIEM de terceiros.

No portal do Azure, você pode selecionar **configurações de diagnóstico** em **monitoramento** no painel esquerdo do Hub IOT seguido de **Adicionar configuração de diagnóstico** para criar configurações de diagnóstico com escopo para os logs e métricas de plataforma emitidos pelo Hub IOT.

A captura de tela a seguir mostra uma configuração de diagnóstico para rotear as *operações de conexão* do tipo log de recursos e todas as métricas de plataforma para um espaço de trabalho log Analytics.

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="Painel de configurações de diagnóstico para um hub IoT.":::

Confira [Criar uma configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../azure-monitor/platform/diagnostic-settings.md) para obter o processo detalhado de criação de uma configuração de diagnóstico usando o portal do Azure, a CLI ou o PowerShell. Ao criar uma configuração de diagnóstico, você especifica quais categorias de logs coletar. As categorias do Hub IoT do Azure são listadas em [logs de recursos na referência de dados monitoramento do Hub IOT do Azure](monitor-iot-hub-reference.md#resource-logs).

Ao rotear métricas da plataforma do Hub IoT para outros locais, lembre-se de que:

- As métricas de plataforma a seguir não são exportáveis por meio de configurações de diagnóstico: *dispositivos conectados (versão prévia)* e *total de dispositivos (versão prévia)*.

- As métricas multidimensionais, por exemplo, algumas [métricas de roteamento](monitor-iot-hub-reference.md#routing-metrics), são atualmente exportadas como métricas unidimensionais simples agregadas entre valores de dimensão. Para obter mais detalhes, consulte [exportando métricas de plataforma para outros locais](../azure-monitor/platform/metrics-supported.md#exporting-platform-metrics-to-other-locations).

## <a name="analyzing-metrics"></a>Analisando as métricas

Você pode analisar as métricas do Hub IoT do Azure com métricas de outros serviços do Azure usando o Metrics Explorer abrindo **métricas** no menu **Azure monitor** . Consulte [Introdução ao Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) para obter detalhes sobre como usar essa ferramenta.

No portal do Azure, você pode selecionar **métricas** em **monitoramento** no painel esquerdo do Hub IOT para abrir o Metrics Explorer com escopo definido, por padrão, para as métricas de plataforma emitidas pelo Hub IOT:

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="Página do Metrics Explorer para um hub IoT.":::

Para obter uma lista das métricas de plataforma coletadas para o Hub IoT do Azure, consulte [métricas na referência de dados monitoramento do Hub IOT do Azure](monitor-iot-hub-reference.md#metrics). Para obter uma lista das métricas de plataforma coletadas para todos os serviços do Azure, consulte [métricas com suporte com Azure monitor](../azure-monitor/platform/metrics-supported.md).

Para métricas da plataforma do Hub IoT que são coletadas em unidades de contagem, algumas agregações podem não estar disponíveis ou utilizáveis. Para saber mais, consulte [agregações com suporte na referência de dados de monitoramento do Hub IOT do Azure](monitor-iot-hub-reference.md#supported-aggregations).

Algumas métricas do Hub IoT, como [métricas de roteamento](monitor-iot-hub-reference.md#routing-metrics), são multidimensionais. Para essas métricas, você pode aplicar [filtros](../azure-monitor/platform/metrics-charts.md#filters) e [divisão](../azure-monitor/platform/metrics-charts.md#apply-splitting) para seus gráficos com base em uma dimensão.

## <a name="analyzing-logs"></a>Análise de logs

Os dados em Logs do Azure Monitor são armazenados em tabelas em que cada tabela tem o próprio conjunto de propriedades exclusivas. Os dados nessas tabelas são associados a um espaço de trabalho Log Analytics e podem ser consultados em Log Analytics. Para saber mais sobre logs de Azure Monitor, consulte [visão geral de logs de Azure monitor](../azure-monitor/platform/data-platform-logs.md) na documentação do Azure monitor. 

Para rotear dados para logs de Azure Monitor, você deve criar uma configuração de diagnóstico para enviar logs de recursos ou métricas de plataforma para um espaço de trabalho Log Analytics. Para saber mais, consulte [coleta e roteamento](#collection-and-routing).

No portal do Azure, você pode selecionar **logs** em **monitoramento** no painel esquerdo do hub IOT para executar log Analytics consultas com escopo, por padrão, para os logs e as métricas coletados nos logs de Azure monitor para o Hub IOT.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="Página de logs para um hub IoT.":::

Para obter uma lista das tabelas usadas por Azure Monitor logs e passível de consulta por Log Analytics, consulte [tabelas de logs de Azure monitor na referência de dados de monitoramento do Hub IOT do Azure](monitor-iot-hub-reference.md#azure-monitor-logs-tables).

Todos os logs de recursos no Azure Monitor têm os mesmos campos seguidos por campos específicos do serviço. O esquema comum é descrito em [Azure monitor esquema de log de recursos](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema). Você pode encontrar o esquema e as categorias de logs de recursos coletados para o Hub IoT do Azure nos [logs de recursos na referência de dados monitoramento do Hub IOT do Azure](monitor-iot-hub-reference.md#resource-logs).

O [log de atividades](../azure-monitor/platform/activity-log.md) é um log de plataforma no Azure que fornece informações sobre eventos de nível de assinatura. Você pode exibi-lo de modo independente ou roteá-lo para os Logs do Azure Monitor, nos quais você pode fazer consultas muito mais complexas usando o Log Analytics.  

Ao rotear métricas da plataforma do Hub IoT para Azure Monitor logs, lembre-se de que:

- As métricas de plataforma a seguir não são exportáveis por meio de configurações de diagnóstico: *dispositivos conectados (versão prévia)* e *total de dispositivos (versão prévia)*.

- As métricas multidimensionais, por exemplo, algumas [métricas de roteamento](monitor-iot-hub-reference.md#routing-metrics), são atualmente exportadas como métricas unidimensionais simples agregadas entre valores de dimensão. Para obter mais detalhes, consulte [exportando métricas de plataforma para outros locais](../azure-monitor/platform/metrics-supported.md#exporting-platform-metrics-to-other-locations).

Para algumas consultas comuns com o Hub IoT, consulte [exemplos de consultas do Kusto](#sample-kusto-queries). Para obter informações detalhadas sobre como usar consultas Log Analytics, consulte [visão geral das consultas de log no Azure monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="sdk-version-in-iot-hub-logs"></a>Versão do SDK nos logs do Hub IoT

Algumas operações nos logs de recursos do Hub IoT retornam uma `sdkVersion` propriedade em seu `properties` objeto. Para essas operações, quando um dispositivo ou aplicativo de back-end estiver usando um dos SDKs do Azure IoT, essa propriedade conterá informações sobre o SDK que está sendo usado, a versão do SDK e a plataforma na qual o SDK está em execução. O exemplo a seguir mostra a `sdkVersion` Propriedade emitida para uma [`deviceConnect`](monitor-iot-hub-reference.md#connections) operação ao usar o SDK do dispositivo Node.js: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` . Veja um exemplo do valor emitido para o SDK do .NET (C#): `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"` .

A tabela a seguir mostra o nome do SDK usado para SDKs diferentes do Azure IoT:

| Nome do SDK na propriedade sdkVersion | Idioma |
|----------|----------|
| .NET | .NET (C#) |
| Microsoft. Azure. Devices | SDK do serviço .NET (C#) |
| Microsoft. Azure. Devices. Client | SDK do dispositivo .NET (C#) |
| iothubclient | C ou Python V1 (preterido) SDK do dispositivo |
| iothubserviceclient | C ou Python V1 (preterido) SDK de serviço |
| Azure-IOT-Device-iothub-py | SDK do dispositivo Python |
| azure-iot-device | SDK do dispositivo Node.js |
| azure-iothub | SDK do serviço Node.js |
| com. Microsoft. Azure. iothub-Java-Client | SDK do dispositivo Java |
| com. Microsoft. Azure. iothub. Service. Sdk | SDK do serviço Java |
| com. Microsoft. Azure. Sdk. IOT. IOT-Device-Client | SDK do dispositivo Java |
| com. Microsoft. Azure. Sdk. IOT. IOT-Service-Client | SDK do serviço Java |
| C | C inserido |
| C + (OSSimplified = RTOS do Azure) | Azure RTOS |

Você pode extrair a propriedade versão do SDK ao executar consultas em logs de recursos do Hub IoT. Por exemplo, a consulta a seguir extrai a propriedade da versão do SDK (e a ID do dispositivo) das propriedades retornadas pelas operações de conexões. Essas duas propriedades são gravadas nos resultados junto com a hora da operação e a ID do recurso do Hub IoT ao qual o dispositivo está se conectando.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>Consultas de exemplo do Kusto

> [!IMPORTANT]
> Quando você seleciona **logs** no menu do Hub iot, log Analytics é aberto com o escopo de consulta definido para o Hub IOT atual. Isso significa que as consultas de log incluirão apenas os dados desse recurso. Se você quiser executar uma consulta que inclui dados de outros hubs IoT ou dados de outros serviços do Azure, selecione **logs** no menu **Azure monitor** . Confira [Escopo da consulta de log e intervalo de tempo no Log Analytics do Azure Monitor](../azure-monitor/log-query/scope.md) para obter detalhes.

Veja a seguir as consultas que você pode usar para ajudá-lo a monitorar o Hub IoT.

- Erros de conectividade: identificar erros de conexão do dispositivo.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- Erros de limitação: identifique os dispositivos que fizeram mais solicitações resultando em erros de limitação.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- Pontos de extremidade inativos: identificar pontos de extremidade inativos ou não íntegros pelo número de vezes que o problema foi relatado, bem como o motivo.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- Resumo do erro: contagem de erros em todas as operações por tipo.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- Dispositivos conectados recentemente: lista de dispositivos que o Hub IoT viu conectar no período de tempo especificado.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- Versão do SDK de dispositivos: lista de dispositivos e suas versões do SDK para conexões de dispositivo ou operações de computação em nuvem.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>Ler logs de Hubs de Eventos do Azure

Depois de configurar o log de eventos por meio das configurações de diagnóstico, é possível criar aplicativos que leem logs de forma que você possa tomar uma ação com base nas informações neles. Esse código de exemplo recupera os logs de um hub de eventos:

```csharp
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
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
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

## <a name="alerts"></a>Alertas

Os alertas do Azure Monitor notificam você proativamente quando condições importantes são encontradas nos dados de monitoramento. Eles permitem que você identifique e resolva problemas no seu sistema antes que os clientes os percebam. Você pode definir alertas em [métricas](../azure-monitor/platform/alerts-metric-overview.md), [logs](../azure-monitor/platform/alerts-unified-log.md) e [log de atividades](../azure-monitor/platform/activity-log-alerts.md). Tipos diferentes de alertas têm benefícios e desvantagens.

Ao criar uma regra de alerta com base nas métricas da plataforma, lembre-se de que, para métricas da plataforma do Hub IoT que são coletadas em unidades de contagem, algumas agregações podem não estar disponíveis ou utilizáveis. Para saber mais, consulte [agregações com suporte na referência de dados de monitoramento do Hub IOT do Azure](monitor-iot-hub-reference.md#supported-aggregations).

## <a name="monitor-per-device-disconnects-with-event-grid"></a>Monitorar desconexões por dispositivo com a grade de eventos

Azure Monitor fornece uma métrica, *dispositivos conectados*, que você pode usar para monitorar o número de dispositivos conectados ao seu hub IOT e disparar um alerta quando o número de dispositivos conectados cair abaixo de um valor de limite. Embora isso possa ser suficiente para alguns cenários, a [grade de eventos do Azure](../event-grid/index.yml) fornece uma solução de monitoramento de baixa latência por dispositivo que você pode usar para rastrear conexões de dispositivo para dispositivos e infraestruturas críticos.

Com a grade de eventos, você pode assinar os eventos [ **DeviceConnected** e **DeviceDisconnected**](iot-hub-event-grid.md#event-types) do Hub IOT para disparar alertas e monitorar o estado da conexão do dispositivo. A grade de eventos fornece uma latência de eventos muito menor do que Azure Monitor, e você pode monitorar por dispositivo, em vez de para o número total de dispositivos conectados. Esses fatores tornam a grade de eventos o método preferencial para o monitoramento de conexões para dispositivos e infraestruturas críticos. É altamente recomendável usar a grade de eventos para monitorar as conexões de dispositivo em ambientes de produção.

Para obter informações mais detalhadas sobre como monitorar conexões de dispositivo com a grade de eventos e Azure Monitor, consulte [monitorar, diagnosticar e solucionar problemas de desconexão com o Hub IOT do Azure](iot-hub-troubleshoot-connectivity.md).

## <a name="next-steps"></a>Próximas etapas

- Consulte [monitorando a referência de dados do Hub IOT do Azure](monitor-iot-hub-reference.md) para obter uma referência das métricas, dos logs e de outros valores importantes criados pelo [nome do serviço].

- Confira [Como monitorar os recursos do Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre o monitoramento de recursos do Azure.
