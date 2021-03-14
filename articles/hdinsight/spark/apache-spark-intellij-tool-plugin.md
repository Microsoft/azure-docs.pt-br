---
title: 'Azure Toolkit for IntelliJ: aplicativo Spark – HDInsight'
description: Use o Kit de Ferramentas do Azure para IntelliJ a fim de desenvolver aplicativos Spark escritos em Scala e enviá-los a um cluster Spark do HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ced066623d23c6894745374ace812454c52f3109
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103420010"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Usar o Azure Toolkit for IntelliJ para criar aplicativos Apache Spark para um cluster do HDInsight

Este artigo demonstra como desenvolver aplicativos Apache Spark no Azure HDInsight usando o plug-in do **Kit de ferramentas do Azure** para o IDE IntelliJ. O [Azure HDInsight](../hdinsight-overview.md) é um serviço de análise gerenciado e de código aberto na nuvem. O serviço permite que você use estruturas de software livre, como Hadoop, Apache Spark, Apache Hive e Apache Kafka.

Você pode usar o plug-in do **Kit de ferramentas do Azure** de algumas maneiras:

* Desenvolva e envie um aplicativo do Spark para um cluster HDInsight Spark.
* Acessar os recursos de cluster Spark do Azure HDInsight.
* Desenvolver e executar um aplicativo Scala Spark localmente.

Neste artigo, você aprenderá como:
> [!div class="checklist"]
> * Usar o plug-in Azure Toolkit for IntelliJ
> * Desenvolver aplicativos do Apache Spark
> * Enviar um aplicativo para o cluster HDInsight do Azure

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Kit de desenvolvimento Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Este artigo usa a versão do Java 8.0.202.

