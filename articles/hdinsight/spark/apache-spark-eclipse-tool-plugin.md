---
title: 'Azure Toolkit for Eclipse: criar aplicativos escalares para o HDInsight Spark'
description: Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para desenvolver aplicativos do Spark escritos em Scala e enviá-los para um cluster do HDInsight Spark, diretamente do IDE Eclipse.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: e4f19afc0edf0f43b64548ae1c93ed5ff8dfa8a1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866566"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Use o Azure Toolkit for Eclipse para criar aplicativos do Apache Spark para um cluster do HDInsight

Use o HDInsight Tools no Azure Toolkit para [Eclipse](https://www.eclipse.org/) para desenvolver [aplicativos do Apache Spark](https://spark.apache.org/) escritos na [Scala](https://www.scala-lang.org/) e enviá-los para um cluster do Azure HDInsight Spark diretamente do Eclipse IDE. Você pode usar o plug-in Ferramentas do HDInsight de algumas maneiras diferentes:

* Para desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark.
* Para acessar os recursos de cluster do Azure HDInsight Spark.
* Para desenvolver e executar um aplicativo Scala Spark localmente.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Java Developer Kit (JDK) versão 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [IDE do eclipse](https://www.eclipse.org/downloads/). Este artigo usa o Eclipse IDE para desenvolvedores Java.

## <a name="install-required-plug-ins"></a>Instalar plug-ins necessários

### <a name="install-azure-toolkit-for-eclipse"></a>Instalar o Azure Toolkit for Eclipse

Para obter instruções de instalação, consulte [Instalando o Kit de Ferramentas do Azure para Eclipse](/azure/developer/java/toolkit-for-eclipse/installation).

### <a name="install-the-scala-plug-in"></a>Instalar o plug-in Scala

Quando você abre o Eclipse, as ferramentas do HDInsight detectam automaticamente se você instalou o plug-in Scala. Selecione **OK** para continuar e, em seguida, siga as instruções para instalar o plug-in Eclipse Marketplace. Reinicie o IDE após a conclusão da instalação.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png" alt-text="Instalação automática do plug-in Scala" border="true":::

### <a name="confirm-plug-ins"></a>Confirmar plug-ins

1. Navegue até **ajuda**  >  **Eclipse Marketplace...**.

1. Selecione a guia **Instalado**.

1. Você deve ver pelo menos:
    * Azure Toolkit for Eclipse \<version> .
    * IDE escalar \<version> .

## <a name="sign-in-to-your-azure-subscription"></a>Entre em sua assinatura do Azure

1. Inicie o Eclipse IDE.

1. Navegue até **janela**  >   **Mostrar modo de exibição**  >  **outro...**  >  **Entrar.**..

1. Na caixa de diálogo **Mostrar exibição** , navegue até **Azure**  >  **Azure Explorer** e, em seguida, selecione **abrir**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png" alt-text="Modo de exibição do Apache Spark Eclipse" border="true":::

1. No **Azure Explorer**, clique com o botão direito do mouse no nó **Azure** e selecione **entrar**.

1. Na caixa de diálogo **entrar no Azure** , escolha o método de autenticação, selecione **entrar** e conclua o processo de entrada.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png" alt-text="Apache Spark o logon do Azure Eclipse" border="true":::

1. Depois que você estiver conectado, a caixa de diálogo **suas assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Pressione **selecionar** para fechar a caixa de diálogo.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png" alt-text="Caixa de diálogo Selecionar Assinaturas" border="true":::

1. No **Azure Explorer**, navegue até **Azure**  >   **hdinsight** para ver os clusters hdinsight Spark em sua assinatura.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png" alt-text="Clusters do HDInsight Spark no Azure Explorer3" border="true":::

1. Além disso, você pode expandir um nó de nome de cluster para ver os recursos (por exemplo, contas de armazenamento) associados ao cluster.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png" alt-text="Expandindo um nome de cluster para ver recursos" border="true":::

## <a name="link-a-cluster"></a>Vincular um cluster

Você pode vincular um cluster normal usando o nome de usuário gerenciado do Ambari. Da mesma forma, para um cluster HDInsight ingressado no domínio, crie o vínculo usando o domínio e o nome de usuário, como `user1@contoso.com`.

1. No **Azure Explorer**, clique com o botão direito do mouse em **HDInsight** e selecione **vincular um cluster**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png" alt-text="Menu de cluster de link do Azure Explorer" border="true":::

1. Insira o **nome do cluster**, o nome de **usuário** e a **senha** e, em seguida, selecione **OK**. Opcionalmente, insira a Conta de Armazenamento, Chave de Armazenamento e, em seguida, selecione o Contêiner de Armazenamento para o Gerenciador de armazenamento trabalhar no modo de exibição de árvore à esquerda

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png" alt-text="Caixa de diálogo Vincular novo cluster HDInsight" border="true":::

   > [!NOTE]  
   > Usamos a chave de armazenamento vinculada, nome de usuário e senha, se o cluster registrou na assinatura do Azure e vinculou um cluster.
   > :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png" alt-text="Contas de armazenamento do Azure Explorer" border="true":::
   >
   > Somente para o teclado, quando o foco atual está na **chave de armazenamento**, você precisa usar **Ctrl + Tab** para se concentrar no próximo campo da caixa de diálogo.

1. Você pode ver o cluster vinculado no **HDInsight**. Agora, você pode enviar um aplicativo para esse cluster vinculado.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png" alt-text="Cluster vinculado do Azure Explorer HDI" border="true":::

1. Também é possível desvincular um cluster a partir do **Azure Explorer**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png" alt-text="Azure Explorer cluster não vinculado" border="true":::

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configurar um projeto Spark Scala para um cluster HDInsight Spark

1. No espaço de trabalho do IDE do eclipse, selecione **arquivo**  >  **novo**  >  **projeto...**.

1. No assistente de **novo projeto** , selecione **projeto hdinsight**  >  **Spark no hdinsight (escala)**. Em seguida, selecione **Avançar**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png" alt-text="Selecionando o projeto Spark no HDInsight (Scala)" border="true":::

1. Na caixa de diálogo **Novo Projeto do HDInsight Scala**, forneça os seguintes valores e selecione **Avançar**:
   * Insira um nome para o projeto.
   * Na área **JRE**, verifique se **Usar um ambiente de execução JRE** está definido como **JavaSE-1.7** ou posterior.
   * Na área **Biblioteca do Spark**, você pode escolher a opção **Usar o Maven para configurar o SDK do Spark**.  Nossa ferramenta integra a versão apropriada para o SDK do Spark e o SDK do Scala. Você também pode escolher a opção **Adicionar SDK do Spark manualmente** , baixar e adicionar o SDK do Spark manualmente.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png" alt-text="Caixa de diálogo Novo Projeto de Scala HDInsight" border="true":::

1. Na próxima caixa de diálogo, examine os detalhes e selecione **concluir**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Criar um aplicativo Scala para cluster Spark no HDInsight

1. No **Explorador de pacotes**, expanda o projeto que você criou anteriormente. Clique com o botão direito do mouse em **src**, selecione **novo**  >  **...**.

1. Na caixa de diálogo **selecionar um assistente** , selecione **assistentes escalares**/  >  **objetos escalares**. Em seguida, selecione **Avançar**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png" alt-text="Selecionar um assistente criar um objeto escalar" border="true":::

1. Na caixa de diálogo **Criar Novo Arquivo**, insira um nome para o objeto e selecione **Concluir**. Um editor de texto será aberto.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png" alt-text="Assistente de novo arquivo criar novo arquivo" border="true":::

1. No editor de texto, substitua o conteúdo atual pelo código abaixo:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Execute o aplicativo em um cluster HDInsight Spark:

   a. No Explorador de Pacotes, clique com o botão direito do mouse no nome do projeto e escolha **Enviar Aplicativo Spark para HDInsight**.

   b. Na caixa de diálogo **envio do Spark** , forneça os seguintes valores e, em seguida, selecione **Enviar**:

   * Para **Nome do Cluster**, selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.
   * Selecione um artefato do projeto Eclipse ou selecionar uma opção do disco rígido. O valor padrão depende do item no qual você clica com o botão direito do mouse por meio do Gerenciador de Pacotes.
   * Na lista suspensa **Nome de classe principal**, o assistente de envio exibe todos os nomes de objeto do projeto. Selecione ou insira um que você deseja executar. Se tiver selecionado um artefato de um disco rígido, você deverá inserir o nome de classe principal manualmente. 
   * Como o código do aplicativo neste exemplo não requer argumentos de linha de comando ou JARs ou arquivos de referência, você pode deixar as caixas de texto restantes vazias.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png" alt-text="Caixa de diálogo Apache Spark envio" border="true":::

1. A guia **Envio de Spark** deve começar a exibir o progresso. Você pode interromper o aplicativo selecionando o botão vermelho na janela **Envio do Spark**. Você também pode exibir os logs para essa execução de aplicativo específica selecionando o ícone de globo (indicado pela caixa azul na imagem).

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png" alt-text="A janela Envio do Apache Spark" border="true":::

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Acessar e gerenciar clusters HDInsight Spark usando as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse

Você pode executar várias operações usando as Ferramentas do HDInsight, incluindo o acesso à saída do trabalho.

### <a name="access-the-job-view"></a>Acessar a exibição do trabalho

1. No **Azure Explorer**, expanda **HDInsight**, depois o nome do cluster Spark e selecione **trabalhos**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png" alt-text="Nó de exibição de trabalho do Azure Explorer Eclipse" border="true":::

1. Selecione o nó **Trabalhos**. Se a versão do Java é menor do que **1.8**, as Ferramentas do HDInsight lembram você automaticamente de instalar o plug-in **E(fx)clipse**. Selecione **OK** para continuar e, em seguida, siga o assistente para instalá-lo do Eclipse Marketplace e reiniciar o Eclipse.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png" alt-text="Instalar o plugin ausente E (FX) Clipse" border="true":::

1. Abra a Exibição de Trabalho do nó **Trabalhos**. No painel direito, a guia **Exibição de Trabalho do Spark** exibe todos os aplicativos que foram executados no cluster. Selecione o nome do aplicativo do qual você deseja ver mais detalhes.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png" alt-text="Exibir detalhes dos logs de trabalho do Apache Eclipse" border="true":::

   Você pode executar uma das seguintes ações:

   * Passe o mouse sobre o grafo de trabalho. Ele exibe informações básicas sobre o trabalho em execução. Selecione o grafo de trabalho e você poderá ver os estágios e as informações que cada trabalho gera.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png" alt-text="Informações de estágio do grafo de trabalho Apache Spark" border="true":::

   * Selecione a guia **Log** para exibir logs usados frequentemente, como **Stderr do Driver**, **Stdout do Driver** e **Informações do diretório**.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png" alt-text="Informações de log de trabalho do Apache Spark Eclipse" border="true":::

   * Abra a IU do histórico do Spark e a IU do Apache Hadoop YARN (no nível do aplicativo) selecionando os hiperlinks na parte superior da janela.

### <a name="access-the-storage-container-for-the-cluster"></a>Acessar o contêiner de armazenamento do cluster

1. No Azure Explorer, expanda o nó raiz **HDInsight** para ver uma lista de clusters HDInsight Spark disponíveis.

1. Expanda o nome do cluster para ver a conta de armazenamento e o contêiner de armazenamento padrão do cluster.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png" alt-text="Contêiner de armazenamento padrão e a conta de armazenamento" border="true":::

1. Selecione o nome do contêiner de armazenamento associado ao cluster. No painel direito, clique duas vezes na pasta **HVACOut**. Abra um dos arquivos **part-** para ver a saída do aplicativo.

### <a name="access-the-spark-history-server"></a>Acessar o servidor de histórico do Spark

1. No Azure Explorer, clique com o botão direito do mouse no nome do cluster Spark e escolha **Abrir a Interface do Usuário de Histórico do Spark**. Quando solicitado, insira as credenciais de administrador para o cluster. Elas foram especificadas no provisionamento do cluster.

1. No painel do Servidor de Histórico do Spark, procure o aplicativo que você acabou de executar usando o nome do aplicativo. No código anterior, você definiu o nome do aplicativo usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Dessa forma, o nome do aplicativo Spark era **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Iniciar o portal do Apache Ambari

1. No Azure Explorer, clique com o botão direito do mouse no nome do cluster Spark e escolha **Abrir o Portal de Gerenciamento do Cluster (Ambari)**.

1. Quando solicitado, insira as credenciais de administrador para o cluster. Elas foram especificadas no provisionamento do cluster.

### <a name="manage-azure-subscriptions"></a>Gerenciar assinaturas do Azure

Por padrão, a Ferramenta do HDInsight no Kit de Ferramentas do Azure para Eclipse lista os clusters Spark de todas as assinaturas do Azure. Se for necessário, você poderá especificar as assinaturas para as quais deseja acessar o cluster.

1. No Azure Explorer, clique com o botão direito do mouse no nó-raiz **Azure** e selecione **Gerenciar Assinaturas**.

1. Na caixa de diálogo, desmarque as caixas de seleção da assinatura que você não deseja acessar e selecione **Fechar**. Você também poderá escolher **Sair** se quiser sair da sua assinatura do Azure.

## <a name="run-a-spark-scala-application-locally"></a>Executar um aplicativo Scala Spark localmente

Você pode usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para executar aplicativos Spark Scala localmente em sua estação de trabalho. Normalmente, esses aplicativos não precisam acessar recursos de cluster como um contêiner de armazenamento e você pode executá-los e testá-los localmente.

### <a name="prerequisite"></a>Pré-requisito

Enquanto estiver executando o aplicativo do Spark escala local em um computador Windows, você poderá receber uma exceção, conforme explicado no [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Essa exceção ocorre porque **WinUtils.exe** está ausente no Windows.

Para resolver esse erro, você precisa [Winutils.exe](https://github.com/steveloughran/winutils) em um local como **C:\WinUtils\bin** e, em seguida, adicionar a variável de ambiente **HADOOP_HOME** e definir o valor da variável como **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Executar um aplicativo Scala Spark local

1. Inicie o Eclipse e crie um projeto. Na caixa de diálogo **Novo Projeto**, faça as opções a seguir e selecione **Avançar**.

1. No assistente de **novo projeto** , selecione **projeto HDInsight**  >  **Spark na amostra de execução local do hdinsight (escala)**. Em seguida, selecione **Avançar**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png" alt-text="Novo projeto seleciona uma caixa de diálogo do assistente" border="true":::

1. Para fornecer os detalhes do projeto, siga as etapas 3 a 6 da seção anterior [Configurar um projeto de Spark Scala para um cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. O modelo adiciona um código de exemplo (**LogQuery**) na pasta **src** que pode ser executada localmente em seu computador.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/local-scala-application.png" alt-text="Local do aplicativo escala de LogQuery local" border="true":::

1. Clique com o botão direito do mouse em **LogQuery. escala** e selecione **Executar como**  >  **um aplicativo escalar**. Uma saída como essa é exibida na guia **Console**:

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png" alt-text="Resultado da execução local do aplicativo Spark" border="true":::

## <a name="reader-only-role"></a>Função somente leitura

Quando os usuários enviam um trabalho a um cluster com permissão de função somente leitura, as credenciais do Ambari são solicitadas.

### <a name="link-cluster-from-context-menu"></a>Vincular cluster do menu de contexto

1. Entrar com conta com função somente leitura.

2. No **Azure Explorer**, expanda **HDInsight** para exibir os clusters do HDInsight em sua assinatura. Os clusters marcados **"Role:Reader"** só tem permissão para a função somente leitura.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png" alt-text="Clusters HDInsight Spark no leitor de função do Azure Explorer" border="true":::

3. Clique com botão direito do mouse no cluster com permissão para a função somente leitura. Selecione **Vincular este cluster** no menu de contexto para vincular o cluster. Insira o nome de usuário e a senha do Ambari.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png" alt-text="Clusters do HDInsight Spark no link do Azure Explorer" border="true":::

4. Se o cluster for vinculado com êxito, o HDInsight será atualizado.
   O estágio do cluster será se tornará vinculado.
  
    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png" alt-text="Clusters do HDInsight Spark no Azure Explorer vinculado" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>Vincular cluster expandindo o nó Trabalhos

1. Clique no nó **Trabalhos**, a janela **Acesso negado ao cluster Trabalhos** é exibida.

2. Clique em **Vincular este cluster** para vincular o cluster.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png" alt-text="Clusters do HDInsight Spark no Azure Explorer9" border="true":::

### <a name="link-cluster-from-spark-submission-window"></a>Vincular cluster da janela de envio do Spark

1. Crie um projeto do HDInsight.

2. Clique com o botão direito do mouse no pacote. Em seguida, selecione **Enviar aplicativo Spark ao HDInsight**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png" alt-text="Clusters do HDInsight Spark no Azure Explorer enviar" border="true":::

3. Selecione um cluster que tenha permissão de função somente de leitor para o **nome do cluster**. Mensagem de aviso mostrada. Você pode clicar em **vincular este cluster** para vincular o cluster.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png" alt-text="Clusters do HDInsight Spark no Azure Explorer link isso" border="true":::

### <a name="view-storage-accounts"></a>Exibir Contas de Armazenamento

* Para clusters com permissão de função somente leitura, clique no nó **Contas de Armazenamento**, a janela **Acesso Negado ao Armazenamento** aparece.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png" alt-text="Clusters do HDInsight Spark no armazenamento do Azure Explorer" border="true":::

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png" alt-text="Clusters HDInsight Spark no Azure Explorer negados" border="true":::

* Para clusters vinculados, clique no nó **Contas de Armazenamento**, a janela **Acesso Negado ao Armazenamento** aparece.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png" alt-text="Clusters do HDInsight Spark no Azure Explorer denied2" border="true":::

## <a name="known-problems"></a>Problemas conhecidos

Ao usar **vincular um cluster**, sugiro que você forneça credenciais de armazenamento.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png" alt-text="vincular cluster com a credencial de armazenamento eclipses" border="true":::

Há dois modos para enviar os trabalhos. Se a credencial de armazenamento for fornecida, o modo de lote será usado para enviar o trabalho. Caso contrário, o modo interativo será usado. Se o cluster estiver ocupado, poderá receber o erro abaixo.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png " alt-text="eclipse recebe um erro quando o cluster estiver ocupado" border="true":::

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png " alt-text="erro de Get do Eclipse quando o cluster está ocupado yarn" border="true":::

## <a name="see-also"></a>Confira também

* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: execute análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para analisar a temperatura do edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criando e executando aplicativos

* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Usar o Kit de Ferramentas do Azure para IntelliJ para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente por meio de VPN](./apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [ Use o Azure Toolkit for IntelliJ para depurar os aplicativos do Apache Spark remotamente por meio do SSH ](./apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com notebooks Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gerenciando recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)