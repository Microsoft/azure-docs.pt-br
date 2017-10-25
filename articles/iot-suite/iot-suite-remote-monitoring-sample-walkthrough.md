---
title: "Passo a passo da solução pré-configurada de monitoramento remoto | Microsoft Docs"
description: "Uma descrição do monitoramento remoto pré-configurado da solução de IoT do Azure e sua arquitetura."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: b28105f300723b542fa6d1aebc569439d5c73dc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Passo a passo da solução pré-configurada de monitoramento remoto

A [solução pré-configurada][lnk-preconfigured-solutions] de monitoramento remoto do IoT Suite é uma implementação de uma solução de monitoramento de ponta a ponta para vários computadores em execução em locais remotos. A solução combina os principais serviços do Azure para fornecer uma implementação genérica de cenário de negócios. Você pode usar a solução como um ponto de partida para sua própria implementação e [personalizar][lnk-customize] para atender às suas próprias necessidades de negócios específicas.

Este artigo explica alguns dos principais elementos da solução de monitoramento remoto para que você possa entender como ela funciona. Esse conhecimento ajuda a:

* Solucionar problemas na solução.
* Planejar como personalizar a solução para atender a seus próprios requisitos específicos. 
* Criar sua própria solução IoT que usa os serviços do Azure.

## <a name="logical-architecture"></a>Arquitetura lógica

O seguinte diagrama descreve os componentes lógicos da solução pré-configurada:

