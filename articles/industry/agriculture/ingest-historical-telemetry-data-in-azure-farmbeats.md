---
title: Ingerir dados telemétricos históricos
description: Este artigo descreve como ingerir dados de telemetria históricos.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.custom: has-adal-ref
ms.openlocfilehash: 603f14d2076b5b74dde0b92a732f8fe816f6dd10
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656777"
---
# <a name="ingest-historical-telemetry-data"></a>Ingerir dados telemétricos históricos

Este artigo descreve como ingerir dados de sensor históricos no Azure FarmBeats.

A ingestão de dados históricos de recursos de Internet das Coisas (IoT), como dispositivos e sensores, é um cenário comum no FarmBeats. Você cria metadados para dispositivos e sensores e, em seguida, ingeri os dados históricos para FarmBeats em um formato canônico.

## <a name="before-you-begin"></a>Antes de começar

Antes de prosseguir com este artigo, verifique se você instalou o FarmBeats e coletou os dados históricos de seus dispositivos IoT. Você também precisa habilitar o acesso do parceiro, conforme mencionado nas etapas a seguir.

## <a name="enable-partner-access"></a>Habilitar o acesso do parceiro

Você precisa habilitar a integração de parceiros à instância do FarmBeats do Azure. Esta etapa cria um cliente que tem acesso à instância do Azure FarmBeats como seu parceiro de dispositivo e fornece os seguintes valores que são necessários nas etapas subsequentes:

- Ponto de extremidade de API: essa é a URL Datahub, por exemplo, https:// \<datahub> . azurewebsites.net
- ID do locatário
- ID do Cliente
- Segredo do cliente
- Cadeia de conexão do EventHub

Siga estas etapas:

> [!NOTE]
> Você deve ser um administrador para executar as etapas a seguir.

1. Entrar no https://portal.azure.com/.

2. **Se você estiver no FarmBeats versão 1.2.7 ou posterior, pule as etapas a, b e c e vá para a etapa 3.** Você pode verificar a versão do FarmBeats selecionando o ícone de **configurações** no canto superior direito da interface do usuário do FarmBeats.

      a.  Vá para **Azure Active Directory**  >  **registros de aplicativo**

      b. Selecione o **registro do aplicativo** que foi criado como parte de sua implantação do FarmBeats. Ele terá o mesmo nome que o FarmBeats datahub.

      c. Selecione **expor uma API** > selecione **Adicionar um aplicativo cliente** e insira **04B07795-8ddb-461A-bbee-02f9e1bf7b46** e marque **autorizar escopo**. Isso dará acesso ao CLI do Azure (Cloud Shell) para executar as etapas abaixo:

