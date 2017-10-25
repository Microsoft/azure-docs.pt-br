---
title: Usar Livy Spark para enviar trabalhos para o cluster do Spark no Azure HDInsight | Microsoft Docs
description: Saiba como usar a API REST do Apache Spark para enviar trabalhos do Spark remotamente para um cluster do Azure HDInsight.
keywords: api rest do apache spark, spark livy
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: nitinme
ms.openlocfilehash: e1a28d69bbf40ea3134a7899a0d2fe70e5fc9e71
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Use a API REST do Apache Spark para enviar trabalhos remotos para um cluster do HDInsight Spark

Saiba como usar Livy, a API REST do Apache Spark, que é usado para enviar trabalhos remotos para um cluster do Azure HDInsight Spark. Para obter a documentação detalhada, confira [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Você pode usar a Livy para executar shells interativos do Spark ou enviar trabalhos em lotes a serem executados no Spark. Este artigo aborda como usar a Livy para enviar trabalhos em lotes. Os trechos nesse artigo usam cURL para fazer chamadas à API REST para o ponto de extremidade da Livy Spark.

**Pré-requisitos:**

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

* [cURL](http://curl.haxx.se/). Este artigo usa cURL para demonstrar como fazer chamadas à API REST em relação a um cluster do HDInsight Spark.

## <a name="submit-a-livy-spark-batch-job"></a>Enviar um trabalho em lotes do Livy Spark
Antes de enviar um trabalho em lotes, você deve carregar o jar do aplicativo no armazenamento de cluster associado ao cluster. Você pode usar [**AzCopy**](../storage/common/storage-use-azcopy.md), um utilitário de linha de comando, para fazer isso. Há muitos outros clientes que podem ser usados para carregar dados. É possível saber mais sobre eles em [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Exemplos**:

* Se o arquivo jar estiver no armazenamento de cluster (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Se quiser passar o nome do arquivo jar e o nome da classe como parte de um arquivo de entrada (nesse exemplo, input.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Obter informações sobre lotes Livy Spark em execução no cluster
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Exemplos**:

* Se quiser recuperar todos os lotes Livy spark em execução no cluster:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Se quiser recuperar um lote específico com uma batchID fornecida
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Excluir um trabalho em lotes do Livy Spark
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Exemplo**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livy Spark e alta disponibilidade
O Livy fornece alta disponibilidade para trabalhos Spark em execução no cluster. Aqui estão alguns exemplos.

* Se o serviço Livy falhar depois de você enviar um trabalho remotamente a um cluster Spark, o trabalho continuará em execução em segundo plano. Quando o Livy for backup, ele restaurará o status do trabalho e enviará o relatório de volta.
* Os blocos de notas Jupyter para HDInsight são ativados pelo Livy no back-end. Se um bloco de anotações estiver executando um trabalho do Spark e o serviço Livy for reiniciado, o bloco de notas continuará a executar as células de código. 

## <a name="show-me-an-example"></a>Mostrar um exemplo
Nesta seção, vamos examinar exemplos sobre como usar a Livy Spark para enviar um trabalho em lotes, monitorar o progresso do trabalho e excluir o trabalho. O aplicativo que usamos neste exemplo é o desenvolvido no artigo [Criar um aplicativo Scala autônomo para ser executado no cluster Spark no HDInsight](hdinsight-apache-spark-create-standalone-application.md). As etapas aqui supõem que:

* Você já copiou o jar do aplicativo para a conta de armazenamento associada ao cluster.
* Você tem o CuRL instalado no computador, onde está executando essas etapas.

Execute as seguintes etapas:

1. Primeiramente, vamos verificar se a Livy Spark está em execução no cluster. Podemos fazer isso obtendo uma lista de lotes em execução. Se estiver executando um trabalho usando a Livy pela primeira vez, a saída deverá ser zero.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Você deverá obter uma saída semelhante ao seguinte trecho:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Observe que a última linha da saída informa **total:0**, o que sugere que não há lotes em execução.

2. Agora vamos enviar um trabalho em lotes. O trecho a seguir usa um arquivo de entrada (input.txt) para passar o nome do jar e o nome de classe como parâmetros. Se você estiver executando essas etapas em um computador Windows, usar um arquivo de entrada será a abordagem recomendada.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Os parâmetros no arquivo **input.txt** são definidos da seguinte maneira:
   
        { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    Você deverá ver uma saída semelhante ao seguinte trecho:
   
        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Observe que a última linha da saída informa **state:starting**. Assim como, **id:0**. Aqui, **0** é a ID do lote.

3. Agora você pode recuperar o status desse lote específico usando a ID do lote.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Você deverá ver uma saída semelhante ao seguinte trecho:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Agora a saída mostra **state:success**, o que sugere que o trabalho foi concluído com êxito.

4. Se quiser, você pode excluir o lote.
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Você deverá ver uma saída semelhante ao seguinte trecho:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    A última linha da saída mostra que o lote foi excluído com êxito. Excluir um trabalho, enquanto ele está em execução, também encerra o trabalho. Se você excluir um trabalho que foi concluído, com êxito ou não, essa ação excluirá por completo as informações sobre o trabalho.

## <a name="using-livy-spark-on-hdinsight-35-clusters"></a>Uso do Livy Spark em clusters do HDInsight 3.5

O cluster HDInsight 3.5, por padrão, desabilita o uso de caminhos de arquivo local para acessar arquivos de dados de exemplo ou jars. Incentivamos o uso do caminho `wasb://` em vez disso, para acessar os jars ou os arquivos de dados de exemplo do cluster. Se você quiser usar o caminho local, atualize a configuração do Ambari adequadamente. Para fazer isso:

1. Acesse o portal do Ambari para o cluster. A interface de usuário da Web do Ambari está disponível no seu cluster HDInsight em https://**CLUSTERNAME**.azurehdidnsight.net, em que CLUSTERNAME é o nome do cluster.

2. Na navegação à esquerda, clique em **Livy** e em **Configurações**.

3. Em **livy-default** adicione o nome da propriedade `livy.file.local-dir-whitelist` e defina seu valor como **"/"** se você quiser permitir acesso total ao sistema de arquivos. Se você quiser permitir o acesso somente a um diretório específico, forneça o caminho ao diretório como o valor.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Enviar trabalhos da Livy para um cluster em uma rede virtual do Azure

Se você se conectar a um cluster do HDInsight Spark de dentro de uma Rede Virtual do Azure, você poderá se conectar diretamente à Livy no cluster. Nesse caso, a URL de ponto de extremidade da Livy é `http://<IP address of the headnode>:8998/batches`. Aqui, **8998** é a porta na qual a Livy é executada em um nó de cabeçalho do cluster. Para obter mais informações sobre como acessar serviços em portas não públicas, consulte [Portas usadas pelos serviços de Hadoop no HDInsight](hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Solucionar problemas

Veja aqui alguns dos problemas que você pode enfrentar ao usar o Livy para envio de trabalho remoto aos clusters Spark.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>Não há suporte para o uso de um jar externo no armazenamento adicional

**Problema:** se o trabalho da Livy Spark fizer referência a um jar externo da conta de armazenamento adicional associada ao cluster, o trabalho falhará.

**Resolução:** verifique se o jar que deseja usar está disponível no armazenamento padrão associado ao cluster HDInsight.





## <a name="next-step"></a>Próxima etapa

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

