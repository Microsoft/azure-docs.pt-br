---
title: Gerenciar os recursos para o cluster do Apache Spark no Azure HDInsight
description: Saiba como gerenciar recursos para clusters Spark no Azure HDInsight para melhorar o desempenho.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/12/2021
ms.openlocfilehash: ff7cfe8ad09201df20db89e14f8c175e678e5107
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929804"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gerenciar os recursos para o cluster do Apache Spark no Azure HDInsight

Saiba como acessar as interfaces como a interface do usuário do [Apache Ambari](https://ambari.apache.org/) , a interface do usuário do [amApache Hadoop yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) e o [servidor de histórico do Spark](./apache-azure-spark-history-server.md) associado ao cluster [Apache Spark](https://spark.apache.org/) e como ajustar a configuração do cluster para obter um desempenho ideal.

## <a name="open-the-spark-history-server"></a>Abrir o servidor de histórico do Spark

O Servidor de Histórico do Spark é a interface do usuário da Web para aplicativos Spark concluídos e em execução. É uma extensão da interface do usuário da Web do Spark. Para obter informações completas, consulte [servidor de histórico do Spark](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Abrir a interface do usuário do Yarn

É possível usar a interface do usuário do YARN para monitorar aplicativos que estão em execução no momento no cluster Spark.

1. No [portal do Azure](https://portal.azure.com/), abra o cluster Spark. Para obter mais informações, consulte [Listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters).

2. Em **painéis de cluster**, selecione **yarn**. Quando solicitado, insira as credenciais de administrador para o cluster Spark.

    ![Iniciar Interface do usuário do YARN](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Alternativamente, também é possível iniciar a interface do usuário do YARN na interface do usuário do Ambari. Na interface do usuário do amAmbari, navegue até **yarn**  >  **links rápidos**  >  **Active**  >  **Resource Manager UI**.

## <a name="optimize-clusters-for-spark-applications"></a>Otimizar clusters para aplicativos do Spark

Os três principais parâmetros que podem ser usados para a configuração do Spark dependendo dos requisitos de aplicativo são `spark.executor.instances`, `spark.executor.cores` e `spark.executor.memory`. Um Executor é um processo iniciado por um aplicativo Spark. Ele é executado no nó de trabalho e é responsável por realizar as tarefas do aplicativo. O número padrão de executores e os tamanhos do executor para cada cluster são calculados com base no número de nós de trabalho e no tamanho do nó de trabalho. Essas informações são armazenadas em `spark-defaults.conf` nos nós do cabeçalho do cluster.

Os três parâmetros de configuração podem ser definidos no nível de cluster (para todos os aplicativos que são executados no cluster) ou também podem ser especificados para cada aplicativo individualmente.

### <a name="change-the-parameters-using-ambari-ui"></a>Alterar os parâmetros usando a interface de usuário do Ambari

1. Na interface do usuário do amAmbari, navegue até **Spark2**  >  **configurações**  >  **personalizado Spark2-padrões**.

    ![Definir parâmetros usando Ambari personalizado](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Definir parâmetros usando Ambari personalizado")

1. Ter quatro aplicativos Spark em execução simultaneamente no cluster é o número de valores padrão ideal. Você pode alterar esses valores na interface do usuário, conforme mostrado na seguinte captura de tela:

    ![Definir parâmetros usando o Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Definir parâmetros usando o Ambari")

1. Selecione **salvar** para salvar as alterações de configuração. Na parte superior da página, você será solicitado a reiniciar todos os serviços afetados. Selecione **Reiniciar**.

    ![Reiniciar serviços](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar os parâmetros de um aplicativo em execução no Jupyter Notebook

Para aplicativos em execução no Jupyter Notebook, você pode usar a `%%configure` mágica para fazer as alterações de configuração. De modo ideal, você deve fazer tais alterações no início do aplicativo, antes de executar a primeira célula de código. Isso garante que a configuração seja aplicada à sessão Livy quando for criada. Se quiser alterar a configuração em uma fase posterior no aplicativo, você deverá usar o parâmetro `-f` . No entanto, ao fazer isso, todo o progresso do aplicativo é perdido.

O snippet a seguir mostra como alterar a configuração para um aplicativo em execução no Jupyter.

```scala
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

Os parâmetros devem ser passados como uma cadeia de caracteres JSON e devem estar na linha seguinte, logo após a mágica, conforme mostrado na coluna de exemplo.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Alterar os parâmetros de um aplicativo enviado usando spark-submit

O comando a seguir é um exemplo de como alterar os parâmetros de configuração para um aplicativo em lote que é enviado usando `spark-submit`.

```scala
spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>
```

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Alterar os parâmetros de um aplicativo enviado usando cURL

O comando a seguir é um exemplo de como alterar os parâmetros de configuração para um aplicativo em lote que é enviado usando o cURL.

```bash
curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches
```

> [!Note]
> Copie o arquivo JAR para sua conta de armazenamento de cluster. Não copie o arquivo JAR diretamente para o nó principal.

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Alterar esses parâmetros em um Servidor Spark Thrift

O Servidor Thrift Spark fornece acesso JDBC/ODBC a um cluster Spark e é usado para atender às consultas SQL do Spark. Ferramentas como Power BI, tableau e assim por diante, usam o protocolo ODBC para se comunicar com o servidor Spark Thrift para executar consultas SQL do Spark como um aplicativo Spark. Quando um cluster Spark é criado, as duas instâncias do Servidor Thrift Spark são iniciadas, uma em cada nó de cabeçalho. Cada Servidor Thrift Spark é visto como um aplicativo Spark na interface de usuário do YARN.

O servidor Spark Thrift usa a alocação de executor dinâmico do Spark e, portanto, o `spark.executor.instances` não é usado. Em vez disso, o Servidor Thrift Spark usa `spark.dynamicAllocation.maxExecutors` e `spark.dynamicAllocation.minExecutors` para especificar a contagem do executor. Os parâmetros de configuração `spark.executor.cores` e `spark.executor.memory` são usados para modificar o tamanho do executor. Altere esses parâmetros, conforme mostrado nas seguintes etapas:

* Expanda a categoria **avançado spark2-Thrift-sparkconf** para atualizar os parâmetros `spark.dynamicAllocation.maxExecutors` e `spark.dynamicAllocation.minExecutors` .

    ![Configurar o servidor Thrift Spark](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Configurar o servidor Thrift Spark")

* Expanda a categoria **spark2-Thrift-Sparkconf personalizada** para atualizar os parâmetros `spark.executor.cores` e `spark.executor.memory` .

    ![Configurar o parâmetro de servidor Spark thrift](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Configurar o parâmetro de servidor Spark thrift")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Alterar a memória do driver do servidor Spark Thrift

A memória do driver do Servidor Spark Thrift é configurada para 25% do tamanho da RAM do nó de cabeçalho, desde que o tamanho total da RAM do nó de cabeçalho seja superior a 14 GB. Use a interface do usuário do Ambari para alterar a configuração da memória do driver, conforme mostrado na seguinte captura de tela:

Na interface do usuário do amAmbari, navegue até **Spark2**  >  **configurações**  >  **avançadas Spark2-env**. Em seguida, forneça o valor para **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Recuperar recursos do cluster Spark

Devido à alocação dinâmica do Spark, os únicos recursos que são consumidos pelo servidor Thrift são os recursos para os dois mestres de aplicativo. Para recuperar esses recursos, é necessário interromper os serviços do Servidor Thrift em execução no cluster.

1. Na interface do usuário do amAmbari, no painel esquerdo, selecione **Spark2**.

2. Na página seguinte, selecione **Spark2 Thrift Servers**.

    ![Reiniciar o Thrift Server1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Reiniciar o Thrift Server1")

3. Você deve ver as duas cabeçalho em que o Spark2 Thrift Server está em execução. Selecione um dos cabeçalho.

    ![Reiniciar o Thrift Servidor2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Reiniciar o Thrift Servidor2")

4. A próxima página lista todos os serviços em execução nesse nó de cabeçalho. Na lista, selecione o botão suspenso ao lado de Spark2 Thrift Server e, em seguida, selecione **parar**.

    ![Reiniciar Thrift Server3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Reiniciar Thrift Server3")
5. Repita essas etapas no outro nó de cabeçalho.

## <a name="restart-the-jupyter-service"></a>Reiniciar o serviço Jupyter

Inicie a interface do usuário da Web do Ambari, conforme mostrado no início do artigo. No painel de navegação esquerdo, selecione **Jupyter**, selecione **ações de serviço** e, em seguida, selecione **reiniciar tudo**. Isso iniciará o serviço Jupyter em todos os nós de cabeçalho.

![Reiniciar Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Reiniciar Jupyter")

## <a name="monitor-resources"></a>Monitorar recursos

Inicie a interface do usuário do Yarn, conforme mostrado no início do artigo. Na tabela de métricas de Cluster na parte superior da tela, verifique os valores de **memória usada** e **memória Total** colunas. Se os dois valores estiverem próximos, talvez não haja recursos suficientes para iniciar o próximo aplicativo. O mesmo se aplica para o **VCores usado** e **VCores Total** colunas. Além disso, na exibição principal, se houver um aplicativo permaneceu no estado **aceito** e não estiver fazendo a transição para o estado **em execução** ou **falha** , isso também pode ser uma indicação de que ele não está obtendo recursos suficientes para iniciar.

![Limite de recursos](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Limite de recursos")

## <a name="kill-running-applications"></a>Encerrar aplicativos em execução

1. Na interface do usuário do amYarn, no painel esquerdo, selecione **em execução**. Na lista de aplicativos em execução, determine o aplicativo a ser eliminado e selecione a **ID**.

    ![Kill App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Kill App1")

2. Selecione **eliminar aplicativo** no canto superior direito e, em seguida, selecione **OK**.

    ![Eliminar App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Eliminar App2")

## <a name="see-also"></a>Veja também

* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Para analistas de dados

* [Apache Spark com Machine Learning: use o Spark no HDInsight para analisar a temperatura do edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados do Application Insight telemetria usando o Apache Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Para desenvolvedores do Apache Spark

* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA para depurar os aplicativos do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com notebooks Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
