---
title: Exportar seus dados para o Armazenamento de Blobs do Azure | Microsoft Docs
description: Como exportar dados de seu aplicativo do Azure IoT Central para o Armazenamento de Blobs do Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 0fcce6bd6ee9461790ca7618f65be9a20a821afc
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360331"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Exportar dados para o Armazenamento de Blobs do Azure

*Este tópico aplica-se aos administradores.*

Este artigo descreve como usar o recurso de exportação de dados contínuos no Azure IoT Central para exportar dados periodicamente para sua **conta de armazenamento de BLOBs do Azure**. Você pode exportar **medidas**, **dispositivos** e **modelos de dispositivo** para arquivos no formato Apache Avro. Use os dados exportados para a análise de caminho frio, assim como modelos de treinamento no Microsoft Azure Machine Learning ou análise de tendências de longo prazo no Power BI.

> [!Note]
> Mais uma vez, ao ativar a exportação contínua de dados, você obtém apenas os dados desse momento em diante. Atualmente, não é possível recuperar dados por um tempo quando a exportação contínua de dados for desativada. Para reter dados mais históricos, ative a exportação contínua de dados no início.


## <a name="prerequisites"></a>Pré-requisitos

- Você precisa ser um administrador do aplicativo IoT Central


## <a name="set-up-export-destination"></a>Configurar o destino de exportação

Se você não tiver um armazenamento existente para exportar para o, siga estas etapas:

## <a name="create-storage-account"></a>Criar Conta de Armazenamento