* IntelliJ IDEA. Este artigo usa o [IntelliJ IDEA Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Confira [Installing the Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) (Instalação do Azure Toolkit for IntelliJ).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in Scala para IntelliJ IDEA

Etapas para instalar o plug-in escalar:

1. Abra o IntelliJ IDEA.

2. Na tela de boas-vindas, navegue até **Configurar** > **Plug-ins** para abrir a janela **Plug-ins**.

    ![IntelliJ IDEA habilita o plug-in escalar](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Selecione **Instalar** para o plug-in Scala caracterizado na nova janela.  

    ![IntelliJ IDEA instala o plug-in escalar](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Depois que o plug-in foi instalado com êxito, você deve reiniciar o IDE.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Criar um aplicativo Scala Spark para um cluster HDInsight Spark

1. Inicie o IntelliJ IDEA e selecione **Criar novo projeto** para abrir a janela **Novo projeto**.

2. Selecione **Azure Spark/HDInsight** no painel esquerdo.

3. Selecione **Projeto Spark (Scala)** na janela principal.

4. Na lista suspensa **ferramenta de compilação** , selecione uma das seguintes opções:
   * **Maven** para obter suporte ao assistente de criação de projetos Scala.
   * **SBT** para gerenciar as dependências e para criar no projeto Scala.

     ![A caixa de diálogo Novo Projeto no IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Selecione **Avançar**.

6. Na janela **Novo Projeto**, forneça as seguintes informações:  

    |  Propriedade   | Descrição   |  
    | ----- | ----- |  
    |Nome do projeto| Insira um nome.  Este artigo usa `myApp`.|  
    |Local do&nbsp;projeto| Insira o local no qual salvar o projeto.|
    |SDK do projeto| Esse campo pode estar em branco no primeiro uso da ideia.  Selecione **Novo...** e navegue até o JDK.|
    |Versão do Spark|O assistente de criação integra a versão apropriada para o SDK do Spark e o SDK do Scala. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Esse exemplo usa o **Spark 2.3.0 (Scala 2.11.8)** .|

    ![Selecionar o SDK do Apache Spark](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Selecione **Concluir**.  Pode levar alguns minutos antes que o projeto fique disponível.

8. O projeto do Spark cria automaticamente um artefato para você. Para exibir o artefato, execute as seguintes etapas:

   a. Na barra de menus, navegue até **Arquivo** > **Estrutura do projeto...** .

   b. Na janela **Estrutura do Projeto**, selecione **Artefatos**.  

   c. Selecione **Cancelar**  depois de exibir o artefato.

      ![Informações do artefato na caixa de diálogo](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Adicione o código-fonte do aplicativo executando as seguintes etapas:

    a. Em projeto, navegue até **MyApp**  >  **src**  >  **Main**  >  **escala**.  

    b. Clique com o botão direito do mouse em **scala** e, em seguida, navegue até **Novo** > **classe Scala**.

   ![Comandos para criar uma classe Scala do Projeto](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Na caixa de diálogo **Criar Nova Classe do Scala**, forneça um nome, selecione **Objeto** na lista suspensa **Tipo** e selecione **OK**.

     ![Criar caixa de diálogo Nova Classe Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. O arquivo **myApp.scala** então se abre na exibição principal. Substitua o código padrão pelo código localizado abaixo:  

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object myApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("myApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV file
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }

    }
    ```

    O código lê os dados no HVAC.csv (disponível em todos os clusters Spark do HDInsight), recupera as linhas com apenas um dígito na sétima coluna no arquivo CSV e grava a saída em `/HVACOut` no contêiner padrão de armazenamento do cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Conectar-se ao cluster HDInsight

O usuário pode [entrar na assinatura do Azure](#sign-in-to-your-azure-subscription)ou [vincular um cluster HDInsight](#link-a-cluster). Use a credencial Ambari/nome de usuário/senha ou ingressado no domínio para se conectar ao cluster HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Entre em sua assinatura do Azure

1. Na barra de menus, navegue até **Exibição** > **Janelas de Ferramentas** > **Azure Explorer**.

   ![IntelliJ IDEA mostra o Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. No Azure Explorer, clique com o botão direito do mouse no nó **Azure** e, em seguida, selecione **Entrar**.

   ![IntelliJ IDEA explorer clique com o botão direito do mouse no Azure](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. Na caixa de diálogo **Entrar no Azure**, escolha **Logon do Dispositivo** e selecione **Entrar**.

    ![' IntelliJ ideia Azure login Device login](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. Na caixa de diálogo **Logon no Dispositivo do Azure**, clique em **Copiar e Abrir**.

   ![' IntelliJ ideia Azure Device login](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. Na interface do navegador, cole o código e clique em **Avançar**.

   ![' Caixa de diálogo Inserir código da Microsoft para HDI '](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Insira suas credenciais do Azure e feche o navegador.

   ![' Caixa de diálogo Inserir email da Microsoft para HDI '](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. Depois que você estiver conectado, a caixa de diálogo **Selecionar Assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Selecione sua assinatura e, em seguida, selecione o botão **Selecionar**.

    ![A caixa de diálogo Selecionar Assinaturas](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. No **Azure Explorer**, expanda **HDInsight** para exibir os clusters Spark do HDInsight em suas assinaturas.

    ![IntelliJ IDEA exibição principal do Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Para exibir os recursos (por exemplo, contas de armazenamento) associados ao cluster, você poderá expandir ainda mais um nó de nome de cluster.

    ![Contas de armazenamento do Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Vincular um cluster

Você pode vincular um cluster HDInsight usando o nome de usuário gerenciado do Apache Ambari. Da mesma forma, para um cluster HDInsight ingressado no domínio, crie o vínculo usando o domínio e o nome de usuário, como `user1@contoso.com`. Also you can link Livy Service cluster.

1. Na barra de menus, navegue até **Exibição** > **Janelas de Ferramentas** > **Azure Explorer**.

1. No Azure Explorer, clique com o botão direito do mouse em **HDInsight** nó e selecione **Vincular um Cluster**.

   ![Menu de contexto do cluster do link do Azure Explorer](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. As opções disponíveis na janela **Vincular um Cluster** vão variar conforme o valor selecionado na lista suspensa **Tipo de Recurso de Link**.  Insira seus valores e, em seguida, selecione **OK**.

    * **Cluster HDInsight**  
  
        |Propriedade |Valor |
        |----|----|
        |Tipo de Recurso de Link|Selecione **Cluster do HDInsight** na lista suspensa.|
        |Nome/URL do cluster| Insira o nome do cluster.|
        |Tipo de autenticação| Deixe como **Autenticação Básica**|
        |Nome do Usuário| Insira o nome de usuário do cluster, o padrão é admin.|
        |Senha| Insira a senha do nome de usuário.|

        ![Caixa de diálogo vincular um cluster do IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Serviço Livy**  
  
        |Propriedade |Valor |
        |----|----|
        |Tipo de Recurso de Link|Selecione **Serviço Livy** na lista suspensa.|
        |Ponto de Extremidade do Livy| Inserir o Ponto de Extremidade Livy|
        |Nome do cluster| Insira o nome do cluster.|
        |Ponto de Extremidade do Yarn|Opcional.|
        |Tipo de autenticação| Deixe como **Autenticação Básica**|
        |Nome do Usuário| Insira o nome de usuário do cluster, o padrão é admin.|
        |Senha| Insira a senha do nome de usuário.|

        ![Caixa de diálogo vincular cluster do Livy do IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. Você pode ver seu cluster vinculado do nó do **HDInsight**.

   ![Azure Explorer cluster vinculado1](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. Também é possível desvincular um cluster a partir do **Azure Explorer**.

   ![Azure Explorer cluster não vinculado](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar um aplicativo Scala Spark em um cluster HDInsight Spark

Depois de criar um aplicativo Scala, você poderá enviá-lo ao cluster.

1. De Projeto, navegue até **myApp** > **src** > **main** > **scala** > **myApp**.  Clique com o botão direito do mouse em **myApp** e selecione **Enviar Aplicativo Spark** (provavelmente estará localizado na parte inferior da lista).

      ![O comando Enviar Aplicativo Spark para HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Na janela da caixa de diálogo **Enviar aplicativo Spark** , selecione **1. Spark no HDInsight**.

3. Na janela **Editar configuração**, forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Clusters do Spark (somente Linux)|Selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.|
    |Selecione um Artefato para enviar|Deixe a configuração padrão.|
    |Nome da classe principal|O valor padrão é a classe principal do arquivo selecionado. Você pode alterar a classe selecionando as reticência ( **...** ) e escolhendo outra classe.|
    |Configurações de trabalho|Você pode alterar as chaves padrão e, ou valores. Para obter mais informações, confira [API REST do Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html).|
    |Argumentos de linha de comando|Você pode inserir argumentos separados por espaço para a classe principal se necessário.|
    |Arquivos Referenciados e Jars Referenciados|você pode inserir os caminhos para os Jars e os arquivos referenciados, se houver. Você também pode procurar arquivos no sistema de arquivos virtual do Azure, que atualmente suporta apenas o cluster ADLS Gen 2. Para obter mais informações: [configuração de Apache Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Confira também [Como carregar recursos para o cluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md).|
    |Armazenamento de Upload de Trabalho|Expanda para revelar opções adicionais.|
    |Tipo de armazenamento|Selecione **Usar Blob do Azure para carregar** na lista suspensa.|
    |Conta de Armazenamento|Insira sua conta de armazenamento.|
    |Chave de Armazenamento|Insira sua chave de armazenamento.|
    |Contêiner de armazenamento|Selecione seu contêiner de armazenamento na lista suspensa depois que **Conta de Armazenamento** e **Chave de Armazenamento** tiverem sido inseridas.|

    ![A caixa de diálogo Envio do Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Selecione **SparkJobRun** para enviar seu projeto para o cluster selecionado. A guia **Trabalho do Spark Remoto no Cluster** exibe o andamento da execução do trabalho na parte inferior. Você pode parar o aplicativo clicando no botão vermelho.

    ![A janela Envio do Apache Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Depurar aplicativos do Apache Spark local ou remotamente em um cluster do HDInsight

Também recomendamos outra forma de enviar o aplicativo Spark ao cluster. Você pode fazer isso definido os parâmetros no IDE **Executar/Depurar configurações**. Consulte [depurar Apache Spark aplicativos localmente ou remotamente em um cluster HDInsight com Azure Toolkit for IntelliJ por meio de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Acessar e gerenciar clusters Spark do HDInsight usando o Kit de Ferramentas do Azure para IntelliJ

Você pode realizar várias operações usando Azure Toolkit for IntelliJ.  A maioria das operações é iniciada no **Azure Explorer**.  Na barra de menus, navegue até **Exibição** > **Janelas de Ferramentas** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Acessar a exibição do trabalho

1. No Azure Explorer, navegue até trabalhos do **HDInsight**  >  \<Your Cluster>  >  .

    ![IntelliJ nó de exibição de trabalho do Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. No painel direito, a guia **Exibição de Trabalho do Spark** exibe todos os aplicativos que foram executados no cluster. Selecione o nome do aplicativo do qual você deseja ver mais detalhes.

    ![Spark detalhes do aplicativo de exibição de trabalho](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Para exibir informações básicas do trabalho em execução, passe o mouse sobre o grafo de trabalhos. Para exibir o grafo de estágios e as informações geradas pelos trabalhos, escolha um nó no grafo de trabalhos.

    ![Detalhes de preparação de trabalhos da exibição de trabalho do Spark](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Para exibir logs usados frequentemente, como *Stderr do Driver*, *Stdout do Driver* e *Informações do diretório*, escolha a guia **Log**.

    ![Spark detalhes do Log de Exibição de Trabalho](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Você pode exibir a interface do usuário do histórico do Spark e a interface do usuário do YARN (no nível do aplicativo). Selecione um link na parte superior da janela.

### <a name="access-the-spark-history-server"></a>Acessar o servidor de histórico do Spark

1. No Azure Explorer, expanda o **HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e selecione **Abrir interface do usuário do Histórico Spark**.  
2. Quando for solicitado, insira as credenciais do administrador do cluster, que você especificou ao configurar o cluster.

3. No painel do servidor de histórico do Spark, é possível usar o nome do aplicativo para procurar o que você acabou de executar. No código anterior, você definiu o nome do aplicativo usando `val conf = new SparkConf().setAppName("myApp")`. O nome do aplicativo Spark é **MyApp**.

### <a name="start-the-ambari-portal"></a>Iniciar o portal do Ambari

1. No Azure Explorer, expanda **HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e selecione **Abrir Portal de Gerenciamento do Cluster (Ambari)**.  

2. Quando solicitado, insira as credenciais de administrador para o cluster. Você especificou essas credenciais durante o processo de configuração do cluster.

### <a name="manage-azure-subscriptions"></a>Gerenciar assinaturas do Azure

Por padrão, o Kit de Ferramentas do Azure para IntelliJ listam os clusters Spark de todas as suas assinaturas do Azure. Se for necessário, você poderá especificar as assinaturas que deseja acessar.  

1. No Azure Explorer, clique com o botão direito do mouse no nó-raiz **Azure** e selecione **Selecionar Assinaturas**.  

2. Na janela **Selecionar Assinaturas**, desmarque as caixas de seleção ao lado das assinaturas que você não deseja acessar e, em seguida, selecione **Fechar**.

## <a name="spark-console"></a>Console do Spark

Você pode executar o Console Local do Spark (Scala) ou o Console de Sessão Interativa do Spark Livy (Scala).

### <a name="spark-local-consolescala"></a>Console Local do Spark (Scala)

Verifique se você satisfez o pré-requisito do WINUTILS.EXE.

1. Na barra de menus, navegue até **Executar** > **Editar Configurações...** .

2. Na janela **Executar/depurar configurações**, no painel esquerdo, navegue até **Apache Spark no HDInsight** > **[Spark no HDInsight] myApp**.

3. Na janela principal, selecione a **`Locally Run`** guia.

4. Forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Classe principal do trabalho|O valor padrão é a classe principal do arquivo selecionado. Você pode alterar a classe selecionando as reticência ( **...** ) e escolhendo outra classe.|
    |Variáveis de ambiente|Verifique se o valor de HADOOP_HOME está correto.|
    |Localização de WINUTILS.exe|Verifique se o caminho está correto.|

    ![Configuração do conjunto de console local](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. De Projeto, navegue até **myApp** > **src** > **main** > **scala** > **myApp**.  

6. Na barra de menus, navegue até **Ferramentas** > **Console do Spark** > **Executar Console Local do Spark (Scala)** .

7. Em seguida, duas caixas de diálogo poderão ser exibidas para perguntar se você deseja corrigir automaticamente as dependências. Em caso afirmativo, selecione **Autocorreção**.

    ![IntelliJ IDEA Spark caixa de diálogo correção automática1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark caixa de diálogo correção automática2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. O console deve ser semelhante à imagem abaixo. Na janela do console, digite `sc.appName` e pressione ENTER.  O resultado será exibido. Você pode encerrar o console local clicando em um botão vermelho.

    ![IntelliJ IDEA resultado do console local](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Console de Sessão Interativa do Spark Livy (Scala)

1. Na barra de menus, navegue até **Executar** > **Editar Configurações...** .

2. Na janela **Executar/depurar configurações**, no painel esquerdo, navegue até **Apache Spark no HDInsight** > **[Spark no HDInsight] myApp**.

3. Na janela principal, selecione a **`Remotely Run in Cluster`** guia.

4. Forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Clusters do Spark (somente Linux)|Selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.|
    |Nome da classe principal|O valor padrão é a classe principal do arquivo selecionado. Você pode alterar a classe selecionando as reticência ( **...** ) e escolhendo outra classe.|

    ![Configuração do conjunto de console interativo](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. De Projeto, navegue até **myApp** > **src** > **main** > **scala** > **myApp**.  

6. Na barra de menus, navegue até **Ferramentas** > **Console do Spark** > **Executar Console de Sessão Interativa do Spark Livy (Scala)** .

7. O console deve ser semelhante à imagem abaixo. Na janela do console, digite `sc.appName` e pressione ENTER.  O resultado será exibido. Você pode encerrar o console local clicando em um botão vermelho.

    ![IntelliJ IDEA resultado do Console Interativo](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Enviar seleção para o Console do Spark

É conveniente para você prever o resultado do script enviando algum código para o console local ou Console (Scala) de sessão interativa Livy. Você pode realçar código no arquivo do Scala e, em seguida, clicar com o botão direito do mouse em **Enviar Seleção ao Console do Spark**. O código selecionado será enviado para o console. O resultado será exibido após o código no console. O console verificará erros, se houver.  

   ![Enviar seleção para o Console do Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integrar com o (HIB) Agente de Identidade do HDInsight

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Conectar-se ao cluster do HDInsight ESP com o agente de ID (HIB)

Você pode seguir as etapas normais para entrar na assinatura do Azure para se conectar ao cluster do HDInsight ESP com o agente de ID (HIB). Depois de entrar, você verá a lista de clusters no Azure Explorer. Para obter mais instruções, confira [Conectar-se a seu cluster do HDInsight](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Executar um aplicativo do Spark escalar em um cluster do HDInsight com o agente de ID (HIB)

Você pode seguir as etapas normais para enviar o trabalho ao cluster do HDInsight ESP com o agente de ID (HIB). Veja [Executar um aplicativo do Spark Scala em um cluster do HDInsight Spark](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster) para obter mais instruções.

Nós carregamos os arquivos necessários em uma pasta chamada com sua conta de entrada e você pode ver o caminho de upload no arquivo de configuração.

   ![fazer upload do caminho na configuração](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Console do Spark em um cluster do HDInsight com o agente de ID (HIB)

Você pode executar o console local do Spark (escala) ou executar o console de sessão interativa do Spark Livy (escala) em um cluster de ESP do HDInsight com o agente de ID (HIB). Veja o [Console do Spark](#spark-console) para obter mais instruções.

   > [!NOTE]  
   > No momento, o cluster do HDInsight ESP com o HIB (Agente de IDs) não é compatível com a [vinculação de um cluster](#link-a-cluster) e a [depuração de aplicativos do Apache Spark remotamente](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster).

## <a name="reader-only-role"></a>Função somente leitura

Quando os usuários enviam um trabalho a um cluster com permissão de função somente leitura, as credenciais do Ambari são solicitadas.

### <a name="link-cluster-from-context-menu"></a>Vincular cluster do menu de contexto

1. Entrar com conta com função somente leitura.

2. No **Azure Explorer**, expanda **HDInsight** para exibir os clusters do HDInsight em sua assinatura. Os clusters marcados **"Role:Reader"** só tem permissão para a função somente leitura.

    ![' IntelliJ função do Azure Explorer: leitor '](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Clique com botão direito do mouse no cluster com permissão para a função somente leitura. Selecione **Vincular este cluster** no menu de contexto para vincular o cluster. Insira o nome de usuário e a senha do Ambari.

    ![IntelliJ Azure Explorer vincular este cluster](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Se o cluster for vinculado com êxito, o HDInsight será atualizado.
   O estágio do cluster será se tornará vinculado.
  
    ![IntelliJ Azure Explorer caixa de diálogo vinculada](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Vincular cluster expandindo o nó Trabalhos

1. Clique no nó **Trabalhos**, a janela **Acesso negado ao cluster Trabalhos** é exibida.

2. Clique em **Vincular este cluster** para vincular o cluster.

    ![caixa de diálogo acesso negado ao trabalho do cluster](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Vincular cluster da janela Configurações de execução/depuração

1. Crie uma configuração do HDInsight. Depois selecione **Executar Remotamente em Cluster**.

2. Selecione um cluster, que tenha a permissão de função somente leitura, para **Clusters do Spark (somente Linux)**. Mensagem de aviso mostrada. Você pode clicar em **vincular este cluster** para vincular o cluster.

   ![IntelliJ IDEA executar/depurar criação de configuração](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Exibir Contas de Armazenamento

* Para clusters com permissão de função somente leitura, clique no nó **Contas de Armazenamento**, a janela **Acesso Negado ao Armazenamento** aparece. Você pode clicar em **Abrir Gerenciador de Armazenamento do Azure** para abrir o Gerenciador de Armazenamento.

   ![' IntelliJ ideia acesso ao armazenamento negado '](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![IntelliJ IDEA botão Acesso de Armazenamento Negado](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* Para clusters vinculados, clique no nó **Contas de Armazenamento**, a janela **Acesso Negado ao Armazenamento** aparece. Você pode clicar em **Abrir Armazenamento do Azure** para abrir o Gerenciador de Armazenamento.

   ![' IntelliJ IDEA Storage Access Denied2 '](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![IntelliJ IDEA botão Acesso de Armazenamento Negado2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Converter aplicativos IntelliJ IDEA existentes a fim de usar o Kit de Ferramentas do Azure para IntelliJ

Você pode converter os aplicativos Scala Spark existentes criados no IDEA do IntelliJ para serem compatíveis com o Kit de Ferramentas do Azure para IntelliJ. Em seguida, você pode usar o plug-in para enviar os aplicativos a um cluster Spark do HDInsight.

1. Para um aplicativo do Spark escalar existente criado por meio de IntelliJ IDEA, abra o `.iml` arquivo associado.

2. No nível raiz, é um elemento de **módulo** como o texto a seguir:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
    ```

   Edite o elemento a ser adicionado para `UniqueKey="HDInsightTool"` que o elemento **Module** seja semelhante ao seguinte texto:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
    ```

3. Salve as alterações. Seu aplicativo deve ser compatível com o Kit de Ferramentas do Azure para IntelliJ. Você pode testar isso clicando com o botão direito do mouse no nome do projeto no Projeto. Agora, o menu pop-up tem a opção **Enviar Aplicativo Spark ao HDInsight**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não for continuar a usar este aplicativo, exclua o cluster que criou seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Na caixa **Pesquisar** na parte superior, digite **HDInsight**.

1. Selecione **Clusters do HDInsight** em **Serviços**.

1. Na lista de clusters HDInsight que aparece, selecione o **...** ao lado do cluster que você criou para este artigo.

1. Selecione **Excluir**. Selecione **Sim** na barra superior.

![portal do Azure exclui o cluster HDInsight](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Excluir cluster HDInsight")

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o plug-in Azure Toolkit for IntelliJ para desenvolver Apache Spark aplicativos escritos em [escalabilidade](https://www.scala-lang.org/). Em seguida, os enviou para um cluster HDInsight Spark diretamente do ambiente de desenvolvimento integrado (IDE) do IntelliJ. Avance para o próximo artigo para ver como os dados que você registrou no Apache Spark podem ser removidos em uma ferramenta de análise de BI, assim como Power BI.

> [!div class="nextstepaction"]
> [Analisar dados de Apache Spark usando Power BI](apache-spark-use-bi-tools.md)
