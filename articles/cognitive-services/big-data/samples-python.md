---
title: Serviços Cognitivos para Big Data – exemplos do Python
description: Experimente os exemplos de Serviços Cognitivos no Python para Azure Databricks executar seu pipeline do MMLSpark para Big Data.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 590ddef27315f37719da5b28c68b6c402371e986
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363248"
---
# <a name="python-samples-for-cognitive-services-for-big-data"></a>Exemplos de Python para Serviços Cognitivos para Big Data

Os snippets a seguir estão prontos para execução e ajudarão você a começar a usar Serviços Cognitivos no Spark com Python.

Os exemplos neste artigo usam estes Serviços Cognitivos:

- Análise de Texto – obtenha a opinião (ou o humor) de um conjunto de frases.
- Pesquisa Visual Computacional – obtenha as marcas (descrições de uma palavra) associadas a um conjunto de imagens.
- Pesquisa de Imagem do Bing – pesquisa na Web por imagens relacionadas a uma consulta em idioma natural.
- Conversão de fala em texto – transcreva arquivos de áudio para extrair transcrições baseadas em texto.
- Detector de Anomalias – detectar anomalias em dados de série temporal.

## <a name="prerequisites"></a>Pré-requisitos

1. Siga as etapas em [Introdução](getting-started.md) para configurar seu ambiente do Azure Databricks e Serviços Cognitivos. Este tutorial mostra como instalar o MMLSpark e como criar seu cluster do Spark no Databricks.
1. Depois de criar um notebook no Azure Databricks, copie o **Código compartilhado** abaixo e cole-o em uma nova célula no seu notebook.
1. Escolha um exemplo de serviço abaixo e copie cole-o em uma segunda nova célula do notebook.
1. Substitua qualquer um dos espaços reservados da chave de assinatura de serviço pela sua chave.
1. Escolha o botão Executar (ícone de triângulo) no canto superior direito da célula e selecione **Célula de Execução**.
1. Exibir resultados em uma tabela abaixo da célula.

## <a name="shared-code"></a>Código compartilhado

Para começar, precisaremos adicionar este código ao projeto:

```python
from mmlspark.cognitive import *

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key = "ADD_YOUR_SUBSCRIPION_KEY"
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY"
# An Anomaly Dectector subscription key
anomaly_key = "ADD_YOUR_SUBSCRIPION_KEY"

# Validate the key
assert service_key != "ADD_YOUR_SUBSCRIPION_KEY"
```    

## <a name="text-analytics-sample"></a>Exemplo de Análise de Texto

O serviço [Análise de Texto](../text-analytics/index.yml) fornece vários algoritmos para extrair Intelligent Insights do texto. Por exemplo, podemos encontrar a sentimentos do texto de entrada fornecido. O serviço retornará uma pontuação entre 0,0 e 1,0 em que as pontuações baixas indicam um sentimento negativo e pontuações altas indicam um sentimento positivo.  Este exemplo usa três frases simples e retorna o sentimento para cada uma.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format
display(sentiment.transform(df).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-result"></a>Resultado esperado

| text                                      | sentimento                                             |
|:------------------------------------------|:------------------------------------------------------|
| Estou tão feliz hoje, está ensolarado!           | positivo                                              |
| Estou frustrado com esse trânsito da horas do rush | negativo                                              |
| Os serviços cognitivos no Spark não são ruins  | positivo                                              |

## <a name="computer-vision-sample"></a>Exemplo de Pesquisa Visual Computacional

A [Pesquisa Visual Computacional](../computer-vision/index.yml) analisa as imagens para identificar estruturas, como rostos, objetos e descrições de idioma natural. Neste exemplo, marcamos uma lista de imagens. Marcas são descrições de uma palavra de coisas na imagem, como objetos reconhecíveis, pessoas, cenários e ações.

```python

# Create a dataframe with the image URLs
df = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select("image", "analysis_results.description.tags"))
```

### <a name="expected-result"></a>Resultado esperado

| image | marcas |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skating' 'person' 'man' 'outdoor' 'riding' 'sport' 'skateboard' 'young' 'board' 'shirt' 'air' 'black' 'park' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'fence' 'wooden' 'small' 'brown' 'building' 'sitting' 'front' 'bench' 'standing' 'table' 'walking' 'board' 'beach' 'white' 'holding' 'bridge' 'track']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'grass' 'house' 'building' 'old' 'home' 'front' 'small' 'church' 'stone' 'large' 'grazing' 'yard' 'green' 'sitting' 'leading' 'sheep' 'brick' 'bench' 'street' 'white' 'country' 'clock' 'sign' 'parked' 'field' 'standing' 'garden' 'water' 'red' 'horse' 'man' 'tall' 'fire' 'group']


## <a name="bing-image-search-sample"></a>Exemplo de Pesquisa de Imagem do Bing

A [Pesquisa de Imagem do Bing](../bing-image-search/overview.md) pesquisa na Web para recuperar imagens relacionadas à consulta de idioma natural de um usuário. Neste exemplo, usamos uma consulta de texto que procura imagens com citações. Ela retorna uma lista de URLs de imagem que contêm fotos relacionadas à nossa consulta.

```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 10
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(100)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bing_search_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")

# This displays the full results returned, uncomment to use
# display(bingSearch.transform(bingParameters))

# Since we have two services, they are put into a pipeline
pipeline = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
display(pipeline.transform(bingParameters))
```

### <a name="expected-result"></a>Resultado esperado

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |


## <a name="speech-to-text-sample"></a>Exemplo de conversão de fala em texto
O serviço de [Conversão de fala em texto](../speech-service/index-speech-to-text.yml) converte fluxos ou arquivos de áudio falado em texto. Neste exemplo, transcrevemos dois arquivos de áudio. O primeiro arquivo é fácil de entender e o segundo é mais desafiador.

```python

# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",),
                           ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```

### <a name="expected-result"></a>Resultado esperado

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | A Fala Personalizada fornece ferramentas que permitem inspecionar visualmente a qualidade do reconhecimento de um modelo comparando dados de áudio com o resultado de reconhecimento correspondente do portal de Fala Personalizada. Você pode reproduzir áudio carregado e determinar se o resultado de reconhecimento fornecido está correto. Essa ferramenta permite que você inspecione rapidamente a qualidade do modelo de conversão de fala em texto de linha de base da Microsoft ou um modelo personalizado treinado sem precisar transcrever nenhum dado de áudio. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Adicione uma verificação visual de cavalheiro Sir pensando.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Eu me ouço. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Gosto da confirmação para rádio que eu posso escutar também. |


## <a name="anomaly-detector-sample"></a>Exemplo de Detector de Anomalias

O [Detector de Anomalias](../anomaly-detector/index.yml) é ótimo para detectar irregularidades em seus dados de série temporal. Neste exemplo, usamos o serviço para encontrar anomalias em toda a série temporal.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomaly_key)
  .setLocation("eastus")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>Resultado esperado

