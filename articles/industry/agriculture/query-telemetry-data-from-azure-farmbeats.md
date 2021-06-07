---
title: Consultar dados telemétricos ingeridos
description: Este artigo descreve como consultar dados de telemetria ingeridos.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: b9067e2f78c8098d4a21263ac89caf03da631274
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92677486"
---
# <a name="query-ingested-telemetry-data"></a>Consultar dados telemétricos ingeridos

Este artigo descreve como consultar dados de sensor ingeridos do Azure FarmBeats.

A ingestão de dados de recursos de IoT (Internet das Coisas), como dispositivos e sensores, é um cenário comum no FarmBeats. Você cria metadados para dispositivos e sensores e, em seguida, ingeri os dados históricos para FarmBeats em um formato canônico. Depois que os dados do sensor estiverem disponíveis no FarmBeats Datahub, podemos consultar o mesmo para gerar informações acionáveis ou modelos de compilação.

## <a name="before-you-begin"></a>Antes de começar

Antes de prosseguir com este artigo, verifique se você instalou os dados de telemetria do sensor FarmBeats e ingerido de seus dispositivos IoT para FarmBeats.

Para ingerir dados de telemetria do sensor, visite [dados de telemetria históricos de ingestão](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Antes de continuar, você também precisa garantir que esteja familiarizado com as APIs REST do FarmBeats, pois você consultará a telemetria ingerida usando as APIs. Para obter mais informações sobre APIs do FarmBeats, consulte [APIs REST do FarmBeats](rest-api-in-azure-farmbeats.md). **Certifique-se de que você possa fazer solicitações de API para o ponto de extremidade FarmBeats Datahub**.

## <a name="query-ingested-sensor-telemetry-data"></a>Consultar dados de telemetria do sensor ingerido

Há duas maneiras de acessar e consultar dados de telemetria do FarmBeats:

- API e
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Consultar usando a API REST

Siga as etapas para consultar os dados de telemetria do sensor ingerido usando as APIs REST do FarmBeats:

1. Identifique o sensor no qual você está interessado. Você pode fazer isso fazendo uma solicitação GET na API/sensor.

> [!NOTE]
> A **ID** e a **sensorModelId** do objeto de sensor interessado.

2. Crie uma GET/{id} na API/SensorModel para o **sensorModelId** , conforme observado na etapa 1. O "modelo de sensor" tem todos os metadados e detalhes sobre a telemetria ingerida do sensor. Por exemplo, a **medida de sensor** dentro do objeto de modelo de **sensor** tem detalhes sobre quais medidas o sensor está enviando e em quais tipos e unidades. Por exemplo,

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Anote a resposta da chamada GET/{id} para o modelo de sensor.

3. Faça uma chamada POST na API/Telemetry com a seguinte carga de entrada

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. A resposta da API/Telemetry terá uma aparência semelhante a esta:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
Na resposta de exemplo acima, a telemetria do sensor consultado fornece dados para dois carimbos de data/hora junto com o nome da medida ("moist_soil_last") e os valores da telemetria relatada nos dois carimbos de data/hora. Você precisará consultar o modelo de sensor associado (conforme descrito na etapa 2) para interpretar o tipo e a unidade dos valores relatados.

### <a name="query-using-azure-time-series-insights-tsi"></a>Consulta usando Azure Time Series Insights (TSI)

O FarmBeats utiliza [Azure Time Series insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) para ingerir, armazenar, consultar e Visualizar dados em escala de IOT – dados altamente contextuais e otimizados para a série temporal.

Os dados de telemetria são recebidos em um EventHub e, em seguida, processados e enviados por push para um ambiente de TSI no grupo de recursos FarmBeats. Em seguida, os dados podem ser consultados diretamente do TSI. Para obter mais informações, consulte a [documentação do TSI](../../time-series-insights/time-series-insights-explorer.md)

Siga as etapas para visualizar os dados em TSI:

1. Vá para o **portal do Azure**  >  **FarmBeats DataHub grupo de recursos** > selecione ambiente de **Time Series insights** (TSI-XXXX) > políticas de **acesso a dados**. Adicionar usuário com acesso de leitor ou colaborador.
2. Vá para a página **visão geral** do ambiente de **Time Series insights** (TSI-XXXX) e selecione a **URL do time Series insights Explorer**. Agora você poderá visualizar a telemetria ingerida.

Além de armazenar, consultar e visualizar a telemetria, o TSI também permite a integração a um painel de Power BI. Para obter mais informações, consulte [aqui]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Próximas etapas

Agora você consultou os dados do sensor de sua instância do FarmBeats do Azure. Agora, saiba como [gerar mapas](generate-maps-in-azure-farmbeats.md#generate-maps) para seus farms.