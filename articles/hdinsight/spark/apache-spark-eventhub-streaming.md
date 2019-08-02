---
title: 'Tutorial: Processar dados dos Hubs de Eventos do Azure com Apache Spark no Azure HDInsight '
description: Tutorial – Conectar-se ao Apache Spark no Azure HDInsight para Hubs de Eventos do Azure e processar dados de streaming.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/24/2019
ms.openlocfilehash: fef551c254eb4fa212333a55bde3d642645a16b6
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489798"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Tutorial: Processar tweets usando Hubs de Eventos do Azure e Apache Spark no HDInsight

Neste tutorial, você aprenderá como criar um aplicativo de streaming do [Apache Spark](https://spark.apache.org/) para enviar tweets para um hub de eventos do Azure e criar outro aplicativo para ler os tweets do hub de eventos. Para obter uma explicação detalhada do streaming Spark, veja [Visão geral do streaming do Apache Spark](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). O HDInsight traz os mesmos recursos de streaming para um cluster Spark no Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Enviar mensagens para o Hub de Eventos do Azure
> * Ler mensagens para o Hub de Eventos do Azure

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Veja [Criar um cluster do Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Familiaridade com o uso de anotações do Jupyter com Spark no HDInsight. Para obter mais informações, confira [Carregar dados e executar consultas com o Apache Spark no HDInsight](./apache-spark-load-data-run-query.md).

* Uma [conta do Twitter](https://twitter.com/i/flow/signup).

## <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter

Para receber uma transmissão de tweets, crie um aplicativo no Twitter. Siga as instruções para criar um aplicativo do Twitter e escrever os valores necessários para concluir este tutorial.

1. Navegue até o [Gerenciamento de Aplicativo do Twitter](https://apps.twitter.com/).

1. Selecione **Criar novo aplicativo**.

1. Forneça os seguintes valores:

    |Propriedade |Valor |
    |---|---|
    |NOME|Forneça o nome do aplicativo. O valor usado para este tutorial é **HDISparkStreamApp0423**. Esse nome deve ser um nome exclusivo.|
    |DESCRIÇÃO|Forneça uma breve descrição do aplicativo. O valor usado para este tutorial é **Um aplicativo de streaming simples do HDInsight Spark**.|
    |Site|Forneça o site do aplicativo. Ele não precisa ser um site válido.  O valor usado para este tutorial é `http://www.contoso.com`.|
    |URL de retorno de chamada|Você pode deixar em branco.|

1. Selecione **Sim, eu li e concordo com o Contrato de Desenvolvedor do Twitter** e, em seguida, selecione **Criar seu aplicativo Twitter**.

1. Selecione a guia **Chaves e Tokens de Acesso** .

1. Selecione **Criar o token de acesso** no final da página.

1. Anote os seguintes valores da página.  Você precisará desses valores mais tarde neste tutorial:

    - **Chave do consumidor (chave de API)**    
    - **Segredo do consumidor (segredo de API)**  
    - **Token de acesso**
    - **Segredo do token de acesso**   

## <a name="create-an-azure-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos do Azure

Você pode usar esse hub de eventos para armazenar tweets.

1. Entre no [Portal do Azure](https://portal.azure.com). 

2. No menu esquerdo, selecione **Todos os serviços**.  

3. Em **INTERNET DAS COISAS**, selecione **Hubs de Eventos**. 

    ![Criar hub de eventos para exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Criar hub de eventos para exemplo de streaming do Spark")

4. Selecione **+ Adicionar**.

5. Insira os seguintes valores para o novo namespace de Hubs de Eventos:

    |Propriedade |Valor |
    |---|---|
    |NOME|Insira um nome para o hub de evento.  O valor usado para este tutorial é **myeventhubns20180403**.|
    |Tipo de preço|Selecione **Padrão**.|
    |Subscription|Selecione a assinatura apropriada.|
    |Resource group|Selecione um grupo de recursos existente na lista suspensa ou selecione **Criar novo** para criar um novo grupo de recursos.|
    |Location|Selecione o mesmo **Local** como o cluster Apache Spark no HDInsight para reduzir latência e custos.|
    |Habilitar Inflar Automaticamente (Opcional) |A inflação automática dimensiona automaticamente o número de Unidades de Produtividade atribuídas ao Namespace de Hubs de Eventos quando o tráfego excede a capacidade das Unidades de Produtividade atribuídas a ele.  |
    |Máximo de Unidades de Produtividade de Inflação Automática (Opcional)|Esse controle deslizante somente aparecerá se você marcar **Habilitar Inflar Automaticamente**.  |

    ![Fornecer um nome ao hub de eventos para o exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Fornecer um nome ao hub de eventos para o exemplo de streaming do Spark")

6. Selecione **Criar** para criar o namespace.  A implantação será concluída em alguns minutos.

## <a name="create-an-azure-event-hub"></a>Criar um hub de eventos do Azure
Crie um hub de eventos depois que o namespace de Hubs de Eventos for implantado.  Do portal:

1. No menu esquerdo, selecione **Todos os serviços**.  

1. Em **INTERNET DAS COISAS**, selecione **Hubs de Eventos**.  

1. Selecione o namespace de Hubs de Eventos na lista.  

1. Na página **Namespace de Hubs de Eventos**, selecione **+ Hubs de Eventos**.  
1. Insira os seguintes valores na página **Criar Hub de Eventos**:

    - **Nome**: Forneça um nome para o Hub de Eventos. 
 
    - **Contagem de partição**: 10.  

    - **Retenção de mensagem**: 1.   
   
      ![Fornecer detalhes do hub de eventos para o exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Fornecer detalhes do hub de eventos para o exemplo de streaming do Spark")

1. Selecione **Criar**.  A implantação deverá ser concluída em alguns segundos e você retornará à página Namespace de Hub de Eventos.

1. Em **Configurações**, selecione **Políticas de Acesso Compartilhado**.

1. Selecione **RootManageSharedAccessKey**.
    
     ![Definir políticas de Hub de Eventos para o exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Definir políticas de Hub de Eventos para o exemplo de streaming do Spark")

1. Salve os valores de **Chave primária** e **Chave primária de cadeia de caracteres de conexão** para usar posteriormente no tutorial.

     ![Ver chaves de política do Hub de Eventos para o exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Ver chaves de política do Hub de Eventos para o exemplo de streaming do Spark")


## <a name="send-tweets-to-the-event-hub"></a>Enviar tweets para o hub de eventos

Crie um Jupyter notebook e nomeie-o **SendTweetsToEventHub**. 

1. Execute o seguinte código para adicionar as bibliotecas externas do Apache Maven:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Edite o código a seguir, substituindo `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>`, `<CONSUMER SECRET>`, `<ACCESS TOKEN>` e `<TOKEN SECRET>` pelos valores apropriados. Execute o código editado para enviar tweets para o hub de eventos:

    ```scala
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Abra o hub de eventos no portal do Azure.  Em **Visão geral**, você deverá ver alguns gráficos mostrando as mensagens enviadas para o hub de eventos.

## <a name="read-tweets-from-the-event-hub"></a>Ler tweets do hub de eventos

Crie outro Jupyter notebook e nomeie-o **ReadTweetsFromEventHub**. 

1. Execute o seguinte código para adicionar uma biblioteca Apache Maven externa:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```

2. Edite o código a seguir, substituindo `<Event hub name>` e `<Event hub namespace connection string>` pelos valores apropriados. Execute o código editado para ler tweets do hub de eventos:

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Com o HDInsight, os dados são armazenados no Armazenamento do Microsoft Azure ou no Azure Data Lake Storage para que você possa excluir um cluster com segurança quando não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Se você pretende trabalhar no próximo tutorial imediatamente, mantenha o cluster, caso contrário, prossiga e exclua o cluster.

Abra o cluster no portal do Azure e selecione **Excluir**.

![Excluir o cluster HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Excluir o cluster HDInsight")

Também é possível selecionar o nome do grupo de recursos para abrir a página do grupo de recursos, e depois selecionar **Excluir grupo de recursos**. Ao excluir o grupo de recursos, você exclui o cluster Spark do HDInsight e a conta de armazenamento padrão.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como criar um aplicativo de streaming do Apache Spark para enviar tweets para um hub de eventos do Azure e criou outro aplicativo para ler os tweets do hub de eventos.  Avance para o próximo artigo para ver que você pode criar uma aplicativo de aprendizado de máquina.

> [!div class="nextstepaction"]
> [Criar um aplicativo de aprendizado de máquina](./apache-spark-ipython-notebook-machine-learning.md)
