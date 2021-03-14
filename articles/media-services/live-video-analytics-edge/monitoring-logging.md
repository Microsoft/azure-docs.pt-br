---
title: Monitoramento e registro em log – Azure
description: Este artigo fornece uma visão geral do monitoramento e registro em log na análise de vídeo ao vivo no IoT Edge.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: e81b1e98fb30bb8876c78c8c911585f5448db8f2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730228"
---
# <a name="monitoring-and-logging"></a>Monitoramento e registro em log

Neste artigo, você aprenderá a receber eventos de monitoramento remoto da análise de vídeo ao vivo no módulo IoT Edge. 

Você também aprenderá a controlar os logs gerados pelo módulo.

## <a name="taxonomy-of-events"></a>Taxonomia de eventos

A análise de vídeo ao vivo em IoT Edge emite eventos ou dados de telemetria, de acordo com a seguinte taxonomia:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Diagrama que mostra a taxonomia de eventos.":::

* Operacional: eventos gerados pelas ações de um usuário ou durante a execução de um [grafo de mídia](media-graph-concept.md)
   
   * Volume: esperado para baixo (algumas vezes por minuto ou mesmo menos)
   * Exemplos:

      - Gravação iniciada (mostrada no exemplo a seguir)
      - Gravação interrompida
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnóstico: eventos que ajudam a diagnosticar problemas com o desempenho

   * Volume: pode ser alto (várias vezes por minuto)
   * Exemplos:
   
      - Informações de [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) RTSP (mostradas no exemplo a seguir) 
      - Lacunas no feed de vídeo de entrada

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Análise: eventos gerados como parte da análise de vídeo

   * Volume: pode ser alto (várias vezes por minuto ou mais)
   * Exemplos:
      
      - Movimento detectado (mostrado no exemplo a seguir) 
      - Resultado da inferência

   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

