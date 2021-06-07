---
title: 'Conceitos: mapeando modelos no conector do Azure IoT para o recurso FHIR (versão prévia) da API do Azure para FHIR'
description: Aprenda a criar dois tipos de modelos de mapeamento no conector IoT do Azure para FHIR (versão prévia). O modelo de mapeamento de dispositivo transforma os dados do dispositivo em um esquema normalizado. O modelo de mapeamento FHIR transforma uma mensagem normalizada em um recurso de Observação baseado no FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: punagpal
ms.openlocfilehash: e43b5068544927ba4d04c86b16d3a15510c32bed
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448465"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Modelos de mapeamento do Conector IoT do Azure para FHIR (versão prévia)
Este artigo fornece detalhes sobre como configurar o conector IoT do Azure para recursos de interoperabilidade do Fast Healthcare (FHIR&#174;) * usando modelos de mapeamento.

O conector do Azure IoT para FHIR requer dois tipos de modelos de mapeamento baseados em JSON. O primeiro tipo, **mapeamento de dispositivo**, é responsável por mapear as cargas de dispositivo enviadas para o `devicedata` ponto de extremidade do hub de eventos do Azure. Ele extrai tipos, identificadores de dispositivo, data e hora de medição e o (s) valor (es) de medida. O segundo tipo, **mapeamento FHIR**, controla o mapeamento para o recurso FHIR. Ele permite a configuração do comprimento do período de observação, o tipo de dados FHIR usado para armazenar os valores e os códigos de terminologia. 

Os modelos de mapeamento são compostos em um documento JSON com base em seu tipo. Esses documentos JSON são então adicionados ao conector IoT do Azure para FHIR por meio do portal do Azure. O documento de mapeamento de dispositivo é adicionado por meio da página **Configurar mapeamento de dispositivo** e do documento de mapeamento FHIR por meio da página **Configurar mapeamento de FHIR** .

> [!NOTE]
> Os modelos de mapeamento são armazenados em um armazenamento de BLOBs subjacente e carregados do blob por execução de computação. Depois de atualizado, eles devem entrar em vigor imediatamente. 

## <a name="device-mapping"></a>Mapeamento de dispositivo
O mapeamento de dispositivo fornece a funcionalidade de mapeamento para extrair o conteúdo do dispositivo em um formato comum para uma avaliação posterior. Cada mensagem recebida é avaliada em todos os modelos. Essa abordagem permite que uma única mensagem de entrada seja projetada para várias mensagens de saída, que são mapeadas posteriormente para observações diferentes no FHIR. O resultado é um objeto de dados normalizado que representa o valor ou os valores analisados pelos modelos. O modelo de dados normalizado tem algumas propriedades necessárias que devem ser encontradas e extraídas:

| Propriedade | Descrição |
| - | - |
|**Tipo**|O nome/tipo para classificar a medida. Esse valor é usado para associar ao modelo de mapeamento FHIR necessário.  Vários modelos podem gerar saída para o mesmo tipo, permitindo que você mapeie diferentes representações em vários dispositivos para uma única saída comum.|
|**OccurenceTimeUtc**|A hora em que a medição ocorreu.|
|**deviceId**|O identificador do dispositivo. Esse valor deve corresponder a um identificador no recurso do dispositivo que existe no servidor de FHIR de destino.|
 |**Propriedades**|Extraia pelo menos uma propriedade para que o valor possa ser salvo no recurso de observação criado.  As propriedades são uma coleção de pares chave-valor extraídos durante a normalização.|

Veja abaixo um exemplo conceitual do que acontece durante a normalização.

![Exemplo de normalização](media/concepts-iot-mapping-templates/normalization-example.png)

A própria carga de conteúdo é uma mensagem do hub de eventos do Azure, que é composta por três partes: corpo, propriedades e SystemProperties. O `Body` é uma matriz de bytes que representa uma cadeia de caracteres codificada em UTF-8. Durante a avaliação do modelo, a matriz de bytes é automaticamente convertida no valor da cadeia de caracteres. `Properties` é uma coleção de valores de chave para uso pelo criador de mensagem. `SystemProperties` também é uma coleção de valores de chave reservada pela estrutura do hub de eventos do Azure com entradas preenchidas automaticamente por ela.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>Mapeando com o caminho JSON
Os três tipos de modelo de conteúdo de dispositivo com suporte hoje dependem do caminho JSON para corresponder ao modelo necessário e aos valores extraídos. Mais informações sobre o caminho JSON podem ser encontradas [aqui](https://goessner.net/articles/JsonPath/). Todos os três tipos de modelo usam a [implementação JSON .net](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) para resolver expressões de caminho JSON.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
O JsonPathContentTemplate permite a correspondência e a extração de valores de uma mensagem do hub de eventos usando o caminho JSON.

| Propriedade | Descrição |<div style="width:150px">Exemplo</div>
| --- | --- | --- 
|**TypeName**|O tipo a ser associado a medidas que correspondem ao modelo.|`heartrate`
|**TypeMatchExpression**|A expressão de caminho JSON que é avaliada em relação à carga do hub de eventos. Se um JToken correspondente for encontrado, o modelo será considerado uma correspondência. Todas as expressões subsequentes são avaliadas em relação ao JToken extraído correspondente aqui.|`$..[?(@heartRate)]`
|**Carimbo de data/hora**|A expressão de caminho JSON para extrair o valor de carimbo de data/hora para o OccurenceTimeUtc da medida.|`$.endDate`
|**DeviceIdExpression**|A expressão de caminho JSON para extrair o identificador do dispositivo.|`$.deviceId`
|**PatientIdExpression**|*Opcional*: a expressão de caminho JSON para extrair o identificador do paciente.|`$.patientId`
|**EncounterIdExpression**|*Opcional*: a expressão de caminho JSON para extrair o identificador de encontre.|`$.encounterId`
|**Valores []. ValueName**|O nome a ser associado ao valor extraído pela expressão subsequente. Usado para associar o valor/componente necessário no modelo de mapeamento FHIR. |`hr`
|**Valores []. Valor da**|A expressão de caminho JSON para extrair o valor necessário.|`$.heartRate`
|**Valores []. Necessário**|Exigirá que o valor esteja presente na carga.  Se não for encontrado, uma medida não será gerada e um InvalidOperationException será gerado.|`true`

##### <a name="examples"></a>Exemplos
---
**Taxa de coração**

*Message*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Modelo*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Pressão sanguínea**

*Message*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Modelo*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceId",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**Projetar várias medições a partir de uma única mensagem**

*Message*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Modelo 1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*Modelo 2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**Projetar várias medidas da matriz na mensagem**

*Message*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Modelo*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

O IotJsonPathContentTemplate é semelhante ao JsonPathContentTemplate, exceto que o DeviceIdExpression e o carimbo de data/hora não são necessários.

A suposição ao usar esse modelo é que as mensagens que estão sendo avaliadas foram enviadas usando os [SDKs de dispositivo do Hub IOT do Azure](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) ou o recurso de exportação de  [dados (Herdado)](../../iot-central/core/howto-export-data-legacy.md) do [Azure IOT central](../../iot-central/core/overview-iot-central.md). Ao usar esses SDKs, a identidade do dispositivo (supondo que o identificador do dispositivo do Hub IOT/central do Azure seja registrado como um identificador para um recurso de dispositivo no servidor FHIR de destino) e o carimbo de data/hora da mensagem é conhecido. Se você estiver usando SDKs de dispositivo do Hub IoT do Azure, mas estiver usando propriedades personalizadas no corpo da mensagem para a identidade do dispositivo ou o carimbo de data/hora da medição, ainda poderá usar o JsonPathContentTemplate.

*Observação: ao usar o IotJsonPathContentTemplate, o TypeMatchExpression deve resolver a mensagem inteira como um JToken. Consulte os exemplos abaixo.* 
##### <a name="examples"></a>Exemplos
---
**Taxa de coração**

*Message*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Modelo*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Pressão sanguínea**

*Message*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Modelo*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

O IotCentralJsonPathContentTemplate também não exige o DeviceIdExpression e o carimbo de data/hora e é usado quando as mensagens que estão sendo avaliadas são enviadas por meio do recurso [exportar dados](../../iot-central/core/howto-export-data.md) do [Azure IOT central](../../iot-central/core/overview-iot-central.md). Ao usar esse recurso, a identidade do dispositivo (supondo que o identificador do dispositivo do Azure IOT central seja registrado como um identificador para um recurso de dispositivo no servidor de destino FHIR) e o carimbo de data/hora da mensagem é conhecido. Se você estiver usando o recurso de exportação de dados do Azure IoT Central, mas estiver usando propriedades personalizadas no corpo da mensagem para a identidade do dispositivo ou o carimbo de data/hora da medição, você ainda poderá usar o JsonPathContentTemplate.

*Observação: ao usar o IotCentralJsonPathContentTemplate, o TypeMatchExpression deve resolver a mensagem inteira como um JToken. Consulte os exemplos abaixo.* 
##### <a name="examples"></a>Exemplos
---
**Taxa de coração**

*Message*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*Modelo*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Pressão sanguínea**

*Message*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*Modelo*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="fhir-mapping"></a>Mapeamento FHIR
Depois que o conteúdo do dispositivo é extraído em um modelo normalizado, os dados são coletados e agrupados de acordo com o identificador do dispositivo, o tipo de medida e o período de tempo. A saída desse Agrupamento é enviada para conversão em um recurso FHIR ([Observação](https://www.hl7.org/fhir/observation.html) no momento). Aqui, o modelo de mapeamento FHIR controla como os dados são mapeados para uma observação FHIR. Uma observação deve ser criada para um ponto no tempo ou durante um período de uma hora? Quais códigos devem ser adicionados à observação? O valor deve ser representado como [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) ou uma [quantidade](https://www.hl7.org/fhir/datatypes.html#Quantity)? Esses tipos de dados são todas as opções dos controles de configuração de mapeamento FHIR.

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
Atualmente, o CodeValueFhirTemplate é o único modelo com suporte no mapeamento FHIR no momento.  Ele permite que você defina códigos, o período efetivo e o valor da observação. Há suporte para vários tipos de valores: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)e [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity). Junto com esses valores configuráveis, o identificador do recurso de observação e o vínculo com os recursos apropriados do dispositivo e do paciente são tratados automaticamente.

| Propriedade | Descrição 
| --- | ---
|**TypeName**| O tipo de medida ao qual este modelo deve se associar. Deve haver pelo menos um modelo de mapeamento de dispositivo que gere esse tipo.
|**PeriodInterval**|O período de tempo que a observação criada deve representar. Os valores com suporte são 0 (uma instância), 60 (uma hora), 1440 (um dia).
|**Categoria**|Qualquer número de [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) para classificar o tipo de observação criado.
|**Códigos**|Uma ou mais [codificações](http://hl7.org/fhir/datatypes-definitions.html#coding) a serem aplicadas à observação criada.
|**Códigos []. Auto-completar**|O código para a [codificação](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Códigos []. Sistema**|O sistema para a [codificação](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Códigos []. Display**|A exibição para a [codificação](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Valor**|O valor a ser extraído e represente na observação. Para obter mais informações, consulte [modelos de tipo de valor](#valuetypes).
|**Componentes**|*Opcional:* Um ou mais componentes a serem criados na observação.
|**Componentes []. CEP**|Uma ou mais [codificações](http://hl7.org/fhir/datatypes-definitions.html#coding) a serem aplicadas ao componente.
|**Componentes []. Valor**|O valor a ser extraído e representado no componente. Para obter mais informações, consulte [modelos de tipo de valor](#valuetypes).

### <a name="value-type-templates"></a>Modelos de tipo de valor <a name="valuetypes"></a>
Abaixo estão os modelos de tipo de valor com suporte no momento. No futuro, outros modelos podem ser adicionados.
#### <a name="sampleddata"></a>SampledData
Representa o tipo de dados [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR. As medidas de observação são gravadas em um fluxo de valor, começando em um ponto no tempo e incrementando para frente usando o período definido. Se nenhum valor estiver presente, um `E` será gravado no fluxo de dados. Se o período for de tal forma que mais dois valores ocupem a mesma posição no fluxo de dados, o valor mais recente será usado. A mesma lógica é aplicada quando uma observação usando o SampledData é atualizada.

| Propriedade | Descrição 
| --- | ---
|**Defaultperiod**|O período padrão em milissegundos a ser usado. 
|**Unidade**|A unidade a ser definida na origem do SampledData. 

#### <a name="quantity"></a>Quantidade
Representa o tipo de dados de [quantidade](http://hl7.org/fhir/datatypes.html#Quantity) FHIR. Se mais de um valor estiver presente no agrupamento, somente o primeiro valor será usado. Quando chegar um novo valor que mapeia para a mesma observação, ele substituirá o valor antigo.

| Propriedade | Descrição 
| --- | --- 
|**Unidade**| Representação de unidade.
|**Código**| Forma codificada da unidade.
|**System**| Sistema que define o formato de unidade codificado.

### <a name="codeableconcept"></a>CodeableConcept
Representa o tipo de dados [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR. O valor real não é usado.

| Propriedade | Descrição 
| --- | --- 
|**Text**|Representação de texto sem formatação. 
|**Códigos**|Uma ou mais [codificações](http://hl7.org/fhir/datatypes-definitions.html#coding) a serem aplicadas à observação criada.
|**Códigos []. Auto-completar**|O código para a [codificação](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Códigos []. Sistema**|O sistema para a [codificação](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Códigos []. Display**|A exibição para a [codificação](http://hl7.org/fhir/datatypes-definitions.html#coding).

### <a name="examples"></a>Exemplos
**Taxa de coração-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**Etapas-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**Pressão de sangue – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**Pressão de sangue – quantidade**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**Dispositivo removido-CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>Próximas etapas

Confira as perguntas frequentes sobre o conector do Azure IoT para FHIR (versão prévia).

>[!div class="nextstepaction"]
>[Azure IoT Connector para FHIR FAQs](fhir-faq.md)

*No portal do Azure, o Conector IoT do Azure para FHIR é chamado de Conector IoT (versão prévia). FHIR é uma marca registrada da HL7, usada com permissão da HL7.