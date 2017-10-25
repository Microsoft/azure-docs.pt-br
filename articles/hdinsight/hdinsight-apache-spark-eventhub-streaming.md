---
title: Usar o streaming do Apache Spark com Hubs de Eventos no Azure HDInsight | Microsoft Docs
description: "Compile um exemplo de streaming do Apache Spark sobre como enviar dados de uma transmissão para o Hub de Eventos do Azure e receber esses eventos no cluster HDInsight Spark usando um aplicativo de escala."
keywords: streaming do apache spark, streaming do spark, exemplo de spark, exemplo de streaming do apache spark, exemplo do hub de evento do azure, exemplo de spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 14d969ecaf1f24a0bb34da4abe78d83f08627796
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="apache-spark-streaming-process-data-from-azure-event-hubs-with-spark-cluster-on-hdinsight"></a>Streaming do Apache Spark: processe dados de Hubs de Eventos do Azure com o cluster Spark no HDInsight

Neste artigo, você criará um exemplo de streaming do Apache Spark que envolve as seguintes etapas:

1. Você pode usar um aplicativo autônomo para ingerir mensagens em um Hub de Eventos do Azure.

2. Com duas abordagens diferentes, você recupera as mensagens do Hub de Eventos em tempo real usando um aplicativo em execução no cluster Spark no Azure HDInsight.

3. Você cria pipelines analíticos para persistir dados para diferentes sistemas de armazenamento de streaming ou obter insights de dados em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="spark-streaming-concepts"></a>Conceitos de Streaming do Spark

Para obter uma explicação detalhada do streaming Spark, veja [Visão geral do streaming do Apache Spark](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). O HDInsight traz os mesmos recursos de streaming para um cluster Spark no Azure.  

## <a name="what-does-this-solution-do"></a>O que essa solução faz?

Neste artigo, para criar um exemplo de streaming do Spark, execute as seguintes etapas:

1. Crie um Hub de Eventos do Azure que vá receber uma transmissão de eventos.