3. Abra o Azure Cloud Shell. Essa opção está disponível na barra de ferramentas no canto superior direito do portal do Azure.

    ![Barra de ferramentas portal do Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Verifique se o ambiente está definido como **PowerShell**. Por padrão, ele é definido como bash.

    ![Configuração da barra de ferramentas do PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Vá para o diretório base.

    ```azurepowershell-interactive
    cd
    ```

6. Execute o comando a seguir. Isso conecta uma conta autenticada a ser usada para solicitações do Azure AD

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. Execute o comando a seguir. Isso baixará um script em seu diretório base.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. Execute o seguinte script. O script solicita a ID do locatário, que pode ser obtida na   >  página de **visão geral** Azure Active Directory.

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

9. Siga as instruções na tela para capturar os valores para **o ponto de extremidade da API**, ID do **locatário**, **ID do cliente**, segredo do **cliente** e cadeia de **conexão do EventHub**.


## <a name="create-device-or-sensor-metadata"></a>Criar metadados do dispositivo ou do sensor

 Agora que você tem as credenciais necessárias, você pode definir o dispositivo e os sensores. Para fazer isso, crie os metadados chamando APIs FarmBeats. Certifique-se de chamar as APIs como o aplicativo cliente que você criou na seção acima.

 FarmBeats Datahub tem as seguintes APIs que permitem a criação e o gerenciamento de metadados do dispositivo ou do sensor.

 > [!NOTE]
 > Como um parceiro, você tem acesso apenas para ler, criar e atualizar os metadados; a **opção Excluir é restrita ao parceiro.**

- /**DeviceModel**: DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, que é um gateway ou um nó.
- /**Device**: Device corresponde a um dispositivo físico presente no farm.
- /**SensorModel**: SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, que é analógico ou digital, e a medição do sensor, como temperatura ambiente e pressão.
- /**Sensor**: Sensor corresponde a um sensor físico que registra valores. Um sensor geralmente está conectado a um dispositivo com uma ID de dispositivo.

| DeviceModel | Sugestões |
|--|--|
| Tipo (nó, gateway) | Tipo do dispositivo-nó ou gateway |
| Fabricante | Nome do fabricante |
| ProductCode | Código de produto do dispositivo ou nome ou número do modelo. Por exemplo, EnviroMonitor#6800. |
| Portas | Nome e tipo da porta, seja digital ou analógica. |
| Nome | Nome amigável para identificar o recurso. Por exemplo, o nome do modelo ou nome do produto. |
| Descrição | Fornece uma descrição significativa da configuração. |
| Propriedades | Propriedades adicionais do fabricante. |
| **Dispositivo** |  |
| DeviceModelId | ID do modelo de dispositivo associado. |
| HardwareId | ID exclusiva para o dispositivo, como o endereço MAC. |
| ReportingInterval | Intervalo de relatórios em segundos. |
| Location | Latitude (-90 a +90), longitude (-180 a 180) e elevação (em metros) do dispositivo. |
| ParentDeviceId | ID do dispositivo pai ao qual este dispositivo está conectado. Por exemplo, um nó que está conectado a um gateway. Um nó tem parentDeviceId como o gateway. |
| Nome | Um nome para identificar o recurso. Os parceiros de dispositivo devem enviar um nome consistente com o nome do dispositivo no lado do parceiro. Se o nome do dispositivo do parceiro for definido pelo usuário, o mesmo nome definido pelo usuário deverá ser propagado para FarmBeats. |
| Descrição | Fornece uma descrição significativa. |
| Propriedades | Propriedades adicionais do fabricante. |
| **SensorModel** |  |
| Tipo (analógico, digital) | O tipo de sensor, se ele é analógico ou digital. |
| Fabricante | O fabricante do sensor. |
| ProductCode | Código de produto ou nome ou número do modelo. Por exemplo, RS-CO2-N01. |
| SensorMeasures > Name | Nome da medida do sensor. Suporte somente para letras minúsculas. Para medidas de diferentes profundidades, especifique a profundidade. Por exemplo, soil_moisture_15cm. Esse nome deve ser consistente com os dados de telemetria. |
| SensorMeasures > DataType | Tipo de dados telemétricos. Atualmente, há suporte apenas para dois. |
| SensorMeasures > Type | Tipo de medida dos dados telemétricos do sensor. Os tipos definidos pelo sistema são AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, comprimento, LiquidLevel, nitrate, O2, PH, phosphate, PointInTime, Potassium, pressão, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, estado, tempo de duração, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Para adicionar mais, confira a API do /ExtendedType. |
| SensorMeasures > Unit | Unidade de dados telemétricos do sensor. As unidades definidas pelo sistema são nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, mercúrio, PSI, milímetro, centímetro, medidor, polegada, pés, milhar, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, grau, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, porcentagem, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litro, MilliLiter, segundos, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour para adicionar mais, consulte a API/ExtendedType. |
| SensorMeasures > AggregationType | Os valores podem ser nenhum, média, máximo, mínimo ou i. |
| Nome | Nome para identificar um recurso. Por exemplo, o nome do modelo ou nome do produto. |
| Descrição | Fornece uma descrição significativa da configuração. |
| Propriedades | Propriedades adicionais do fabricante. |
| **Sensor** |  |
| HardwareId | ID exclusiva do sensor definida pelo fabricante. |
| SensorModelId | ID do modelo de sensor associado. |
| Location | Latitude (-90 a +90), longitude (-180 a 180) e elevação (em metros) do sensor. |
| Porta > Nome | Nome e tipo da porta à qual o sensor está conectado no dispositivo. Isso precisa ter o mesmo nome definido no modelo do dispositivo. |
| DeviceID | ID do dispositivo ao qual o sensor está conectado. |
| Nome | Nome para identificar o recurso. Por exemplo, nome do sensor ou nome do produto e número do modelo ou código do produto. |
| Descrição | Fornece uma descrição significativa. |
| Propriedades | Propriedades adicionais do fabricante. |

Para obter mais informações sobre objetos, consulte [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Solicitação de API para criar metadados

Para fazer uma solicitação de API, você combina o método HTTP (POST), a URL para o serviço de API e o URI para um recurso para consultar, enviar dados para, criar ou excluir uma solicitação. Em seguida, você adiciona um ou mais cabeçalhos de solicitação HTTP. A URL para o serviço de API é o ponto de extremidade de API, ou seja, a URL Datahub (https:// \<yourdatahub> . azurewebsites.net).

### <a name="authentication"></a>Autenticação

FarmBeats Datahub usa a autenticação de portador, que precisa das seguintes credenciais que foram geradas na seção anterior:

- ID do Cliente
- Segredo do cliente
- ID do locatário

Usando essas credenciais, o chamador pode solicitar um token de acesso. O token deve ser enviado nas solicitações de API subsequentes, na seção de cabeçalho, da seguinte maneira:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

O código Python de exemplo a seguir fornece o token de acesso, que pode ser usado para chamadas de API subsequentes para FarmBeats: 

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```

**Cabeçalhos de solicitação HTTP**

Aqui estão os cabeçalhos de solicitação mais comuns que devem ser especificados quando você faz uma chamada à API para FarmBeats Datahub:

- **Tipo de conteúdo**: aplicativo/JSON
- **Autorização**: <de portador Access-Token>
- **Aceitar**: aplicativo/JSON

### <a name="input-payload-to-create-metadata"></a>Carga de entrada para criar metadados

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Dispositivo

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Sensor

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

A solicitação de exemplo a seguir cria um dispositivo. Esta solicitação tem um JSON de entrada como carga com o corpo da solicitação.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",
\"reportingInterval\": 900,  \"name\": \"Device123\",
\"description\": \"Test Device 123\"}" *
```

Veja abaixo um código de exemplo em Python. O token de acesso usado neste exemplo é o mesmo que é recebido durante a autenticação.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> As APIs retornam IDs exclusivas para cada instância criada. Você deve manter as IDs para enviar as mensagens de telemetria correspondentes.

### <a name="send-telemetry"></a>Enviar telemetria

Agora que você criou os dispositivos e sensores no FarmBeats, você pode enviar as mensagens de telemetria associadas.

### <a name="create-a-telemetry-client"></a>Criar um cliente de telemetria

Você deve enviar a telemetria para os hubs de eventos do Azure para processamento. Os Hubs de Eventos do Azure são um serviço que permite a ingestão de dados em tempo real (telemetria) a partir de dispositivos e aplicativos conectados. Para enviar dados de telemetria para o FarmBeats, crie um cliente que envie mensagens para um hub de eventos no FarmBeats. Para obter mais informações sobre como enviar telemetria, consulte [hubs de eventos do Azure](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

### <a name="send-a-telemetry-message-as-the-client"></a>Enviar uma mensagem de telemetria como o cliente

Depois de ter uma conexão estabelecida como um cliente de hubs de eventos, você pode enviar mensagens para o Hub de eventos como JSON.

Aqui está o código Python de exemplo que envia a telemetria como um cliente para um hub de eventos especificado:

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Converta o formato de dados do sensor histórico em um formato canônico que o FarmBeats do Azure entenda. O formato da mensagem canônica é o seguinte:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

Depois de adicionar os dispositivos e sensores correspondentes, obtenha a ID do dispositivo e a ID do sensor na mensagem de telemetria, conforme descrito na seção anterior.

Aqui está um exemplo de uma mensagem de telemetria:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```

## <a name="troubleshooting"></a>Solução de problemas

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Não é possível exibir dados de telemetria depois de ingerir dados históricos/de streaming de seus sensores

**Sintoma**: Os dispositivos ou sensores são implantados e você criou os dispositivos/sensores em FarmBeats e a telemetria ingerida para o EventHub, mas não consegue obter ou exibir dados de telemetria no FarmBeats.

**Ação corretiva**:

1. Verifique se você fez o registro de parceiro apropriado-você pode verificar isso acessando o Swagger do datahub, navegue até a API do/Partner, faça uma obtenção e verifique se o parceiro está registrado. Caso contrário, siga as [etapas aqui](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) para adicionar o parceiro.

2. Verifique se você criou os metadados (DeviceModel, dispositivo, SensorModel, sensor) usando as credenciais de cliente do parceiro.

3. Verifique se você usou o formato de mensagem de telemetria correto (conforme especificado abaixo):

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre detalhes de integração baseados na API REST, consulte [REST API](rest-api-in-azure-farmbeats.md).