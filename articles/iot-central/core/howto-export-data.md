---
title: Exportar dados do Azure IoT Central | Microsoft Docs
description: Como usar a nova exportação de dados para exportar seus dados de IoT para o Azure e destinos de nuvem personalizados.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 11/05/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: 350cd7c14a4f1ee5058a60ccf60c1205ce97916a
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226050"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>Exportar dados de IoT para destinos de nuvem usando a exportação de dados

> [!Note]
> Este artigo descreve os recursos de exportação de dados no IoT Central. Para obter informações sobre os recursos de exportação de dados herdados, consulte [exportar dados de IOT para destinos de nuvem usando a exportação de dados (Herdado)](./howto-export-data-legacy.md).

Este artigo descreve como usar o novo recurso de exportação de dados no Azure IoT Central. Use esse recurso para exportar continuamente dados de IoT filtrados e aprimorados do seu aplicativo IoT Central. A exportação de dados envia as alterações quase em tempo real para outras partes da sua solução de nuvem para percepções, análises e armazenamento de caminho quente.

Por exemplo, você pode:

- Exporte continuamente os dados de telemetria e as alterações de propriedade no formato JSON quase em tempo real.
- Filtre os fluxos de dados para exportar dados que correspondam às condições personalizadas.
- Enriquecer os fluxos de dados com valores personalizados e valores de Propriedade do dispositivo.
- Envie os dados para destinos como hubs de eventos do Azure, barramento de serviço do Azure, armazenamento de BLOBs do Azure e pontos de extremidade de webhook.

> [!Tip]
> Ao ativar a exportação de dados, você obtém apenas os dados desse momento em diante. No momento, os dados não podem ser recuperados por um horário em que a exportação de dados estava desativada. Para reter mais dados históricos, ative a exportação de dados antecipadamente.

## <a name="prerequisites"></a>Pré-requisitos

Para usar os recursos de exportação de dados, você deve ter um [aplicativo v3](howto-get-app-info.md)e deve ter a permissão de [exportação de dados](howto-manage-users-roles.md) .

Se você tiver um aplicativo v2, consulte [migrar seu aplicativo de IOT central v2 para v3](howto-migrate.md).

## <a name="set-up-export-destination"></a>Configurar o destino de exportação

O destino de exportação deve existir antes de você configurar a exportação de dados. Os seguintes tipos de destino estão disponíveis no momento:

- Hubs de eventos do Azure
- Fila do Barramento de Serviço do Azure
- Tópico do Barramento de Serviço do Azure
- Armazenamento do Blobs do Azure
- webhook

### <a name="create-an-event-hubs-destination"></a>Criar um destino dos hubs de eventos

Se você não tiver um namespace de hubs de eventos existente para exportar para o, siga estas etapas:

