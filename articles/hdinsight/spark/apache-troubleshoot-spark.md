---
title: Solucionar problemas do Spark usando o Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre como trabalhar com o Apache Spark e o Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: a4dc7293c00097c7a5752e29bf7c9a203cbb31a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721160"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Solucionar problemas do Apache Spark usando o Azure HDInsight

Saiba mais sobre os principais problemas e suas soluções ao trabalhar com cargas de [Apache Spark](https://spark.apache.org/) no [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Como fazer para configurar um aplicativo Apache Spark usando o Ambari nos clusters?

### <a name="resolution-steps"></a>Etapas de resolução

Valores de configuração do Spark podem ser ajustadas ajudar a evitar uma exceção de OutofMemoryError de aplicativo do Apache Spark. As etapas a seguir mostram os valores de configuração do Spark padrão no Azure HDInsight: 

1. Na lista de clusters, selecione **Spark2**.

    ![Selecione o cluster na lista](./media/apache-troubleshoot-spark/update-config-1.png)

2. Selecione a guia **Configurações** .

    ![Selecione a guia Configurações](./media/apache-troubleshoot-spark/update-config-2.png)

3. Na lista de configurações, selecione **Custom-spark2-defaults**.

    ![Selecione custom-spark-defaults](./media/apache-troubleshoot-spark/update-config-3.png)

4. Procure a configuração do valor que você precisa ajustar, como **spark.executor.memory**. Nesse caso, o valor de **4608m** é alto demais.

    ![Selecione o campo spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Defina o valor para a configuração recomendada. O valor de **2048m** é recomendado para essa configuração.

    ![Altere o valor para 2048m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Salve o valor e, em seguida, salve a configuração. Na barra de ferramentas, selecione **Salvar**.

    ![Salvar a configuração e a configuração](./media/apache-troubleshoot-spark/update-config-6a.png)

    Você será notificado se todas as configurações precisarem de atenção. Observe os itens e, em seguida, selecione **Continuar Assim Mesmo**. 

    ![Selecione Continuar Assim Mesmo](./media/apache-troubleshoot-spark/update-config-6b.png)

    Escreva uma observação sobre as alterações de configuração e selecione **Salvar**.

    ![Insira uma observação sobre as alterações feitas](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Sempre que uma configuração é salva, você é solicitado a reiniciar o serviço. Selecione **Reiniciar**.

    ![Selecione reiniciar](./media/apache-troubleshoot-spark/update-config-7a.png)

    Confirme a reinicialização.

    ![Selecione Confirmar Reiniciar Tudo](./media/apache-troubleshoot-spark/update-config-7b.png)

    Você pode examinar os processos em execução.

    ![Examinar processos em execução](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Você pode adicionar configurações. Na lista de configurações, selecione **Custom-spark2-defaults** e, em seguida, selecione **Adicionar Propriedade**.

    ![Selecione adicionar propriedade](./media/apache-troubleshoot-spark/update-config-8.png)

9. Defina uma nova propriedade. Você pode definir uma única propriedade usando uma caixa de diálogo para configurações específicas, como o tipo de dados. Ou você pode definir várias propriedades usando uma definição por linha. 

    Neste exemplo, a propriedade **spark.driver.memory** é definida com um valor de **4g**.

    ![Definir nova propriedade](./media/apache-troubleshoot-spark/update-config-9.png)

10. Salve a configuração e, em seguida, reinicie o serviço conforme descrito nas etapas 6 e 7.

Essas alterações valem para todo o cluster, mas podem ser substituídas quando você enviar o trabalho do Spark.

### <a name="additional-reading"></a>Leitura adicional

[Envio de trabalho do Apache Spark em clusters do HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Como fazer para configurar um aplicativo Apache Spark usando um Jupyter Notebook nos clusters?

### <a name="resolution-steps"></a>Etapas de resolução

1. Para determinar quais configurações do Apache Spark precisam ser definidas e com quais valores, consulte O que causa uma exceção OutofMemoryError de um aplicativo Apache Spark.

2. Na primeira célula do bloco de anotações do Jupyter, após a diretiva **%%configure**, especifique as configurações do Spark em um formato JSON válido. Altere os valores reais conforme necessário:

    ![Adicionar uma configuração](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Leitura adicional

[Envio de trabalho do Apache Spark em clusters do HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Como fazer para configurar um aplicativo Apache Spark usando o Apache Livy nos clusters?

### <a name="resolution-steps"></a>Etapas de resolução

1. Para determinar quais configurações do Apache Spark precisam ser definidas e com quais valores, consulte O que causa uma exceção OutofMemoryError de um aplicativo Apache Spark. 

2. Envie o aplicativo Spark ao Livy usando um cliente REST, como cURL. Use um comando semelhante ao seguinte. Altere os valores reais conforme necessário:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Leitura adicional

[Envio de trabalho do Apache Spark em clusters do HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Como fazer para configurar um aplicativo Apache Spark usando o envio spark nos clusters?

### <a name="resolution-steps"></a>Etapas de resolução

1. Para determinar quais configurações do Apache Spark precisam ser definidas e com quais valores, consulte O que causa uma exceção OutofMemoryError de um aplicativo Apache Spark.

2. Inicie o shell do Spark usando um comando semelhante ao seguinte. Altere o valor real das configurações conforme necessário: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Leitura adicional

[Envio de trabalho do Apache Spark em clusters do HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-an-apache-spark-application-outofmemoryerror-exception"></a>O que causa uma exceção OutofMemoryError de um aplicativo Apache Spark?

### <a name="detailed-description"></a>Descrição detalhada

O aplicativo Spark falhou com os seguintes tipos de exceções não capturadas:

```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>Causa provável

A causa mais provável dessa exceção é que não há memória de heap suficiente alocada para as JVMs (máquinas virtuais Java). Essas JVMs são iniciadas como executores ou drivers, como parte do aplicativo Spark. 

### <a name="resolution-steps"></a>Etapas de resolução

1. Determine o tamanho máximo dos dados com que o aplicativo Spark lida. Você pode fazer uma previsão com base no tamanho máximo dos dados de entrada, nos dados intermediários produzidos transformando os dados de entrada e nos dados de saída produzidos quando o aplicativo está transformando ainda mais os dados intermediários. Esse processo poderá ser iterativo se você não puder fazer uma previsão inicial formal. 

2. Verifique se o cluster HDInsight que será usado tem recursos suficientes em termos de memória e de núcleos para acomodar o aplicativo Spark. Você pode determinar isso consultando a seção de métricas do cluster da interface do usuário do YARN para obter os valores da **Memória Usada** versus **Total de Memória** e de **VCores Usados** versus **VCores Totais**.

3. Defina as seguintes configurações de Spark com os valores apropriados, que não devem ultrapassar 90% da memória e dos núcleos disponíveis. Os valores devem estar dentro dos requisitos de memória do aplicativo Spark: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Para calcular a memória total usada por todos os executores: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Para calcular a memória total usada pelo driver:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Leitura adicional

- [Visão geral do gerenciamento de memória do Apache Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Depurar um aplicativo Apache Spark em um cluster HDInsight](https://web.archive.org/web/20190112152909/https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Veja também
[Solucionar problemas usando o Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