Os eventos emitidos pelo módulo são enviados para o [Hub de IOT Edge](../../iot-edge/iot-edge-runtime.md#iot-edge-hub). Eles podem ser roteados de lá para outros destinos. 

### <a name="timestamps-in-analytic-events"></a>Carimbos de data/hora em eventos analíticos

Conforme indicado anteriormente, os eventos gerados como parte da análise de vídeo têm carimbos de data/hora associados a eles. Se você [gravou o vídeo ao vivo](video-recording-concept.md) como parte da sua topologia de grafo, esses carimbos de data/hora ajudam a localizar onde ocorreu o evento em questão no vídeo gravado. Veja a seguir as diretrizes sobre como mapear o carimbo de data/hora em um evento analítico para a linha do tempo do vídeo gravado em um [ativo dos serviços de mídia do Azure](terminology.md#asset).

Primeiro, extraia o `eventTime` valor. Use esse valor em um [filtro de intervalo de tempo](playback-recordings-how-to.md#time-range-filters) para recuperar uma parte adequada da gravação. Por exemplo, talvez você queira recuperar o vídeo que inicia 30 segundos antes `eventTime` e termina 30 segundos após ele. Para o exemplo anterior, em que `eventTime` é 2020-05-12T23:33:09.381 z, uma solicitação para um manifesto HLS por 30 segundos antes e depois `eventTime` seria semelhante a esta solicitação:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

A URL anterior retornaria uma [lista de reprodução mestre](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) que contém URLs para listas de reprodução de mídia. A playlist de mídia conterá entradas como esta:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
A entrada anterior relata que um fragmento de vídeo está disponível e que começa com um `timestamp` valor de `143039375031270` . O `timestamp` valor no evento analítico usa a mesma escala de TIMESCALE da lista de reprodução de mídia. Ele pode ser usado para identificar o fragmento de vídeo relevante e buscar o quadro correto.

Para obter mais informações, consulte estes [artigos sobre a busca precisa de quadros](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) em HLS.

## <a name="controlling-events"></a>Controle de eventos

Você pode usar as propriedades do módulo CTRL a seguir para controlar os eventos operacionais e de diagnóstico publicados pela análise de vídeo ao vivo no módulo IoT Edge. Essas propriedades são documentadas no [esquema do JSON do módulo](module-twin-configuration-schema.md).

- `diagnosticsEventsOutputName`: Para obter eventos de diagnóstico do módulo, inclua essa propriedade e forneça qualquer valor para ela. Omita-o ou deixe-o vazio para impedir que o módulo publique eventos de diagnóstico.
   
- `operationalEventsOutputName`: Para obter eventos operacionais do módulo, inclua essa propriedade e forneça qualquer valor para ela. Omita-o ou deixe-o vazio para impedir que o módulo publique eventos operacionais.
   
Os eventos de análise são gerados por nós como o processador de detecção de movimento ou o processador de extensão HTTP. O coletor do Hub IoT é usado para enviá-los ao Hub de IoT Edge. 

Você pode controlar o [Roteamento de todos os eventos anteriores](../../iot-edge/module-composition.md#declare-routes) usando a `desired` Propriedade do módulo " `$edgeHub` d" no manifesto de implantação:

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

No JSON anterior, `lvaEdge` é o nome da análise de vídeo ao vivo no módulo IOT Edge. A regra de roteamento segue o esquema definido em [declarar rotas](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Para garantir que os eventos de análise alcancem o Hub de IoT Edge, você precisa ter um nó de coletor de Hub IoT downstream de qualquer nó de processador de detecção de movimento e/ou qualquer nó de processador de extensão HTTP.

## <a name="event-schema"></a>Esquema do evento

Os eventos originam-se no dispositivo de borda e podem ser consumidos na borda ou na nuvem. Eventos gerados pela análise de vídeo ao vivo em IoT Edge estão em conformidade com o [padrão de mensagens de streaming](../../iot-hub/iot-hub-devguide-messages-construct.md) estabelecido pelo Hub IOT do Azure. O padrão consiste em Propriedades do sistema, propriedades do aplicativo e um corpo.

### <a name="summary"></a>Resumo

Cada evento, quando observado por meio do Hub IoT, tem um conjunto de propriedades comuns:

|Propriedade   |Tipo de propriedade| Tipo de dados   |Descrição|
|---|---|---|---|
|`message-id`   |sistema |guid|  ID de evento exclusivo.|
|`topic`|   applicationproperty |string|    Azure Resource Manager caminho para a conta dos serviços de mídia do Azure.|
|`subject`| applicationproperty |string|    Subcaminho da entidade que emite o evento.|
|`eventTime`|   applicationproperty|    string| Hora em que o evento foi gerado.|
|`eventType`|   applicationproperty |string|    Identificador de tipo de evento. (Consulte a seção a seguir.)|
|`body`|body    |objeto|    Dados de evento específicos.|
|`dataVersion`  |applicationproperty|   string  |{Major}. Secundária|

### <a name="properties"></a>Propriedades

#### <a name="message-id"></a>message-id

Um GUID (identificador global exclusivo) para o evento.

#### <a name="topic"></a>topic

Representa a conta dos serviços de mídia do Azure associada ao grafo.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

A entidade que está emitindo o evento:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

A `subject` propriedade permite mapear eventos genéricos para o módulo de geração. Por exemplo, para um nome de usuário ou senha RTSP inválido, o evento gerado estaria `Microsoft.Media.Graph.Diagnostics.ProtocolError` no `/graphInstances/myGraph/sources/myRtspSource` nó.

#### <a name="event-types"></a>Tipos de evento

Os tipos de evento são atribuídos a um namespace de acordo com este esquema:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Classes de evento

|Nome da classe|Descrição|
|---|---|
|Análise  |Eventos gerados como parte da análise de conteúdo.|
|Diagnósticos    |Eventos que ajudam no diagnóstico de problemas e desempenho.|
|Operacional    |Eventos gerados como parte da operação de recurso.|

Os tipos de evento são específicos para cada classe de evento.

Exemplos:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>Hora do evento

A hora do evento é formatada em uma cadeia de caracteres ISO 8601. Representa a hora em que o evento ocorreu.

### <a name="azure-monitor-collection-via-telegraf"></a>Coleta de Azure Monitor via Telegraf

Essas métricas serão relatadas da análise de vídeo ao vivo no módulo IoT Edge:  

|Nome da métrica|Tipo|Rótulo|Descrição|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Medidor|iothub, edge_device, module_name, graph_topology|Número total de grafos ativos por topologia.|
|lva_received_bytes_total|Contador|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Número total de bytes recebidos por um nó. Com suporte apenas para fontes RTSP.|
|lva_data_dropped_total|Contador|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|Contador de quaisquer dados eliminados (eventos, mídia e assim por diante).|

> [!NOTE]
> Um [ponto de extremidade Prometheus](https://prometheus.io/docs/practices/naming/) é exposto na porta 9600 do contêiner. Se você nomear sua análise de vídeo ao vivo no módulo IoT Edge "lvaEdge", eles poderão acessar as métricas enviando uma solicitação GET para http://lvaEdge:9600/metrics .   

Siga estas etapas para habilitar a coleta de métricas da análise de vídeo ao vivo no módulo IoT Edge:

1. Crie uma pasta no seu computador de desenvolvimento e vá para essa pasta.

1. Na pasta, crie um `telegraf.toml` arquivo que contenha as seguintes configurações:
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Certifique-se de substituir as variáveis no arquivo. toml. As variáveis são indicadas por chaves ( `{}` ).

1. Na mesma pasta, crie um Dockerfile que contenha os seguintes comandos:
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Use comandos da CLI do Docker para criar o arquivo do Docker e publicar a imagem no registro de contêiner do Azure.
    
   Para obter mais informações sobre como usar a CLI do Docker para enviar por push para um registro de contêiner, confira [imagens do Docker de push e pull](../../container-registry/container-registry-get-started-docker-cli.md). Para obter outras informações sobre o registro de contêiner do Azure, consulte a [documentação](../../container-registry/index.yml).


1. Após a conclusão do push para o registro de contêiner do Azure, adicione o seguinte nó ao seu arquivo de manifesto de implantação:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > Certifique-se de substituir as variáveis no arquivo de manifesto. As variáveis são indicadas por chaves ( `{}` ).


   Azure Monitor pode ser [autenticado por meio da entidade de serviço](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        
   O plug-in Azure Monitor Telegraf expõe [vários métodos de autenticação](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication). 

  1. Para usar a autenticação de entidade de serviço, defina estas variáveis de ambiente:  
     `AZURE_TENANT_ID`: Especifica o locatário ao qual autenticar.  
     `AZURE_CLIENT_ID`: Especifica a ID do cliente do aplicativo a ser usada.  
     `AZURE_CLIENT_SECRET`: Especifica o segredo do aplicativo a ser usado.  
     
     >[!TIP]
     > Você pode dar à entidade de serviço a função de **Editor de métricas de monitoramento** . Siga as etapas em **[criar entidade de serviço](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** para criar a entidade de serviço e atribuir a função.

1. Depois que os módulos são implantados, as métricas aparecerão em Azure Monitor em um único namespace. Os nomes de métrica corresponderão aos emitidos por Prometheus. 

   Nesse caso, na portal do Azure, vá para o Hub IoT e selecione **métricas** no painel esquerdo. Você deve ver as métricas ali.

### <a name="log-analytics-metrics-collection"></a>Coleta de métricas Log Analytics
Usando o [ponto de extremidade Prometheus](https://prometheus.io/docs/practices/naming/) juntamente com [log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/log-analytics-tutorial), você pode gerar e [monitorar métricas](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported) como, por exemplo, o usado CPUPercent, MemoryUsedPercent, etc.   

> [!NOTE]
> A configuração a seguir não coleta logs, **somente métricas**. É possível estender o módulo coletor para também coletar e carregar logs.

[![Diagrama que mostra a coleção de métricas usando log Analytics.](./media/telemetry-schema/log-analytics.png)](./media/telemetry-schema/log-analytics.png#lightbox)

1. Saiba como [coletar métricas](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector)
1. Use comandos da CLI do Docker para criar o [arquivo do Docker](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux) e publicar a imagem no registro de contêiner do Azure.
    
   Para obter mais informações sobre como usar a CLI do Docker para enviar por push para um registro de contêiner, confira [imagens do Docker de push e pull](../../container-registry/container-registry-get-started-docker-cli.md). Para obter outras informações sobre o registro de contêiner do Azure, consulte a [documentação](../../container-registry/index.yml).

1. Após a conclusão do push para o registro de contêiner do Azure, o seguinte é inserido no manifesto de implantação:
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://lvaEdge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > Os módulos `edgeHub` `edgeAgent` e `lvaEdge` são os nomes dos módulos definidos no arquivo de manifesto de implantação. Verifique se os nomes dos módulos correspondem.   

    Você pode obter os `LogAnalyticsWorkspaceId` valores e seguindo `LogAnalyticsSharedKey` estas etapas:
    1. Vá para o portal do Azure
    1. Procure seus espaços de trabalho do Log Analytics
    1. Quando encontrar seu espaço de trabalho Log Analytics, navegue até a `Agents management` opção no painel de navegação à esquerda.
    1. Você encontrará a ID do espaço de trabalho e as chaves secretas que você pode usar.

1. Em seguida, crie uma pasta de trabalho clicando na `Workbooks` guia no painel de navegação à esquerda.
1. Usando a linguagem de consulta Kusto, você pode gravar consultas como abaixo e obter a porcentagem de CPU usada pelos módulos de IoT Edge.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("lvaEdge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [![Diagrama que mostra as métricas usando a consulta Kusto.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>Registro em log

Assim como ocorre com outros módulos IoT Edge, você também pode [examinar os logs de contêiner](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) no dispositivo de borda. Você pode configurar as informações gravadas nos logs usando as seguintes propriedades de [mymódulo](module-twin-configuration-schema.md) :

* `logLevel`

   * Os valores permitidos são `Verbose`, `Information`, `Warning`, `Error` e `None`.
   * O valor padrão é `Information`. Os logs conterão mensagens de erro, de aviso e de informações.
   * Se você definir o valor como `Warning` , os logs conterão mensagens de erro e de aviso.
   * Se você definir o valor como `Error` , os logs conterão apenas mensagens de erro.
   * Se você definir o valor como `None` , nenhum log será gerado. (Não recomendamos essa configuração.)
   * Use `Verbose` somente se você precisar compartilhar logs com o suporte do Azure para diagnosticar um problema.

* `logCategories`

   * Uma lista separada por vírgulas de um ou mais destes valores: `Application` , `Events` , `MediaPipeline` .
   * O valor padrão é `Application, Events`.
   * `Application`: Informações de alto nível do módulo, como mensagens de inicialização de módulo, erros de ambiente e chamadas de método direta.
   * `Events`: Todos os eventos que foram descritos anteriormente neste artigo.
   * `MediaPipeline`: Logs de baixo nível que podem oferecer insights quando você estiver solucionando problemas, como dificuldades ao estabelecer uma conexão com uma câmera compatível com RTSP.
   
### <a name="generating-debug-logs"></a>Gerando logs de depuração

Em alguns casos, para ajudar o suporte do Azure a resolver um problema, talvez seja necessário gerar logs mais detalhados do que aqueles descritos anteriormente. Para gerar esses logs:

1. [Vincule o armazenamento do módulo ao armazenamento do dispositivo](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) via `createOptions` . Se você examinar um [modelo de manifesto de implantação](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) por meio dos guias de início rápido, verá este código:

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   Esse código permite que o módulo do Edge grave logs no caminho de armazenamento do dispositivo `/var/local/mediaservices/` . 

 1. Adicione a seguinte `desired` propriedade ao módulo:

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Agora, o módulo gravará os logs de depuração em um formato binário no caminho de armazenamento do dispositivo `/var/local/mediaservices/debuglogs/` . Você pode compartilhar esses logs com o suporte do Azure.

## <a name="faq"></a>Perguntas frequentes

Se você tiver dúvidas, consulte as [perguntas frequentes sobre monitoramento e métricas](faq.md#monitoring-and-metrics).

## <a name="next-steps"></a>Próximas etapas

[Gravação de vídeo contínua](continuous-video-recording-tutorial.md)