2. Execute um aplicativo local autônomo que gere eventos e envie-os por push ao Hub de Eventos do Azure. O aplicativo de exemplo que faz isso é publicado em [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

3. Execute um aplicativo de streaming remotamente em um cluster Spark que lê o eventos de streaming do Hub de Eventos do Azure e execute procedimentos diversos de análise/processamento de dados.

## <a name="create-an-azure-event-hub"></a>Criar um Hub de Eventos do Azure

1. Faça logon no [Portal do Azure](https://ms.portal.azure.com) e clique em **Novo** na parte superior esquerda da tela.

2. Clique em **Internet das Coisas** e, em seguida, clique em **Hubs de Eventos**.

    ![Criar hub de eventos para exemplo de streaming do Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Criar hub de eventos para exemplo de streaming do Spark")

3. Na folha **Criar um namespace** , insira um nome de namespace. Escolha o tipo de preço (Básico ou Standard). Além disso, escolha uma assinatura do Azure, o grupo de recursos e o local no qual o recurso será criado. Clique em **Criar** para criar o namespace.

      ![Fornecer um nome ao hub de eventos para o exemplo de streaming do Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Fornecer um nome ao hub de eventos para o exemplo de streaming do Spark")

    > [!NOTE]
    > Você deve selecionar o mesmo **Local** do cluster Apache Spark no HDInsight para reduzir latência e custos.
    >
    >

4. Na lista de namespaces do Hubs de Eventos, clique no namespace recém-criado.      


5. Na folha de namespace, clique em **Hubs de Eventos** e, em seguida, clique em **+ Hub de Eventos** para criar um novo Hub de Eventos.
   
    ![Criar hub de eventos para exemplo de streaming do Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-open-event-hubs-blade-for-spark-streaming-example.png "Criar hub de eventos para exemplo de streaming do Spark")

6. Digite um nome para seu Hub de Eventos, defina a contagem de partições para 10 e a retenção de mensagens para 1. Nós não estamos arquivando as mensagens nesta solução, então você pode deixar o restante como padrão e depois clicar em **Criar**.
   
    ![Fornecer detalhes do hub de eventos para o exemplo de streaming do Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Fornecer detalhes do hub de eventos para o exemplo de streaming do Spark")

7. O Hub de Eventos recém-criado é listado na folha Hub de Eventos.
    
     ![Ver Hub de Eventos para o exemplo de streaming do Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-view-event-hub-for-spark-streaming-example.png "Ver Hub de Eventos para o exemplo de streaming do Spark")

8. Novamente na folha do namespace (e não na folha do Hub de Eventos específico), clique em **Políticas de acesso compartilhado** e clique em **RootManageSharedAccessKey**.
    
     ![Definir políticas de Hub de Eventos para o exemplo de streaming do Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Definir políticas de Hub de Eventos para o exemplo de streaming do Spark")

9. Clique no botão de cópia para copiar a cadeia de conexão e a chave primária **RootManageSharedAccessKey** para a área de transferência. Salve-as para usar posteriormente no tutorial.
    
     ![Ver chaves de política do Hub de Eventos para o exemplo de streaming do Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Ver chaves de política do Hub de Eventos para o exemplo de streaming do Spark")

## <a name="send-messages-to-azure-event-hub-using-a-sample-scala-application"></a>Enviar mensagens para o Hub de Eventos do Azure usando um exemplo de aplicativo Scala

Nesta seção, você usará um aplicativo Scala autônomo local que gera um fluxo de eventos e os envia ao Hub de Eventos do Azure criado anteriormente. Este aplicativo está disponível no GitHub em [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). As etapas aqui supõem que você já tenha bifurcado esse repositório GitHub.

1. Verifique se os itens a seguir estão instalados no computador no qual você está executando esse aplicativo.

    * Kit de desenvolvimento Oracle Java. Você pode instalá-lo clicando [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
    * Apache Maven. Você pode baixá-lo [aqui](https://maven.apache.org/download.cgi). As instruções para instalar o Maven estão disponíveis [aqui](https://maven.apache.org/install.html).

2. Abra um prompt de comando e navegue até o local em que você clonou o repositório do GitHub para o aplicativo de exemplo do Scala e execute o comando a seguir para compilar o aplicativo.

        mvn package

3. O jar de saída para o aplicativo, **com-microsoft-azure-eventhubs-client-example-0.2.0.jar**, é criado sob o diretório **/target**. Você pode usar este JAR posteriormente neste artigo para testar a solução completa.

## <a name="create-application-to-receive-messages-from-event-hub-into-a-spark-cluster"></a>Criar aplicativo para receber mensagens do Hub de Eventos em um cluster Spark 

Temos duas abordagens para conectar o Streaming do Spark e Hubs de Eventos do Azure, conexão com base no destinatário e conexão com base em Direct DStream. A conexão com base em Direct DStream foi introduzida em janeiro de 2017 na versão 2.0.3. Ela é destinada a substituir a conexão original com base no receptor, pois é mais eficaz e usa os recursos de modo mais eficiente. Mais detalhes podem ser encontrados em [https://github.com/hdinsight/spark-eventhubs](https://github.com/hdinsight/spark-eventhubs). O Direct DStream dá suporte apenas ao Spark 2.0+.

### <a name="build-applications-with-the-dependency-to-spark-eventhubs-connector"></a>Criar aplicativos com a dependência para o conector spark-eventhubs

Publicaremos também a versão de preparo do Spark-EventHubs no GitHub. Para usar a versão de preparo do Spark EventHubs, a primeira etapa é indicar o GitHub como o repositório de origem, adicionando a seguinte entrada a pom.xml:

```xml
<repository>
      <id>spark-eventhubs</id>
      <url>https://raw.github.com/hdinsight/spark-eventhubs/maven-repo/</url>
      <snapshots>
        <enabled>true</enabled>
        <updatePolicy>always</updatePolicy>
      </snapshots>
</repository>
```

Em seguida, você pode adicionar a dependência a seguir ao seu projeto para usar a versão de pré-lançamento.

Dependência do Maven

```xml
<!-- https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11 -->
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-streaming-eventhubs_2.11</artifactId>
    <version>2.0.4</version>
</dependency>
```

Dependência SBT

```
// https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11
libraryDependencies += "com.microsoft.azure" % "spark-streaming-eventhubs_2.11" % "2.0.4"
```

### <a name="direct-dstream-connection"></a>Conexão por Direct DStream

Um arquivo jar pré-criado que contém exemplos que usam Direct DStream pode ser baixado em [http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar](http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar).

O arquivo jar contém três exemplos cujo código-fonte está disponível em [https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream](https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream).

Usando [WindowingWordCount](https://github.com/hdinsight/spark-eventhubs/blob/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream/WindowingWordCount.scala) como um exemplo:

```scala
private def createStreamingContext(
  sparkCheckpointDir: String,
  batchDuration: Int,
  namespace: String,
  eventHunName: String,
  eventhubParams: Map[String, String],
  progressDir: String) = {
val ssc = new StreamingContext(new SparkContext(), Seconds(batchDuration))
ssc.checkpoint(sparkCheckpointDir)
val inputDirectStream = EventHubsUtils.createDirectStreams(
  ssc,
  namespace,
  progressDir,
  Map(eventHunName -> eventhubParams))

inputDirectStream.map(receivedRecord => (new String(receivedRecord.getBody), 1)).
  reduceByKeyAndWindow((v1, v2) => v1 + v2, (v1, v2) => v1 - v2, Seconds(batchDuration * 3),
    Seconds(batchDuration)).print()

ssc
}

def main(args: Array[String]): Unit = {

if (args.length != 8) {
  println("Usage: program progressDir PolicyName PolicyKey EventHubNamespace EventHubName" +
    " BatchDuration(seconds) Spark_Checkpoint_Directory maxRate")
  sys.exit(1)
}

val progressDir = args(0)
val policyName = args(1)
val policykey = args(2)
val namespace = args(3)
val name = args(4)
val batchDuration = args(5).toInt
val sparkCheckpointDir = args(6)
val maxRate = args(7)

val eventhubParameters = Map[String, String] (
  "eventhubs.policyname" -> policyName,
  "eventhubs.policykey" -> policykey,
  "eventhubs.namespace" -> namespace,
  "eventhubs.name" -> name,
  "eventhubs.partition.count" -> "32",
  "eventhubs.consumergroup" -> "$Default",
  "eventhubs.maxRate" -> s"$maxRate"
)

val ssc = StreamingContext.getOrCreate(sparkCheckpointDir,
  () => createStreamingContext(sparkCheckpointDir, batchDuration, namespace, name,
    eventhubParameters, progressDir))

ssc.start()
ssc.awaitTermination()
}
```

No exemplo acima, `eventhubParameters` são os parâmetros específicos para uma única instância de EventHubs e é necessário passá-los para a API `createDirectStreams`, que constrói um mapeamento de objeto Direct DStream para um namespace de Hubs de Eventos. Sobre o objeto DStream direto, você pode chamar qualquer API DStream fornecida pela estrutura de API de Streaming do Spark. Neste exemplo, calculamos a frequência de cada palavra nos últimos três microintervalos de lote.

### <a name="receiver-based-connection"></a>Conexão com base em receptor

Um exemplo de aplicativo de streaming Spark escrito em Scala, que recebe eventos e os encaminha para destinos diferentes, está disponível em [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Execute as etapas abaixo para atualizar o aplicativo para sua configuração do Hub de Eventos e criar o jar de saída.

1. Inicie o IntelliJ IDEA e, na tela de inicialização, selecione **Fazer Check-out do Controle de Versão** e clique em **Git**.
   
    ![Exemplo de streaming do Apache Spark – obter fontes do Git](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-get-source-from-git.png "Exemplo de streaming do Apache Spark – obter fontes do Git")

2. Na caixa de diálogo **Clonar Repositório**, forneça a URL para o repositório Git do qual clonar, especifique o diretório para o qual clonar e clique em **Clonar**.
   
    ![Exemplo de streaming do Apache Spark – clonar do Git](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-clone-from-git.png "Exemplo de streaming do Apache Spark – clonar do Git")
3. Siga as instruções até que o projeto estar completamente clonado. Pressione **Alt + 1** para abrir a **Exibição do Projeto**. O resultado deve ser parecido com o seguinte:
   
    ![Exemplo de streaming do Apache Spark – Exibição do Projeto](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-project-view.png "Exemplo de streaming do Apache Spark – Exibição do Projeto")
4. Verifique se o código do aplicativo foi compilado com Java8. Para garantir isso, clique em **Arquivo**, clique em **Estrutura do Projeto** e, na guia **Projeto**, verifique se o nível de Linguagem do projeto foi definido como **8 - Lambdas, anotações de tipo etc**.
   
    ![Exemplo de streaming do Apache Spark – Definir compilador](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-java-8-compiler.png "Exemplo de streaming do Apache Spark – Definir compilador")
5. Abra o **pom.xml** e verifique se que a versão do Spark está correta. No nó `<properties>`, procure o trecho a seguir e verifique a versão do Spark.

        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. O aplicativo requer um jar de dependência chamado **jar do driver JDBC**. Isso é necessário para gravar as mensagens recebidas do hub de eventos em um Banco de Dados SQL do Azure. Você pode baixar esse jar (v 4.1 ou posterior) [aqui](https://msdn.microsoft.com/sqlserver/aa937724.aspx). Adicione a referência a esse jar na biblioteca do projeto. Execute as seguintes etapas:
     
     1. Na janela IntelliJ IDEA em que o aplicativo abrir, clique em **Arquivo**, clique em **Estrutura do Projeto** e clique em **Bibliotecas**. 
     2. Clique no ícone de adicionar (![adicionar ícone](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)), clique em **Java**e, em seguida, navegue até a localização em que você baixou o jar do driver JDBC. Siga os prompts para adicionar o arquivo jar à biblioteca do projeto.

         ![adicionar dependências ausentes](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Adicionar jars de dependência ausente")
     3. Clique em **Aplicar**.

7. Crie o arquivo jar de saída. Execute as seguintes etapas:

   1. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** e, em seguida, clique no sinal de mais. Na caixa de diálogo pop-up, clique em **JAR** e, em seguida, clique em **Dos módulos com dependências**.      
       
       ![Exemplo de streaming do Apache Spark – Criar JAR](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-create-jar.png "Exemplo de streaming do Apache Spark – Criar JAR")
   2. Na caixa de diálogo **Criar JAR de Módulos,** clique no botão de reticências (![reticências](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) em relação à **Classe Principal**.
   3. Na caixa de diálogo **Selecionar Classe Principal**, selecione qualquer uma das classes disponíveis e clique em **OK**.
      
       ![Exemplo de streaming do Apache Spark – selecionar classe para jar](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-select-class-for-jar.png "Exemplo de streaming do Apache Spark – selecionar classe para jar")
   4. Na caixa de diálogo **Criar JAR de Módulos**, certifique-se de que a opção **extrair para o JAR de destino** esteja selecionada e, em seguida, clique em **OK**. Isso cria um JAR único com todas as dependências.
      
       ![Exemplo de streaming do Apache Spark – criar jar a partir de módulos](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-create-jar-from-modules.png "Exemplo de streaming do Apache Spark – criar jar a partir de módulos")
   5. A guia **Layout de Saída** lista todos os jars incluídos como parte do projeto Maven. Você pode selecionar e excluir aqueles dos quais o aplicativo Scala não tem qualquer dependência direta. Para o aplicativo que estamos criando aqui, você pode remover tudo, exceto o último (**spark-streaming-data-persistence-examples compile output**). Selecione os jars para excluir e, em seguida, clique no ícone **Excluir** (![ícone de excluir](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)).
      
       ![Exemplo de streaming do Apache Spark – excluir jars extraídos](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-delete-output-jars.png "Exemplo de streaming do Apache Spark – excluir jars extraídos")
      
       Certifique-se de que a caixa **Compilar à criação** esteja marcada, o que garante que o jar seja criado sempre que o projeto for criado ou atualizado. Clique em **Aplicar**.
   6. Na guia **Layout de Saída**, à direita na parte inferior da caixa **Elementos Disponíveis**, você tem o jar do JDBC do SQL adicionado anteriormente à biblioteca do projeto. Você deve adicioná-lo à guia **Layout de Saída** . Clique com o botão direito do mouse no arquivo jar e, em seguida, clique em **Extrair para Raiz de Saída**.
      
       ![Exemplo de streaming do Apache Spark – extrair jar de dependência](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-extract-dependency-jar.png "Exemplo de streaming do Apache Spark – extrair jar de dependência")  
      
       A guia **Layout de Saída** deve ter esta aparência.
      
       ![Exemplo de streaming do Apache Spark – guia de saída final](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-final-output-tab.png "Exemplo de streaming do Apache Spark – guia de saída final")        
      
       Na caixa de diálogo **Estrutura do Projeto**, clique em **Aplicar** e clique em **OK**.    
   7. Na barra de menus, clique em **Compilar** e, em seguida, clique em **Criar Projeto**. Você também pode clicar em **Compilar Artefatos** para criar o jar. O jar de saída é criado em **\classes\artifacts**.
      
       ![Exemplo de streaming do Apache Spark – JAR de saída](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-output-jar.png "Exemplo de streaming do Apache Spark – JAR de saída")

## <a name="run-the-application-remotely-on-a-spark-cluster-using-livy"></a>Executar o aplicativo remotamente em um cluster Spark usando Livy

Neste artigo, usaremos o Livy para executar remotamente o aplicativo de streaming do Apache Spark em um cluster Spark. Para uma discussão detalhada de como usar Livy com cluster HDInsight Spark, consulte [Enviar trabalhos remotamente para um cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-livy-rest-interface.md). Antes de começar a executar o aplicativo de streaming Spark, há algumas ações que você deve executar:

1. Inicie o aplicativo autônomo local para gerar eventos e enviar ao hub de eventos. Use o seguinte comando para fazer isso:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Copiar o jar de transmissão (**spark-streaming-data-persistence-examples.jar**) para o armazenamento de Blobs do Azure associado ao cluster. Isso torna o jar acessível ao Livy. Você pode usar [**AzCopy**](../storage/common/storage-use-azcopy.md), um utilitário de linha de comando, para fazer isso. Há muitos outros clientes que podem ser usados para carregar dados. É possível saber mais sobre eles em [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).
3. Instale CURL no computador do qual você está executando a esses aplicativos. Usamos CURL para invocar os pontos de extremidade do Livy para executar os trabalhos remotamente.

### <a name="run-the-spark-streaming-application-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Execute o aplicativo de streaming do Spark para receber os eventos em um Azure Storage Blob como texto

Abra um prompt de comando, navegue até o diretório em que você instalou CURL e execute o seguinte comando (substitua nome de usuário/senha e cluster nome):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no arquivo **inputBlob.txt** são definidos da seguinte maneira:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Vamos entender quais são os parâmetros no arquivo de entrada:

* **arquivo** é o caminho para o arquivo de jar do aplicativo na conta de armazenamento do Azure associada ao cluster.
* **className** é o nome da classe no jar.
* **args** é a lista de argumentos exigidos pela classe
* **numExecutors** é o número de núcleos usados pelo Spark para executar o aplicativo de transmissão. Isso sempre deve ser pelo menos duas vezes o número de partições do hub de eventos.
* **executorMemory**, **executorCores**, **driverMemory** são parâmetros usados para atribuir os recursos necessários para o aplicativo de transmissão.

> [!NOTE]
> Você não precisa criar as pastas de saída (EventCheckpoint, EventCount/EventCount10) que são usadas como parâmetros. O aplicativo de streaming cria para você.
>
>

Quando você executar o comando, deve ver uma saída semelhante à seguinte:

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Anote a ID do lote na última linha da saída (no exemplo, é '1'). Para verificar se o aplicativo é executado com sucesso, você pode examinar sua conta de armazenamento do Azure associada ao cluster e deverá ver a pasta **/EventCount/EventCount10** criada ali. Essa pasta deve conter blobs que capturam o número de eventos processados dentro do período de tempo especificado para o parâmetro **batch-interval-in-seconds**.

O aplicativo de streaming do Spark continuará a ser executado até que você o elimine. Para fazer isso, use o seguinte comando:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Execute os aplicativos para receber os eventos em um Azure Storage Blob como JSON
Abra um prompt de comando, navegue até o diretório em que você instalou CURL e execute o seguinte comando (substitua nome de usuário/senha e cluster nome):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no arquivo **inputJSON.txt** são definidos da seguinte maneira:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Os parâmetros são semelhantes aos especificados para a saída de texto na etapa anterior. Novamente, você não precisa criar as pastas de saída (EventCheckpoint, EventCount/EventCount10) que são usadas como parâmetros. O aplicativo de streaming cria para você.

 Depois de executar o comando, você pode examinar a sua conta de armazenamento do Azure associada ao cluster, e deverá ver a pasta **/EventStore10** criada ali. Abra qualquer arquivo prefixado com **part-** e você deverá ver os eventos processados em um formato JSON.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Executar os aplicativos para receber os eventos em uma tabela Hive
Para executar o aplicativo de streaming do Spark que transmite eventos em uma tabela Hive, são necessários alguns componentes adicionais. Estes são:

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

Os arquivos **.jar**r estão disponíveis no seu cluster HDInsight Spark em `/usr/hdp/current/spark-client/lib`. O **hive-site.xml** está disponível em `/usr/hdp/current/spark-client/conf`.

Você pode usar [WinScp](http://winscp.net/eng/download.php) para copiar esses arquivos do cluster para seu computador local. Em seguida, você pode usar ferramentas para copiar esses arquivos para a sua conta de armazenamento associada ao cluster. Para obter mais informações sobre como carregar arquivos para a conta de armazenamento, consulte [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).

Depois de copiar os arquivos da sua conta de armazenamento do Azure, abra um prompt de comando, navegue até o diretório em que instalou CURL e execute o seguinte comando (substitua nome de usuário/senha e nome do cluster):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no arquivo **inputHive.txt** são definidos da seguinte maneira:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasb:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasb:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasb:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasb:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Os parâmetros são semelhantes aos especificados para a saída de texto nas etapas anteriores. Novamente, você não precisa criar as pastas de saída (EventCheckpoint, EventCount/EventCount10) ou a tabela do Hive de saída (EventHiveTable10) usadas como parâmetros. O aplicativo de streaming cria para você. Observe que a opção **jars** e **arquivos** inclui caminhos para os arquivos .jar e o hive-site.xml copiados para a conta de armazenamento.

Para verificar se a tabela de hive foi criada com êxito, use a [exibição do Hive do Ambari](hdinsight-hadoop-use-hive-ambari-view.md). Também é possível executar uma consulta SELECT lá para exibir o conteúdo da tabela.

    SELECT * FROM eventhivetable10 LIMIT 10;

Você verá algo semelhante ao mostrado a seguir:

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Executar os aplicativos para receber os eventos em uma tabela de Banco de Dados SQL do Azure
Antes de executar essa etapa, verifique se que você tem um banco de dados SQL Azure criado. Para obter instruções, consulte [Criar um banco de dados SQL em minutos](../sql-database/sql-database-get-started.md). Para completar esta seção, você precisa de valores para nome do banco de dados, nome do servidor de banco de dados e credenciais de administrador de banco de dados como parâmetros. Porém, você não precisa criar a tabela de banco de dados. O aplicativo de streaming do Spark a cria para você.

Abra um prompt de comando, navegue até o diretório no qual você instalou CURL e execute o seguinte comando:

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no arquivo **inputSQL.txt** são definidos da seguinte maneira:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Para verificar se o aplicativo é executado com êxito, você pode se conectar ao Banco de Dados SQL do Azure usando o SQL Server Management Studio. Para obter instruções sobre como fazer isso, consulte [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md). Quando você estiver conectado ao banco de dados, pode navegar para a tabela **EventContent** criada pelo aplicativo de transmissão. Você pode executar uma consulta rápida para obter os dados da tabela. Execute a consulta a seguir:

    SELECT * FROM EventCount

Você deverá ver uma saída semelhante ao seguinte:

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Design de Conexão com Base no Receptor e Direct DStream](https://www.slideshare.net/NanZhu/seattle-sparkmeetup032317)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]: ../storage-create-storage-account/
