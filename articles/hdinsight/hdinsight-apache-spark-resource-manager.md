---
title: Gerenciar recursos para cluster do Apache Spark no Azure HDInsight | Microsoft Docs
description: Saiba como gerenciar recursos para clusters do Spark no Azure HDInsight para melhorar o desempenho.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 1d69c361b609a2f50ce11432bc422acd0d8cb178
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gerenciar os recursos para o cluster do Apache Spark no Azure HDInsight 

Neste artigo, você aprenderá como acessar interfaces do usuário, como a do Ambari, a do YARN e a do Servidor de Histórico do Spark, associadas ao seu cluster do Spark. Você também aprenderá a ajustar a configuração do cluster para obter desempenho ideal.

**Pré-requisitos:**

* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-do-i-launch-the-ambari-web-ui"></a>Como inicio a interface do usuário do Ambari Web?
1. No [portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.
2. Para o seu cluster do Spark, clique em **Painel**. Quando solicitado, insira as credenciais de administrador para o cluster Spark.

    ![Inicie o Ambari](./media/hdinsight-apache-spark-resource-manager/hdinsight-launch-cluster-dashboard.png "iniciar o Gerenciador de recursos")
3. Isso deve iniciar a interface do usuário Web do Ambari, como mostrado na captura de tela.

    ![Interface da Web Ambari](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Ambari Web da interface do usuário")   

## <a name="how-do-i-launch-the-spark-history-server"></a>Como inicio o Servidor de Histórico do Spark?
1. No [portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial).
2. Na folha do cluster, em **Links Rápidos**, clique em **Painel do Cluster**. Na folha **Painel do Cluster**, clique em **Servidor de Histórico do Spark**.

    ![Spark History Server](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Spark History Server")

    Quando solicitado, insira as credenciais de administrador para o cluster Spark.

## <a name="how-do-i-launch-the-yarn-ui"></a>Como inicio a interface do usuário do Yarn?
É possível usar a interface do usuário do YARN para monitorar aplicativos que estão em execução no momento no cluster Spark.

1. Na folha do cluster, clique em **Painel do Cluster** e em **YARN**.

    ![Iniciar Interface do usuário do YARN](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > Alternativamente, também é possível iniciar a interface do usuário do YARN na interface do usuário do Ambari. Para iniciar a interface de usuário do Ambari, na folha do cluster, clique em **Painel do Cluster** e em **Painel do Cluster HDInsight**. Na interface do usuário do Ambari, clique em **YARN**, em **Links Rápidos**, no Resource Manager ativo e, por fim, em **Interface do Usuário do Resource Manager**.
   >
   >

## <a name="what-is-the-optimum-cluster-configuration-to-run-spark-applications"></a>Qual é a configuração de cluster ideal para executar aplicativos Spark?
Os três principais parâmetros que podem ser usados para a configuração do Spark dependendo dos requisitos de aplicativo são `spark.executor.instances`, `spark.executor.cores` e `spark.executor.memory`. Um Executor é um processo iniciado por um aplicativo Spark. Ele é executado no nó de trabalho e é responsável por realizar as tarefas do aplicativo. O número padrão de executores e os tamanhos do executor para cada cluster são calculados com base no número de nós de trabalho e no tamanho do nó de trabalho. Essas informações são armazenadas em `spark-defaults.conf` nos nós do cabeçalho do cluster.

Os três parâmetros de configuração podem ser definidos no nível de cluster (para todos os aplicativos que são executados no cluster) ou também podem ser especificados para cada aplicativo individualmente.

### <a name="change-the-parameters-using-ambari-ui"></a>Alterar os parâmetros usando a interface de usuário do Ambari
1. Na interface do usuário do Ambari, clique em **Spark**, em **Configurações** e expanda **spark-defaults personalizados**.

    ![Definir parâmetros usando o Ambari](./media/hdinsight-apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Ter 4 aplicativos Spark em execução simultaneamente no cluster é o número de valores padrão ideal. Você pode alterar esses valores na interface do usuário, conforme mostrado abaixo.

    ![Definir parâmetros usando o Ambari](./media/hdinsight-apache-spark-resource-manager/set-executor-parameters.png)
3. Clique em **Salvar** para salvar as alterações na configuração. Na parte superior da página, será solicitado que você reinicie todos os serviços afetados. Clique em **Reiniciar**.

    ![Reiniciar serviços](./media/hdinsight-apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar os parâmetros para um aplicativo em execução no bloco de notas do Jupyter
Para aplicativos em execução no bloco de notas Jupyter, você pode usar a mágica `%%configure` para fazer as alterações na configuração. De modo ideal, você deve fazer tais alterações no início do aplicativo, antes de executar a primeira célula de código. Isso garante que a configuração seja aplicada à sessão Livy quando for criada. Se quiser alterar a configuração em uma fase posterior no aplicativo, você deverá usar o parâmetro `-f` . No entanto, ao fazer isso, todo o progresso do aplicativo será perdido.

O trecho de código abaixo mostra como alterar a configuração para um aplicativo em execução no Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Os parâmetros devem ser passados como uma cadeia de caracteres JSON e devem estar na linha seguinte, logo após a mágica, conforme mostrado na coluna de exemplo.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Alterar os parâmetros de um aplicativo enviado usando spark-submit
O comando a seguir é um exemplo de como alterar os parâmetros de configuração para um aplicativo em lote que é enviado usando `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Alterar os parâmetros de um aplicativo enviado usando cURL
O comando a seguir é um exemplo de como alterar os parâmetros de configuração para um aplicativo em lote que é enviado usando cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="how-do-i-change-these-parameters-on-a-spark-thrift-server"></a>Como altero esses parâmetros em um Servidor Thrift Spark?
O Servidor Thrift Spark fornece acesso JDBC/ODBC a um cluster Spark e é usado para atender às consultas SQL do Spark. Ferramentas como Power BI, Tableau, etc. usam o protocolo ODBC para se comunicar com o Servidor Thrift Spark e executar consultas SQL do Spark como um Aplicativo Spark. Quando um cluster Spark é criado, as duas instâncias do Servidor Thrift Spark são iniciadas, uma em cada nó de cabeçalho. Cada Servidor Thrift Spark é visto como um aplicativo Spark na interface de usuário do YARN.

O Servidor Thrift Spark usa a alocação dinâmica de executor e, portanto, `spark.executor.instances` não é usado. Em vez disso, o Servidor Thrift Spark usa `spark.dynamicAllocation.minExecutors` e `spark.dynamicAllocation.maxExecutors` para especificar a contagem do executor. Os parâmetros de configuração `spark.executor.cores` e `spark.executor.memory` são usados para modificar o tamanho do executor. É possível alterar esses parâmetros, conforme mostrado nas etapas a seguir.

* Expanda a categoria **spark-thrift-sparkconf avançada** para atualizar os parâmetros `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors` e `spark.executor.memory`.

    ![Configurar o servidor Thrift Spark](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-1.png)    
* Expanda a categoria **spark-thrift-sparkconf personalizada** para atualizar o parâmetro `spark.executor.cores`.

    ![Configurar o servidor Thrift Spark](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="how-do-i-change-the-driver-memory-of-the-spark-thrift-server"></a>Como altero a memória do driver do servidor Thrift Spark?
A memória do driver do Servidor Thrift Spark é configurada para 25% do tamanho da RAM do nó de cabeçalho, desde que o tamanho total dessa RAM seja superior a 14 GB. Você pode usar a interface de usuário do Ambari para alterar a configuração de memória do driver, conforme mostrado abaixo.

* Na interface de usuário do Ambari, clique em **Spark**, em **Configurações**, expanda **spark-env avançado** e forneça o valor para **spark_thrift_cmd_opts**.

    ![Configurar a RAM do servidor Thrift Spark](./media/hdinsight-apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="i-do-not-use-bi-with-spark-cluster-how-do-i-take-the-resources-back"></a>Não uso o BI com cluster Spark. Como recupero os recursos?
Uma vez que usamos a alocação dinâmica do Spark, os únicos recursos que são consumidos pelo servidor Thrift são os recursos para os dois mestres de aplicativo. Para recuperar esses recursos, você deve interromper os serviços do Servidor Thrift em execução no cluster.

1. Na interface de usuário do Ambari, no painel esquerdo, clique em **Spark**.
2. Na página seguinte, clique em **Servidor Thrift Spark**.

    ![Reiniciar o servidor Thrift](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-1.png)
3. Você deve ver os dois nós de cabeçalho nos quais o Servidor Thrift Spark está em execução. Clique em um dos nós de cabeçalho.

    ![Reiniciar o servidor Thrift](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-2.png)
4. A próxima página lista todos os serviços em execução nesse nó de cabeçalho. Na lista, clique no botão suspenso próximo ao servidor Thrift Spark e clique em **Parar**.

    ![Reiniciar o servidor Thrift](./media/hdinsight-apache-spark-resource-manager/restart-thrift-server-3.png)
5. Repita essas etapas no outro nó de cabeçalho.

## <a name="my-jupyter-notebooks-are-not-running-as-expected-how-can-i-restart-the-service"></a>Meus blocos de anotações do Jupyter não estão sendo executados conforme esperado. Como é possível reiniciar o serviço?
Inicie a interface do usuário do Ambari Web, conforme mostrado acima. No painel de navegação esquerdo, clique em **Jupyter**, em **Ações de Serviço** e em **Reiniciar Tudo**. Isso iniciará o serviço Jupyter em todos os nós de cabeçalho.

    ![Restart Jupyter](./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "Restart Jupyter")

## <a name="how-do-i-know-if-i-am-running-out-of-resources"></a>Como fazer para saber se estou ficando sem recursos?
Inicie a interface do usuário Yarn, conforme mostrado acima. Na tabela de métricas de Cluster na parte superior da tela, verifique os valores de **memória usada** e **memória Total** colunas. Se os 2 valores são muito parecidos, talvez não haja recursos suficientes para iniciar o próximo aplicativo. O mesmo se aplica para o **VCores usado** e **VCores Total** colunas. Além disso, no modo de exibição principal, se houver um aplicativo continuaram em **aceito** estado e não a transição para **executando** nem **falha** estado, isso também pode ser uma indicação de que ele não estiver obtendo recursos suficientes para iniciar.

    ![Resource Limit](./media/hdinsight-apache-spark-resource-manager/resource-limit.png "Resource Limit")

## <a name="how-do-i-kill-a-running-application-to-free-up-resource"></a>Como posso eliminar um aplicativo em execução para liberar recursos?
1. Na interface do usuário Yarn, no painel esquerdo, clique em **executando**. Na lista de aplicativos em execução, determine o aplicativo a ser interrompida e clique no **ID**.

    ![Eliminar App1](./media/hdinsight-apache-spark-resource-manager/kill-app1.png "Eliminar App1")

2. Clique em **Eliminar Aplicativo** no canto superior direito e clique em **OK**.

    ![Eliminar App2](./media/hdinsight-apache-spark-resource-manager/kill-app2.png "Eliminar App2")

## <a name="see-also"></a>Consulte também
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Para analistas de dados

* [Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)
* [Análise da telemetria de dados do Application Insight usando o Spark no HDInsight](hdinsight-spark-analyze-application-insight-logs.md)
* [Usar o Caffe no Azure HDInsight Spark para aprendizado aprofundado distribuído](hdinsight-deep-learning-caffe-spark.md)

### <a name="for-spark-developers"></a>Para desenvolvedores do Spark

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
