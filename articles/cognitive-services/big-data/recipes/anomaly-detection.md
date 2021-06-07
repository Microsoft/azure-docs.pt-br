---
title: 'Receita: manutenção preditiva com os serviços cognitivas para Big data'
titleSuffix: Azure Cognitive Services
description: Este guia de início rápido mostra como executar a detecção de anomalias distribuídas com os serviços cognitivas para Big data
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a583d74ae0bf421a7a863a65442d250489a2f8f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775384"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>Receita: manutenção preditiva com os serviços cognitivas para Big data

Esta receita mostra como você pode usar o Azure Synapse Analytics e serviços cognitivas em Apache Spark para manutenção preditiva de dispositivos IoT. Acompanharemos o exemplo de [link CosmosDB e Synapse](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) . Para manter as coisas simples, nesta receita, vamos ler os dados diretamente de um arquivo CSV em vez de obter dados transmitidos por meio do link CosmosDB e Synapse. É altamente recomendável que você examine o exemplo de link Synapse.

## <a name="hypothetical-scenario"></a>Cenário hipotético

O cenário hipotético é uma planta de energia, em que os dispositivos IoT estão monitorando [turbinas de vapor](https://en.wikipedia.org/wiki/Steam_turbine). A coleção IoTSignals tem dados de rotações por minuto (RPM) e megawatts (MW) para cada turbina. Os sinais de turbinas de vapor estão sendo analisados e os sinais anormais são detectados.

Pode haver exceções nos dados em frequência aleatória. Nessas situações, os valores de RPM aumentarão e a saída de MW será desativada, para proteção de circuito. A ideia é Ver os dados que variam ao mesmo tempo, mas com sinais diferentes.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* [Espaço de trabalho do Azure Synapse](../../../synapse-analytics/quickstart-create-workspace.md) configurado com um [pool de Apache Spark sem servidor](../../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

## <a name="setup"></a>Instalação

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso do Detector de Anomalias

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso para o tradutor usando o [portal do Azure](../../cognitive-services-apis-create-account.md) ou [CLI do Azure](../../cognitive-services-apis-create-account-cli.md). Você também pode:

- Exiba um recurso existente no  [portal do Azure](https://portal.azure.com/).

Anote o ponto de extremidade e a chave para esse recurso, você precisará dele neste guia.

## <a name="enter-your-service-keys"></a>Insira suas chaves de serviço

Vamos começar adicionando a chave e o local.

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>Ler dados em um dataframe

Em seguida, vamos ler o arquivo IoTSignals em um dataframe. Abra um novo bloco de anotações em seu espaço de trabalho Synapse e crie um dataframe a partir do arquivo.

```python
df_signals = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Executar a detecção de anomalias usando serviços cognitivas no Spark

O objetivo é encontrar instâncias em que os sinais dos dispositivos IoT estavam gerando valores anormais para que possamos ver quando algo está errado e fazer a manutenção preditiva. Para fazer isso, vamos usar o detector de anomalias no Spark:

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

Vamos dar uma olhada nos dados:

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

Essa célula deve produzir um resultado semelhante a:

| timestamp           |   value | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | desenvolvimento-7      | Falso       |
| 2020-05-01 18:33:52 |    2976 | desenvolvimento-7      | Falso       |
| 2020-05-01 18:33:53 |    2714 | desenvolvimento-7      | Falso       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>Visualizar anomalias para um dos dispositivos

IoTSignals.csv tem sinais de vários dispositivos IoT. Vamos nos concentrar em um dispositivo específico e visualizar as saídas anormais do dispositivo.

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

Agora que criamos um dataframe que representa as anomalias de um dispositivo específico, podemos Visualizar essas anomalias:

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

Se for bem-sucedido, sua saída terá a seguinte aparência:

![Gráfico de detector de anomalias](../media/anomaly-output.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como fazer a manutenção preditiva em escala com os serviços cognitivas do Azure, o Azure Synapse Analytics e o Azure CosmosDB. Para obter mais informações, consulte a amostra completa no [GitHub](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples).
