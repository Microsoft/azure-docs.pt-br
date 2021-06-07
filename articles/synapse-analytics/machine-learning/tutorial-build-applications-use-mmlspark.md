---
title: 'Tutorial: criar aplicativos de Machine Learning usando o Microsoft Machine Learning para Apache Spark (versão prévia)'
description: Saiba como usar o Microsoft Machine Learning para Apache Spark para criar aplicativos de machine learning no Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruixinxu
ms.author: ruxu
ms.openlocfilehash: 928e2ef8b373626a91a291b1798f3ebb7ef290e8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608826"
---
# <a name="tutorial-build-machine-learning-applications-using-microsoft-machine-learning-for-apache-spark-preview"></a>Tutorial: criar aplicativos de Machine Learning usando o Microsoft Machine Learning para Apache Spark (versão prévia)

Neste artigo, você aprenderá a usar o [MMLSpark](https://github.com/Azure/mmlspark) (Microsoft Machine Learning para Apache Spark) para criar aplicativos de machine learning. O MMLSpark expande a solução de machine learning distribuída do Apache Spark adicionando muitas ferramentas de aprendizado profundo e ciência de dados, como [Serviços Cognitivos do Azure](../../cognitive-services/big-data/cognitive-services-for-big-data.md), [OpenCV](https://opencv.org/), [LightGBM](https://github.com/Microsoft/LightGBM) e muito mais.  O MMLSpark permite que você crie modelos preditivos e analíticos eficientes e altamente escalonáveis de várias fontes de dados do Spark.
O Synapse Spark fornece bibliotecas MMLSpark internas, incluindo:

- [Vowpal Wabbit](https://github.com/Azure/mmlspark/blob/master/docs/vw.md) – serviços de biblioteca para machine learning para habilitar a Análise de Texto como análise de sentimento em tweets.
- [Serviços Cognitivos no Spark](https://github.com/Azure/mmlspark/blob/master/docs/cogsvc.md) – para combinar o recurso de Serviços Cognitivos do Azure em pipelines do SparkML a fim de derivar o design da solução para serviços cognitivos de modelagem de dados, como a detecção de anomalias.
- [LightBGM](https://github.com/Azure/mmlspark/blob/master/docs/lightgbm.md) – LightGBM é uma estrutura de gradient boosting que usa algoritmos de aprendizado baseados em árvore. Ele foi projetado para ser distribuído e oferecer maior eficiência.
- KNN condicional– modelos de KNN escalonáveis com consultas condicionais.
- [HTTP no Spark](https://github.com/Azure/mmlspark/blob/master/docs/http.md) – habilita a orquestração de microsserviços distribuídos na integração do Spark e a acessibilidade baseada em protocolo HTTP.

Este tutorial aborda os exemplos que usam os Serviços Cognitivos do Azure no MMLSpark para 

- Análise de Texto – obtenha a opinião (ou o humor) de um conjunto de frases.
- Pesquisa Visual Computacional – obtenha as marcas (descrições de uma palavra) associadas a um conjunto de imagens.
- Pesquisa de Imagem do Bing – pesquisa na Web por imagens relacionadas a uma consulta em idioma natural.
- Detector de Anomalias – detectar anomalias em dados de série temporal.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos 

- [Workspace do Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento do Azure Data Lake Storage Gen2 configurada como o armazenamento padrão. Você precisa ser *Colaborador de Dados do Storage Blob* do sistema de arquivos Data Lake Storage Gen2 com o qual você trabalha.
- Pool do Spark no workspace do Azure Synapse Analytics. Para obter detalhes, confira [Criar um Pool do Spark no Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Etapas de pré-configuração descritas no tutorial [Configurar Serviços Cognitivos no Azure Synapse](./tutorial-configure-cognitive-services-synapse.md).


## <a name="get-started"></a>Introdução
Para começar, importe o mmlspark e configure as chaves de serviço. 

```python
import mmlspark
mmlspark.__spark_package_version__ # current version: 1.0.0-rc3-6-a862d6b1-SNAPSHOT

from mmlspark.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY" 
# An Anomaly Dectector subscription key
anomaly_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# Your linked key vault for Synapse workspace
key_vault = "YOUR_KEY_VAULT_NAME"


cognitive_service_key = mssparkutils.credentials.getSecret(key_vault, service_key)
bingsearch_service_key = mssparkutils.credentials.getSecret(key_vault, bing_search_key)
anomalydetector_key = mssparkutils.credentials.getSecret(key_vault, anomaly_key)

```


## <a name="text-analytics-sample"></a>Exemplo de análise de texto

O serviço [Análise de Texto](../../cognitive-services/text-analytics/index.yml) fornece vários algoritmos para extrair Intelligent Insights do texto. Por exemplo, podemos encontrar a sentimentos do texto de entrada fornecido. O serviço retornará uma pontuação entre 0,0 e 1,0 em que as pontuações baixas indicam um sentimento negativo e pontuações altas indicam um sentimento positivo. Este exemplo usa três frases simples e retorna o sentimento para cada uma.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>Resultados esperados

|text | sentimento|
|--|--|
| Estou frustrado com esse trânsito da hora do rush. | negativo |
| isso é um cachorro | neutro |
| Estou tão feliz hoje, está ensolarado! | positivo |

## <a name="computer-vision-sample"></a>Exemplo de Pesquisa Visual Computacional
A [Pesquisa Visual Computacional](../../cognitive-services/computer-vision/index.yml) analisa as imagens para identificar estruturas, como rostos, objetos e descrições de idioma natural. Neste exemplo, marcamos a imagem a seguir. Marcas são descrições de uma palavra de coisas na imagem, como objetos reconhecíveis, pessoas, cenários e ações.


![image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>Resultados esperados

|image | marcas|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [patinação, pessoa, homem, ar livre, patinando, esporte, skate, jovem, prancha, camisa, ar, parque, garoto, lado, pulando, rampa, manobra, fazendo, voando] |

## <a name="bing-image-search-sample"></a>Exemplo da Pesquisa de Imagem do Bing
A [Pesquisa de Imagem do Bing](../../cognitive-services/bing-image-search/overview.md) pesquisa na Web para recuperar imagens relacionadas à consulta de idioma natural de um usuário. Neste exemplo, usamos uma consulta de texto que procura imagens com citações. Ela retorna uma lista de URLs de imagem que contêm fotos relacionadas à nossa consulta.


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>Resultados esperados

|image | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>Exemplo de Detector de Anomalias

O [Detector de Anomalias](../../cognitive-services/anomaly-detector/index.yml) é ótimo para detectar irregularidades em seus dados de série temporal. Neste exemplo, usamos o serviço para encontrar anomalias em toda a série temporal.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>Resultados esperados

|timestamp | value | isAnomaly |
|--|--|--|
| 1972-01-01T00:00:00Z|826,0|false|
|1972-02-01T00:00:00Z|799,0|false|
|1972-03-01T00:00:00Z|890,0|false|
|1972-04-01T00:00:00Z|900,0|false|
|1972-05-01T00:00:00Z|766,0|false|
|1972-06-01T00:00:00Z|805,0|false|
|1972-07-01T00:00:00Z|821,0|false|
|1972-08-01T00:00:00Z|20000,0|true|
|1972-09-01T00:00:00Z|883,0|false|
|1972-10-01T00:00:00Z|898,0|false|
|1972-11-01T00:00:00Z|957,0|false|
|1972-12-01T00:00:00Z|924,0|false|
|1973-01-01T00:00:00Z|881,0|false|
|1973-02-01T00:00:00Z|837,0|false|
|1973-03-01T00:00:00Z|9000,0|true|

## <a name="next-steps"></a>Próximas etapas

* [Confira os notebooks de exemplo do Azure Synapse](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks) 
* [Repositório do GitHub MMLSpark](https://github.com/Azure/mmlspark)