| timestamp            |   value | isAnomaly   |
|:---------------------|--------:|:------------|
| 1972-01-01T00:00:00Z |     826 | Falso       |
| 1972-02-01T00:00:00Z |     799 | Falso       |
| 1972-03-01T00:00:00Z |     890 | Falso       |
| 1972-04-01T00:00:00Z |     900 | Falso       |
| 1972-05-01T00:00:00Z |     766 | Falso       |
| 1972-06-01T00:00:00Z |     805 | Falso       |
| 1972-07-01T00:00:00Z |     821 | Falso       |
| 1972-08-01T00:00:00Z |   20000 | True        |
| 1972-09-01T00:00:00Z |     883 | Falso       |
| 1972-10-01T00:00:00Z |     898 | Falso       |
| 1972-11-01T00:00:00Z |     957 | Falso       |
| 1972-12-01T00:00:00Z |     924 | Falso       |
| 1973-01-01T00:00:00Z |     881 | Falso       |
| 1973-02-01T00:00:00Z |     837 | Falso       |
| 1973-03-01T00:00:00Z |    9000 | True        |

## <a name="arbitrary-web-apis"></a>APIs da Web arbitrárias

Com HTTP no Spark, qualquer serviço Web pode ser usado em seu pipeline de Big Data. Neste exemplo, usamos a API [World Bank](http://api.worldbank.org/v2/country/) para obter informações sobre vários países em todo o mundo.

```python
from requests import Request
from mmlspark.io.http import HTTPTransformer, http_udf
from pyspark.sql.functions import udf, col

# Use any requests from the python requests library
def world_bank_request(country):
  return Request("GET", "http://api.worldbank.org/v2/country/{}?format=json".format(country))

# Create a dataframe with spcificies which countries we want data on
df = (spark.createDataFrame([("br",),("usa",)], ["country"])
  .withColumn("request", http_udf(world_bank_request)(col("country"))))

# Much faster for big data because of the concurrency :)
client = (HTTPTransformer()
      .setConcurrency(3)
      .setInputCol("request")
      .setOutputCol("response"))

# Get the body of the response
def get_response_body(resp):
  return resp.entity.content.decode()

# Show the details of the country data returned
display(client.transform(df).select("country", udf(get_response_body)(col("response")).alias("response")))
```

### <a name="expected-result"></a>Resultado esperado

| country   | resposta |
|:----------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| br | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"BRA","iso2Code":"BR","name":"Brazil","region":{"id":"LCN","iso2code":"ZJ","value":"Latin America & Caribbean "},"adminregion":{"id":"LAC","iso2code":"XJ","value":"Latin America & Caribbean (excluding high income)"},"incomeLevel":{"id":"UMC","iso2code":"XT","value":"Upper middle income"},"lendingType":{"id":"IBD","iso2code":"XF","value":"IBRD"},"capitalCity":"Brasilia","longitude":"-47.9292","latitude":"-15.7801"}]] |
| usa  | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"USA","iso2Code":"US","name":"United States","region":{"id":"NAC","iso2code":"XU","value":"North America"},"adminregion":{"id":"","iso2code":"","value":""},"incomeLevel":{"id":"HIC","iso2code":"XD","value":"High income"},"lendingType":{"id":"LNX","iso2code":"XX","value":"Not classified"},"capitalCity":"Washington D.C.","longitude":"-77.032","latitude":"38.8895"}]] |

## <a name="see-also"></a>Confira também

* [Receita: Detecção de Anomalias](./recipes/anomaly-detection.md)
* [Receita: Gerenciador de Arte](./recipes/art-explorer.md)