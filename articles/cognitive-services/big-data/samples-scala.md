---
title: Exemplos do Scala de Serviços Cognitivos para Big Data
description: Use os Serviços Cognitivos para Azure Databricks com o objetivo de executar um pipeline do MMLSpark para Big Data.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: c47aa803774343b39efeabe3452f1b256cc64c0d
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363265"
---
# <a name="quick-examples"></a>Exemplos rápidos

Os snippets a seguir estão prontos para execução e ajudarão você a começar a usar Serviços Cognitivos no Spark. Os exemplos a seguir estão em Scala.

Os exemplos usam estes Serviços Cognitivos:

- Análise de Texto – obtenha a opinião (ou o humor) de um conjunto de frases.
- Pesquisa Visual Computacional – obtenha as marcas (descrições de uma palavra) associadas a um conjunto de imagens.
- Pesquisa de Imagem do Bing – pesquisa na Web por imagens relacionadas a uma consulta em idioma natural.
- Conversão de fala em texto – transcreva arquivos de áudio para extrair transcrições baseadas em texto.
- Detector de Anomalias – detectar anomalias em dados de série temporal.

## <a name="prerequisites"></a>Pré-requisitos

1. Siga as etapas em [Introdução](getting-started.md) para configurar seu ambiente do Azure Databricks e Serviços Cognitivos. Este tutorial incluirá como instalar o MMLSpark e como criar seu cluster do Spark no Databricks.
1. Depois de criar um notebook no Azure Databricks, copie o **Código compartilhado** abaixo e cole-o em uma nova célula no seu notebook.
1. Escolha um exemplo de serviço abaixo e copie cole-o em uma segunda nova célula do notebook.
1. Substitua qualquer um dos espaços reservados da chave de assinatura de serviço pela sua chave.
1. Escolha o botão Executar (ícone de triângulo) no canto superior direito da célula e selecione **Célula de Execução**.
1. Exibir resultados em uma tabela abaixo da célula.

## <a name="shared-code"></a>Código compartilhado
Para começar, adicione este código ao seu projeto:

```scala
import com.microsoft.ml.spark.cognitive._
import spark.implicits._ 

val serviceKey = "ADD-YOUR-SUBSCRIPTION-KEY"
val location = "eastus"
```

## <a name="text-analytics"></a>Análise de texto

O serviço [Análise de Texto](../text-analytics/index.yml) fornece vários algoritmos para extrair Intelligent Insights do texto. Por exemplo, podemos encontrar a sentimentos do texto de entrada fornecido. O serviço retornará uma pontuação entre `0.0` e `1.0` em que as pontuações baixas indicam um sentimento negativo e pontuações altas indicam um sentimento positivo.  O exemplo a seguir usa três frases simples e retorna a pontuação de sentimentos para cada uma.

```scala
import org.apache.spark.sql.functions.col

val df = Seq(
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US")
).toDF("text", "language")

val sentiment = new TextSentiment()
    .setTextCol("text")
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language")

display(sentiment.transform(df).select(col("text"), col("sentiment")(0).getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>Resultado esperado

| text                                      | sentimento                                             |
|:------------------------------------------|:------------------------------------------------------|
| Estou tão feliz hoje, está ensolarado!           | 0,9789592027664185                                    |
| Estou frustrado com esse trânsito da horas do rush | 0,023795604705810547                                  |
| Os serviços cognitivos no Spark não são ruins  | 0,8888956308364868                                    |

## <a name="computer-vision"></a>Visual Computacional

A [Pesquisa Visual Computacional](../computer-vision/index.yml) analisa as imagens para identificar estruturas, como rostos, objetos e descrições de idioma natural.
Neste exemplo, marcamos uma lista de imagens. Marcas são descrições de uma palavra de coisas na imagem, como objetos reconhecíveis, pessoas, cenários e ações.

```scala
// Create a dataframe with the image URLs
val df = Seq(
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg")
).toDF("image")

// Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
val analysis = new AnalyzeImage()
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setVisualFeatures(Seq("Categories","Color","Description","Faces","Objects","Tags"))
    .setOutputCol("results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

// Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select(col("image"), col("results").getItem("tags").getItem("name")).alias("results")))

// Uncomment for full results with all visual feature requests
//display(analysis.transform(df).select(col("image"), col("results")))
``` 

### <a name="expected-result"></a>Resultado esperado

| image | marcas |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skating' 'person' 'man' 'outdoor' 'riding' 'sport' 'skateboard' 'young' 'board' 'shirt' 'air' 'black' 'park' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'fence' 'wooden' 'small' 'brown' 'building' 'sitting' 'front' 'bench' 'standing' 'table' 'walking' 'board' 'beach' 'white' 'holding' 'bridge' 'track']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'grass' 'house' 'building' 'old' 'home' 'front' 'small' 'church' 'stone' 'large' 'grazing' 'yard' 'green' 'sitting' 'leading' 'sheep' 'brick' 'bench' 'street' 'white' 'country' 'clock' 'sign' 'parked' 'field' 'standing' 'garden' 'water' 'red' 'horse' 'man' 'tall' 'fire' 'group'] 

## <a name="bing-image-search"></a>Pesquisa de Imagem do Bing

A [Pesquisa de Imagem do Bing](../bing-image-search/overview.md) pesquisa na Web para recuperar imagens relacionadas à consulta de idioma natural de um usuário. Neste exemplo, usamos uma consulta de texto que procura imagens com citações. Ela retorna uma lista de URLs de imagem que contêm fotos relacionadas à nossa consulta.


```scala
import org.apache.spark.ml.Pipeline

// Number of images Bing will return per query
val imgsPerBatch = 10

// A list of offsets, used to page into the search results
val df = (0 until 100).map(o => Tuple1(o*imgsPerBatch)).toSeq.toDF("offset")

// Run the Bing Image Search service with our text query
val bingSearch = new BingImageSearch()
    .setSubscriptionKey(bingSearchKey)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images")

// Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
val getUrls = BingImageSearch.getUrlTransformer("images", "url")

// This displays the full results returned, uncomment to use
// display(bingSearch.transform(bingParameters))

// Since we have two services, they are put into a pipeline
val pipeline = new Pipeline().setStages(Array(bingSearch, getUrls))

// Show the results of your search: image URLs
display(pipeline.fit(df).transform(df))
```

### <a name="expected-result"></a>Resultado esperado

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |

## <a name="speech-to-text"></a>Conversão de fala em texto

O serviço de [Conversão de fala em texto](../speech-service/index-speech-to-text.yml) converte fluxos ou arquivos de áudio falado em texto. Neste exemplo, transcrevemos dois arquivos de áudio. O primeiro arquivo é fácil de entender e o segundo é mais desafiador.

```scala
import org.apache.spark.sql.functions.col

// Create a dataframe with audio URLs, tied to the column called "url"
val df = Seq(("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav"),
             ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3")).toDF("url")

// Run the Speech-to-text service to translate the audio into text
val speechToText = new SpeechToTextSDK()
    .setSubscriptionKey(serviceKey)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked")

// Show the results of the translation
display(speechToText.transform(df).select(col("url"), col("text").getItem("DisplayText")))
```

### <a name="expected-result"></a>Resultado esperado

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | A Fala Personalizada fornece ferramentas que permitem inspecionar visualmente a qualidade do reconhecimento de um modelo comparando dados de áudio com o resultado de reconhecimento correspondente do portal de Fala Personalizada. Você pode reproduzir áudio carregado e determinar se o resultado de reconhecimento fornecido está correto. Essa ferramenta permite que você inspecione rapidamente a qualidade do modelo de conversão de fala em texto de linha de base da Microsoft ou um modelo personalizado treinado sem precisar transcrever nenhum dado de áudio. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Adicione uma verificação visual de cavalheiro Sir pensando.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Eu me ouço. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Gosto da confirmação para rádio que eu posso escutar também. |

## <a name="anomaly-detector"></a>Detector de Anomalias

O [Detector de Anomalias](../anomaly-detector/index.yml) é ótimo para detectar irregularidades em seus dados de série temporal. Neste exemplo, usamos o serviço para encontrar anomalias em toda a série temporal.

```scala
import org.apache.spark.sql.functions.{col, lit}

val anomalyKey = "84a2c303cc7e49f6a44d692c27fb9967"

val df = Seq(
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
  ).toDF("timestamp", "value").withColumn("group", lit("series1"))

// Run the Anomaly Detector service to look for irregular data
val anamolyDetector = new SimpleDetectAnomalies()
    .setSubscriptionKey(anomalyKey)
    .setLocation("eastus")
    .setTimestampCol("timestamp")
    .setValueCol("value")
    .setOutputCol("anomalies")
    .setGroupbyCol("group")
    .setGranularity("monthly")

// Show the full results of the analysis with the anomalies marked as "True"
display(anamolyDetector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
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