1. Crie um [namespace dos Hubs de Eventos no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Saiba mais na [documentação dos Hubs de Eventos do Azure](../../event-hubs/event-hubs-create.md).

1. Crie um hub de eventos em seu namespace dos Hubs de Eventos. Acesse o namespace e selecione **+ Hub de Eventos** na parte superior para criar uma instância de hub de eventos.

1. Gere uma chave a ser usada quando você configurar sua exportação de dados no IoT Central:

    - Selecione a instância do hub de eventos que você criou.
    - Selecione **configurações > políticas de acesso compartilhado**.
    - Crie uma nova chave ou escolha uma chave existente que tenha permissões de **envio** .
    - Copie a cadeia de conexão primária ou secundária. Use essa cadeia de conexão para configurar um novo destino no IoT Central.
    - Como alternativa, você pode gerar uma cadeia de conexão para todo o namespace de hubs de eventos:
        1. Vá para seu namespace de hubs de eventos na portal do Azure.
        2. Em **configurações**, selecione **políticas de acesso compartilhado**
        3. Crie uma nova chave ou escolha uma chave existente que tenha permissões de **envio** .
        4. Copie a cadeia de conexão primária ou secundária
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Criar um destino de fila ou de tópico do barramento de serviço

Se você não tiver um namespace do barramento de serviço existente para exportar para o, siga estas etapas:

1. Crie um [novo namespace do barramento de serviço no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Saiba mais na [documentação do Barramento de Serviço do Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).

1. Para criar uma fila ou um tópico para exportar para o, vá para o namespace do barramento de serviço e selecione **+ fila** ou **+ tópico**.

1. Gere uma chave a ser usada quando você configurar sua exportação de dados no IoT Central:

    - Selecione a fila ou o tópico que você criou.
    - Selecione **configurações/políticas de acesso compartilhado**.
    - Crie uma nova chave ou escolha uma chave existente que tenha permissões de **envio** .
    - Copie a cadeia de conexão primária ou secundária. Use essa cadeia de conexão para configurar um novo destino no IoT Central.
    - Como alternativa, você pode gerar uma cadeia de conexão para todo o namespace do barramento de serviço:
        1. Vá para o namespace do barramento de serviço na portal do Azure.
        2. Em **configurações**, selecione **políticas de acesso compartilhado**
        3. Crie uma nova chave ou escolha uma chave existente que tenha permissões de **envio** .
        4. Copie a cadeia de conexão primária ou secundária

### <a name="create-an-azure-blob-storage-destination"></a>Criar um destino de armazenamento de BLOBs do Azure

Se você não tiver uma conta de armazenamento do Azure existente para a qual exportar, siga estas etapas:

1. Crie uma [conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Você pode saber mais sobre como criar novas [contas de armazenamento de BLOBs do Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) ou [contas de armazenamento Azure data Lake Storage v2](../../storage/common/storage-account-create.md). A exportação de dados só pode gravar dados em contas de armazenamento que dão suporte a blobs de blocos. A lista a seguir mostra os tipos de conta de armazenamento compatíveis conhecidos:

    |Nível de desempenho|Tipo de Conta|
    |-|-|
    |Standard|Uso Geral v2|
    |Standard|Uso Geral v1|
    |Standard|Armazenamento de Blobs|
    |Premium|Armazenamento de blobs de blocos|

1. Para criar um contêiner em sua conta de armazenamento, vá para sua conta de armazenamento. Em **Serviço Blob**, selecione **Procurar Blobs**. Selecione **+ Contêiner** na parte superior para criar um contêiner.

1. Gere uma cadeia de conexão para sua conta de armazenamento acessando **configurações > chaves de acesso**. Copie uma das duas cadeias de conexão.

### <a name="create-a-webhook-endpoint"></a>Criar um ponto de extremidade de webhook

Você pode exportar dados para um ponto de extremidade de webhook HTTP disponível publicamente. Você pode criar um ponto de extremidade de webhook de teste usando [RequestBin](https://requestbin.net/). A solicitação de restrições RequestBin quando o limite de solicitação é atingido:

1. Abra [RequestBin](https://requestbin.net/).
2. Crie um novo RequestBin e copie a **URL do Compartimento**. Você usa essa URL ao testar a exportação de dados.

## <a name="set-up-data-export"></a>Configurar a exportação de dados

Agora que você tem um destino para exportar seus dados, configure a exportação de dados em seu aplicativo IoT Central:

1. Entre no aplicativo IoT Central.

1. No painel esquerdo, selecione **exportação de dados**.

    > [!Tip]
    > Se você não vir a **exportação de dados** no painel esquerdo, você não tem permissões para configurar a exportação de dados em seu aplicativo. Contate o administrador para configurar a exportação de dados.

1. Selecione **+ nova exportação**.

1. Insira um nome de exibição para a nova exportação e verifique se a exportação de dados está **habilitada**.

1. Escolha o tipo de dados a ser exportado. A tabela a seguir lista os tipos de exportação de dados com suporte:

    | Tipo de dados | Descrição | Formato de dados |
    | :------------- | :---------- | :----------- |
    |  Telemetria | Exporte mensagens de telemetria de dispositivos quase em tempo real. Cada mensagem exportada contém o conteúdo completo da mensagem do dispositivo original, normalizado.   |  [Formato de mensagem de telemetria](#telemetry-format)   |
    | Alterações de propriedade | Exporte alterações para propriedades de dispositivo e de nuvem quase em tempo real. Para propriedades de dispositivo somente leitura, as alterações nos valores relatados são exportadas. Para propriedades de leitura/gravação, os valores relatados e desejados são exportados. | [Formato de mensagem de alteração de propriedade](#property-changes-format) |

<a name="DataExportFilters"></a>
1. Opcionalmente, adicione filtros para reduzir a quantidade de dados exportados. Há diferentes tipos de filtro disponíveis para cada tipo de exportação de dados:

    Para filtrar a telemetria, você pode:

    - **Filtre** o fluxo exportado para conter apenas telemetria de dispositivos que correspondam ao nome do dispositivo, ID do dispositivo e condição de filtro do modelo do dispositivo.
    - **Filtrar** recursos: se você escolher um item de telemetria na lista suspensa **nome** , o fluxo exportado conterá apenas telemetria que atenda à condição de filtro. Se você escolher um item de propriedade de dispositivo ou de nuvem na lista suspensa **nome** , o fluxo exportado conterá apenas a telemetria de dispositivos com propriedades correspondentes à condição de filtro.
    - **Filtro de propriedade de mensagem**: os dispositivos que usam os SDKs de dispositivo podem enviar *Propriedades de mensagem* ou *Propriedades de aplicativo* em cada mensagem de telemetria. As propriedades são um recipiente de pares chave-valor que marcam a mensagem com identificadores personalizados. Para criar um filtro de propriedade de mensagem, insira a chave de propriedade de mensagem que você está procurando e especifique uma condição. Somente mensagens de telemetria com propriedades que correspondem à condição de filtro especificada são exportadas. Os seguintes operadores de comparação de cadeia de caracteres têm suporte: Equals, não é igual a, Contains, não contém, existe, não existe. [Saiba mais sobre as propriedades do aplicativo nos documentos do Hub IOT](../../iot-hub/iot-hub-devguide-messages-construct.md).

    Para filtrar as alterações de propriedade, use um **filtro de recurso**. Escolha um item de propriedade na lista suspensa. O fluxo exportado contém apenas alterações à propriedade selecionada que atende à condição de filtro.

<a name="DataExportEnrichmnents"></a>
1. Opcionalmente, enriquecer mensagens exportadas com metadados adicionais de pares chave-valor. Os aprimoramentos a seguir estão disponíveis para os tipos de exportação de dados de alterações de telemetria e Propriedade:

    - **Cadeia de caracteres personalizada**: adiciona uma cadeia de caracteres estática personalizada a cada mensagem. Insira qualquer chave e insira qualquer valor de cadeia de caracteres.
    - **Propriedade**: adiciona a propriedade relatada do dispositivo atual ou o valor da propriedade de nuvem a cada mensagem. Insira qualquer chave e escolha um dispositivo ou uma propriedade de nuvem. Se a mensagem exportada for de um dispositivo que não tem a propriedade especificada, a mensagem exportada não obterá o enriquecimento.

1. Adicione um novo destino ou adicione um destino que você já criou. Selecione o link **criar um novo** e adicione as seguintes informações:

    - **Nome de destino**: o nome de exibição do destino em IOT central.
    - **Tipo de destino**: escolha o tipo de destino. Se você ainda não configurou o destino, consulte [Configurar destino de exportação](#set-up-export-destination).
    - Para os hubs de eventos do Azure, a fila ou o tópico do barramento de serviço do Azure, Cole a cadeia de conexão para o recurso e insira o Hub de eventos, a fila ou o nome do tópico que diferencia maiúsculas de minúsculas, se necessário.
    - Para o armazenamento de BLOBs do Azure, Cole a cadeia de conexão para o recurso e insira o nome do contêiner que diferencia maiúsculas de minúsculas, se necessário.
    - Para webhook, Cole a URL de retorno de chamada para o ponto de extremidade do webhook. Opcionalmente, você pode configurar a autorização de webhook (OAuth 2,0 e o token de autorização) e adicionar cabeçalhos personalizados. 
        - Para o OAuth 2,0, há suporte apenas para o fluxo de credenciais do cliente. Quando o destino for salvo, IoT Central se comunicará com seu provedor OAuth para recuperar um token de autorização. Esse token será anexado ao cabeçalho "Authorization" para cada mensagem enviada para esse destino.
        - Para o token de autorização, você pode especificar um valor de token que será anexado diretamente ao cabeçalho "Authorization" para cada mensagem enviada para esse destino.
    - Selecione **Criar**.

1. Selecione **+ destino** e escolha um destino na lista suspensa. Você pode adicionar até cinco destinos a uma única exportação.

1. Quando você terminar de configurar sua exportação, selecione **salvar**. Após alguns minutos, seus dados aparecerão em seus destinos.

## <a name="destinations"></a>Destinos

### <a name="azure-blob-storage-destination"></a>Destino do armazenamento de BLOBs do Azure

Os dados são exportados uma vez por minuto, com cada arquivo que contém o lote de alterações desde a exportação anterior. Os dados exportados são salvos no formato JSON. Os caminhos padrão para os dados exportados em sua conta de armazenamento são:

- Telemetria: _{container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Alterações de propriedade: _{container}/{app-id}/{partition_id}/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Para procurar os arquivos exportados no portal do Azure, navegue até o arquivo e selecione **Editar blob**.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Hubs de eventos do Azure e destinos do barramento de serviço do Azure

Os dados são exportados quase em tempo real. Os dados estão no corpo da mensagem e estão no formato JSON codificado como UTF-8.

As anotações ou o recipiente de propriedades do sistema da mensagem contém `iotcentral-device-id` os `iotcentral-application-id` campos,, e `iotcentral-message-source` `iotcentral-message-type` que têm os mesmos valores dos campos correspondentes no corpo da mensagem.

### <a name="webhook-destination"></a>Destino do webhook

Para destinos de WebHooks, os dados também são exportados quase em tempo real. Os dados no corpo da mensagem estão no mesmo formato que para os hubs de eventos e o barramento de serviço.

## <a name="telemetry-format"></a>Formato de telemetria

Cada mensagem exportada contém uma forma normalizada da mensagem completa que o dispositivo enviou no corpo da mensagem. A mensagem está no formato JSON e codificada como UTF-8. As informações em cada mensagem incluem:

- `applicationId`: A ID do aplicativo IoT Central.
- `messageSource`: A origem da mensagem- `telemetry` .
- `deviceId`: A ID do dispositivo que enviou a mensagem de telemetria.
- `schema`: O nome e a versão do esquema de carga.
- `templateId`: A ID do modelo de dispositivo associado ao dispositivo.
- `enrichments`: Quaisquer aprimoramentos configurados na exportação.
- `messageProperties`: Propriedades adicionais que o dispositivo enviou com a mensagem. Essas propriedades são, às vezes, chamadas de *Propriedades do aplicativo*. [Saiba mais nos documentos do Hub IOT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Para os hubs de eventos e o barramento de serviço, IoT Central exporta uma nova mensagem rapidamente depois de receber a mensagem de um dispositivo. Nas propriedades do usuário (também chamadas de propriedades do aplicativo) de cada mensagem, o `iotcentral-device-id` , o `iotcentral-application-id` e o `iotcentral-message-source` são incluídos automaticamente.

Para o armazenamento de BLOBs, as mensagens são agrupadas em lote e exportadas uma vez por minuto.

O exemplo a seguir mostra uma mensagem de telemetria exportada:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

### <a name="message-properties"></a>Propriedades da mensagem

As mensagens de telemetria têm propriedades para metadados além da carga de telemetria. O trecho anterior mostra exemplos de mensagens do sistema, como `deviceId` e `enqueuedTime` . Para saber mais sobre as propriedades de mensagem do sistema, consulte [Propriedades do sistema de mensagens do Hub IOT do D2C](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages).

Você pode adicionar propriedades a mensagens de telemetria se precisar adicionar metadados personalizados às suas mensagens de telemetria. Por exemplo, você precisa adicionar um carimbo de data/hora quando o dispositivo cria a mensagem.

O trecho de código a seguir mostra como adicionar a `iothub-creation-time-utc` Propriedade à mensagem ao criá-la no dispositivo:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

O trecho a seguir mostra essa propriedade na mensagem exportada para o armazenamento de BLOBs:

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>Formato de alterações de propriedade

Cada mensagem ou registro representa uma alteração em um dispositivo ou uma propriedade de nuvem. Para as propriedades do dispositivo, somente as alterações no valor relatado são exportadas como uma mensagem separada. As informações na mensagem exportada incluem:

- `applicationId`: A ID do aplicativo IoT Central.
- `messageSource`: A origem da mensagem- `properties` .
- `messageType`: `cloudPropertyChange` Ou, `devicePropertyDesiredChange` ou `devicePropertyReportedChange` .
- `deviceId`: A ID do dispositivo que enviou a mensagem de telemetria.
- `schema`: O nome e a versão do esquema de carga.
- `templateId`: A ID do modelo de dispositivo associado ao dispositivo.
- `enrichments`: Quaisquer aprimoramentos configurados na exportação.

Para hubs de eventos e barramento de serviço, IoT Central exporta dados de novas mensagens para o Hub de eventos ou a fila ou tópico do barramento de serviço quase em tempo real. Nas propriedades do usuário (também conhecidas como propriedades do aplicativo) de cada mensagem, o `iotcentral-device-id` , `iotcentral-application-id` , `iotcentral-message-source` e `iotcentral-message-type` são incluídos automaticamente.

Para o armazenamento de BLOBs, as mensagens são agrupadas em lote e exportadas uma vez por minuto.

O exemplo a seguir mostra uma mensagem de alteração de propriedade exportada recebida no armazenamento de BLOBs do Azure.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>Comparação de exportação de dados herdados e exportação de dados

A tabela a seguir mostra as diferenças entre a [exportação de dados herdados](howto-export-data-legacy.md) e os novos recursos de exportação de dados:

| Funcionalidades  | Exportação de dados herdados | Nova exportação de dados |
| :------------- | :---------- | :----------- |
| Tipos de dados disponíveis | Telemetria, dispositivos, modelos de dispositivo | Telemetria, alterações de propriedade |
| Filtragem | Nenhum | Depende do tipo de dados exportado. Para telemetria, filtragem por telemetria, propriedades de mensagem, valores de propriedade |
| Aprimoramentos | Nenhum | Enriquecer com uma cadeia de caracteres personalizada ou um valor de propriedade no dispositivo |
| Destinos | Hubs de eventos do Azure, filas e tópicos do barramento de serviço do Azure, armazenamento de BLOBs do Azure | O mesmo que para exportação de dados herdados mais WebHooks|
| Versões do aplicativo com suporte | V2, V3 | Somente V3 |
| Limites notáveis | 5 exportações por aplicativo, 1 destino por exportação | 10 exportações-conexões de destino por aplicativo |

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como usar a nova exportação de dados, uma próxima etapa sugerida é aprender a [usar a análise no IOT central](./howto-create-analytics.md)
