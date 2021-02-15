---
title: Exportar dados do Azure IoT Central (Herdado) | Microsoft Docs
description: Como exportar dados de seu aplicativo de IoT Central do Azure para os hubs de eventos do Azure, o barramento de serviço do Azure e o armazenamento de BLOBs do Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: d4c099d29a843b4c354ffb218887dc7ffab51771
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065432"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>Exportar dados de IoT para destinos de nuvem usando a exportação de dados (herdada)

> [!Note]
> Este artigo descreve os recursos de exportação de dados herdados no IoT Central.
>
> - Para obter informações sobre os recursos de exportação de dados mais recentes, consulte [exportar dados de IOT para destinos de nuvem usando a exportação de dados](./howto-export-data.md).
> - Para saber mais sobre as diferenças entre os recursos de exportação de dados de visualização e exportação de dados herdados, consulte a [tabela de comparação](./howto-export-data.md#comparison-of-legacy-data-export-and-data-export).

Este artigo descreve como usar o recurso de exportação de dados no Azure IoT Central. Esse recurso permite exportar os dados continuamente para os **hubs de eventos do** Azure, o **barramento de serviço do Azure** ou as instâncias **do armazenamento de BLOBs do Azure** . A exportação de dados usa o formato JSON e pode incluir telemetria, informações do dispositivo e informações do modelo do dispositivo. Use os dados exportados para:

- Análise e insights de caminho quentes. Essa opção inclui o disparo de regras personalizadas no Azure Stream Analytics, o disparo de fluxos de trabalho personalizados em aplicativos lógicos do Azure ou a passagem por Azure Functions ser transformada.
- Análise de caminho frio, como modelos de treinamento em Azure Machine Learning ou análise de tendência de longo prazo no Microsoft Power BI.

> [!Note]
> Ao ativar a exportação de dados, você obtém apenas os dados desse momento em diante. No momento, os dados não podem ser recuperados por um horário em que a exportação de dados estava desativada. Para reter mais dados históricos, ative a exportação de dados antecipadamente.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ser um administrador em seu aplicativo IoT Central ou ter permissões de exportação de dados.

## <a name="set-up-export-destination"></a>Configurar o destino de exportação

O destino de exportação deve existir antes de você configurar a exportação de dados.

### <a name="create-event-hubs-namespace"></a>Criar um namespace dos Hubs de Eventos

Se você não tiver um namespace de hubs de eventos existente para exportar para o, siga estas etapas:

1. Crie um [namespace dos Hubs de Eventos no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Saiba mais na [documentação dos Hubs de Eventos do Azure](../../event-hubs/event-hubs-create.md).

2. Escolha uma assinatura. Você pode exportar dados para outras assinaturas que não estão na mesma assinatura que o aplicativo IoT Central. Você se conecta usando uma cadeia de conexão nesse caso.

3. Crie um hub de eventos em seu namespace dos Hubs de Eventos. Acesse o namespace e selecione **+ Hub de Eventos** na parte superior para criar uma instância de hub de eventos.

### <a name="create-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço

Se você não tiver um namespace do barramento de serviço existente para exportar para o, siga estas etapas:

1. Crie um [novo namespace do barramento de serviço no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Saiba mais na [documentação do Barramento de Serviço do Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Escolha uma assinatura. Você pode exportar dados para outras assinaturas que não estão na mesma assinatura que o aplicativo IoT Central. Você se conecta usando uma cadeia de conexão nesse caso.

3. Para criar uma fila ou um tópico para exportar para o, vá para o namespace do barramento de serviço e selecione **+ fila** ou **+ tópico**.

Quando você escolhe o barramento de serviço como um destino de exportação, as filas e os tópicos não devem ter sessões ou detecção de duplicidades habilitadas. Se alguma dessas opções estiver habilitada, algumas mensagens não chegarão à sua fila ou tópico.

### <a name="create-storage-account"></a>Criar Conta de Armazenamento

Se você não tiver uma conta de armazenamento do Azure existente para a qual exportar, siga estas etapas:

1. Crie uma [conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Você pode saber mais sobre como criar novas [contas de armazenamento de BLOBs do Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) ou [contas de armazenamento Azure data Lake Storage v2](../../storage/common/storage-account-create.md). A exportação de dados só pode gravar dados em contas de armazenamento que dão suporte a blobs de blocos. A lista a seguir mostra os tipos de conta de armazenamento compatíveis conhecidos:

    |Nível de desempenho|Tipo de Conta|
    |-|-|
    |Standard|Uso Geral v2|
    |Standard|Uso Geral v1|
    |Standard|Armazenamento de Blobs|
    |Premium|Armazenamento de blobs de blocos|

2. Crie um contêiner em sua conta de armazenamento. Vá até sua conta de armazenamento. Em **Serviço Blob**, selecione **Procurar Blobs**. Selecione **+ Contêiner** na parte superior para criar um contêiner.

## <a name="set-up-data-export"></a>Configurar a exportação de dados

Agora que você tem um destino para exportar dados para o, siga estas etapas para configurar a exportação de dados.

1. Entre no aplicativo IoT Central.

2. No painel esquerdo, selecione **exportação de dados**.

    > [!Tip]
    > Se você não vir a **exportação de dados** no painel esquerdo, você não tem permissões para configurar a exportação de dados em seu aplicativo. Contate o administrador para configurar a exportação de dados.

3. Selecione o botão **+ Novo**. Escolha um dos **armazenamento de BLOBs do Azure**, **hubs de eventos do Azure**, **fila do barramento de serviço do Azure** ou o **tópico do barramento de serviço do Azure** como o destino de sua exportação. O número máximo de exportações por aplicativo é cinco.

4. Insira um nome para a exportação. Na caixa de listagem suspensa, selecione o **namespace** ou **Insira uma cadeia de conexão**.

    - Você só vê contas de armazenamento, namespaces de hubs de eventos e namespaces do barramento de serviço na mesma assinatura que seu aplicativo IoT Central. Se você quiser exportar para um destino fora dessa assinatura, escolha **Inserir uma cadeia de conexão** e consulte a etapa 6.
    - Para aplicativos criados usando o plano de preços gratuito, a única maneira de configurar a exportação de dados é por meio de uma cadeia de conexão. Os aplicativos no plano de preços gratuito não têm uma assinatura do Azure associada.

    ![Criar novo hub de eventos](media/howto-export-data-legacy/export-event-hub.png)

5. Escolha um hub de eventos, uma fila, um tópico ou um contêiner na caixa de listagem suspensa.

6. (Opcional) Se você escolher **Inserir uma cadeia de conexão**, uma nova caixa será exibida para que você cole a cadeia de conexão. Para obter a cadeia de conexão para:

    - Hubs de eventos ou barramento de serviço, acesse o namespace no portal do Azure:
        - Para usar uma cadeia de conexão para o namespace inteiro:
            1. Em **configurações**, selecione **políticas de acesso compartilhado**
            2. Crie uma nova chave ou escolha uma chave existente que tenha permissões de **envio** .
            3. Copie a cadeia de conexão primária ou secundária
        - Para usar a cadeia de conexão para uma instância específica do hub de eventos ou uma fila ou tópico do barramento de serviço, acesse **entidades > hubs de eventos** ou **entidades > filas** ou **entidades > tópicos**. Escolha uma instância específica e siga as mesmas etapas acima para obter uma cadeia de conexão.
    - Conta de armazenamento, vá para a conta de armazenamento no portal do Azure:
        - Há suporte apenas para cadeias de conexão para toda a conta de armazenamento. Não há suporte para cadeias de conexão com escopo para um único contêiner.
          1. Em **configurações**, selecione **chaves de acesso**
          2. Copie a cadeia de conexão key1 ou a cadeia de conexão Key2

    Cole a cadeia de conexão. Digite a instância ou o **nome do contêiner** que diferencia maiúsculas de minúsculas.

7. Em **dados a serem exportados**, escolha os tipos de dados a serem exportados definindo o tipo como **ativado**.

8. Para ativar a exportação de dados, **Verifique se a** opção **habilitado** está ativada. Selecione **Salvar**.

9. Depois de alguns minutos, seus dados aparecerão no destino escolhido.

## <a name="export-contents-and-format"></a>Exportar conteúdo e formato

Os dados de telemetria exportados contêm todo o valor da mensagem que seus dispositivos enviaram para IoT Central, não apenas os próprios valores de telemetria. Dados de dispositivos exportados contém alterações em Propriedades e metadados de todos os dispositivos, e os modelos de dispositivo exportados contêm alterações em todos os modelos de dispositivo.

Para os hubs de eventos e o barramento de serviço, os dados são exportados quase em tempo real. Os dados estão na `body` propriedade e estão no formato JSON. Consulte abaixo para obter exemplos.

Para o armazenamento de BLOBs, os dados são exportados uma vez por minuto, com cada arquivo que contém o lote de alterações desde o último arquivo exportado. Os dados exportados são colocados em três pastas no formato JSON. Os caminhos padrão em sua conta de armazenamento são:

- Telemetria: _{container}/{app-ID}/Telemetry/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Dispositivos: _{container}/{app-ID}/Devices/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Modelos de dispositivo: _{container}/{app-ID}/deviceTemplates/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Para procurar os arquivos exportados no portal do Azure, navegue até o arquivo e selecione a guia **Editar blob** .

## <a name="telemetry"></a>Telemetria

Para os hubs de eventos e o barramento de serviço, IoT Central exporta uma nova mensagem rapidamente depois de receber a mensagem de um dispositivo. Cada mensagem exportada contém a mensagem completa que o dispositivo enviou na Propriedade Body no formato JSON.

Para o armazenamento de BLOBs, as mensagens são agrupadas em lote e exportadas uma vez por minuto. Os arquivos exportados usam o mesmo formato que os arquivos de mensagem exportados pelo [Roteamento de mensagens do Hub IOT](../../iot-hub/tutorial-routing.md) para o armazenamento de BLOBs.

> [!NOTE]
> Para o armazenamento de BLOBs, verifique se os dispositivos estão enviando mensagens que têm `contentType: application/JSON` e `contentEncoding:utf-8` (ou `utf-16` `utf-32` ). Consulte a [documentação do Hub IOT](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) para obter um exemplo.

O dispositivo que enviou a telemetria é representado pela ID do dispositivo (consulte as seções a seguir). Para obter os nomes dos dispositivos, exporte os dados do dispositivo e correlacione cada mensagem usando o **connectionDeviceId** que corresponde à **DeviceID** da mensagem do dispositivo.

O exemplo a seguir mostra uma mensagem recebida de um hub de eventos ou de uma fila ou tópico do barramento de serviço:

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

Essa mensagem não inclui a ID do dispositivo de envio.

Para recuperar a ID do dispositivo dos dados da mensagem em uma consulta Azure Stream Analytics, use a função [GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue) . Para obter um exemplo, consulte a consulta em [estender IOT central do Azure com regras personalizadas usando Stream Analytics, Azure Functions e SendGrid](./howto-create-custom-rules.md).

Para recuperar a ID do dispositivo em um Azure Databricks ou Apache Spark espaço de trabalho, use [SystemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md). Para obter um exemplo, consulte o espaço de trabalho do databricks em [estender o Azure IOT central com a análise personalizada usando Azure Databricks](./howto-create-custom-analytics.md).

O exemplo a seguir mostra um registro exportado para o armazenamento de BLOBs:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Dispositivos

Cada mensagem ou registro em um instantâneo representa uma ou mais alterações em um dispositivo e suas propriedades de dispositivo e de nuvem desde a última mensagem exportada. A mensagem inclui:

- `id` do dispositivo na IoT Central
- `displayName` do dispositivo
- ID do modelo de dispositivo em `instanceOf`
- `simulated` sinalizador, verdadeiro se o dispositivo for um dispositivo simulado
- `provisioned` sinalizador, verdadeiro se o dispositivo tiver sido provisionado
- `approved` sinalizador, verdadeiro se o dispositivo tiver sido aprovado para enviar dados
- Valores de propriedade
- `properties` incluindo valores de propriedades de dispositivo e de nuvem

Os dispositivos excluídos não são exportados. Atualmente, não há indicadores em mensagens exportadas para dispositivos excluídos.

Para os hubs de eventos e o barramento de serviço, IoT Central envia mensagens que contêm dados de dispositivo para o Hub de eventos ou a fila ou tópico do barramento de serviço quase em tempo real.

Para o armazenamento de BLOBs, um novo instantâneo que contém todas as alterações desde a última gravação é exportado uma vez por minuto.

A seguinte mensagem de exemplo mostra informações sobre dispositivos e dados de propriedades em um hub de eventos ou em uma fila ou tópico do barramento de serviço:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Esse instantâneo é uma mensagem de exemplo que mostra os dispositivos e os dados de propriedades no armazenamento de BLOBs. Os arquivos exportados contêm uma única linha por registro.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Modelos de dispositivo

Cada registro de mensagem ou de instantâneo representa uma ou mais alterações em um modelo de dispositivo publicado desde a última mensagem exportada. As informações enviadas em cada mensagem ou registro incluem:

- `id` do modelo de dispositivo que corresponde ao `instanceOf` do fluxo de dispositivos acima
- `displayName` do modelo de dispositivo
- O dispositivo que `capabilityModel` inclui suas `interfaces` e as definições telemetria, propriedades e comandos
- `cloudProperties` definições
- Substituições e valores iniciais, embutidos com o `capabilityModel`

Os modelos de dispositivo excluídos não são exportados. Atualmente, não há indicadores em mensagens exportadas para modelos de dispositivos excluídos.

Para os hubs de eventos e o barramento de serviço, IoT Central envia mensagens contendo dados de modelo de dispositivo para o Hub de eventos ou a fila ou tópico do barramento de serviço quase em tempo real.

Para o armazenamento de BLOBs, um novo instantâneo que contém todas as alterações desde a última gravação é exportado uma vez por minuto.

Este exemplo mostra uma mensagem sobre os dados de modelos de dispositivo no Hub de eventos ou na fila ou no tópico do barramento de serviço:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Este instantâneo de exemplo mostra uma mensagem que contém dados de dispositivo e de propriedades no armazenamento de BLOBs. Os arquivos exportados contêm uma única linha por registro.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```

## <a name="data-format-change-notice"></a>Aviso de alteração do formato de dados

> [!Note]
> O formato de dados do fluxo de telemetria não é afetado por essa alteração. Somente os fluxos de dados de dispositivos e modelos de dispositivo são afetados.

Se você tiver uma exportação de dados existente em seu aplicativo de visualização com os *dispositivos* e fluxos de *modelos de dispositivo* ativados, atualize sua exportação em 30 de **junho de 2020**. Esse requisito se aplica a exportações para o armazenamento de BLOBs do Azure, hubs de eventos do Azure e barramento de serviço do Azure.

A partir de 3 de fevereiro de 2020, todas as novas exportações em aplicativos com dispositivos e modelos de dispositivo habilitados terão o formato de dados descrito acima. Todas as exportações criadas antes dessa data permanecem no formato de dados antigo até 30 de junho de 2020, no momento em que essas exportações serão migradas automaticamente para o novo formato de dados. O novo formato de dados corresponde ao [dispositivo](/rest/api/iotcentral/devices/get), à [propriedade de dispositivo](/rest/api/iotcentral/devices/getproperties), à propriedade de nuvem de [dispositivo](/rest/api/iotcentral/devices/getcloudproperties)e aos objetos de modelo de [dispositivo](/rest/api/iotcentral/devicetemplates/get) na API pública IOT central.

Para **dispositivos**, as diferenças notáveis entre o formato de dados antigo e o novo formato de dados incluem:
- `@id` para o dispositivo é removido, `deviceId` é renomeado para `id` 
- `provisioned` o sinalizador é adicionado para descrever o status de provisionamento do dispositivo
- `approved` o sinalizador é adicionado para descrever o estado de aprovação do dispositivo
- `properties` incluindo propriedades de dispositivo e de nuvem, corresponde às entidades na API pública

Para **modelos de dispositivo**, as diferenças notáveis entre o formato de dados antigo e o novo formato de dados incluem:

- `@id` para o modelo de dispositivo é renomeado para `id`
- `@type` para o modelo de dispositivo é renomeado para `types` e agora é uma matriz

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Dispositivos (formato preterido a partir de 3 de fevereiro de 2020)

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Modelos de dispositivo (formato preterido a partir de 3 de fevereiro de 2020)

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como exportar seus dados para os hubs de eventos do Azure, o barramento de serviço do Azure e o armazenamento de BLOBs do Azure, vá para a próxima etapa:

> [!div class="nextstepaction"]
> [Como executar análises personalizadas com o databricks](./howto-create-custom-analytics.md)