---
title: 'Kit de Ferramentas do Azure para Eclipse: criar aplicativos Scala para HDInsight Spark | Microsoft Docs'
description: "Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para desenvolver aplicativos do Spark escritos em Scala e enviá-los para um cluster do HDInsight Spark, diretamente do IDE Eclipse."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 79b3183171e3c28276c8e4e6d4fe3998e0109643
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Usar o Kit de ferramentas do Azure para Eclipse a fim de criar aplicativos Spark para cluster HDInsight

Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para desenvolver aplicativos Spark escritos em Scala e enviá-los para um cluster Spark no Azure HDInsight, diretamente do IDE Eclipse. Você pode usar o plug-in Ferramentas do HDInsight de algumas maneiras diferentes:

* Para desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark
* Para acessar os recursos de cluster do Azure HDInsight Spark
* Para desenvolver e executar um aplicativo Scala Spark localmente

> [!IMPORTANT]
> Você pode usar essa ferramenta para criar e enviar aplicativos somente para um cluster HDInsight Spark no Linux.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit versão 8, que é usado para o tempo de execução do IDE do Eclipse. Você pode baixá-lo do [site da Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Eclipse IDE. Este artigo usa o Eclipse Neon. Você pode instalá-lo do [site do Eclipse](https://www.eclipse.org/downloads/).
* Spark SDK. Você pode baixá-lo do [GitHub](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).


## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Instalar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse e plug-in Scala
### <a name="install-hdinsight-tools"></a>Instalar Ferramentas do HDInsight
As Ferramentas do HDInsight para Eclipse estão disponíveis como parte do Kit de Ferramentas do Azure para Eclipse. Para obter instruções de instalação, consulte [Instalando o Kit de Ferramentas do Azure para Eclipse](../azure-toolkit-for-eclipse-installation.md).
### <a name="install-the-scala-plug-in"></a>Instalar o plug-in Scala
Quando você abre o Eclipse, as ferramentas do HDInsight detectam automaticamente se você instalou o plug-in Scala. Selecione **OK** para continuar e, em seguida, siga as instruções para instalar o plug-in Eclipse Marketplace.

![Instalação automática do plug-in Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/auto-install-scala.png)

## <a name="sign-in-to-your-azure-subscription"></a>Entre em sua assinatura do Azure
1. Inicie o IDE Eclipse e abra o Azure Explorer. No menu **Janela**, selecione **Mostrar Exibição** e selecione **Outros**. Na caixa de diálogo que é aberta, expanda **Azure**, selecione **Azure Explorer** e selecione **OK**.

   ![Caixa de Diálogo Mostrar Modo de Exibição](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. Clique com o botão direito do mouse no nó **Azure** e selecione **Entrar**.
3. Na caixa de diálogo **Entrada do Azure**, escolha o modo de autenticação, selecione **Entrar** e insira suas credenciais do Azure.
   
   ![Caixa de diálogo Entrar no Azure](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. Após a entrada, a caixa de diálogo **Selecionar Assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Clique em **Selecionar** para fechar a caixa de diálogo.

   ![Caixa de diálogo Selecionar Assinaturas](./media/hdinsight-apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. Na guia **Azure Explorer**, expanda **HDInsight** para ver os clusters Spark do HDInsight da sua assinatura.
   
   ![Clusters Spark do HDInsight no Azure Explorer](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. Além disso, você pode expandir um nó de nome de cluster para ver os recursos (por exemplo, contas de armazenamento) associados ao cluster.
   
   ![Expandindo um nome de cluster para ver recursos](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)



## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configurar um projeto Spark Scala para um cluster HDInsight Spark

1. No espaço de trabalho do IDE do Eclipse, selecione **Arquivo**, selecione **Novo**e, em seguida, selecione **Projeto**. 
2. No assistente Novo Projeto, expanda **HDInsight**, selecione **Spark no HDInsight (Scala)** e selecione **Avançar**.

   ![Selecionando o projeto Spark no HDInsight (Scala)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. O assistente de criação de projetos Scala detecta automaticamente se você instalou o plug-in Scala. Selecione **OK** para continuar a baixar o plug-in Scala e, em seguida, siga as instruções para reiniciar o Eclipse.

   ![Verificação do Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
4. Na caixa de diálogo **Novo Projeto do HDInsight Scala**, forneça os seguintes valores e selecione **Avançar**:
   * Insira um nome para o projeto.
   * Na área **JRE**, verifique se **Usar um ambiente de execução JRE** está definido como **JavaSE-1.7** ou posterior.
   * Certifique-se de que o SDK do Spark esteja definido como o local em que você baixou o SDK. O link para o local de download está incluído nos [pré-requisitos](#prerequisites) anteriormente apresentados neste artigo. Você também pode baixar o SDK no link na caixa de diálogo.

   ![Caixa de diálogo Novo Projeto de Scala HDInsight](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
5. Na próxima caixa de diálogo, selecione a guia **Bibliotecas** e mantenha os padrões e clique em **Concluir**. 
   
   ![Guia Bibliotecas](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Criar um aplicativo Scala para cluster Spark no HDInsight

1. No IDE do Eclipse, em Explorador de Pacotes, expanda o projeto que você criou anteriormente, clique com botão direito do mouse em **src**, aponte para **Novo** e clique em **Outros**.
2. Na caixa de diálogo **Selecionar um assistente**, expanda **Assistentes Scala**, selecione **Objeto Scala** e selecione **Avançar**.
   
   ![Selecione uma caixa de diálogo do assistente](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. Na caixa de diálogo **Criar Novo Arquivo**, insira um nome para o objeto e selecione **Concluir**.
   
   ![Criar caixa de diálogo Novo Arquivo](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. Cole o seguinte código no editor de texto:
   
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
5. Execute o aplicativo em um cluster HDInsight Spark:
   
   a. No Explorador de Pacotes, clique com o botão direito do mouse no nome do projeto e escolha **Enviar Aplicativo Spark para HDInsight**.        
   b. Na caixa de diálogo **Envio do Spark**, forneça os valores a seguir e selecione **Enviar**:
      
      * Para **Nome do Cluster**, selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.
      * Selecione um artefato do projeto Eclipse ou selecionar uma opção do disco rígido. O valor padrão depende do item no qual você clica com o botão direito do mouse por meio do Gerenciador de Pacotes.
      * Na lista suspensa **Nome de classe principal**, o assistente de envio exibe todos os nomes de objeto do projeto. Selecione ou insira um que você deseja executar. Se tiver selecionado um artefato de um disco rígido, você deverá inserir o nome de classe principal manualmente. 
      * Como o código do aplicativo neste exemplo não exige argumentos de linha de comando ou JARs ou arquivos de referência, você pode deixar as caixas de texto restantes vazias.
        
      ![Caixa de diálogo Envio do Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
6. A guia **Envio de Spark** deve começar a exibir o progresso. Você pode interromper o aplicativo selecionando o botão vermelho na janela **Envio do Spark**. Você também pode exibir os logs para essa execução de aplicativo específica selecionando o ícone de globo (indicado pela caixa azul na imagem).
      
   ![Janela Envio do Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Acessar e gerenciar clusters HDInsight Spark usando as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse
Você pode executar várias operações usando as Ferramentas do HDInsight, incluindo o acesso à saída do trabalho.

### <a name="access-the-job-view"></a>Acessar a exibição do trabalho
1. No Azure Explorer, expanda **HDInsight**, expanda o nome do cluster Spark e escolha **Trabalhos**. 

   ![Nó de exibição de trabalho](./media/hdinsight-apache-spark-intellij-tool-plugin/job-view-node.png)

2. Selecione o nó **Trabalhos**. As Ferramentas do HDInsight detectam automaticamente se você instalou o plug-in E(fx)clipse. Selecione **OK** para continuar e, em seguida, siga as instruções para instalar o Eclipse Marketplace e reinicie o Eclipse.

   ![Instalar E(fx)clipse](./media/hdinsight-apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

3. Abra a Exibição de Trabalho do nó **Trabalhos**. No painel direito, a guia **Exibição de Trabalho do Spark** exibe todos os aplicativos que foram executados no cluster. Selecione o nome do aplicativo do qual você deseja ver mais detalhes.

   ![Detalhes do aplicativo](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

   Você pode executar uma das seguintes ações:

   * Passe o mouse sobre o gráfico de trabalho. Ele exibe informações básicas sobre o trabalho em execução. Selecione o gráfico de trabalho e você poderá ver os estágios e as informações que cada trabalho gera.

     ![Detalhes do estágio do trabalho](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

   * Selecione a guia **Log** para exibir logs usados frequentemente, como **Stderr do Driver**, **Stdout do Driver** e **Informações do diretório**.

     ![Detalhes do log](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-log-info.png)

   * Abra a interface do usuário de histórico do Spark e a interface do usuário do YARN (no nível do aplicativo) selecionando os hiperlinks na parte superior da janela.

### <a name="access-the-storage-container-for-the-cluster"></a>Acessar o contêiner de armazenamento do cluster
1. No Azure Explorer, expanda o nó raiz **HDInsight** para ver uma lista de clusters HDInsight Spark disponíveis.
2. Expanda o nome do cluster para ver a conta de armazenamento e o contêiner de armazenamento padrão do cluster.
   
   ![Contêiner de armazenamento padrão e a conta de armazenamento](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. Selecione o nome do contêiner de armazenamento associado ao cluster. No painel direito, clique duas vezes na pasta **HVACOut**. Abra um dos arquivos **part-** para ver a saída do aplicativo.

### <a name="access-the-spark-history-server"></a>Acessar o servidor de histórico do Spark
1. No Azure Explorer, clique com o botão direito do mouse no nome do cluster Spark e escolha **Abrir a Interface do Usuário de Histórico do Spark**. Quando solicitado, insira as credenciais de administrador para o cluster. Elas foram especificadas no provisionamento do cluster.
2. No painel do Servidor de Histórico do Spark, procure o aplicativo que você acabou de executar usando o nome do aplicativo. No código anterior, você definiu o nome do aplicativo usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Dessa forma, o nome do aplicativo Spark era **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Iniciar o portal do Ambari
1. No Azure Explorer, clique com o botão direito do mouse no nome do cluster Spark e escolha **Abrir o Portal de Gerenciamento do Cluster (Ambari)**. 
2. Quando solicitado, insira as credenciais de administrador para o cluster. Elas foram especificadas no provisionamento do cluster.

### <a name="manage-azure-subscriptions"></a>Gerenciar assinaturas do Azure
Por padrão, as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse listam os clusters Spark de todas as suas assinaturas do Azure. Se for necessário, você poderá especificar as assinaturas para as quais deseja acessar o cluster. 

1. No Azure Explorer, clique com o botão direito do mouse no nó-raiz **Azure** e selecione **Gerenciar Assinaturas**. 
2. Na caixa de diálogo, desmarque as caixas de seleção da assinatura que você não deseja acessar e selecione **Fechar**. Você também poderá escolher **Sair** se quiser sair da sua assinatura do Azure.

## <a name="run-a-spark-scala-application-locally"></a>Executar um aplicativo Scala Spark localmente
Você pode usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para executar aplicativos Spark Scala localmente em sua estação de trabalho. Normalmente, esses aplicativos não precisam acessar recursos de cluster como um contêiner de armazenamento e você pode executá-los e testá-los localmente.

### <a name="prerequisite"></a>Pré-requisito
Durante a execução do aplicativo Spark Scala local em um computador Windows, você pode receber uma exceção, conforme explicado em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Essa exceção ocorre porque **WinUtils.exe** está ausente no Windows. 

Para solucionar esse erro, você deve [baixar o executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) em um local como **C:\WinUtils\bin**. Em seguida, adicione uma variável de ambiente **HADOOP_HOME** e defina o valor da variável como **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Executar um aplicativo Scala Spark local
1. Inicie o Eclipse e crie um projeto. Na caixa de diálogo **Novo Projeto**, faça as opções a seguir e selecione **Avançar**.
   
   * No painel esquerdo, escolha **HDInsight**.
   * No painel direito, selecione **Exemplo de Execução Local do Spark no HDInsight (Scala)**.

   ![Caixa de diálogo Novo Projeto](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
2. Para fornecer os detalhes do projeto, siga as etapas 3 a 6 da seção anterior [Configurar um projeto de Spark Scala para um cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

3. O modelo adiciona um código de exemplo (**LogQuery**) na pasta **src** que pode ser executada localmente em seu computador.
   
   ![Local do LogQuery](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)
   
4. Clique com botão direito do mouse no aplicativo **LogQuery**, aponte para **Executar como** e selecione **1 Aplicativo Scala**. Uma saída como essa é exibida na guia **Console**:
   
   ![Resultado da execução local do aplicativo Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>Problemas conhecidos
Para enviar um aplicativo ao Azure Data Lake Store, selecione o modo **Interativo** durante o processo de entrada no Azure. Se você selecionar o modo **Automatizado**, obterá um erro.

![Entrada interativa](./media/hdinsight-apache-spark-eclipse-tool-plugin/interactive-authentication.png)

Você pode escolher um cluster do Azure Data Lake para enviar seu aplicativo com qualquer método de entrada.

Atualmente, não há suporte para exibir saídas do Spark diretamente.

## <a name="feedback"></a>Comentários
Se você tiver comentários ou se encontrar problemas ao usar essa ferramenta, fique à vontade para enviar um email para hdivstool@microsoft.com.

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criando e executando aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o Kit de Ferramentas do Azure para IntelliJ para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio da VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio do SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usar ferramentas do HDInsight para IntelliJ com a área restrita do Hortonworks](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gerenciando recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

