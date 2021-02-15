---
title: Integração com parceiros de clima
description: Saiba mais sobre como um provedor de dados meteorológicos pode se integrar ao FarmBeats.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147072"
---
# <a name="weather-partner-integration-with-farmbeats"></a>Integração de parceiros meteorológicos com o FarmBeats

Este artigo fornece informações sobre o componente Docker do conector do Azure FarmBeats. Como um provedor de dados meteorológicos, você pode usar o conector de encaixe para integrar com o FarmBeats. Use suas APIs para enviar dados meteorológicos para FarmBeats. No FarmBeats, os dados podem ser usados para a fusão de dados e para a criação de modelos de aprendizado de máquina ou modelos de inteligência artificial.

 > [!NOTE]
 > Neste artigo, usamos uma implementação de [referência](https://github.com/azurefarmbeats/noaa_docker) que foi criada usando os conjuntos de dados abertos do Azure e o clima data da NOAA (National Oceanic and atmosférica Administration). Também usamos a imagem correspondente do [Docker](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa).

Você deve fornecer uma [imagem ou programa do Docker adequado](#docker-specifications) e hospedar a imagem do Docker em um registro de contêiner que os clientes podem acessar. Forneça as seguintes informações para seus clientes:

- URL da imagem do Docker
- Marca de imagem do Docker
- Chaves ou credenciais para acessar a imagem do Docker
- Credenciais ou chaves de API específicas do cliente para acessar os dados do seu sistema
- Detalhes da SKU da VM (forneça esses detalhes se a imagem do Docker tiver requisitos específicos de VM. Caso contrário, os clientes podem escolher entre SKUs de VM com suporte no Azure.)

Os clientes usam essas informações do Docker para registrar um parceiro meteorológico em sua instância FarmBeats. Para obter mais informações sobre como os clientes podem usar o Docker para ingerir dados meteorológicos no FarmBeats, consulte [obter dados de parceiros meteorológicos](./get-weather-data-from-weather-partner.md).

## <a name="connector-docker-development"></a>Desenvolvimento do Docker do conector

**Integração com base em API REST**

As APIs FarmBeats contêm documentação técnica do Swagger. Para obter mais informações sobre as APIs e suas solicitações ou respostas correspondentes, consulte o [Swagger FarmBeats](https://aka.ms/farmbeatsswagger). 

Se você já tiver instalado o FarmBeats, acesse o Swagger do FarmBeats em `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Observe que *-API* é anexada ao nome do seu site FarmBeats. O ponto de extremidade da API é `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub lib

O FarmBeats fornece uma lib que você pode usar. A lib está atualmente disponível como [parte da implementação de referência](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib). Posteriormente, ele estará disponível como um SDK para vários idiomas.

### <a name="authentication"></a>Autenticação

**Autenticação com APIs FarmBeats**

FarmBeats usa autenticação de portador. Você pode acessar as APIs fornecendo um token de acesso na seção de cabeçalho da solicitação. Veja um exemplo:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Você pode solicitar o token de acesso de um aplicativo Azure Functions em execução na instância FarmBeats do cliente. A URL de Azure Functions é fornecida ao programa Docker como um argumento. Você pode obter o token de acesso fazendo uma `GET` solicitação na URL. A resposta da URL contém o token de acesso. 

Use as funções auxiliares na lib Datahub para obter o token de acesso. Para obter mais informações, consulte a [página do GitHub para a imagem do Docker NOAA](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

O token de acesso é válido somente por algumas horas. Quando ele expirar, você deverá solicitá-lo novamente.

**Autenticação com APIs do lado do parceiro**

Para autenticar com as APIs do lado do parceiro enquanto o trabalho do Docker está em execução, os clientes precisam fornecer as credenciais durante o registro do parceiro. Veja um exemplo:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
O serviço de API serializa esse dicter e o armazena em um [cofre de chaves](../../key-vault/general/basic-concepts.md).

[Azure data Factory](../../data-factory/introduction.md) é usado para orquestrar trabalhos meteorológicos. Ele gira os recursos para executar o código do Docker. Data Factory também fornece um mecanismo para enviar dados por push com segurança para a VM na qual o trabalho do Docker é executado. As credenciais de API são armazenadas com segurança no cofre de chaves. 

As credenciais são lidas como cadeias de caracteres seguras do cofre de chaves. Eles são fornecidos como propriedades estendidas no diretório de trabalho do contêiner do Docker. O caminho do arquivo é */mnt/working_dir/activity.js* . 

O código do Docker pode ler as credenciais de *activity.js* durante o tempo de execução para acessar as APIs do lado do parceiro para o cliente. No arquivo JSON, as credenciais são parecidas com este exemplo de código:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
A `partnerCredentials` credencial está disponível no modo como o cliente a forneceu durante o registro do parceiro.

O FarmBeats lib fornece funções auxiliares. Use essas funções para ler as credenciais das propriedades da atividade. Para obter mais informações, consulte a [página do GitHub para a imagem do Docker NOAA](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

O arquivo é usado somente enquanto o código do Docker está em execução. Depois que o código for concluído, o arquivo será excluído.

Para obter mais informações sobre como Data Factory pipelines e atividades funcionam, consulte [esquema e mapeamento de tipo de dados](../../data-factory/copy-activity-schema-and-type-mapping.md).

**Cabeçalhos de solicitação HTTP**

A tabela a seguir mostra os cabeçalhos de solicitação mais comuns que você precisa especificar ao fazer uma chamada à API para FarmBeats.

Cabeçalho | Descrição e exemplo
--- | ---
Tipo de conteúdo | O formato da solicitação. Exemplo: `Content-Type: application/<format>` <br/>Para APIs do Datahub do FarmBeats, o formato é JSON. Exemplo: ` Content-Type: application/json`
Autorização | O token de acesso necessário para fazer uma chamada à API. Exemplo: `Authorization: Bearer <Access-Token>`
Aceitar | O formato da resposta. Para APIs do Datahub do FarmBeats, o formato é JSON. Exemplo: `Accept: application/json`

## <a name="data-format"></a>Formato de dados

O JSON é um formato de dados comum independente de linguagem que fornece uma representação de texto simples de estruturas de dados arbitrárias. Para obter mais informações, consulte [JSON.org](https://json.org).

## <a name="docker-specifications"></a>Especificações do Docker

O programa Docker precisa de dois componentes: a inicialização e o trabalho. O programa pode ter mais de um trabalho.

### <a name="bootstrap"></a>Bootstrap

O componente Bootstrap deve ser executado quando o cliente inicia o registro do Docker no FarmBeats. Os seguintes argumentos ( `arg1` e `arg2` ) são passados para o programa:

- **Ponto de extremidade de API FarmBeats** : o ponto de extremidade de API do FarmBeats para solicitações de API. Esse ponto de extremidade faz chamadas à API para a implantação do FarmBeats.
- **URL de Azure Functions** : seu próprio ponto de extremidade. Essa URL fornece seu token de acesso para APIs FarmBeats. Você pode chamar nessa `GET` URL para buscar o token de acesso.

A inicialização cria os metadados que os usuários precisam para executar seus trabalhos para obter dados meteorológicos. Para obter mais informações, consulte a [implementação de referência](https://github.com/azurefarmbeats/noaa_docker). 

Se você personalizar o *bootstrap_manifest.jsno* arquivo, o programa de inicialização de referência criará os metadados necessários para você. O programa de inicialização cria os seguintes metadados: 

 > [!NOTE]
 > Se você atualizar o *bootstrap_manifest.jsno* arquivo conforme descrito na [implementação de referência](https://github.com/azurefarmbeats/noaa_docker) , não será necessário criar os metadados a seguir. O programa de inicialização usará o arquivo de manifesto para criar os metadados necessários.

- /**WeatherDataModel** : os metadados de WeatherDataModel representam os dados meteorológicos. Ele corresponde aos conjuntos de dados que a fonte fornece. Por exemplo, um DailyForecastSimpleModel pode fornecer informações de temperatura média, umidade e precipitação uma vez por dia. Por outro lado, um DailyForecastAdvancedModel pode fornecer muito mais informações em granularidade por hora. Você pode criar qualquer número de modelos de dados meteorológicos.
- /**JobType** : o FarmBeats tem um sistema de gerenciamento de trabalhos extensível. Como um provedor de dados meteorológicos, você terá vários DataSets e APIs (por exemplo, GetDailyForecasts). Você pode habilitar esses conjuntos de e APIs em FarmBeats usando JobType. Depois que um tipo de trabalho é criado, um cliente pode disparar trabalhos desse tipo para obter dados meteorológicos para seu local ou seu farm de interesse. Para obter mais informações, consulte JobType e APIs de trabalho no [Swagger do FarmBeats](https://aka.ms/farmbeatsswagger).

### <a name="jobs"></a>Trabalhos

O componente Jobs é invocado toda vez que um usuário FarmBeats executa um trabalho do/JobType que você criou como parte do processo de inicialização. O comando Docker execute para o trabalho é definido como parte do/JobType que você criou.

O trabalho busca dados da origem e envia-os por push para FarmBeats. Durante o processo de inicialização, os parâmetros necessários para obter os dados devem ser definidos como parte do/JobType.

Como parte do trabalho, o programa deve criar um/WeatherDataLocation com base no/WeatherDataModel que foi criado durante o processo de inicialização. O/WeatherDataLocation corresponde a um local (coordenadas de latitude e longitude) que o usuário forneceu como um parâmetro para o trabalho.

### <a name="object-details"></a>Detalhes do objeto

WeatherDataModel | Descrição |
--- | ---
Name  | Nome do modelo de dados meteorológico. |
Descrição  | Uma descrição significativa do modelo. |
Propriedades  | Propriedades adicionais definidas pelo provedor de dados. |
Nome > weatherMeasures  | Nome da medida do clima. Por exemplo, humidity_max. |
weatherMeasures > DataType  | Double ou enum. Se enum, measureEnumDefinition é necessário. |
weatherMeasures > measureEnumDefinition  | Necessário somente se DataType for enum. Por exemplo, `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
Tipo de > weatherMeasures  | Tipo de dados de telemetria do clima. Por exemplo, RelativeHumidity. Os tipos definidos pelo sistema são AmbientTemperature, nounitário, CO2, Depth, ElectricalConductivity, LeafWetness, comprimento, LiquidLevel, nitrate, O2, PH, phosphate, PointInTime, Potassium, pressão, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, estado, tempo de duração, UVRadiation, UVIndex, volume, WindDirection, WindRun, WindSpeed, Evapotranspiration e PAR. Para adicionar mais tipos, consulte a seção [add Extended](#add-extendedtype) neste artigo.
Unidade de > weatherMeasures | Unidade de dados de telemetria do clima. As unidades definidas pelo sistema são nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, mercúrio, PSI, milímetro, centímetro, medidor, polegada, pés, milhar, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, grau, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, porcentagem, PartsPerMillion, MicroMole, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litro, MilliLiter, segundos, UnixTimestamp, MicroMolePerMeterSquaredPerSecond e InchesPerHour. Para adicionar mais unidades, consulte a seção [add Extended](#add-extendedtype) neste artigo.
Agregatype de > weatherMeasures  | O tipo de agregação. Os valores possíveis são nenhum, média, máximo, mínimo, i, soma e total.
Profundidade de > de weatherMeasures  | A profundidade do sensor em centímetros. Por exemplo, a medida da umidade de 10 cm sob o solo.
Descrição de > weatherMeasures  | Uma descrição significativa da medida. 

JobType | Descrição |
--- | ---
Name  | Nome do trabalho. Por exemplo, Get_Daily_Forecast. O cliente executará esse trabalho para obter dados meteorológicos.|
pipelineDetails > parâmetros > nome  | Nome do parâmetro. |
pipelineDetails > > tipo de parâmetros | O tipo de parâmetro. Os valores possíveis incluem cadeia de caracteres, int, float, bool e matriz. |
parâmetros de > pipelineDetails > IsRequired | O valor booliano do parâmetro. O valor será true se o parâmetro for necessário. Caso contrário, o valor será false. O padrão é true. |
parâmetros > de pipelineDetails > defaultValue | Valor padrão do parâmetro. |
pipelineDetails > parâmetros > descrição | Descrição do parâmetro. |
Propriedades  | Propriedades adicionais do fabricante.
Propriedades > programRunCommand | Comando de execução do Docker. Esse comando é executado quando o cliente executa o trabalho meteorológico. |

WeatherDataLocation | Descrição |
--- | ---
weatherDataModelId  | ID do WeatherDataModel correspondente que foi criado durante o processo de inicialização.|
local  | Latitude, longitude e elevação. |
Name | Nome do objeto. |
Descrição | Descrição do local dos dados meteorológicos. |
farmid | Adicional ID do farm. O cliente fornece essa ID como parte do parâmetro de trabalho. |
Propriedades  | Propriedades adicionais do fabricante.

Para obter mais informações sobre os objetos e suas propriedades, consulte o [Swagger FarmBeats](https://aka.ms/FarmBeatsSwagger).

> [!NOTE]
> As APIs retornam IDs exclusivas para cada instância criada. O tradutor para gerenciamento de dispositivos e sincronização de metadados precisa reter essa ID.

**Sincronização de metadados**

O componente Docker do conector deve ser capaz de enviar atualizações nos metadados. Por exemplo, ele deve enviar atualizações quando o provedor meteorológico adicionar novos parâmetros a um conjunto de um ou quando novas funcionalidades, como uma nova previsão de 30 dias, forem adicionadas.

> [!NOTE]
> A exclusão não tem suporte para metadados no modelo de dados meteorológico.
>
> Para atualizar os metadados, você precisa chamar `/Get/{ID}` o modelo de dados meteorológico. Atualize as propriedades alteradas e, em seguida, faça um `/Put/{ID}` para manter todas as propriedades que o usuário define.

## <a name="weather-data-telemetry-specifications"></a>Especificações de dados meteorológicos (telemetria)

Os dados meteorológicos são mapeados para uma mensagem canônica enviada por push a um hub de eventos do Azure para processamento. Os Hubs de Eventos do Azure são um serviço que permite a ingestão de dados em tempo real (telemetria) a partir de dispositivos e aplicativos conectados. 

Para enviar dados meteorológicos para o FarmBeats, crie um cliente que envia mensagens para um hub de eventos no FarmBeats. Para obter mais informações, consulte [enviando telemetria para um hub de eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

O código Python de exemplo a seguir envia telemetria como um cliente para um hub de eventos especificado.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Este é o formato de mensagem canônica:

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Aqui está um exemplo de uma mensagem de telemetria:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Solução de problemas e gerenciamento de erros

### <a name="error-logging"></a>Log de erros

O trabalho do parceiro é executado na estrutura de trabalho existente. Portanto, os erros são registrados da mesma maneira que erros para outros trabalhos de FarmBeats preexistentes (como GetFarmData e SensorPlacement). A atividade de Data Factory que é executada no pipeline Data Factory registra `STDERR` e `STDOUT` . Ambos os arquivos estão disponíveis na `datahublogs-xxx` conta de armazenamento dentro do grupo de recursos FarmBeats.

A Datahub lib fornece funções auxiliares para habilitar o registro em log como parte dos logs de Datahub geral. Para obter mais informações, consulte a [página do GitHub para a imagem do Docker NOAA](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

### <a name="troubleshooting-and-support"></a>Solução de problemas e suporte

Se o cliente não puder receber dados meteorológicos na instância do FarmBeats, forneça suporte e um mecanismo para solucionar o problema.

## <a name="add-extendedtype"></a>Adicionar Extended

O FarmBeats oferece suporte à adição de novos tipos e unidades de medida de sensor. Você pode adicionar novas unidades ou tipos atualizando o *bootstrap_manifest.jsno* arquivo na [implementação de referência](https://github.com/azurefarmbeats/noaa_docker).

Siga estas etapas para adicionar um novo tipo de WeatherMeasure, por exemplo, PrecipitationDepth.

1. Faça uma `GET` solicitação em/ExtendedType usando a consulta `filter - key = WeatherMeasureType` .
2. Observe a ID do objeto retornado.
3. Adicione o novo tipo à lista no objeto retornado. Faça uma `PUT` solicitação no/ExtendedType{ID} com a nova lista a seguir. A carga de entrada deve ser a mesma que a resposta que você recebeu anteriormente. A nova unidade deve ser acrescentada no final da lista de valores.

Para obter mais informações sobre a API do/ExtendedType, consulte o [Swagger do FarmBeats](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Próximas etapas

Agora, você tem um componente Docker do conector que se integra ao FarmBeats. Em seguida, descubra como [obter dados meteorológicos](get-weather-data-from-weather-partner.md) usando a imagem do Docker no FarmBeats. 
