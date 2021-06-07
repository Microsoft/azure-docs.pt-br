---
title: Exportar dados do Azure IoT Central | Microsoft Docs
description: Como usar a nova exportação de dados para exportar dados de IoT para o Azure e destinos de nuvem personalizados.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/24/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: 7d57f24f8cb4b59ce9b9cd5853be11fb2d104d75
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277888"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>Exportar dados de IoT para destinos de nuvem usando a exportação de dados

> [!Note]
> Este artigo descreve os recursos de exportação de dados no IoT Central. Para obter informações sobre os recursos de exportação de dados herdados, confira [Exportar dados de IoT para destinos de nuvem usando a exportação de dados (herdado)](./howto-export-data-legacy.md).

Este artigo descreve como usar o novo recurso de exportação de dados do Azure IoT Central. Use esse recurso para exportar continuamente dados de IoT filtrados e aprimorados do aplicativo IoT Central. A exportação de dados envia as alterações quase em tempo real para outras partes da solução de nuvem para percepções, análises e armazenamento de caminho quente.

Por exemplo, você pode:

- Exportar continuamente dados de telemetria, de alterações de propriedade, de ciclo de vida de dispositivos e de ciclo de vida de modelos de dispositivos no formato JSON quase em tempo real.
- Filtrar os fluxos de dados para exportar dados que correspondam às condições personalizadas.
- Enriquecer os fluxos de dados com valores personalizados e valores de propriedade do dispositivo.
- Enviar os dados para destinos como Hubs de Eventos do Azure, Barramento de Serviço do Azure, Armazenamento de Blobs do Azure e pontos de extremidade de webhook.

> [!Tip]
> Ao ativar a exportação de dados, você obtém apenas os dados desse momento em diante. Atualmente, não é possível recuperar os dados do período em que a exportação estava desativada. Para reter dados mais históricos, ative a exportação de dados no início.

## <a name="prerequisites"></a>Pré-requisitos

Para usar os recursos de exportação de dados, você deve ter um [aplicativo v3](howto-get-app-info.md)e a permissão [Exportação de dados](howto-manage-users-roles.md).

Se você tem um aplicativo v2, confira [Migrar aplicativos do IoT Central v2 para v3](howto-migrate.md).

## <a name="set-up-export-destination"></a>Configurar o destino de exportação

O destino de exportação deve existir antes de você configurar a exportação de dados. Os seguintes tipos de destino estão disponíveis no momento:

- Hubs de eventos do Azure
- Fila do Barramento de Serviço do Azure
- Tópico do Barramento de Serviço do Azure
- Armazenamento do Blobs do Azure
- webhook

### <a name="create-an-event-hubs-destination"></a>Criar um destino dos Hubs de Eventos

Se você não tem um namespace dos Hubs de Eventos para o qual exportar, siga estas etapas:

