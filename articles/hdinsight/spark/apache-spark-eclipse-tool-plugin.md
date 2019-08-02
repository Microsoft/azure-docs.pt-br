---
title: 'Kit de Ferramentas do para Eclipse: Criar aplicativos Scala para HDInsight Spark '
description: Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para desenvolver aplicativos do Spark escritos em Scala e enviá-los para um cluster do HDInsight Spark, diretamente do IDE Eclipse.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: hrasheed
ms.openlocfilehash: 6e8f8e7d8324f23a81cd6ae3284bd6d7cff24117
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489849"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Use o Azure Toolkit for Eclipse para criar aplicativos do Apache Spark para um cluster do HDInsight

Use o HDInsight Tools no Azure Toolkit para [Eclipse](https://www.eclipse.org/) para desenvolver [aplicativos do Apache Spark](https://spark.apache.org/) escritos na [Scala](https://www.scala-lang.org/) e enviá-los para um cluster do Azure HDInsight Spark diretamente do Eclipse IDE. Você pode usar o plug-in Ferramentas do HDInsight de algumas maneiras diferentes:

* Para desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark.
* Para acessar os recursos de cluster do Azure HDInsight Spark.
* Para desenvolver e executar um aplicativo Scala Spark localmente.

> [!IMPORTANT]  
> Você pode usar essa ferramenta para criar e enviar aplicativos somente para um cluster HDInsight Spark no Linux.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit versão 8, que é usado para o tempo de execução do IDE do Eclipse. Você pode baixá-lo do [site da Oracle](https://aka.ms/azure-jdks).
* Eclipse IDE. Este artigo usa o Eclipse Neon. Você pode instalá-lo do [site do Eclipse](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Instalar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse e plug-in Scala

### <a name="install-azure-toolkit-for-eclipse"></a>Instalar Kit de Ferramentas do Azure para Eclipse
As Ferramentas do HDInsight para Eclipse estão disponíveis como parte do Kit de Ferramentas do Azure para Eclipse. Para obter instruções de instalação, consulte [Instalando o Kit de Ferramentas do Azure para Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Instalar o plug-in Scala
Quando você abre o Eclipse, a Ferramenta do HDInsight detecta automaticamente se você instalou o plug-in Scala. Selecione **OK** para continuar e, em seguida, siga as instruções para instalar o plug-in Eclipse Marketplace.

![Instalação automática do plug-in Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

O usuário pode [entrar a assinatura do Azure](#sign-in-to-your-azure-subscription), ou [vincular um cluster HDInsight](#link-a-cluster) usando a credencial de usuário/senha ou domínio unido do Ambari para iniciar. 

## <a name="sign-in-to-your-azure-subscription"></a>Entrar na assinatura do Azure
1. Inicie o IDE Eclipse e abra o Azure Explorer. No menu **Janela**, selecione **Mostrar Exibição** e selecione **Outros**. Na caixa de diálogo que é aberta, expanda **Azure**, selecione **Azure Explorer** e selecione **OK**.

   ![Caixa de Diálogo Mostrar Modo de Exibição](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
1. Clique com o botão direito do mouse no nó **Azure** e selecione **Entrar**.
1. Na caixa de diálogo **Entrada do Azure**, escolha o modo de autenticação, selecione **Entrar** e insira suas credenciais do Azure.
   
   ![Caixa de diálogo Entrar no Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
1. Após a entrada, a caixa de diálogo **Selecionar Assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Clique em **Selecionar** para fechar a caixa de diálogo.

   ![Caixa de diálogo Selecionar Assinaturas](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
1. Na guia **Azure Explorer**, expanda **HDInsight** para ver os clusters Spark do HDInsight da sua assinatura.
   
   ![Clusters Spark do HDInsight no Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
1. Além disso, você pode expandir um nó de nome de cluster para ver os recursos (por exemplo, contas de armazenamento) associados ao cluster.
   
   ![Expandindo um nome de cluster para ver recursos](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Vincular um cluster
Você pode vincular um cluster normal usando o nome de usuário gerenciado do Ambari. Da mesma forma, para um cluster HDInsight ingressado no domínio, crie o vínculo usando o domínio e o nome de usuário, como `user1@contoso.com`.

1. Selecione **Vincular um cluster** no **Azure Explorer**.

   ![menu de contexto para vincular cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Digite **Nome do Cluster**, **Nome de usuário** e **Senha**, clique no botão OK para vincular o cluster. Opcionalmente, insira a Conta de Armazenamento, Chave de Armazenamento e, em seguida, selecione o Contêiner de Armazenamento para o Gerenciador de armazenamento trabalhar no modo de exibição de árvore à esquerda
   
   ![caixa de diálogo para vincular cluster](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Usamos a chave de armazenamento vinculada, nome de usuário e senha, se o cluster registrou na assinatura do Azure e vinculou um cluster.
   > ![gerenciador de armazenamento no Eclipse](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. Se as informações estiverem corretas, será possível ver um cluster vinculado no nó **HDInsight**, após clicar no botão OK. Agora, você pode enviar um aplicativo para esse cluster vinculado.

   ![cluster vinculado](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. Também é possível desvincular um cluster a partir do **Azure Explorer**.
   
   ![cluster desvinculado](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configurar um projeto Spark Scala para um cluster HDInsight Spark

1. No workspace do IDE do Eclipse, selecione **Arquivo**, selecione **Novo**e, em seguida, selecione **Projeto**. 
1. No assistente Novo Projeto, expanda **HDInsight**, selecione **Spark no HDInsight (Scala)** e selecione **Avançar**.

   ![Selecionando o projeto Spark no HDInsight (Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
1. O assistente de criação de projetos Scala detecta automaticamente se você instalou o plug-in Scala. Selecione **OK** para continuar a baixar o plug-in Scala e, em seguida, siga as instruções para reiniciar o Eclipse.

   ![Verificação do Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
1. Na caixa de diálogo **Novo Projeto do HDInsight Scala**, forneça os seguintes valores e selecione **Avançar**:
   * Insira um nome para o projeto.
   * Na área **JRE**, verifique se **Usar um ambiente de execução JRE** está definido como **JavaSE-1.7** ou posterior.
   * Na área **Biblioteca do Spark**, você pode escolher a opção **Usar o Maven para configurar o SDK do Spark**.  Nossa ferramenta integra a versão apropriada para o SDK do Spark e o SDK do Scala. Você também pode escolher a opção **Adicionar o SDK do Spark manualmente**, baixar e adicionar o SDK do Spark manualmente.

   ![Caixa de diálogo Novo Projeto de Scala HDInsight](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
1. Na próxima caixa de diálogo, selecione **Concluir**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Criar um aplicativo Scala para cluster Spark no HDInsight

1. No IDE do Eclipse, em Explorador de Pacotes, expanda o projeto que você criou anteriormente, clique com botão direito do mouse em **src**, aponte para **Novo** e clique em **Outros**.
1. Na caixa de diálogo **Selecionar um assistente**, expanda **Assistentes Scala**, selecione **Objeto Scala** e selecione **Avançar**.
   
   ![Selecione uma caixa de diálogo do assistente](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
1. Na caixa de diálogo **Criar Novo Arquivo**, insira um nome para o objeto e selecione **Concluir**.
   
   ![Criar caixa de diálogo Novo Arquivo](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
1. Cole o seguinte código no editor de texto:
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
1. Execute o aplicativo em um cluster HDInsight Spark:
   
   a. No Explorador de Pacotes, clique com o botão direito do mouse no nome do projeto e escolha **Enviar Aplicativo Spark para HDInsight**.        
   b. Na caixa de diálogo **Envio do Spark**, forneça os valores a seguir e selecione **Enviar**:
      
   * Para **Nome do Cluster**, selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.
   * Selecione um artefato do projeto Eclipse ou selecionar uma opção do disco rígido. O valor padrão depende do item no qual você clica com o botão direito do mouse por meio do Gerenciador de Pacotes.
   * Na lista suspensa **Nome de classe principal**, o assistente de envio exibe todos os nomes de objeto do projeto. Selecione ou insira um que você deseja executar. Se tiver selecionado um artefato de um disco rígido, você deverá inserir o nome de classe principal manualmente. 
   * Como o código do aplicativo neste exemplo não exige argumentos de linha de comando ou JARs ou arquivos de referência, você pode deixar as caixas de texto restantes vazias.
        
     ![Caixa de diálogo Envio do Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
1. A guia **Envio de Spark** deve começar a exibir o progresso. Você pode interromper o aplicativo selecionando o botão vermelho na janela **Envio do Spark**. Você também pode exibir os logs para essa execução de aplicativo específica selecionando o ícone de globo (indicado pela caixa azul na imagem).
      
   ![Janela Envio do Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Acessar e gerenciar clusters HDInsight Spark usando as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse
Você pode executar várias operações usando as Ferramentas do HDInsight, incluindo o acesso à saída do trabalho.

### <a name="access-the-job-view"></a>Acessar a exibição do trabalho
1. No Azure Explorer, expanda **HDInsight**, expanda o nome do cluster Spark e escolha **Trabalhos**. 

   ![Nó de exibição de trabalho](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

1. Selecione o nó **Trabalhos**. Se a versão do Java é menor do que **1.8**, as Ferramentas do HDInsight lembram você automaticamente de instalar o plug-in **E(fx)clipse**. Selecione **OK** para continuar e, em seguida, siga o assistente para instalá-lo do Eclipse Marketplace e reiniciar o Eclipse. 

   ![Instalar E(fx)clipse](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Abra a Exibição de Trabalho do nó **Trabalhos**. No painel direito, a guia **Exibição de Trabalho do Spark** exibe todos os aplicativos que foram executados no cluster. Selecione o nome do aplicativo do qual você deseja ver mais detalhes.

   ![Detalhes do aplicativo](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Você pode executar uma das seguintes ações:

   * Passe o mouse sobre o grafo de trabalho. Ele exibe informações básicas sobre o trabalho em execução. Selecione o grafo de trabalho e você poderá ver os estágios e as informações que cada trabalho gera.

     ![Detalhes do estágio do trabalho](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Selecione a guia **Log** para exibir logs usados frequentemente, como **Stderr do Driver**, **Stdout do Driver** e **Informações do diretório**.

     ![Detalhes do log](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Abra a IU do histórico do Spark e a IU do Apache Hadoop YARN (no nível do aplicativo) selecionando os hiperlinks na parte superior da janela.

### <a name="access-the-storage-container-for-the-cluster"></a>Acessar o contêiner de armazenamento do cluster
1. No Azure Explorer, expanda o nó raiz **HDInsight** para ver uma lista de clusters HDInsight Spark disponíveis.
1. Expanda o nome do cluster para ver a conta de armazenamento e o contêiner de armazenamento padrão do cluster.
   
   ![Contêiner de armazenamento padrão e a conta de armazenamento](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
1. Selecione o nome do contêiner de armazenamento associado ao cluster. No painel direito, clique duas vezes na pasta **HVACOut**. Abra um dos arquivos **part-** para ver a saída do aplicativo.

### <a name="access-the-spark-history-server"></a>Acessar o servidor de histórico do Spark
1. No Azure Explorer, clique com o botão direito do mouse no nome do cluster Spark e escolha **Abrir a Interface do Usuário de Histórico do Spark**. Quando solicitado, insira as credenciais de administrador para o cluster. Elas foram especificadas no provisionamento do cluster.
1. No painel do Servidor de Histórico do Spark, procure o aplicativo que você acabou de executar usando o nome do aplicativo. No código anterior, você definiu o nome do aplicativo usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Dessa forma, o nome do aplicativo Spark era **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Iniciar o portal do Apache Ambari
1. No Azure Explorer, clique com o botão direito do mouse no nome do cluster Spark e escolha **Abrir o Portal de Gerenciamento do Cluster (Ambari)** . 
1. Quando solicitado, insira as credenciais de administrador para o cluster. Elas foram especificadas no provisionamento do cluster.

### <a name="manage-azure-subscriptions"></a>Gerenciar assinaturas do Azure
Por padrão, a Ferramenta do HDInsight no Kit de Ferramentas do Azure para Eclipse lista os clusters Spark de todas as assinaturas do Azure. Se for necessário, você poderá especificar as assinaturas para as quais deseja acessar o cluster. 

1. No Azure Explorer, clique com o botão direito do mouse no nó-raiz **Azure** e selecione **Gerenciar Assinaturas**. 
1. Na caixa de diálogo, desmarque as caixas de seleção da assinatura que você não deseja acessar e selecione **Fechar**. Você também poderá escolher **Sair** se quiser sair da sua assinatura do Azure.

## <a name="run-a-spark-scala-application-locally"></a>Executar um aplicativo Scala Spark localmente
Você pode usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para executar aplicativos Spark Scala localmente em sua estação de trabalho. Normalmente, esses aplicativos não precisam acessar recursos de cluster como um contêiner de armazenamento e você pode executá-los e testá-los localmente.

### <a name="prerequisite"></a>Pré-requisito
Durante a execução do aplicativo Spark Scala local em um computador Windows, você pode receber uma exceção, conforme explicado em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Essa exceção ocorre porque **WinUtils.exe** está ausente no Windows. 

Para resolver esse erro, você deve [baixar o executável](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) para um local como **C:\WinUtils\bin** e, depois, adicionar a variável de ambiente **HADOOP_HOME** e definir o valor da variável como **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Executar um aplicativo Scala Spark local
1. Inicie o Eclipse e crie um projeto. Na caixa de diálogo **Novo Projeto**, faça as opções a seguir e selecione **Avançar**.
   
   * No painel esquerdo, escolha **HDInsight**.
   * No painel direito, selecione **Exemplo de Execução Local do Spark no HDInsight (Scala)** .

   ![Caixa de diálogo Novo Projeto](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
1. Para fornecer os detalhes do projeto, siga as etapas 3 a 6 da seção anterior [Configurar um projeto de Spark Scala para um cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. O modelo adiciona um código de exemplo (**LogQuery**) na pasta **src** que pode ser executada localmente em seu computador.
   
   ![Local do LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
1. Clique com botão direito do mouse no aplicativo **LogQuery**, aponte para **Executar como** e selecione **1 Aplicativo Scala**. Uma saída como essa é exibida na guia **Console**:
   
   ![Resultado da execução local do aplicativo Spark](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Função somente leitura
Quando os usuários enviam um trabalho a um cluster com permissão de função somente leitura, as credenciais do Ambari são solicitadas.

### <a name="link-cluster-from-context-menu"></a>Vincular cluster do menu de contexto

1. Entrar com conta com função somente leitura.
       
2. No **Azure Explorer**, expanda **HDInsight** para exibir os clusters do HDInsight em sua assinatura. Os clusters marcados **"Role:Reader"** só tem permissão para a função somente leitura.

    ![Clusters Spark do HDInsight no Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-6.png)

3. Clique com o botão direito do mouse no cluster com permissão de função somente leitura. Selecione **Vincular este cluster** no menu de contexto para vincular o cluster. Insira o nome de usuário e a senha do Ambari.

    ![Clusters Spark do HDInsight no Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-7.png)

4. Se o cluster for vinculado com êxito, o HDInsight será atualizado.
   O estágio do cluster será se tornará vinculado.
  
    ![Clusters Spark do HDInsight no Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-8.png)



### <a name="link-cluster-by-expanding-jobs-node"></a>Vincular cluster expandindo o nó Trabalhos

1. Clique no nó **Trabalhos**, a janela **Acesso negado ao cluster Trabalhos** é exibida.
   
2. Clique em **Vincular este cluster** para vincular o cluster.
   
    ![Clusters Spark do HDInsight no Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Vincular cluster da janela de envio do Spark

1. Crie um projeto do HDInsight.

2. Clique com o botão direito do mouse no pacote. Em seguida, selecione **Enviar aplicativo Spark ao HDInsight**.
   
   ![Clusters Spark do HDInsight no Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-11.png)

3. Selecione um cluster que tenha permissão de função somente de leitor para o **nome do cluster**. Uma mensagem de aviso é exibida. Você pode clicar em **vincular este cluster** para vincular o cluster.
   
   ![Clusters Spark do HDInsight no Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-15.png)
   
### <a name="view-storage-accounts"></a>Exibir Contas de Armazenamento

* Para clusters com permissão de função somente leitura, clique no nó **Contas de Armazenamento**, a janela **Acesso Negado ao Armazenamento** aparece. 
     
   ![Clusters Spark do HDInsight no Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-13.png)

   ![Clusters Spark do HDInsight no Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-12.png)

* Para clusters vinculados, clique no nó **Contas de Armazenamento**, a janela **Acesso Negado ao Armazenamento** aparece. 
     
   ![Clusters Spark do HDInsight no Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-14.png)

## <a name="known-problems"></a>Problemas conhecidos
Ao vincular um cluster, sugiro que você forneça credenciais de armazenamento.

![Entrada interativa](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Há dois modos para enviar os trabalhos. Se a credencial de armazenamento for fornecida, o modo de lote será usado para enviar o trabalho. Caso contrário, o modo interativo será usado. Se o cluster estiver ocupado, poderá receber o erro abaixo.

![eclipse recebe um erro quando o cluster estiver ocupado](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png)

![eclipse recebe um erro quando o cluster estiver ocupado](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png)

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criando e executando aplicativos
* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o Kit de Ferramentas do Azure para IntelliJ para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente por meio de VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use o Azure Toolkit for IntelliJ para depurar os aplicativos do Apache Spark remotamente por meio do SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o notebook Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gerenciando recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)