![Arquitetura lógica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="simulated-devices"></a>Dispositivos simulados

Na solução pré-configurada, o dispositivo simulado representa um dispositivo de resfriamento (como um ar-condicionado para edifícios ou uma unidade de tratamento de ar para instalações). Ao implantar a solução pré-configurada, você também provisiona automaticamente quatro dispositivos simulados que são executados em um [Trabalho Web do Azure][lnk-webjobs]. Os dispositivos simulados facilitam a exploração do comportamento da solução sem a necessidade de implantar dispositivos físicos. Para implantar um dispositivo físico real, consulte o tutorial [Conectar o dispositivo à solução pré-configurada de monitoramento remoto][lnk-connect-rm].

### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a nuvem

Cada dispositivo simulado pode enviar os seguintes tipos de mensagens ao Hub IoT:

| Mensagem | Descrição |
| --- | --- |
| Inicialização |Quando o dispositivo é iniciado, ele envia uma mensagem de **informações do dispositivo** com informações sobre ele mesmo para o back-end. Esses dados incluem a identificação do dispositivo e uma lista de comandos e métodos que o dispositivo oferece suporte. |
| Presença |Um dispositivo envia periodicamente uma mensagem de **presença** para relatar se o dispositivo pode detectar a presença de um sensor. |
| Telemetria |Um dispositivo envia periodicamente uma mensagem de **telemetria** que relata os valores simulados de temperatura e umidade coletados nos sensores simulados do dispositivo. |

> [!NOTE]
> A solução armazena a lista de comandos com suporte pelo dispositivo em banco de dados Cosmos DB e não no dispositivo gêmeo.

### <a name="properties-and-device-twins"></a>Propriedades e dispositivos gêmeos

Os dispositivos simulados enviam as seguintes propriedades do dispositivo para o [gêmeo][lnk-device-twins] no hub IoT como *propriedades relatadas*. O dispositivo envia propriedades relatadas na inicialização e em resposta a um comando ou método **Alterar Estado do Dispositivo**.

| Propriedade | Finalidade |
| --- | --- |
| Config.TelemetryInterval | Frequência (segundos) em que o dispositivo envia telemetria |
| Config.TemperatureMeanValue | Especifica o valor médio para a telemetria de temperatura simulada |
| Device.DeviceID |A Id que é fornecida ou atribuída quando um dispositivo é criado na solução |
| Device.DeviceState | Estado relatado pelo dispositivo |
| Device.CreatedTime |Hora de criação do dispositivo na solução |
| Device.StartupTime |Hora de que início do dispositivo |
| Device.LastDesiredPropertyChange |O número de versão da última propriedade desejada é alterado |
| Device.Location.Latitude |Localização de latitude do dispositivo |
| Device.Location.Longitude |Localização de longitude do dispositivo |
| System.Manufacturer |Fabricante do dispositivo |
| System.ModelNumber |O número do modelo do dispositivo |
| System.SerialNumber |Número de série do dispositivo |
| System.FirmwareVersion |Versão atual do firmware no dispositivo |
| System.Platform |Arquitetura da plataforma do dispositivo |
| System.Processor |Processador que executa o dispositivo |
| System.InstalledRAM |Quantidade de RAM instalada no dispositivo |

O simulador propaga essas propriedades em dispositivos simulados com valores de exemplo. Sempre que o simulador inicializa um dispositivo simulado, o dispositivo posta os metadados predefinidos no Hub IoT como propriedades relatadas. As propriedades relatadas só podem ser atualizadas pelo dispositivo. Para alterar uma propriedade relatada, você pode definir uma propriedade desejada no portal de solução. É responsabilidade do dispositivo:

1. Recuperar periodicamente as propriedades desejadas do hub IoT.
2. Atualizar sua configuração com o valor da propriedade desejada.
3. Enviar o novo valor para o hub como uma propriedade relatada.

No painel de solução, você pode usar *propriedades desejadas* para definir propriedades em um dispositivo usando o [dispositivo gêmeo][lnk-device-twins]. Normalmente, um dispositivo lê um valor de propriedade desejada do hub para atualizar seu estado interno e relatar a alteração como uma propriedade relatada.

> [!NOTE]
> O código do dispositivo simulado usa apenas as propriedades **Desired.Config.TemperatureMeanValue** e **Desired.Config.TelemetryInterval** desejadas para atualizar as propriedades relatadas enviadas de volta ao Hub IoT. Todas as outras solicitações de alteração de propriedade desejados são ignoradas no dispositivo simulado.

### <a name="methods"></a>Métodos

Os dispositivos simulados podem lidar com os seguintes métodos ([métodos diretos][lnk-direct-methods]) chamados do portal de solução por meio do hub IoT:

| Método | Descrição |
| --- | --- |
| InitiateFirmwareUpdate |Instrui o dispositivo a realizar uma atualização de firmware |
| Reboot |Instrui o dispositivo a reinicializar |
| FactoryReset |Instrui o dispositivo a executar uma redefinição de fábrica |

Alguns métodos usam propriedades relatadas para relatar o progresso. Por exemplo, o método **InitiateFirmwareUpdate** simula a execuçã da atualização de maneira assíncrona no dispositivo. O método retorna imediatamente no dispositivo, enquanto a tarefa assíncrona continua a enviar atualizações de status de volta para o painel de solução usando as propriedades relatadas.

### <a name="commands"></a>Comandos

Os dispositivos simulados podem lidar com os seguintes comandos (mensagens de nuvem para dispositivos) enviados a partir do portal de solução por meio do hub IoT:

| Command | Descrição |
| --- | --- |
| PingDevice |Envia um *ping* ao dispositivo para verificar se ele está ativo |
| StartTelemetry |Inicia o dispositivo de envio de telemetria |
| StopTelemetry |Interrompe o dispositivo de enviar a telemetria |
| ChangeSetPointTemp |Altera o valor de ponto definido em torno do qual os dados aleatórios são gerados |
| DiagnosticTelemetry |Dispara o simulador de dispositivo para enviar um valor de telemetria adicional (externalTemp) |
| ChangeDeviceState |Altera uma propriedade estendida do estado do dispositivo e envia a mensagem de informações do dispositivo |

> [!NOTE]
> Para obter uma comparação desses comandos (mensagens da nuvem para o dispositivo) e métodos (métodos diretos), consulte [Orientação para comunicações entre o dispositivo e a nuvem][lnk-c2d-guidance].

## <a name="iot-hub"></a>Hub IoT

O [hub IoT][lnk-iothub] consome os dados enviados dos dispositivos para a nuvem e os disponibiliza para os trabalhos do Stream Analytics do Azure (ASA). Cada trabalho de fluxo do ASA usa um grupo separado de consumidores do Hub IoT para ler o fluxo de mensagens dos dispositivos.

O hub IoT na solução também:

- Mantém um registro de identidade que armazena as ids e chaves de autenticação de todos os dispositivos permitidas para se conectar ao portal. Você pode habilitar e desabilitar dispositivos por meio do registro de identidade.
- Envia comandos para os dispositivos em nome do portal de solução.
- Chama métodos em seus dispositivos em nome do portal de solução.
- Mantém dispositivos gêmeos para todos os dispositivos registrados. Um dispositivo gêmeo armazena os valores de propriedade relatados por um dispositivo. Um dispositivo gêmeo também armazena propriedades desejadas, definidas no portal de solução para o dispositivo recuperar ao lado se conectar.
- Agenda trabalhos para definir propriedades para vários dispositivos ou chama métodos em vários dispositivos.

## <a name="azure-stream-analytics"></a>Stream Analytics do Azure

Na solução de monitoramento remoto, o [Stream Analytics do Azure][lnk-asa] (ASA) envia mensagens do dispositivo recebidas pelo hub IoT para outros componentes de back-end para o processamento ou armazenamento. Diferentes trabalhos do ASA executam funções específicas com base no conteúdo das mensagens.

**Trabalho 1: as Informações do Dispositivo** filtram as mensagens de informações do dispositivo a partir do fluxo de mensagens de entrada e enviam-nas para um ponto de extremidade do Hubs de Eventos. Um dispositivo envia mensagens de informações do dispositivo na inicialização e em resposta a um comando **SendDeviceInfo** . Esse trabalho usa a seguinte definição de consulta para identificar as mensagens de **informações do dispositivo** :

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Esse trabalho envia a saída a um Hub de Eventos para processamento adicional.

**Trabalho 2: as Regras** avaliam os valores de telemetria da temperatura e da umidade de entrada em relação aos limites de cada dispositivo. Os valores de limite são definidos no editor de regras disponível no portal da solução. Cada par de dispositivo/valor é armazenado por carimbo de data/hora em um blob que o Stream Analytics lê como **Dados de Referência**. O trabalho compara qualquer valor não vazio com o limite definido para o dispositivo. Se ele exceder a condição ‘>’, o trabalho gerará um evento de **alarme** indicando que o limite foi excedido e fornecendo o dispositivo, o valor e os valores do carimbo de data/hora. Esse trabalho usa a seguinte definição de consulta para identificar mensagens de telemetria que devem disparar um alarme:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

O trabalho envia a saída a um Hub de Eventos para processamento adicional e salva os detalhes de cada alerta no armazenamento de blobs, em que o portal da solução pode ler as informações do alerta.

**Trabalho 3: a Telemetria** opera no fluxo de entrada da telemetria do dispositivo de duas maneiras. A primeira envia todas as mensagens de telemetria dos dispositivos ao armazenamento de blobs persistente para armazenamento de longo prazo. A segunda calcula os valores de umidade média, mínima e máxima em uma janela deslizante de cinco minutos e envia os dados para o armazenamento de blobs. O portal da solução lê os dados de telemetria do armazenamento de blobs para popular os gráficos. Esse trabalho usa a seguinte definição de consulta:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Hubs de Eventos

Os trabalhos do ASA de **informações do dispositivo** e **regras** enviam seus dados para os Hubs de Eventos para um encaminhamento confiável ao **Processador de Eventos** em execução no Trabalho Web.

## <a name="azure-storage"></a>Armazenamento do Azure

A solução usa o armazenamento de blobs do Azure para persistir todos os dados de telemetria brutos e resumidos dos dispositivos na solução. O portal lê os dados de telemetria do armazenamento de blobs para popular os gráficos. Para exibir alertas, o portal da solução lê os dados do armazenamento de blobs que registra quando os valores de telemetria excederam os valores de limite configurados. A solução também usa o armazenamento de blobs para registrar os valores de limite definidos no portal da solução.

## <a name="webjobs"></a>Trabalhos Web

Além de hospedar os simuladores de dispositivo, os Trabalhos Web na solução também hospedam o **Processador de Eventos** em execução em um Trabalho Web do Azure que lida com as respostas do comando. Ele usa mensagens de resposta de comando para atualizar o histórico de comandos do dispositivo (armazenados no banco de dados Cosmos DB).

## <a name="cosmos-db"></a>Banco de Dados Cosmos

A solução usa um banco de dados Cosmos DB para armazenar informações sobre os dispositivos conectados à solução. Essas informações incluem o histórico de comandos enviados aos dispositivos no portal de solução e os métodos invocados do portal de solução.

## <a name="solution-portal"></a>Portal de solução

O portal de solução é um aplicativo Web implantado como parte da solução pré-configurada. As páginas principais no portal de solução são o painel e a lista de dispositivos.

### <a name="dashboard"></a>Painel

Esta página no aplicativo Web usa os controles javascript do PowerBI (veja [Repositório PowerBI-visuals](https://www.github.com/Microsoft/PowerBI-visuals)) para visualizar os dados de telemetria dos dispositivos. A solução usa o trabalho de telemetria do ASA para gravar os dados de telemetria no armazenamento de blobs.

### <a name="device-list"></a>Lista de dispositivos

Nesta página no portal de solução, você pode:

* Provisione um novo dispositivo. Esta ação define a id do dispositivo exclusiva e gera a chave de autenticação. Ela grava as informações sobre o dispositivo no registro de identidade do Hub IoT e no banco de dados Cosmos DB específico da solução.
* Gerencie propriedades de dispositivo. Esta ação inclui a exibição de propriedades existentes e a atualização com novas propriedades.
* Envie comandos para um dispositivo.
* Exiba o histórico de comandos de um dispositivo.
* Habilite e desabilite dispositivos.

## <a name="next-steps"></a>Próximas etapas

As postagens de blog do TechNet abaixo fornecem mais detalhes sobre a solução pré-configurada de monitoramento remoto:

* [IoT Suite - Under the Hood - Remote Monitoring (Monitoramento remoto)](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
* [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices (Adicionando dispositivos ativos e simulados)](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Você pode continuar a introdução ao IoT Suite lendo os seguintes artigos:

* [Conectar seu dispositivo à solução pré-configurada de monitoramento remoto][lnk-connect-rm]
* [Permissões no site azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twins]:  ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