1. Crie um [namespace dos Hubs de Eventos no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Saiba mais na [documentação dos Hubs de Eventos do Azure](../../event-hubs/event-hubs-create.md).

1. Crie um hub de eventos em seu namespace dos Hubs de Eventos. Acesse o namespace e selecione **+ Hub de Eventos** na parte superior para criar uma instância de hub de eventos.

1. Gere uma chave a ser usada quando você configurar a exportação de dados no IoT Central:

    - Selecione a instância do hub de eventos que você criou.
    - Selecione **Configurações > Políticas de acesso compartilhado**.
    - Crie ou escolha uma chave que tenha permissões de **envio**.
    - Copie a cadeia de conexão primária ou secundária. Use essa cadeia de conexão para configurar um novo destino no IoT Central.
    - Outra opção é gerar uma cadeia de conexão para todo o namespace dos Hubs de Eventos:
        1. Acesse seu namespace dos Hubs de Eventos no portal do Azure.
        2. Em **Configurações**, selecione **Políticas de Acesso Compartilhado**
        3. Crie ou escolha uma chave que tenha permissões de **envio**.
        4. Copie a cadeia de conexão primária ou secundária
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Criar uma fila do Barramento de Serviço ou um destino de tópico

Se você não tem um namespace do Barramento de Serviço para o qual exportar, siga estas etapas:

1. Crie um [namespace do Barramento de Serviço no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Saiba mais na [documentação do Barramento de Serviço do Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).

1. Para criar uma fila ou um tópico para exportação, acesse seu namespace do Barramento de Serviço e selecione **+ Fila** ou **+ Tópico**.

1. Gere uma chave a ser usada quando você configurar a exportação de dados no IoT Central:

    - Selecione a fila ou o tópico você criou.
    - Selecione **Configurações/Políticas de acesso compartilhado**.
    - Crie ou escolha uma chave que tenha permissões de **envio**.
    - Copie a cadeia de conexão primária ou secundária. Use essa cadeia de conexão para configurar um novo destino no IoT Central.
    - Outra opção é gerar uma cadeia de conexão para todo o namespace do Barramento de Serviço:
        1. Acesse seu namespace do Barramento de Serviço no portal do Azure.
        2. Em **Configurações**, selecione **Políticas de Acesso Compartilhado**
        3. Crie ou escolha uma chave que tenha permissões de **envio**.
        4. Copie a cadeia de conexão primária ou secundária

### <a name="create-an-azure-blob-storage-destination"></a>Criar um destino do Armazenamento de Blobs do Azure

Se você não tem uma conta do Armazenamento do Azure para a qual exportar, siga estas etapas:

1. Crie uma [conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Você pode saber mais sobre a criação de [contas do Armazenamento de Blobs do Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) ou de [contas de armazenamento do Azure Data Lake Storage v2](../../storage/common/storage-account-create.md). A exportação de dados só pode gravar dados em contas de armazenamento que dão suporte para blobs de blocos. A seguinte lista mostra os tipos de conta de armazenamento compatíveis conhecidos:

    |Nível de desempenho|Tipo de Conta|
    |-|-|
    |Standard|Uso Geral V2|
    |Standard|Uso Geral V1|
    |Standard|Armazenamento de blobs|
    |Premium|Armazenamento de Blobs de blocos|

1. Para criar um contêiner em sua conta de armazenamento, acesse a conta. Em **Serviço Blob**, selecione **Procurar Blobs**. Selecione **+ Contêiner** na parte superior para criar um contêiner.

1. Para gerar uma cadeia de conexão para sua conta de armazenamento, acesse **Configurações > Chaves de acesso**. Copie uma das duas cadeias de conexão.

### <a name="create-a-webhook-endpoint"></a>Criar um ponto de extremidade do webhook

Você pode exportar dados para um ponto de extremidade de webhook HTTP público. Você pode criar um ponto de extremidade de webhook de teste usando [RequestBin](https://requestbin.net/). O RequestBin limita as solicitações quando o limite delas é atingido:

1. Abra [RequestBin](https://requestbin.net/).
2. Crie um novo RequestBin e copie a **URL do Compartimento**. Você usa essa URL ao testar a exportação de dados.

## <a name="set-up-data-export"></a>Configurar a exportação de dados

Agora que você tem um destino para o qual exportar os dados, configure a exportação em seu aplicativo do IoT Central:

1. Entre no aplicativo IoT Central.

1. No painel esquerdo, selecione **Exportação de dados**.

    > [!Tip]
    > Caso a opção **Exportação de dados** não apareça no painel esquerdo, você não tem permissões para configurar a exportação de dados no aplicativo. Contate o administrador para configurar a exportação de dados.

1. Selecione **+ Nova exportação**.

1. Insira um nome de exibição para a nova exportação e verifique se a exportação de dados está **Habilitada**.

1. Escolha o tipo de dados a serem exportados. A seguinte tabela lista os tipos de exportação de dados compatíveis:

    | Tipo de dados | Descrição | Formato de dados |
    | :------------- | :---------- | :----------- |
    |  Telemetria | Exporte mensagens de telemetria de dispositivos quase em tempo real. Cada mensagem exportada contém o conteúdo completo da mensagem do dispositivo original, normalizado.   |  [Formato de mensagem de telemetria](#telemetry-format)   |
    | Alterações de propriedade | Exporte as alterações em propriedades de dispositivo e de nuvem quase em tempo real. No caso de propriedades de dispositivo somente leitura, são exportadas as alterações nos valores relatados. No caso de propriedades de leitura/gravação, são exportados tanto o valor relatado quanto o desejado. | [Formato de mensagem de alteração de propriedade](#property-changes-format) |
    | Ciclo de vida do dispositivo | Exportar eventos de registro e de exclusão de dispositivos. | [Formato de mensagem de alterações de ciclo de vida de dispositivo](#device-lifecycle-changes-format) |
    | Ciclo de vida de modelos de dispositivos | Exportar alterações em modelos de dispositivos publicadas, incluindo criação, atualização e exclusão. | [Formato de mensagem de alterações de ciclo de vida de modelo de dispositivo](#device-template-lifecycle-changes-format) | 

1. Opcionalmente, adicione filtros para reduzir a quantidade de dados exportados. Há diferentes tipos de filtro disponíveis para cada tipo de exportação de dados: <a name="DataExportFilters"></a>
    
    | Tipo de dados | Filtros disponíveis| 
    |--------------|------------------|
    |Telemetria|<ul><li>Filtrar por nome do dispositivo, ID do dispositivo e modelo do dispositivo</li><li>Filtrar o fluxo para conter apenas telemetria que atenda às condições de filtro</li><li>Filtrar o fluxo para conter somente telemetria de dispositivos com propriedades que correspondem às condições de filtro</li><li>Filtre o fluxo para conter somente a telemetria com *propriedades de mensagem* que correspondem às condições de filtro. *As propriedades de mensagem* (também conhecidas como *propriedades do aplicativo*) são enviadas em um recipiente de pares chave-valor em cada mensagem de telemetria, opcionalmente enviada por dispositivos que usam os SDKs do dispositivo. Para criar um filtro de propriedade de mensagem, insira a chave de propriedade de mensagem que você está procurando e especifique uma condição. Somente as mensagens de telemetria com propriedades que correspondem à condição de filtro especificada são exportadas. [Saiba mais sobre as propriedades do aplicativo nos documentos do Hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md) </li></ul>|
    |Alterações de propriedade|<ul><li>Filtrar por nome do dispositivo, ID do dispositivo e modelo do dispositivo</li><li>Filtrar o fluxo para conter apenas alterações de propriedade que atendam às condições de filtro</li></ul>|
    |Ciclo de vida do dispositivo|<ul><li>Filtrar por nome do dispositivo, ID do dispositivo e modelo do dispositivo</li><li>Filtrar o fluxo para conter somente alterações de dispositivos com propriedades que correspondem às condições de filtro</li></ul>|
    |Ciclo de vida de modelos de dispositivos|<ul><li>Filtrar por modelo de dispositivo</li></ul>|
    
1. Opcionalmente, enriqueça as mensagens exportadas com mais metadados de pares chave-valor. Os seguintes aprimoramentos estão disponíveis para os tipos de exportação de dados de telemetria e de alterações de propriedades: <a name="DataExportEnrichmnents"></a>
    - **Cadeia de caracteres personalizada**: adiciona uma cadeia de caracteres estática personalizada a cada mensagem. Insira qualquer chave e qualquer valor de cadeia de caracteres.
    - **Propriedade**: adiciona o valor atual da propriedade relatada do dispositivo ou da propriedade de nuvem a cada mensagem. Insira qualquer chave e escolha uma propriedade de dispositivo ou de nuvem. Se a mensagem exportada for de um dispositivo que não tem a propriedade especificada, ela não obterá o enriquecimento.

1. Adicione um destino novo ou um destino que você já criou. Selecione o link **Criar um novo** e adicione as seguintes informações:

    - **Nome de destino**: o nome de exibição do destino no IoT Central.
    - **Tipo de destino**: escolha o tipo de destino. Se você ainda não configurou o destino, veja [Configurar destino de exportação](#set-up-export-destination).
    - No caso dos Hubs de Eventos do Azure, fila ou tópico do Barramento de Serviço do Azure, cole a cadeia de conexão do recurso e insira o nome do hub de eventos, da fila ou do tópico, diferenciando maiúsculas de minúsculas.
    - No caso do Armazenamento de Blobs do Azure, cole a cadeia de conexão do recurso e insira o nome do contêiner, se necessário, diferenciando maiúsculas de minúsculas.
    - No caso de webhooks, cole a URL de retorno de chamada do ponto de extremidade do webhook. Opcionalmente, você pode configurar a autorização de webhook (OAuth 2.0 e o token de autorização) e adicionar cabeçalhos personalizados. 
        - Para o OAuth 2,0, há suporte apenas para o fluxo de credenciais do cliente. Quando o destino é salvo, o IoT Central entra em contato com o provedor de OAuth para recuperar um token de autorização. O token é anexado ao cabeçalho "Authorization" de cada mensagem enviada para esse destino.
        - Para o token de autorização, você pode especificar um valor de token que será anexado diretamente ao cabeçalho "Authorization" de cada mensagem enviada para esse destino.
    - Selecione **Criar**.

1. Selecione **+ Destino** e escolha um destino na lista suspensa. Você pode adicionar até cinco destinos a uma exportação.

1. Quando terminar de configurar a exportação, selecione **Salvar**. Depois de alguns minutos, os dados aparecem nos destinos.

## <a name="monitor-your-export"></a>Monitorar a exportação

Além de ver o status das exportações no IoT Central, você pode usar o [Azure Monitor](../../azure-monitor/overview.md) para ver a quantidade de dados exportados e eventuais erros de exportação. Você pode acessar as métricas de integridade de dispositivo e de exportação em gráficos no portal do Azure, com uma API REST ou com consultas no PowerShell ou na CLI do Azure. No momento, você pode monitorar as seguintes métricas de exportação de dados no Azure Monitor:

- O número de mensagens recebidas para exportação antes da aplicação dos filtros.
- O número de mensagens que passam pelos filtros.
- O número de mensagens exportadas para os destinos.
- Número de erros encontrados.

Para saber mais, confira [Monitorar a integridade geral de um aplicativo do IoT Central](howto-monitor-application-health.md).

## <a name="destinations"></a>Destinos

### <a name="azure-blob-storage-destination"></a>Destino do Armazenamento de Blobs do Azure

Os dados são exportados uma vez por minuto, com cada arquivo contendo o lote de alterações desde a exportação anterior. Os dados exportados são salvos no formato JSON. Os caminhos padrão dos dados exportados em sua conta de armazenamento são:

- Telemetria: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Alterações de propriedades: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Para procurar os arquivos exportados no portal do Azure, navegue até o arquivo e selecione **Editar blob**.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Destinos dos Hubs de Eventos do Azure e do Barramento de Serviço do Azure

Os dados são exportados quase em tempo real. Os dados estão no corpo da mensagem, no formato JSON codificado como UTF-8.

As anotações ou recipiente de propriedades do sistema da mensagem contém os campos `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` e `iotcentral-message-type` que têm os mesmos valores dos campos correspondentes no corpo da mensagem.

### <a name="webhook-destination"></a>Destino do webhook

Para os destinos de webhooks, os dados também são exportados quase em tempo real. Os dados no corpo da mensagem estão no mesmo formato dos Hubs de Eventos e do Barramento de Serviço.

## <a name="telemetry-format"></a>Formato da telemetria

Cada mensagem exportada contém uma forma normalizada da mensagem completa que o dispositivo enviou no corpo. A mensagem está no formato JSON e codificada como UTF-8. As informações em cada mensagem incluem:

- `applicationId`: a ID do aplicativo do IoT Central.
- `messageSource`: a origem da mensagem – `telemetry`.
- `deviceId`: a ID do dispositivo que enviou a mensagem de telemetria.
- `schema`: o nome e a versão do esquema de conteúdo.
- `templateId`: a ID do modelo de dispositivo associado ao dispositivo.
- `enqueuedTime`: a hora em que a mensagem foi recebida pelo IoT Central.
- `enrichments`: os aprimoramentos configurados na exportação.
- `messageProperties`: propriedades adicionais que o dispositivo enviou com a mensagem. Essas propriedades também são chamadas de *propriedades do aplicativo*. [Saiba mais na documentação do Hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Para os Hubs de Eventos e o Barramento de Serviço, o IoT Central exporta as mensagens novas rapidamente depois de recebê-las do dispositivo. Nas propriedades do usuário (também chamadas de propriedades do aplicativo) de cada mensagem, o `iotcentral-device-id`, o `iotcentral-application-id` e o `iotcentral-message-source` são incluídos automaticamente.

Para o Armazenamento de Blobs, as mensagens são agrupadas em lote e exportadas uma vez por minuto.

O seguinte exemplo mostra uma mensagem de telemetria exportada:

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

As mensagens de telemetria têm propriedades para metadados além do conteúdo. O snippet anterior mostra exemplos de mensagens do sistema, como `deviceId` e `enqueuedTime`. Para saber mais sobre as propriedades de mensagem do sistema, confira [Propriedades do sistema de mensagens do Hub IoT do D2C](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages).

Você pode adicionar propriedades às mensagens de telemetria para incluir metadados personalizados. Por exemplo, você precisa adicionar um carimbo de data/hora quando o dispositivo cria a mensagem.

O seguinte snippet de código mostra como adicionar a propriedade `iothub-creation-time-utc` à mensagem ao criá-la no dispositivo:

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

O seguinte snippet mostra essa propriedade na mensagem exportada para o Armazenamento de Blobs:

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

## <a name="property-changes-format"></a>Formato das alterações de propriedades

Cada mensagem ou registro representa uma alteração em uma propriedade de dispositivo ou de nuvem. Para as propriedades de dispositivo, somente as alterações no valor relatado são exportadas em uma mensagem separada. As informações na mensagem exportada incluem:

- `applicationId`: a ID do aplicativo do IoT Central.
- `messageSource`: a origem da mensagem – `properties`.
- `messageType`: `cloudPropertyChange`, `devicePropertyDesiredChange` ou `devicePropertyReportedChange`.
- `deviceId`: a ID do dispositivo que enviou a mensagem de telemetria.
- `schema`: o nome e a versão do esquema de conteúdo.
- `enqueuedTime`: a hora em que a alteração foi detectada pelo IoT Central.
- `templateId`: a ID do modelo de dispositivo associado ao dispositivo.
- `enrichments`: os aprimoramentos configurados na exportação.

Para Hubs de Eventos e Barramento de Serviço, o IoT Central exporta dados de novas mensagens para o Hub de Eventos ou a fila ou o tópico do Barramento de Serviço quase em tempo real. Nas propriedades do usuário (também chamadas de propriedades do aplicativo) de cada mensagem, `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` e `iotcentral-message-type` são incluídos automaticamente.

Para o Armazenamento de Blobs, as mensagens são agrupadas em lote e exportadas uma vez por minuto.

O exemplo a seguir mostra uma mensagem de alteração de propriedade exportada recebida no Armazenamento de Blobs do Azure.

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

## <a name="device-lifecycle-changes-format"></a>Formato das alterações de ciclo de vida de dispositivo

Cada mensagem ou registro representa uma alteração em um dispositivo. As informações na mensagem exportada incluem:

- `applicationId`: a ID do aplicativo do IoT Central.
- `messageSource`: a origem da mensagem – `deviceLifecycle`.
- `messageType`: `registered` ou `deleted`.
- `deviceId`: a ID do dispositivo que foi alterado.
- `schema`: o nome e a versão do esquema de conteúdo.
- `templateId`: a ID do modelo de dispositivo associado ao dispositivo.
- `enqueuedTime`: a hora em que a alteração ocorreu no IoT Central.
- `enrichments`: os aprimoramentos configurados na exportação.

Para Hubs de Eventos e Barramento de Serviço, o IoT Central exporta dados de novas mensagens para o Hub de Eventos ou a fila ou o tópico do Barramento de Serviço quase em tempo real. Nas propriedades do usuário (também chamadas de propriedades do aplicativo) de cada mensagem, `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` e `iotcentral-message-type` são incluídos automaticamente.

Para o Armazenamento de Blobs, as mensagens são agrupadas em lote e exportadas uma vez por minuto.

O exemplo a seguir mostra uma mensagem de ciclo de vida de dispositivo exportada recebida no Armazenamento de Blobs do Azure.

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceLifecycle",
  "messageType": "registered",
  "deviceId": "1vzb5ghlsg1",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```
## <a name="device-template-lifecycle-changes-format"></a>Formato de alterações de ciclo de vida de modelo de dispositivo

Cada mensagem ou registro representa uma alteração em um modelo de dispositivo publicado. As informações na mensagem exportada incluem:

- `applicationId`: a ID do aplicativo do IoT Central.
- `messageSource`: a origem da mensagem – `deviceTemplateLifecycle`.
- `messageType`: `created`, `updated` ou `deleted`.
- `schema`: o nome e a versão do esquema de conteúdo.
- `templateId`: a ID do modelo de dispositivo associado ao dispositivo.
- `enqueuedTime`: a hora em que a alteração ocorreu no IoT Central.
- `enrichments`: os aprimoramentos configurados na exportação.

Para Hubs de Eventos e Barramento de Serviço, o IoT Central exporta dados de novas mensagens para o Hub de Eventos ou a fila ou o tópico do Barramento de Serviço quase em tempo real. Nas propriedades do usuário (também chamadas de propriedades do aplicativo) de cada mensagem, `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` e `iotcentral-message-type` são incluídos automaticamente.

Para o Armazenamento de Blobs, as mensagens são agrupadas em lote e exportadas uma vez por minuto.

O exemplo a seguir mostra uma mensagem de ciclo de vida de dispositivo exportada recebida no Armazenamento de Blobs do Azure.

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceTemplateLifecycle",
  "messageType": "created",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>Comparação entre a exportação de dados herdada e a atual

A seguinte tabela mostra as diferenças entre a [exportação de dados herdada](howto-export-data-legacy.md) e os novos recursos da exportação de dados:

| Funcionalidades  | Exportação de dados herdada | Nova exportação de dados |
| :------------- | :---------- | :----------- |
| Tipos de dados disponíveis | Telemetria, dispositivos, modelos de dispositivo | Telemetria, alterações de propriedade, alterações de ciclo de vida de dispositivo, alterações de ciclo de vida de modelo de dispositivo |
| Filtragem | Nenhum | Depende do tipo de dados exportado. Para telemetria, filtragem por telemetria, propriedades de mensagem, valores de propriedade |
| Enriquecimentos | Nenhum | Enriquecer com uma cadeia de caracteres personalizada ou um valor de propriedade no dispositivo |
| Destinos | Hubs de Eventos do Azure, filas e tópicos do Barramento de Serviço do Azure, Armazenamento de Blobs do Azure | O mesmo que para exportação de dados herdados mais webhooks|
| Versões do aplicativo com suporte | V2, V3 | Somente V3 |
| Limites importantes | Cinco exportações por aplicativo, um destino por exportação | 10 exportações-conexões de destino por aplicativo |

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe usar a nova exportação de dados, a próxima etapa é aprender a [usar a análise no IoT Central](./howto-create-analytics.md)
