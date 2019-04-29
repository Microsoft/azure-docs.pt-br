---
title: Spark Structured Streaming no Azure HDInsight
description: Como usar aplicativos Spark Structured Streaming em clusters Spark do HDInsight.
services: hdinsight
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
orgin.date: 02/05/2018
ms.date: 04/01/2019
ms.author: v-yiso
ms.openlocfilehash: 0e9d87e5b344b7091a2a0cf41d6f7fa3484dfcf3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098568"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Visão geral do streaming estruturado do Apache Spark

O [Apache Spark](https://spark.apache.org/) Structured Streaming permite que você implemente aplicativos dimensionáveis, tolerantes a falhas e de alto rendimento para o processamento de fluxos de dados. O Structured Streaming baseia-se no mecanismo do Spark SQL e aprimora as construções de quadros de dados e conjuntos de dados do Spark SQL, para que você possa gravar consultas de streaming da mesma forma que gravaria consultas de lote.  

Aplicativos de Fluxo Estruturado são executados em clusters do HDInsight Spark e se conectam a dados de fluxo contínuo do [Apache Kafka](https://kafka.apache.org/), um soquete TCP (para fins de depuração), Armazenamento do Microsoft Azure ou Armazenamento do Azure Data Lake Storage. As duas últimas opções, que contam com serviços de armazenamento externos, permitem detectar novos arquivos adicionados no armazenamento e processar seu conteúdo, como se fossem transmitidos. 

O Structured Streaming cria uma consulta de longa execução durante a qual você aplica operações aos dados de entrada, como seleção, projeção, agregação, janelas e união de DataFrame de streaming com referência de DataFrames. Em seguida, você gera os resultados para o armazenamento de arquivo (Azure Storage Blobs ou Data Lake Storage) ou para qualquer repositório de dados usando código personalizado (como banco de dados SQL ou Power BI). Structured Streaming também fornece a saída para o console para depuração local e para uma tabela na memória para que você possa ver os dados gerados para depuração no HDInsight. 

![Processamento de Stream com o HDInsight e o Spark Structured Streaming ](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]
> O Spark Structured Streaming substitui o Spark Streaming (DStreams). No futuro, Structured Streaming receberá aprimoramentos e manutenção, enquanto DStreams estará apenas no modo de manutenção. O Structured Streaming não tem no momento todos os recursos do DStreams para as fontes e coletores compatíveis instantaneamente, então avalie os requisitos para escolher a opção adequada de processamento de transmissão do Spark. 

## <a name="streams-as-tables"></a>Fluxos como tabelas

O Structured Streaming do Spark representa um fluxo de dados como tabela que não é vinculada em camadas, ou seja, a tabela continuará a crescer conforme novos dados chegam. Essa *tabela de entrada* é processada continuamente por uma consulta de execução longa e os resultados são enviados a uma *tabela de saída*:

![Conceito de Structured Streaming](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

No Structured Streaming, os dados chegam ao sistema e são ingeridos imediatamente em uma tabela de entrada. Você grava as consultas (usando as APIs de DataFrame e conjuntos de dados) que executam operações nessa tabela de entrada. O resultado da consulta produz outra tabela, a *tabela de resultados*. A tabela de resultados contém os resultados da sua consulta, a partir da qual você extrai dados para um armazenamento de dados externo, como um banco de dados relacional. O tempo de quando os dados serão processados a partir da tabela de entrada é controlado pelo *intervalo do gatilho*. Por padrão, o intervalo do gatilho é zero, para que o Structured Streaming tente processar os dados assim que eles chegam. Na prática, isso significa que assim que o Structured Streaming termina de processar a execução da consulta anterior, ele inicia outro processamento executado quaisquer dados recebidos recentemente. É possível configurar o gatilho para executar em um intervalo, de modo que os dados de streaming sejam processados em lotes baseados em tempo. 

Os dados na tabela de resultados podem conter apenas os dados que forem novos desde a última vez que a consulta foi processada (*modo de acréscimo*) ou a tabela pode ser completamente atualizada a cada vez que houver novos dados, de modo que inclua todos os dados de saída assim que a consulta de streaming tiver iniciado (*modo completo*).

### <a name="append-mode"></a>Modo de acréscimo

No modo de acréscimo, apenas as linhas adicionadas à tabela de resultados, desde a última execução de consulta estão presentes na tabela de resultados e gravadas no armazenamento externo. Por exemplo, a consulta mais simples apenas copia todos os dados da tabela de entrada para a tabela de resultados inalterada. Cada vez que um intervalo do gatilho expira, os novos dados são processados e as linhas que representam os novos dados aparecem na tabela de resultados. 

Considere um cenário onde você está processando a telemetria de sensores de temperatura, como, por exemplo, um termostato. Suponha que o primeiro gatilho processou um evento no tempo 00:01 para o dispositivo 1 com uma leitura de temperatura de 95 graus. No primeiro gatilho da consulta, apenas a linha com tempo 00:01 é exibida na tabela de resultados. No tempo 00:02 quando outro evento chega, apenas a nova linha é a linha com tempo 00:02 e assim a tabela de resultados conterá somente essa única linha.

![Modo de acréscimo do Structured Streaming](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Ao usar o modo de acréscimo, sua consulta aplicaria projeções (selecionando as colunas que são importantes), filtragem (separando somente linhas que correspondam a determinadas condições) ou associação (aumentando os dados com dados de uma tabela de pesquisa estática). O modo de acréscimo facilita o envio de somente novos pontos de dados relevantes para o armazenamento externo.

### <a name="complete-mode"></a>Modo completo

Considere o mesmo cenário, desta vez usando o modo completo. No modo completo, a tabela de saída inteira é atualizada em cada gatilho de modo que a tabela inclui dados não apenas da execução de gatilho mais recente, mas de todas as execuções. Você pode usar o modo completo para copiar os dados inalterados da tabela de entrada para a tabela de resultados. Em cada execução disparada, as novas linhas de resultado aparecem junto com todas as linhas anteriores. A tabela de resultados de saída acabarão armazenando todos os dados coletados desde o início da consulta e uma hora você ficaria sem memória. O modo completo é destinado ao uso com consultas de agregação que resumem os dados de entrada de alguma forma e assim, em cada gatilho, a tabela de resultados é atualizada com um novo resumo. 

Suponha que até agora há cinco segundos de dados já processados e é hora de processar os dados para o sexto segundo. A tabela de entrada tem eventos para o tempo 00:01 e o tempo 00:03. O objetivo dessa consulta de exemplo é apresentar a temperatura média do dispositivo a cada cinco segundos. A implementação dessa consulta se aplica a uma agregação que usa todos os valores que se enquadram em cada janela de 5 segundos, calcula a média da temperatura e produz uma linha para a temperatura média durante esse intervalo. No final da primeira janela de 5 segundos, há duas tuplas: (00:01, 1, 95) e (00:03, 1, 98). Portanto, para a janela 00:00-00:05, a agregação produz uma tupla com a temperatura média de 96,5 graus. Na próxima janela de 5 segundos, há apenas um ponto de dados no tempo 00:06, portanto, a temperatura média resultante é de 98 graus. No tempo 00:10, usando o modo completo, a tabela de resultados tem linhas para as duas janelas 00:00-00:05 e 05:00-00:10 porque a consulta produz todas as linhas agregadas, não apenas as novas. Portanto, a tabela de resultados continua a crescer à medida que novas janelas são adicionadas.    

![Modo completo do Structured Streaming](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nem todas as consultas usando o modo completo farão com que a tabela cresça sem limites.  Considere o exemplo anterior que, em vez de calcular a média de temperatura pela janela de tempo, a média é calculada, em vez disso, por ID do dispositivo. A tabela de resultados contém um número fixo de linhas (um por dispositivo) com a temperatura média para o dispositivo em todos os pontos de dados recebidos do dispositivo. Conforme novas temperaturas são recebidas, a tabela de resultados é atualizada para que as médias na tabela fiquem sempre atuais. 

## <a name="components-of-a-spark-structured-streaming-application"></a>Componentes de um aplicativo Spark Structured Streaming

Uma consulta de exemplo simples pode resumir as leituras de temperatura por janelas com duração de uma hora. Nesse caso, os dados são armazenados em arquivos JSON no Armazenamento do Microsoft Azure (anexados como armazenamento padrão para o cluster HDInsight):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Esses arquivos JSON são armazenados na subpasta `temps` sob o contêiner do cluster HDInsight. 

### <a name="define-the-input-source"></a>Definir a fonte de entrada

Primeiro, configure um DataFrame que descreve a origem dos dados e as configurações exigidas por essa fonte. Esse exemplo extrai os arquivos JSON no Armazenamento do Microsoft Azure e aplica um esquema a eles em tempo de leitura.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Aplicar a consulta

Em seguida, aplique uma consulta que contenha as operações desejadas em relação ao DataFrame de Streaming. Neste caso, uma agregação agrupa todas as linhas em janelas de 1 hora e, em seguida, calcula as temperaturas mínima, média e máxima nessa janela de 1 hora.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Definir o coletor de saída

Em seguida, defina o destino para as linhas que são adicionadas à tabela de resultados em cada intervalo de gatilho. Este exemplo produz apenas todas as linhas para uma tabela na memória `temps` que mais tarde você pode consultar com SparkSQL. O modo de saída completa garante que todas as linhas de todas as janelas sejam geradas toda vez.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Iniciar a consulta

Inicie a consulta de streaming e execute-a até receber um sinal de encerramento. 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Exibir os resultados

Enquanto a consulta está em execução, na mesma SparkSession, você pode executar uma consulta SparkSQL em relação à tabela `temps` onde os resultados da consulta são armazenados. 

    select * from temps

Esta consulta gera resultados semelhantes a:


| janela |  mín(temp) | méd(Temp) | máx(temp) |
| --- | --- | --- | --- |
|{u'start': u'2016-07-26T02:00:00.000Z', u'end'... |    95 |    95.231579 | 99 |
|{u'start': u'2016-07-26T03:00:00.000Z', u'end'...  |95 |   96.023048 | 99 |
|{u'start': u'2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': u'2016-07-26T05:00:00.000Z', u'end'...  |95 |   96.984639 | 99 |
|{u'start': u'2016-07-26T06:00:00.000Z', u'end'...  |95 |   97.014749 | 99 |
|{u'start': u'2016-07-26T07:00:00.000Z', u'end'...  |95 |   96.980971 | 99 |
|{u'start': u'2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

Para obter detalhes sobre a API do Spark Structured Stream, junto com as fontes de dados de entrada, operações e coletores de saída que ele suporta, consulte [Guia de programação de streaming estruturado do Apache Spark](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Pontos de verificação e logs write-ahead

Para fornecer resiliência e tolerância a falhas, o Structured Streaming conta com um *ponto de verificação* para garantir que o processamento do fluxo continue sem interrupções, mesmo com falhas nos nós. No HDInsight, o Spark cria pontos de verificação para que o armazenamento seja durável, Armazenamento do Microsoft Azure ou Data Lake Storage. Esses pontos de verificação armazenam as informações de progresso sobre a consulta de streaming. Além disso, o Structured Streaming utiliza um *log write-ahead* (WAL). O WAL captura dados ingeridos que foram recebidos, mas ainda não processados por uma consulta. Se ocorrer uma falha e o processamento for reiniciado pelo WAL, quaisquer eventos recebidos da fonte não são perdidos.

## <a name="deploying-spark-streaming-applications"></a>Implantar aplicativos Spark Streaming

Normalmente, você cria um aplicativo Spark Streaming localmente em um arquivo JAR e, em seguida, implanta-o no Spark no HDInsight copiando o arquivo JAR para o armazenamento padrão anexado ao cluster HDInsight. Você pode iniciar seu aplicativo com as APIs REST do [Apache Livy](https://livy.incubator.apache.org/) disponíveis em seu cluster usando uma operação POST. O corpo do POST inclui um documento JSON que fornece o caminho para o JAR, o nome da classe cujo método principal define e executa o aplicativo de streaming e, opcionalmente, os requisitos de recursos do trabalho (como a quantidade de executores, memória e núcleos) e as definições de configuração exigidas pelo código do aplicativo.

![Implantando um aplicativo Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

O status de todos os aplicativos também pode ser verificado com uma solicitação GET em um ponto de extremidade LIVY. Por fim, encerre um aplicativo em execução emitindo uma solicitação DELETE no ponto de extremidade do LIVY. Para detalhes sobre a API LIVY, veja [ Trabalhos remotos com o Apache LIVY ](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Próximas etapas

* [Criar um cluster Apache Spark no HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Guia de programação de streaming estruturado do Apache Spark](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Iniciar trabalhos do Apache Spark remotamente com LIVY Apache](apache-spark-livy-rest-interface.md)
