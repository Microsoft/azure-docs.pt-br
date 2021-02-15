---
title: Solucionar problemas Apache Spark no Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre como trabalhar com o Apache Spark e o Azure HDInsight.
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: af488cd253e8a8ebedd838aa5286185ea556f69d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942503"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Solucionar problemas do Apache Spark usando o Azure HDInsight

Saiba mais sobre os principais problemas e suas resoluções ao trabalhar com cargas de Apache Spark no [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Como fazer para configurar um aplicativo Apache Spark usando o Ambari nos clusters?

Os valores de configuração do Spark podem ser ajustados ajudam a evitar uma exceção Apache Spark aplicativo `OutofMemoryError` . As etapas a seguir mostram os valores de configuração padrão do Spark no Azure HDInsight:

1. Faça logon no Ambari `https://CLUSTERNAME.azurehdidnsight.net` com suas credenciais de cluster. A tela inicial exibe um painel de visão geral. Há pequenas diferenças superficiais entre o HDInsight 3,6 e o 4,0.

1. Navegue até   >  **configurações** do Spark2.

    ![Selecione a guia Configurações](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. Na lista de configurações, selecione e expanda **personalizado-spark2-padrões**.

1. Procure a configuração do valor que você precisa ajustar, como **spark.executor.memory**. Nesse caso, o valor de **9728m** é muito alto.

    ![Selecione custom-spark-defaults](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Defina o valor para a configuração recomendada. O valor de **2048m** é recomendado para essa configuração.

1. Salve o valor e, em seguida, salve a configuração. Selecione **Salvar**.

    ![Altere o valor para 2048m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Escreva uma observação sobre as alterações de configuração e selecione **Salvar**.

    ![Insira uma observação sobre as alterações feitas](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Você será notificado se todas as configurações precisarem de atenção. Observe os itens e, em seguida, selecione **Continuar Assim Mesmo**.

    ![Selecione Continuar Assim Mesmo](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Sempre que uma configuração é salva, você é solicitado a reiniciar o serviço. Selecione **Reiniciar**.

    ![Selecione reiniciar](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Confirme a reinicialização.

    ![Selecione Confirmar Reiniciar Tudo](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Você pode examinar os processos em execução.

    ![Examinar processos em execução](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Você pode adicionar configurações. Na lista de configurações, selecione **Custom-spark2-defaults** e, em seguida, selecione **Adicionar Propriedade**.

    ![Selecione adicionar propriedade](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Defina uma nova propriedade. Você pode definir uma única propriedade usando uma caixa de diálogo para configurações específicas, como o tipo de dados. Ou você pode definir várias propriedades usando uma definição por linha.

    Neste exemplo, a propriedade **spark.driver.memory** é definida com um valor de **4g**.

    ![Definir nova propriedade](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Salve a configuração e, em seguida, reinicie o serviço conforme descrito nas etapas 6 e 7.

Essas alterações valem para todo o cluster, mas podem ser substituídas quando você enviar o trabalho do Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Como fazer configurar um aplicativo de Apache Spark usando um Jupyter Notebook em clusters?

Na primeira célula do Jupyter Notebook, após a diretiva **%% Configure** , especifique as configurações do Spark no formato JSON válido. Altere os valores reais conforme necessário:

![Adicionar uma configuração](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Como fazer para configurar um aplicativo Apache Spark usando o Apache Livy nos clusters?

Envie o aplicativo Spark ao Livy usando um cliente REST, como cURL. Use um comando semelhante ao seguinte. Altere os valores reais conforme necessário:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Como fazer para configurar um aplicativo Apache Spark usando o envio spark nos clusters?

Inicie o shell do Spark usando um comando semelhante ao seguinte. Altere o valor real das configurações conforme necessário:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Leituras adicionais

[Envio de trabalho do Apache Spark em clusters do HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* [Visão geral do gerenciamento de memória do Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Depurando o aplicativo Spark em clusters HDInsight](/archive/blogs/azuredatalake/spark-debugging-101).

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Como se conectar à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).