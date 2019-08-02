---
title: 'Tutorial: Detecção de anomalias nos dados de streaming usando o Azure Databricks'
titleSuffix: Azure Cognitive Services
description: Use a API do Detector de Anomalias e o Azure Databricks para monitorar anormalidades em seus dados.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: aahi
ms.openlocfilehash: 8d3f5d0e10fadd31fd8bde77339b872c1b90451f
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721463"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Tutorial: Detecção de anomalias nos dados de streaming usando o Azure Databricks

O [Azure Databricks](https://azure.microsoft.com/services/databricks/) é um serviço analítico rápido, fácil e colaborativo baseado no Apache Spark. A API do Detector de Anomalias, parte dos Serviços Cognitivos do Azure, fornece uma maneira de monitorar seus dados de série temporal. Use este tutorial para executar a detecção de anomalias em um fluxo de dados quase em tempo real usando o Azure Databricks. Você ingerirá dados do Twitter usando os Hubs de Eventos do Azure e os importará no Azure Databricks usando o conector dos Hubs de Eventos do Spark. Depois disso, você usará a API para detectar anomalias nos dados transmitidos. 

A ilustração a seguir mostra o fluxo do aplicativo:

![Azure Databricks com Hubs de Eventos e Serviços Cognitivos](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks com Hubs de Eventos e Serviços Cognitivos")

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Criar um workspace do Azure Databricks
> * Criar um cluster Spark no Azure Databricks
> * Criar um aplicativo do Twitter para acessar dados de streaming
> * Criar blocos de notas no Azure Databricks
> * Anexar bibliotecas para Hubs de Eventos e API do Twitter
> * Criar um recurso do Detector de Anomalias e recuperar a chave de acesso
> * Enviar tweets para Hubs de Eventos
> * Ler tweets de Hubs de Eventos
> * Executar a detecção de anomalias em tweets

> [!Note]
> Este tutorial apresenta uma abordagem para implementar a [arquitetura da solução](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) recomendada para a API do Detector de Anomalias.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!Note]
> Este tutorial não pode ser concluído com uma chave de avaliação gratuita da API do Detector de Anomalias. Para usar uma conta gratuita para criar o cluster do Azure Databricks, antes de criar o cluster, vá até o seu perfil e altere sua assinatura para **pré-pago**. Para saber mais, confira [Conta gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Um [namespace dos Hubs de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) e um hub de eventos.

- A [cadeia de conexão](../../../event-hubs/event-hubs-get-connection-string.md) para acessar o namespace dos Hubs de Eventos. A cadeia de conexão deve ter um formato semelhante a:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- O nome da política de acesso compartilhado e a chave de política dos Hubs de Eventos.

Confira o [início rápido](../../../event-hubs/event-hubs-create.md) dos Hubs de Eventos do Azure para obter informações sobre como criar um namespace e um hub de eventos.

## <a name="create-an-azure-databricks-workspace"></a>Criar um workspace do Azure Databricks

Nesta seção, você criará um workspace do Azure Databricks usando o [portal do Azure](https://portal.azure.com/).

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](../media/tutorials/azure-databricks-on-portal.png "Databricks no portal do Azure")

3. Em **Serviço do Azure Databricks**, forneça os valores a seguir para criar um workspace do Databricks:


    |Propriedade  |DESCRIÇÃO  |
    |---------|---------|
    |**Nome do workspace**     | Forneça um nome para seu workspace do Databricks        |
    |**Assinatura**     | Na lista suspensa, selecione sua assinatura do Azure.        |
    |**Grupo de recursos**     | Especifique se deseja criar um novo grupo de recursos ou usar um existente. Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. Para obter mais informações, consulte [Visão geral do Grupo de Recursos do Azure](../../../azure-resource-manager/resource-group-overview.md). |
    |**Localidade**     | Selecione **Leste dos EUA 2** ou uma das outras regiões disponíveis. Confira [Serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/) para obter a disponibilidade da região.        |
    |**Tipo de preço**     |  Escolha entre o cluster **Standard** e o **Premium**. NÃO escolha **Avaliação**. Para saber mais sobre essas camadas, confira [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Selecione **Criar**.

4. A criação do workspace leva alguns minutos. 

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster Spark no Databricks

1. No portal do Azure, vá para o workspace do Databricks que você criou e selecione **Inicializar Workspace**.

2. Você será redirecionado ao portal do Azure Databricks. No portal, selecione **Novo Cluster**.

    ![Databricks no Azure](../media/tutorials/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo Cluster**, forneça os valores para criar um cluster.

    ![Criar cluster do Databricks Spark no Azure](../media/tutorials/create-databricks-spark-cluster.png "Criar cluster do Databricks Spark no Azure")

    Aceite todos os outros valores padrão que não sejam o seguinte:

   * Insira um nome para o cluster.
   * Neste artigo, crie um cluster com o tempo de execução **5.2**. NÃO selecione o tempo de execução **5.3**.
   * Verifique se a caixa de seleção **Terminar depois de \_\_ minutos de inatividade** está marcada. Forneça uma duração (em minutos) para terminar o cluster se ele não estiver sendo usado.

     Selecione **Criar cluster**. 
4. A criação do cluster leva vários minutos. Quando o cluster está em execução, você pode anexar blocos de notas a ele e executar trabalhos do Spark.

## <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter

Para receber uma transmissão de tweets, você deve criar um aplicativo no Twitter. Siga as etapas para criar um aplicativo do Twitter e registre os valores necessários para concluir este tutorial.

1. Em um navegador da Web, vá para [Gerenciamento de Aplicativo do Twitter](https://apps.twitter.com/) e selecione **Criar Novo Aplicativo**.

    ![Criar aplicativo do Twitter](../media/tutorials/databricks-create-twitter-app.png "Criar aplicativo do Twitter")

2. Na página **Criar um aplicativo**, forneça os detalhes para o novo aplicativo e selecione **Criar seu aplicativo do Twitter**.

    ![Detalhes do aplicativo do Twitter](../media/tutorials/databricks-provide-twitter-app-details.png "Detalhes do aplicativo do Twitter")

3. Na página do aplicativo, selecione a guia **Chaves e Tokens de Acesso** e copie os valores de **Chave do Consumidor** e **Segredo do Consumidor**. Além disso, selecione **Criar meu token de acesso** para gerar os tokens de acesso. Copie os valores do **Token de Acesso** e do **Segredo do Token de Acesso**.

    ![Detalhes do aplicativo do Twitter](../media/tutorials/twitter-app-key-secret.png "Detalhes do aplicativo do Twitter")

Salve os valores recuperados do aplicativo do Twitter. Mais tarde, você precisará desses valores neste tutorial.

## <a name="attach-libraries-to-spark-cluster"></a>Anexar bibliotecas do cluster Spark

Neste tutorial, você usará as APIs do Twitter para enviar tweets aos Hubs de Eventos. Também usará o [Conector de Hubs de Eventos do Apache Spark](https://github.com/Azure/azure-event-hubs-spark) para ler e gravar dados nos Hubs de Eventos do Azure. Para usar essas APIs como parte do cluster, adicione-as como bibliotecas do Azure Databricks, depois as associe ao seu cluster Spark. As instruções a seguir mostram como adicionar as bibliotecas à pasta **Compartilhada** no workspace.

1. No workspace do Azure Databricks, selecione **Workspace**, depois clique com botão direito do mouse em **Compartilhado**. No menu de contexto, selecione **Criar** > **Biblioteca**.

   ![Caixa de diálogo Adicionar biblioteca](../media/tutorials/databricks-add-library-option.png "Caixa de diálogo Adicionar biblioteca")

2. Na página Nova Biblioteca, em **Fonte**, selecione **Maven**. Em **Coordenadas**, insira a coordenada do pacote que deseja adicionar. Estas são as coordenadas Maven para as bibliotecas usadas neste tutorial:

   * Conector dos Hubs de Eventos do Spark – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * API do Twitter – `org.twitter4j:twitter4j-core:4.0.7`

     ![Fornecer coordenadas Maven](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Fornecer coordenadas Maven")

3. Selecione **Criar**.

4. Selecione a pasta em que você adicionou a biblioteca e selecione o nome da biblioteca.

    ![Selecionar biblioteca a ser adicionada](../media/tutorials/select-library.png "Selecionar biblioteca a ser adicionada")

5. Se não houver nenhum cluster na página da biblioteca, selecione **Clusters** e execute o cluster que você criou. Aguarde até o estado mostrar 'Em execução' e, em seguida, volte à página da biblioteca.
Na página da biblioteca, selecione o cluster no qual deseja usar a biblioteca e, em seguida, selecione **Instalar**. Depois que a biblioteca for associada ao cluster com êxito, o status será alterado imediatamente para **Instalada**.

    ![Instalar a biblioteca no cluster](../media/tutorials/databricks-library-attached.png "Install library to cluster")

6. Repita essas etapas para o pacote do Twitter, `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Obter uma chave de acesso dos Serviços Cognitivos

Neste tutorial, você usará as [APIs do Detector de Anomalias dos Serviços Cognitivos do Azure](../overview.md) para executar a detecção de anomalias em um fluxo de tweets quase em tempo real. Antes de usar as APIs, é necessário criar um recurso do Detector de Anomalias no Azure e recuperar uma chave de acesso para usar as APIs do Detector de Anomalias.

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Selecione **+ Criar um recurso**.

3. No Azure Marketplace, selecione **IA + Machine Learning** > **Ver tudo** > **Serviços Cognitivos – Mais** > **Detector de Anomalias**. Se preferir, use [este link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) para acessar a caixa de diálogo **Criar** diretamente.

    ![Criar recurso do Detector de Anomalias](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Create Anomaly Detector resource")

4. Na caixa de diálogo **Criar**, forneça os seguintes valores:

    |Valor |DESCRIÇÃO  |
    |---------|---------|
    |NOME     | Um nome para o recurso do Detector de Anomalias.        |
    |Assinatura     | A assinatura do Azure à qual o recurso será associado.        |
    |Local padrão     | Uma localização do Azure.        |
    |Tipo de preço     | Um tipo de preço para o serviço. Para obter mais informações sobre os preços do Detector de Anomalias, confira a [página de preços](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Grupo de recursos     | Especifique se deseja criar um grupo de recursos ou selecionar um existente.        |


     Selecione **Criar**.

5. Depois que o recurso for criado, na guia **Visão Geral**, copie e salve a URL do **ponto de extremidade**, conforme mostrado na captura de tela. Em seguida, selecione **Mostrar chaves de acesso**.

    ![Mostrar chaves de acesso](../media/tutorials/cognitive-services-get-access-keys.png "Mostrar chaves de acesso")

6. Em **Chaves**, selecione o ícone de cópia na chave que deseja usar. Salve a chave de acesso.

    ![Copiar chaves de acesso](../media/tutorials/cognitive-services-copy-access-keys.png "Copiar chaves de acesso")

## <a name="create-notebooks-in-databricks"></a>Criar blocos de notas no Databricks

Nesta seção, você criará dois blocos de notas no workspace do Databricks com os seguintes nomes

- **SendTweetsToEventHub** – um bloco de notas de produtor que você usa para obter tweets do Twitter e transmiti-los para Hubs de Eventos.
- **AnalyzeTweetsFromEventHub** – um notebook do consumidor usado para ler os tweets dos Hubs de Eventos e executar a detecção de anomalias.

1. No workspace do Azure Databricks, selecione **Workspace** no painel esquerdo. Na lista suspensa **Workspace**, selecione **Criar**e selecione **Bloco de Anotações**.

    ![Criar bloco de notas em Databricks](../media/tutorials/databricks-create-notebook.png "Criar bloco de notas em Databricks")

2. Na caixa de diálogo **Criar Notebook**, insira **SendTweetsToEventHub** como o nome, selecione **Scala** como a linguagem e selecione o cluster Spark criado anteriormente.

    ![Criar bloco de notas em Databricks](../media/tutorials/databricks-notebook-details.png "Criar bloco de notas em Databricks")

    Selecione **Criar**.

3. Repita as etapas para criar o bloco de notas de **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Enviar tweets para Hubs de Eventos

No notebook **SendTweetsToEventHub**, cole o código a seguir e substitua o espaço reservado por valores do namespace de Hubs de Eventos e do aplicativo do Twitter criado anteriormente. Esse notebook extrai a hora de criação e o número de "Curtidas" de tweets com a palavra-chave "Azure" e transmite-os como eventos nos Hubs de Eventos em tempo real.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

Para executar o bloco de notas, pressione **SHIFT+ENTER**. Você verá uma saída, conforme mostrado no snippet de código a seguir. Cada evento na saída é uma combinação do carimbo de data/hora e do número de "Curtidas" ingerido pelos Hubs de Eventos.

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Ler tweets de Hubs de Eventos

No notebook **AnalyzeTweetsFromEventHub**, cole o código a seguir e substitua o espaço reservado por valores do recurso do Detector de Anomalias criado anteriormente. Este bloco de anotações lê os tweets transmitidos anteriormente aos Hubs de Eventos usando o bloco de anotações  **SendTweetsToEventHub**.

Primeiro, escreva um cliente para chamar o Detector de Anomalias. 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

Para executar o bloco de notas, pressione **SHIFT+ENTER**. Você verá uma saída, conforme mostrado no snippet de código a seguir.

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

Em seguida, prepare uma função de agregação para uso futuro.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

Para executar o bloco de notas, pressione **SHIFT+ENTER**. Você verá uma saída, conforme mostrado no snippet de código a seguir.

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

Em seguida, carregue os dados do hub de eventos para a detecção de anomalias. Substitua o espaço reservado por valores para os Hubs de Eventos do Azure criados anteriormente.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

Agora a saída é semelhante à imagem a seguir. Observe que a data na tabela pode ser diferente da data neste tutorial, pois os dados são em tempo real.
![Carregar dados do Hub de Eventos](../media/tutorials/load-data-from-eventhub.png "Load Data From Event Hub")

Agora você transmitiu dados dos Hubs de Eventos do Azure para o Azure Databricks quase em tempo real usando o conector dos Hubs de Eventos para o Apache Spark. Para obter mais informações sobre como usar o conector dos Hubs de Eventos para o Spark, veja a [documentação do conector](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Executar a detecção de anomalias em tweets

Nesta seção, você executará a detecção de anomalias nos tweets recebidos com a API do Detector de Anomalias. Para esta seção, você deve adicionar os snippets de código ao mesmo bloco de anotações **AnalyzeTweetsFromEventHub**.

Para fazer a detecção de anomalias, primeiro, você precisará agregar a contagem de métricas por hora.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
Agora a saída é semelhante aos snippets a seguir.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Em seguida, obtenha o resultado agregado da saída para o Delta. Como a detecção de anomalias exige uma janela de histórico mais longa, estamos usando o Delta para manter os dados do histórico no ponto em que você deseja fazer a detecção. Substitua o "[espaço reservado: nome da tabela]" por um nome qualificado da tabela do Delta a ser criado (por exemplo, "tweets"). Substitua "[espaço reservado: nome da pasta para pontos de verificação]" por um valor de cadeia de caracteres que seja exclusivo sempre que você executar esse código (por exemplo, "etl-from-eventhub-20190605").
Para saber mais sobre o Delta Lake no Azure Databricks, veja o [Guia do Delta Lake](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

Substitua o "[espaço reservado: nome da tabela]" pelo mesmo nome da tabela do Delta selecionado acima.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
A saída será semelhante à mostrada abaixo: 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

Agora os dados agregados de série temporal são ingeridos continuamente no Delta. Em seguida, você poderá agendar um trabalho por hora para detectar as anomalias do último ponto. Substitua o "[espaço reservado: nome da tabela]" pelo mesmo nome da tabela do Delta selecionado acima.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
O resultado será semelhante ao mostrado abaixo: 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

É isso! Usando o Azure Databricks, você transmitiu dados com êxito para os Hubs de Eventos do Azure, consumiu os dados de fluxo usando o conector dos Hubs de Eventos e, em seguida, executou a detecção de anomalias nos dados de streaming quase em tempo real.
Embora, neste tutorial, a granularidade seja por hora, você sempre poderá alterá-la de acordo com suas necessidades. 

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar de executar o tutorial, você poderá encerrar o cluster. Para fazer isso, no workspace do Azure Databricks, selecione **Clusters** no painel esquerdo. No cluster que deseja terminar, mova o cursor sobre as reticências na coluna **Ações**, selecione o ícone **Terminar** e, em seguida, selecione **Confirmar**.

![Parar um cluster do Databricks](../media/tutorials/terminate-databricks-cluster.png "Parar um cluster do Databricks")

Se você não terminar o cluster manualmente, ele será interrompido automaticamente, desde que você tenha marcado a caixa de seleção **Terminar depois de \_\_ minutos de inatividade** ao criar o cluster. Nesse caso, o cluster será interrompido automaticamente se ficar inativo durante o tempo especificado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o Azure Databricks para transmitir dados para os Hubs de Eventos do Azure e ler os dados de streaming dos Hubs de Eventos em tempo real. Avance para o próximo tutorial para saber como chamar a API do Detector de Anomalias e visualizar anomalias usando o Power BI Desktop. 

> [!div class="nextstepaction"]
>[Detecção de anomalias em lotes com o Power BI Desktop](batch-anomaly-detection-powerbi.md)
