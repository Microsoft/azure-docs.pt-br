---
title: 'Receita: exploração de arte inteligente com os serviços cognitivas para Big data'
titleSuffix: Azure Cognitive Services
description: Esta receita mostra como criar um banco de dados de arte pesquisável usando Azure Search e MMLSpark.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a65ff28a38e42e05844063a330c0325f16b2247
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363282"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>Receita: exploração de arte inteligente com os serviços cognitivas para Big data

Neste exemplo, usaremos os serviços cognitivas para Big data para adicionar anotações inteligentes à coleção de acesso aberto do Museu de arte metropolitano (atendido). Isso nos permitirá criar um mecanismo de pesquisa inteligente usando Azure Search mesmo sem anotações manuais. 

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter uma chave de assinatura para Pesquisa Visual Computacional e Pesquisa Cognitiva. Siga as instruções em [criar uma conta de serviços cognitivas](../../cognitive-services-apis-create-account.md) para assinar pesquisa Visual computacional e obter sua chave.
  > [!NOTE]
  > Para obter informações sobre preços, consulte [pesquisa cognitiva do Azure](https://azure.microsoft.com/services/search/#pricing).

## <a name="import-libraries"></a>Importar bibliotecas

Execute o comando a seguir para importar bibliotecas para esta receita.

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>Configurar chaves de assinatura

Execute o comando a seguir para configurar variáveis para chaves de serviço. Insira suas chaves de assinatura para Pesquisa Visual Computacional e Pesquisa Cognitiva do Azure.

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>Ler os dados

Execute o comando a seguir para carregar dados da coleção de acesso aberta do atendido.

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>Analisar as imagens

Execute o comando a seguir para usar Pesquisa Visual Computacional na coleção de trabalhos artísticos de acesso aberto do atendido. Como resultado, você obterá recursos visuais de arte.

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>Criar o índice de pesquisa

Execute o comando a seguir para gravar os resultados em Azure Search para criar um mecanismo de pesquisa de trabalhos artísticos com metadados aprimorados do Pesquisa Visual Computacional.

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>Consultar o índice de pesquisa

Execute o comando a seguir para consultar o índice de Azure Search.

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>Próximas etapas

Saiba como usar [Serviços cognitivas para Big data para detecção de anomalias](anomaly-detection.md).