---
title: Integração com parceiros de sensor
description: Este artigo descreve a integração com parceiros de sensor.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 8d1b8203fa50609daf59431c2cfecba68eba52b1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179741"
---
# <a name="sensor-partner-integration"></a>Integração com parceiros de sensor

Este artigo fornece informações sobre o componente **Tradutor** do Azure FarmBeats, que permite a integração com parceiros de sensor.

Usando esse componente, os parceiros podem se integrar com o FarmBeats usando APIs do Datahub do FarmBeats e enviar dados e telemetria do dispositivo do cliente para o Datahub do FarmBeats. Depois que os dados estão disponíveis no FarmBeats, eles são visualizados usando o FarmBeats Accelerator e podem ser usados para fusão de dados e para a criação de modelos de aprendizado de máquina/inteligência artificial.

## <a name="before-you-start"></a>Antes de começar

Para desenvolver o componente Tradutor, você precisará das seguintes credenciais que permitirão o acesso às APIs do FarmBeats.

- Ponto de extremidade de API
- ID do locatário
- ID do Cliente
- Segredo do cliente
- Cadeia de conexão do EventHub

Confira esta seção para obter as credenciais acima: [Habilitar a integração do dispositivo](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Desenvolvimento do Tradutor

**Integração com base em API REST**

Os recursos de integração de dados de sensor do FarmBeats são expostos por meio da API REST. Os recursos incluem a definição de metadados, o provisionamento de dispositivo e sensor, e o gerenciamento de dispositivo e sensor.

**Ingestão de telemetria**

Os dados de telemetria são mapeados para uma mensagem canônica que é publicada nos Hubs de Eventos do Azure para processamento. Os Hubs de Eventos do Azure são um serviço que permite a ingestão de dados em tempo real (telemetria) a partir de dispositivos e aplicativos conectados.

**Desenvolvimento de API**

As APIs contêm documentação técnica do Swagger. Para saber mais sobre as APIs e suas solicitações ou respostas correspondentes, confira [Swagger](https://aka.ms/FarmBeatsSwagger).

**Autenticação**

O FarmBeats usa a autenticação do Microsoft Azure Active Directory. O Serviço de Aplicativo do Azure fornece suporte interno para a autenticação e autorização.

Para obter mais informações, consulte [Azure Active Directory](../../app-service/overview-authentication-authorization.md).

O Datahub do FarmBeats usa autenticação de portador, que precisa das seguintes credenciais:
   - ID do Cliente
   - Segredo do cliente
   - ID do locatário

Usando essas credenciais, o chamador pode solicitar um token de acesso. O token precisa ser enviado nas solicitações de API subsequentes, na seção de cabeçalho, da seguinte maneira:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

O seguinte exemplo de código Python fornece o token de acesso, que pode ser usado para chamadas à API subsequentes para o FarmBeats.

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

Aqui estão os cabeçalhos de solicitação mais comuns que precisam ser especificados quando você faz uma chamada à API para o Datahub do FarmBeats.


**Cabeçalho** | **Descrição e exemplo**
--- | ---
Tipo de conteúdo | O formato da solicitação (Content-Type: application/<format>). Para APIs do Datahub do FarmBeats, o formato é JSON. Content-Type: application/json
Autorização | Especifica o token de acesso necessário para fazer uma chamada à API. Autorização: Bearer <token-de-acesso>
Aceitar | O formato da resposta. Para APIs do Datahub do FarmBeats, o formato é JSON. Aceitar: application/json

**Solicitações da API**

Para fazer uma solicitação de API REST, combine o método HTTP (GET, POST ou PUT), a URL para o serviço da API, o URI (Uniform Resource Identifier) para um recurso para consultar, enviar dados, atualizar ou excluir, e um ou mais cabeçalhos de solicitação HTTP. A URL para o serviço da API é o ponto de extremidade de API que você fornece. Aqui está um exemplo: https:// \<yourdatahub-website-name> . azurewebsites.net

Opcionalmente, você pode incluir parâmetros de consulta nas chamadas GET para filtrar, limitar o tamanho e classificar os dados nas respostas.

O exemplo de solicitação a seguir é a obtenção da lista de dispositivos.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
A maioria das chamadas GET, POST e PUT exigem um corpo de solicitação JSON.

O exemplo de solicitação a seguir é a criação de um dispositivo. (Este exemplo tem um JSON de entrada com o corpo da solicitação).

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Formato de dados

O JSON é um formato de dados comum independente de linguagem que fornece uma representação de texto simples de estruturas de dados arbitrárias. Para saber mais, confira [json.org](http://json.org).

## <a name="metadata-specifications"></a>Especificações de metadados

O FarmBeats Datahub tem as seguintes APIs que permitem aos parceiros de dispositivo criar e gerenciar metadados de dispositivo ou sensor.

- /**DeviceModel**: DeviceModel corresponde aos metadados do dispositivo, como o fabricante e o tipo de dispositivo, seja gateway ou nó.
- /**Device**: Device corresponde a um dispositivo físico presente no farm.
- /**SensorModel**: SensorModel corresponde aos metadados do sensor, como o fabricante, o tipo de sensor, seja analógico ou digital, e a medida do sensor, como temperatura ambiente e pressão.
- /**Sensor**: Sensor corresponde a um sensor físico que registra valores. Um sensor geralmente está conectado a um dispositivo com uma ID de dispositivo.

  DeviceModel | Descrição |
  --- | ---
  Tipo (nó, gateway)  | Tipo de dispositivo: nó ou gateway |
  Fabricante  | Nome do fabricante |
  ProductCode  | Código de produto do dispositivo ou nome ou número do modelo. Por exemplo, EnviroMonitor#6800. |
  Portas  | Nome e tipo da porta, seja digital ou analógica.  |
  Nome  | Nome para identificar o recurso. Por exemplo, nome do modelo ou nome do produto. |
  Descrição  | Fornece uma descrição significativa da configuração. |
  Propriedades  | Propriedades adicionais do fabricante. |
  **Dispositivo** | **Descrição** |
  DeviceModelId  |ID do modelo de dispositivo associado. |
  HardwareId   |ID exclusiva para o dispositivo, como um endereço MAC.  |
  ReportingInterval |Intervalo de relatórios em segundos. |
  Location    |Latitude (-90 a +90), longitude (-180 a 180) e elevação (em metros) do dispositivo. |
  ParentDeviceId | ID do dispositivo pai ao qual este dispositivo está conectado. Por exemplo, se um nó estiver conectado a um gateway, o nó terá parentDeviceID como o gateway. |
  Nome  | Nome amigável para identificar o recurso. Os parceiros de dispositivo precisam enviar um nome consistente com o nome do dispositivo no lado do parceiro. Se o nome do dispositivo for definido pelo usuário no lado do parceiro do dispositivo, o mesmo nome definido pelo usuário deverá ser propagado para o FarmBeats.  |
  Descrição  | Fornece uma descrição significativa.  |
  Propriedades  |Propriedades adicionais do fabricante.  |
  **SensorModel** | **Descrição** |
  Tipo (analógico, digital)  |Indica o sensor analógico ou digital.|
  Fabricante  | Nome do fabricante. |
  ProductCode  | Código de produto ou nome ou número do modelo. Por exemplo, RS-CO2-N01.  |
  SensorMeasures > Name  | Nome da medida do sensor. Suporte somente para letras minúsculas. Para medidas de diferentes profundidades, especifique a profundidade. Por exemplo, soil_moisture_15cm. Esse nome deve ser consistente com os dados telemétricos. |
  SensorMeasures > DataType  | Tipo de dados telemétricos. Atualmente, há suporte apenas para dois. |
  SensorMeasures > Type  | Tipo de medida dos dados telemétricos do sensor. A seguir, estão os tipos definidos pelo sistema: AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Para adicionar mais, confira a API do /ExtendedType.
  SensorMeasures > Unit | Unidade de dados telemétricos do sensor. A seguir, estão as unidades definidas pelo sistema: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond e InchesPerHour. Para adicionar mais, confira a API do /ExtendedType.
  SensorMeasures > AggregationType  | Nenhum, médio, máximo, mínimo ou StandardDeviation.
  SensorMeasures > Depth  | A profundidade do sensor em centímetros. Por exemplo, a medida da umidade de 10 cm sob o solo.
  SensorMeasures > Description  | Fornece uma descrição significativa da medida.
  Nome  | Nome para identificar o recurso. Por exemplo, o nome do modelo ou nome do produto.
  Descrição  | Fornece uma descrição significativa da configuração.
  Propriedades  | Propriedades adicionais do fabricante.
  **Sensor**  | **Descrição** |
  HardwareId  | ID exclusiva do sensor definida pelo fabricante.
  SensorModelId  | ID do modelo de sensor associado.
  Location  | Latitude (-90 a +90), longitude (-180 a 180) e elevação (em metros) do sensor.
  Porta > Nome  |Nome e tipo da porta à qual o sensor está conectado no dispositivo. Deve ser o mesmo nome definido no modelo do dispositivo.
  deviceId  | ID do dispositivo ao qual o sensor está conectado.
  Nome  | Nome amigável para identificar o recurso. Por exemplo, o nome do sensor ou nome do produto e número do modelo ou código do produto.
  Descrição  | Fornece uma descrição significativa.
  Propriedades  | Propriedades adicionais do fabricante.

 Para obter informações sobre cada um dos objetos e suas propriedades, confira [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > As APIs retornam IDs exclusivas para cada instância criada. Essa ID precisa ser retida pelo Tradutor para o gerenciamento de dispositivos e a sincronização de metadados.


**Sincronização de metadados**

O Tradutor deve enviar atualizações sobre os metadados. Por exemplo, os cenários de atualização são a alteração do nome do dispositivo ou sensor e a alteração da localização do dispositivo ou sensor.

O Tradutor deve ter a capacidade de adicionar novos dispositivos ou sensores que foram instalados pelo usuário após a vinculação do FarmBeats. Da mesma forma, se um dispositivo ou sensor foi atualizado pelo usuário, o mesmo deve ser atualizado no FarmBeats para o dispositivo ou sensor correspondente. Os cenários típicos que exigem atualização de um dispositivo ou sensor são uma alteração no local do dispositivo ou a adição de sensores em um nó.


> [!NOTE]
> Não há suporte para exclusão nos metadados do dispositivo ou do sensor.
>
> Para atualizar os metadados, é obrigatório chamar /Get/{ID} no dispositivo ou sensor, atualizar as propriedades alteradas e, em seguida, executar uma /Put/{id} para que todas as propriedades definidas pelo usuário não sejam perdidas.

### <a name="add-new-types-and-units"></a>Adicionar novos tipos e unidades

O FarmBeats oferece suporte à adição de novos tipos e unidades de medida de sensor. Para saber mais sobre a API do /ExtendedType, confira [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="telemetry-specifications"></a>Especificações de telemetria

Os dados de telemetria são mapeados para uma mensagem canônica que é publicada nos Hubs de Eventos do Azure para processamento. Os Hubs de Eventos do Azure são um serviço que permite a ingestão de dados em tempo real (telemetria) a partir de dispositivos e aplicativos conectados.

## <a name="send-telemetry-data-to-farmbeats"></a>Enviar dados de telemetria para o FarmBeats

Para enviar dados de telemetria para o FarmBeats, crie um cliente que envie mensagens para um hub de eventos no FarmBeats. Para saber mais sobre dados de telemetria, confira [Enviar telemetria para um hub de eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Aqui está um exemplo de código Python que envia telemetria como cliente para um hub de eventos especificado.

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

O formato da mensagem canônica é o seguinte:

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
Todos os nomes de chave no JSON de telemetria devem estar em minúsculas. Exemplos são deviceid e sensordata.

Por exemplo, aqui está uma mensagem de telemetria:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
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

> [!NOTE]
> As seções a seguir estão relacionadas a outras alterações (por exemplo, interface do usuário, gerenciamento de erros, etc.) às quais o parceiro de sensor pode se referir no desenvolvimento do componente Tradutor.


## <a name="link-a-farmbeats-account"></a>Vincular uma conta do FarmBeats

Depois que os clientes adquirem e implantam dispositivos ou sensores, eles podem acessar os dados do dispositivo e a telemetria no portal de SaaS (software como serviço) dos parceiros do dispositivo. Os parceiros de dispositivo podem permitir que os clientes vinculem suas contas à instância do FarmBeats no Azure, fornecendo uma maneira de inserir as seguintes credenciais:

   - Nome de exibição (um campo opcional para os usuários definirem um nome para esta integração)
   - Ponto de extremidade de API
   - ID do locatário
   - ID do Cliente
   - Segredo do cliente
   - Cadeia de conexão do EventHub
   - Data de início

   > [!NOTE]
   > A data de início habilita o feed de dados históricos, ou seja, os dados da data especificada pelo usuário.

## <a name="unlink-farmbeats"></a>Desvincular o FarmBeats

Os parceiros de dispositivo podem permitir que os clientes desvinculem uma integração existente do FarmBeats. A desvinculação do FarmBeats não deve excluir nenhum metadado de dispositivo ou sensor criado no Datahub do FarmBeats. A desvinculação faz o seguinte:

   - Interrompe o fluxo de telemetria.
   - Exclui e apaga as credenciais de integração no parceiro do dispositivo.

## <a name="edit-farmbeats-integration"></a>Editar a integração do FarmBeats

Os parceiros de dispositivo podem permitir que os clientes editem as configurações de integração do FarmBeats se a cadeia de conexão ou o segredo do cliente for alterado. Nesse caso, somente os campos a seguir são editáveis:

   - Nome de exibição (se aplicável)
   - Segredo do cliente (deve ser exibido no formato "2x8***********" ou no recurso Mostrar/Ocultar em vez de texto não criptografado)
   - Cadeia de conexão (deve ser exibido no formato "2x8***********" ou no recurso Mostrar/Ocultar em vez de texto não criptografado)

## <a name="view-the-last-telemetry-sent"></a>Exibir a última telemetria enviada

Os parceiros de dispositivo podem permitir que os clientes visualizem o carimbo de data/hora da última telemetria enviada, encontrada em **Telemetria Enviada**. Esta é a hora em que a telemetria mais recente foi enviada com sucesso para o FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Solução de problemas e gerenciamento de erros

**Solução de problemas ou suporte**

Se o cliente não conseguir receber dados ou telemetria do dispositivo na instância especificada do FarmBeats, o parceiro do dispositivo deve fornecer suporte e um mecanismo para solução de problemas.

**Retenção de dados de telemetria**

Os dados de telemetria também devem ser retidos por um período de tempo predefinido, para que possam ser úteis na depuração ou reenvio da telemetria no caso de erro ou perda de dados.

**Gerenciamento de erros ou notificação de erros**

Se um erro afetar os metadados do dispositivo ou sensor ou a integração de dados ou o fluxo de dados de telemetria no sistema parceiro do dispositivo, o cliente deverá receber uma notificação. Um mecanismo para resolver qualquer erro também deve ser desenvolvido e implementado.

**Lista de verificação de conexão**

Os parceiros ou fabricantes de dispositivos podem usar a seguinte lista de verificação para assegurar que as credenciais fornecidas pelo cliente sejam precisas:

   - Verifique se um token de acesso foi recebido com as credenciais fornecidas.
   - Verifique se uma chamada à API foi realizada com êxito com o token de acesso recebido.
   - Verifique se a conexão do cliente EventHub foi estabelecida.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a API REST, confira [API REST](rest-api-in-azure-farmbeats.md).