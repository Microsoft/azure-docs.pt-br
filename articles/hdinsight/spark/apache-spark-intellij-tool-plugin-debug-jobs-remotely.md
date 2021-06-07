---
title: 'Kit de ferramentas do Azure: depurar aplicativos Apache Spark remotamente-Azure HDInsight'
description: Saiba como usar as ferramentas do HDInsight no Azure Toolkit for IntelliJ para depurar remotamente aplicativos Spark executados em clusters HDInsight por meio de VPN.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/28/2017
ms.openlocfilehash: e6ed52f89c38a9c4c5476bd1db0f5a67326954e4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866260"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Usar o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente no HDInsight por meio de VPN

Recomendamos a depuração remota do aplicativo [Apache Spark](https://spark.apache.org/) por meio do SSH. Para obter instruções, consulte [Depurar aplicativos Apache Spark remotamente em um cluster HDInsight com o Azure Toolkit for IntelliJ por meio do SSH](./apache-spark-intellij-tool-debug-remotely-through-ssh.md).

Este artigo oferece diretrizes passo a passo sobre como usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para enviar um trabalho do Spark no cluster do HDInsight Spark e depurá-lo remotamente do seu computador desktop. Para concluir essas tarefas, você deve executar as seguintes etapas de alto nível:

1. Crie uma rede virtual do Azure site a site ou ponto a site. As etapas neste documento pressupõem que você esteja usando uma rede site a site.
1. Crie um cluster do Spark no HDInsight que faça parte da rede virtual site a site.
1. Verifique a conectividade entre o nó de cabeçalho do cluster e sua área de trabalho.
1. Crie um aplicativo Scala no IntelliJ IDEA e configure-o para a depuração remota.
1. Execute e depure o aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Para mais informações, consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um cluster apache Spark no HDInsight**. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Kit de desenvolvimento Oracle Java**. É possível instalá-lo do [site da Oracle](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* **INTELLIJ ideia**. Este artigo usa a versão 2017.1. É possível instalá-lo do [site da JetBrains](https://www.jetbrains.com/idea/download/).
* **Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ**. As ferramentas do HDInsight para IntelliJ estão disponíveis como parte do Kit de Ferramentas do Azure para IntelliJ. Para obter instruções sobre como instalar o Kit de Ferramentas do Azure, consulte [Instalar o Kit de Ferramentas do Azure para IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Entre em sua assinatura do Azure no IntelliJ IDEA**. Siga as instruções em [Usar Azure Toolkit for IntelliJ para criar aplicativos Spark para um cluster HDInsight](apache-spark-intellij-tool-plugin.md).
* **Solução alternativa de exceção**. Durante a execução do aplicativo Scala Spark para depuração remota em um computador Windows, você pode receber uma exceção. Essa exceção é explicada em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) e ocorre devido a um arquivo WinUtils.exe ausente no Windows. Para contornar esse erro, você deve baixar [Winutils.exe](https://github.com/steveloughran/winutils) em um local como **C:\WinUtils\bin**. Adicione uma variável de ambiente **HADOOP_HOME** e defina o valor da variável como **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Etapa 1: Criar uma rede virtual do Azure

Siga as instruções dos links a seguir para criar uma rede virtual do Azure e então verifique a conectividade entre a área de trabalho e a rede virtual:

* [Criar uma VNet com uma conexão VPN site a site usando o portal do Azure](../../vpn-gateway/tutorial-site-to-site-portal.md)
* [Criar uma VNet com uma conexão VPN site a site usando o PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Configurar uma conexão ponto a site com uma rede virtual usando o PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Etapa 2: Criar um cluster HDInsight Spark

Você também deve criar um cluster Apache Spark no Azure HDInsight que faça parte da rede virtual do Azure criada. Use as informações disponíveis em [Criar clusters baseados em Linux no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Como parte da configuração opcional, selecione a rede virtual do Azure que você criou na etapa anterior.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Etapa 3: Verificar a conectividade entre o nó de cabeçalho do cluster e sua área de trabalho

1. Obter endereço IP do nó de cabeçalho. Abra a IU do Ambari para o cluster. Na folha do cluster, selecione **Painel**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png" alt-text="Selecionar painel no Apache Ambari" border="true":::

1. Na interface do usuário do Ambari, selecione **Hosts**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png" alt-text="Selecionar hosts no Apache Ambari" border="true":::

1. Você deve ver uma lista de nós de cabeçalho, nós de trabalho e nós do zookeeper. Os nós de cabeçalho têm um prefixo **hn**\*. Selecione o primeiro nó de cabeçalho.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png" alt-text="Localizar o nó de cabeçalho no Apache Ambari" border="true":::

1. No painel **Resumo** na parte inferior da página que abre, copie o **endereço IP** do nó de cabeçalho e o **nome do host**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png" alt-text="Localizar o endereço IP no Apache Ambari" border="true":::

1. Adicione o endereço IP e o nome do host do nó de cabeçalho no arquivo **hosts** no computador do qual você deseja executar e depurar os trabalhos do Spark remotamente. Isso permitirá que você se comunique com o nó de cabeçalho usando o endereço IP, bem como o nome do host.

   a. Abra um arquivo do Bloco de notas com permissões elevadas. No menu **Arquivo**, selecione **Abrir** e navegue até o local do arquivo hosts. Em um computador com Windows, o local é **C:\Windows\System32\Drivers\etc\hosts**.

   b. Adicione as seguintes informações ao arquivo **hosts**:

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. No computador conectado à rede virtual do Azure usada pelo cluster do HDInsight, verifique se você pode executar ping nos nós de cabeçalho usando o endereço IP, bem como o nome do host.

1. Use o SSH para conexão ao nó de cabeçalho do cluster usando as instruções em [Conectar-se a um cluster do HDInsight usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Do nó de cabeçalho do cluster, execute ping no endereço IP do computador desktop. Teste a conectividade com os dois endereços IP atribuídos ao computador:

    - Um para a conexão de rede
    - Um para a rede virtual do Azure

1. Repita estas etapas no outro nó de cabeçalho.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Etapa 4: Criar um aplicativo Apache Spark usando as Ferramentas do Azure Toolkit for IntelliJ e configurá-lo para a depuração remota

1. Abra o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo **Novo Projeto** , faça o seguinte:

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png" alt-text="Selecione o novo modelo de projeto no IntelliJ IDEA" border="true":::

    a. Selecione **HDInsight** > **Spark no HDInsight (Scala)** .

    b. Selecione **Avançar**.
1. Na caixa de diálogo **Novo Projeto** a seguir, faça o seguinte e, depois, selecione **Concluir**:

    - Insira um nome e uma localização para o projeto.

    - Na lista suspensa **SDK do Projeto**, selecione **Java 1.8** para o cluster do Spark 2.x ou selecione **Java 1.7** para o cluster do Spark 1.x.

    - Na lista suspensa **Versão do Spark**, o assistente de criação de projeto Scala integra a versão apropriada do SDK do Spark e do SDK do Scala. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo usa o **Spark 2.0.2 (Scala 2.11.8)** .
  
   :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png" alt-text="Selecione a versão do Spark e o SDK do projeto" border="true":::
  
1. O projeto do Spark cria automaticamente um artefato para você. Para exibir o artefato, faça o seguinte:

    a. No menu **arquivo** , selecione **estrutura do projeto**.

    b. Na caixa de diálogo **Estrutura do Projeto**, selecione **Artefatos** para exibir o artefato padrão que é criado. Você também pode criar seu próprio artefato selecionando o sinal de adição ( **+** ).

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png" alt-text="Artefatos da ideia IntelliJ criar jar" border="true":::

1. Adicione bibliotecas ao seu projeto. Para adicionar uma biblioteca, faça o seguinte:

    a. Clique com o botão direito do mouse no nome do projeto na árvore do projeto e selecione **Abrir Configurações do Módulo**.

    b. Na caixa de diálogo **Estrutura do Projeto**, selecione **Bibliotecas**, selecione o símbolo (**+**) e, em seguida, selecione **Do Maven**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png" alt-text="Biblioteca de download do IntelliJ IDEA" border="true":::

    c. Na caixa de diálogo **Baixar a Biblioteca do Repositório Maven**, pesquise e adicione as bibliotecas a seguir:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. Copie `yarn-site.xml` e `core-site.xml` do nó de cabeçalho do cluster e os adicione ao projeto. Use os comandos a seguir para copiar os arquivos. Você pode usar [Cygwin](https://cygwin.com/install.html) para executar os seguintes comandos `scp` para copiar os arquivos dos nós de cabeçalho do cluster:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Como nós já adicionamos o endereço IP e os nomes do host do nó de cabeçalho do cluster para o arquivo hosts na área de trabalho, podemos usar os comandos `scp` da seguinte maneira:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Adicione esses arquivos ao seu projeto copiando-os na pasta **/src** na sua árvore de projeto, por exemplo, `<your project directory>\src`.

1. Atualize o arquivo `core-site.xml` para fazer as alterações a seguir:

   a. Substitua a chave criptografada. O arquivo `core-site.xml` inclui a chave criptografada para a conta de armazenamento associada ao cluster. No arquivo `core-site.xml` que você adicionou ao projeto, substitua a chave criptografada pela chave de armazenamento real associada à conta de armazenamento padrão. Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md).

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Remova as entradas a seguir do `core-site.xml`:

    ```xml
    <property>
            <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
            <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
    </property>

    <property>
            <name>fs.azure.shellkeyprovider.script</name>
            <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
    </property>

    <property>
            <name>net.topology.script.file.name</name>
            <value>/etc/hadoop/conf/topology_script.py</value>
    </property>
    ```

   c. Salve o arquivo.

1. Adicione a classe principal ao seu aplicativo. No **Gerenciador de Projetos**, clique com o botão direito do mouse em **src**, aponte para **Novo** e escolha **Classe do Scala**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png" alt-text="IntelliJ IDEA selecionar a classe principal" border="true":::

1. Na caixa de diálogo **Criar Nova Classe do Scala**, forneça um nome, selecione **Objeto** na caixa **Tipo** e selecione **OK**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png" alt-text="IntelliJ IDEA criar nova classe escalar" border="true":::

1. No arquivo `MyClusterAppMain.scala` , cole o código a seguir. Esse código cria o contexto do Spark e abre um método `executeJob` do objeto `SparkSample`.

    ```scala
    import org.apache.spark.{SparkConf, SparkContext}

    object SparkSampleMain {
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("SparkSample")
                                    .set("spark.hadoop.validateOutputSpecs", "false")
        val sc = new SparkContext(conf)

        SparkSample.executeJob(sc,
                            "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                            "wasb:///HVACOut")
        }
    }
    ```

1. Repita as etapas 8 e 9 para adicionar um novo objeto Scala chamado `*SparkSample`. Adicione o código a seguir a essa classe. Esse código lê os dados do HVAC.csv (disponível em todos os clusters do HDInsight Spark). Ele recupera as linhas com apenas um dígito na sétima coluna no arquivo CSV e grava a saída em **/HVACOut** no contêiner padrão de armazenamento do cluster.

    ```scala
    import org.apache.spark.SparkContext

    object SparkSample {
        def executeJob (sc: SparkContext, input: String, output: String): Unit = {
        val rdd = sc.textFile(input)

        //find the rows which have only one digit in the 7th column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
        println(s)

        rdd1.saveAsTextFile(output)
        //rdd1.collect().foreach(println)
         }
    }
    ```

1. Repita as etapas 8 e 9 para adicionar uma nova classe chamada `RemoteClusterDebugging`. Essa classe implementa a estrutura de teste Spark usada para depuração de aplicativos. Adicione o código a seguir à classe `RemoteClusterDebugging`:

    ```scala
        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }
    ```

     Há dois aspectos importantes a serem observados:

      * Para `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, verifique se o assembly JAR do Spark está disponível no armazenamento de cluster no caminho especificado.
      * Para `setJars`, especifique o local em que o artefato JAR será criado. Geralmente, é `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.

1. Na classe `*RemoteClusterDebugging`, clique com botão direito do mouse na palavra-chave `test` e selecione **Criar Configuração de RemoteClusterDebugging**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png" alt-text="IntelliJ IDEA criar uma configuração remota" border="true":::

1. Na caixa de diálogo **Criar Configuração de RemoteClusterDebugging**, forneça um nome para a configuração e, em seguida, selecione **Tipo de teste** como o **Nome do teste**. Deixe todos os outros valores como configurações padrão. Selecione **Aplicar** e, depois, **OK**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png" alt-text="Criar configuração do RemoteClusterDebugging" border="true":::

1. Agora você deve ver uma lista suspensa de configuração **Execução Remota** na barra de menus.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png" alt-text="IntelliJ a lista suspensa execução remota" border="true":::

## <a name="step-5-run-the-application-in-debug-mode"></a>Etapa 5: Executar o aplicativo no modo de depuração

1. No projeto IntelliJ IDEA, abra `SparkSample.scala` e crie um ponto de interrupção ao lado de `val rdd1`. No menu pop-up **Criar ponto de interrupção para**, selecione **linha na função executeJob**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png" alt-text="IntelliJ IDEA adicionar um ponto de interrupção" border="true":::

1. Para executar o aplicativo, selecione o botão **Execução de Depuração** ao lado da lista suspensa de configuração de **Execução Remota**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png" alt-text="IntelliJ ideia selecione o botão de execução de depuração" border="true":::

1. Quando a execução do programa atingir o ponto de interrupção, você verá uma guia **Depurador** no painel inferior.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png" alt-text="IntelliJ IDEA exibir a guia depurador" border="true":::

1. Para adicionar uma inspeção, selecione o **+** ícone ().

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png" alt-text="IntelliJ Debug-Add-Watch-variável" border="true":::

    Neste exemplo, o aplicativo rompeu antes de a variável `rdd1` ter sido criada. Usando essa inspeção, podemos ver as primeiras cinco linhas na variável `rdd`. Selecione **Enter**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png" alt-text="IntelliJ executar o programa no modo de depuração" border="true":::

    O que você vê na imagem anterior é que, em runtime, você poderia consultar terabytes de dados e de depuração à medida que o aplicativo progride. Por exemplo, na saída mostrada na imagem anterior, você pode ver que a primeira linha da saída é um cabeçalho. Com base nessa saída, você pode modificar seu código do aplicativo para ignorar a linha de cabeçalho, se necessário.

1. Agora você pode selecionar o ícone **Retomar Programa** para prosseguir com a execução do aplicativo.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png" alt-text="IntelliJ IDEA selecionar retomar programa" border="true":::

1. Se o aplicativo for concluído com êxito, você verá uma saída semelhante à seguinte:

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png" alt-text="Saída do console do depurador IntelliJ IDEA" border="true":::

## <a name="next-steps"></a><a name="seealso"></a>Próximas etapas

* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração

* Crie o projeto Scala (vídeo): [Crie aplicativos Scala do Spark do Apache](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Usar o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente em um cluster do HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: realize a análise interativa de dados usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para analisar a temperatura do edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](./apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Use o Azure Toolkit for IntelliJ para criar aplicativos do Apache Spark para um cluster do HDInsight](apache-spark-intellij-tool-plugin.md)
* [ Use o Azure Toolkit for IntelliJ para depurar os aplicativos do Apache Spark remotamente por meio do SSH ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Use as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos do Apache Spark](./apache-spark-eclipse-tool-plugin.md)
* [Usar notebooks Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook em um cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com notebooks Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)