1. Crie uma [conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Saiba mais na [documentação do Armazenamento do Azure](https://aka.ms/blobdocscreatestorageaccount).
2. Para o tipo de conta, escolha **Uso geral** ou **Armazenamento de Blobs**.
3. Escolha uma assinatura. 

    > [!Note] 
    > Agora você pode exportar dados para outras assinaturas que **não são a mesma** usada para o aplicativo IoT Central pago conforme o uso. Você se conectará usando uma cadeia de conexão, nesse caso.

4. Crie um contêiner em sua conta de armazenamento. Vá até sua conta de armazenamento. Em **Serviço Blob**, selecione **Procurar Blobs**. Selecione **+ contêiner** na parte superior para criar um novo contêiner


## <a name="set-up-continuous-data-export"></a>Configuração da exportação contínua de dados

Agora que você tem um destino de armazenamento para o qual exportar dados, siga estas etapas para configurar a exportação de dados contínuas. 

1. Entre no aplicativo IoT Central.

2. No menu à esquerda, selecione **exportação de dados contínuas**.

    > [!Note]
    > Se a Exportação Contínua de Dados não for exibida no menu à esquerda, isso indicará que você não é um administrador no aplicativo. Contate o administrador para configurar a exportação de dados.

    ![Criar novo Hub de Eventos cde](media/howto-export-data/export_menu1.png)

3. Selecione o botão **+ novo** no canto superior direito. Escolha o **armazenamento de BLOBs do Azure** como o destino de sua exportação. 

    > [!NOTE] 
    > O número máximo de exportações por aplicativo é cinco. 

    ![Criar exportação contínua de dados](media/howto-export-data/export_new1.png)

4. Na caixa de listagem suspensa, selecione o namespace da **conta de armazenamento**. Você também pode escolher a última opção da lista, **Inserir uma cadeia de conexão**. 

    > [!NOTE] 
    > Você só verá namespaces de contas de armazenamento na **mesma assinatura que seu aplicativo IOT central**. Caso deseje exportar para um destino fora dessa assinatura, escolha **Inserir uma cadeia de conexão** e veja a etapa 5.

    > [!NOTE] 
    > Para aplicativos de avaliação de 7 dias, a única maneira de configurar a exportação contínua de dados é por meio de uma cadeia de conexão. Isso ocorre porque os aplicativos de avaliação de 7 dias não têm uma assinatura do Azure associada.

    ![Criar novo Hub de Eventos cde](media/howto-export-data/export-create-blob.png)

5. (Opcional) Se você escolher **Inserir uma cadeia de conexão**, uma nova caixa será exibida para que você cole a cadeia de conexão. Para obter a cadeia de conexão para:
    - Conta de armazenamento, vá para a conta de armazenamento no portal do Azure.
        - Em **configurações**, selecione **chaves de acesso**
        - Copie a Cadeia de conexão de key1 ou a Cadeia de conexão de key2
 
6. Escolha um contêiner na caixa de listagem suspensa.

7. Em **Dados a serem exportados**, especifique cada tipo de dados a serem exportados, definindo o tipo para **Ativar**.

6. Para ativar a exportação contínua de dados, verifique se a opção **Exportação de dados** está **Ativada**. Clique em **Salvar**.

   ![Configure a exportação contínua de dados](media/howto-export-data/export-list-blob.png)

7. Após alguns minutos, seus dados serão exibidos no destino escolhido.


## <a name="export-to-azure-blob-storage"></a>Exportar para o Armazenamento de Blobs do Azure

Os dados de medidas, dispositivos e modelos de dispositivo são exportados para sua conta de armazenamento uma vez por minuto, com cada arquivo contendo o lote de alterações desde o último arquivo exportado. Os dados exportados estão no formato [Apache Avro](https://avro.apache.org/docs/current/index.html) e serão exportados em três pastas. Os caminhos padrão em sua conta de armazenamento são:
- Mensagens: {contêiner}/measurements/{nomedohub}/{AAAA}/{MM}/{dd}/{hh}/{mm}/{nomedoarquivo}.avro
- Dispositivos: {contêiner}/devices/{AAAA}/{MM}/{dd}/{hh}/{mm}/{nomedoarquivo}.avro
- Modelos de dispositivo: {contêiner}/deviceTemplates/{AAAA}/{MM}/{dd}/{hh}/{mm}/{nomedoarquivo}.avro

### <a name="measurements"></a>Medidas

Os dados das medidas recebidos têm todas as novas mensagens recebidas pelo IoT Central de todos os dispositivos durante esse período. Os arquivos exportados usam o mesmo formato que os arquivos de mensagens exportados pelo [roteamento de mensagens do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) para o Armazenamento de Blobs.

> [!NOTE]
> Os dispositivos que enviaram as medidas são representados por IDs de dispositivo (veja as seções abaixo). Para obter os nomes dos dispositivos, exporte os instantâneos do dispositivo. Correlacione cada registro de mensagem usando o **connectionDeviceId** que corresponde ao **deviceId** do registro do dispositivo.

O exemplo a seguir mostra um registro em um arquivo Avro decodificado:

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "<connectionDeviceId>",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "<generationId>",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Dispositivos

Ao ativar a exportação contínua pela primeira vez, um único instantâneo que contém todos os dispositivos é exportado. Cada dispositivo inclui:
- `id` do dispositivo na IoT Central
- `name` do dispositivo
- `deviceId` do [Serviço de Provisionamento de Dispositivos](https://aka.ms/iotcentraldocsdps)
- Informações de modelo de dispositivo
- Valores de propriedade
- Valores de configuração

Um novo instantâneo é gravado uma vez por minuto. O instantâneo inclui:

- Novos dispositivos adicionados desde o último instantâneo.
- Dispositivos com propriedade alterada e valores de configuração desde o último instantâneo.

> [!NOTE]
> Dispositivos excluídos desde o último instantâneo que não foram exportados. Atualmente, os instantâneos não têm indicadores para dispositivos excluídos.
>
> O modelo de dispositivo ao qual cada dispositivo pertence é representado por uma ID de modelo de dispositivo. Para obter o nome do modelo de dispositivo, exporte os instantâneos do modelo de dispositivo.

Um registro no arquivo Avro decodificado pode ter este aspecto:

```json
{
    "id": "<id>",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
        "id": "<template id>",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>Modelos de dispositivo

Ao ativar a exportação contínua pela primeira vez, um único instantâneo com todos os dispositivos é exportado. Cada modelo de dispositivo inclui:
- `id` do modelo de dispositivo
- `name` do modelo de dispositivo
- `version` do modelo de dispositivo
- Tipos de dados de medição e valores mínimos/máximos.
- Tipos de dados de propriedade e valores padrão.
- Configuração de tipos de dados e valores padrão.

Um novo instantâneo é gravado uma vez por minuto. O instantâneo inclui:

- Os novos modelos de dispositivo adicionados desde o último instantâneo.
- Modelos de dispositivo com medidas alteradas, propriedade e definições de configurações que foram alteradas desde o último instantâneo.

> [!NOTE]
> Modelos de dispositivo excluídos desde que o último instantâneo não foi exportado. Atualmente, os instantâneos não têm indicadores para modelos excluídos.

Um registro no arquivo Avro decodificado pode ter este aspecto:

```json
{
    "id": "<id>",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="read-exported-avro-files"></a>Como ler arquivos Avro exportados

Avro é um formato binário, então os arquivos não podem ser lidos em seu estado bruto. Os arquivos podem ser decodificados para o formato JSON. Os exemplos a seguir mostram como analisar os as medidas, dispositivos e modelos de dispositivo dos arquivos Avro. Os exemplos correspondem aos exemplos descritos na seção anterior.

### <a name="read-avro-files-by-using-python"></a>Ler arquivos Avro usando o Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Instalar o pandas e o pacote pandavro

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Analisar um arquivo Avro de medidas

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(
        lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(
        lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)
```

#### <a name="parse-a-devices-avro-file"></a>Analisar um arquivo Avro de dispositivos

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(
        lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(
        lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

#### <a name="parse-a-device-templates-avro-file"></a>Analisar o arquivo Avro de modelos de dispositivo

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Ler arquivos Avro usando C#

#### <a name="install-the-microsofthadoopavro-package"></a>Instalar o pacote Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Analisar um arquivo Avro de medidas

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>Analisar um arquivo Avro de dispositivos

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>Analisar o arquivo Avro de modelos de dispositivo

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>Ler arquivos Avro usando o Javascript

#### <a name="install-the-avsc-package"></a>Instalar o pacote avsc

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Analisar um arquivo Avro de medidas

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Analisar um arquivo Avro de dispositivos

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Analisar o arquivo Avro de modelos de dispositivo

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como exportar seus dados, continue para a próxima etapa:

> [!div class="nextstepaction"]
> [Como visualizar os dados no Power BI](howto-connect-powerbi.md)
