---
title: 'Kit de Ferramentas do Azure para IntelliJ: criar aplicativos Spark para um cluster HDInsight | Microsoft Docs'
description: "Use o Kit de Ferramentas do Azure para IntelliJ a fim de desenvolver aplicativos Spark escritos em Scala e enviá-los a um cluster Spark do HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: nitinme
ms.openlocfilehash: 19cb8f436fa4d86f323013a5d4b3b50bf6c80a1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Usar o Kit de Ferramentas do Azure para IntelliJ a fim de criar aplicativos Spark para um cluster HDInsight

Use o plug-in Kit de Ferramentas do Azure para IntelliJ a fim de desenvolver aplicativos Spark escritos em Scala e enviá-los a um cluster Spark do HDInsight diretamente do IDE (ambiente de desenvolvimento integrado) do IntelliJ. Você pode usar o plug-in destas maneiras:

* Desenvolver e enviar um aplicativo Scala Spark em um cluster HDInsight Spark.
* Acessar os recursos de cluster Spark do Azure HDInsight.
* Desenvolver e executar um aplicativo Scala Spark localmente.

Para criar seu projeto, veja o vídeo [Create Spark Applications with the Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Criar aplicativos Spark com o Kit de Ferramentas do Azure para IntelliJ).

> [!IMPORTANT]
> Você pode usar esse plug-in e enviar aplicativos somente para um cluster Spark do HDInsight no Linux.
> 

## <a name="prerequisites"></a>Pré-requisitos

- Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
- Kit de desenvolvimento Oracle Java. Você pode instalá-lo do [site da Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- IntelliJ IDEA. Este artigo usa a versão 2017.1. Você pode instalá-lo do [site da JetBrains](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Instalar Kit de Ferramentas do Azure para IntelliJ
Para obter instruções de instalação, confira [Instalação do Kit de Ferramentas do Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="sign-in-to-your-azure-subscription"></a>Entre em sua assinatura do Azure

1. Inicie o IDE do IntelliJ e abra o Azure Explorer. No menu **Exibir**, selecione **Janelas de Ferramentas** e **Azure Explorer**.
       
   ![O link do Azure Explorer](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Clique com o botão direito do mouse no nó **Azure** e selecione **Entrar**.

3. Na caixa de diálogo **Entrada do Azure**, selecione **Entrar** e insira suas credenciais do Azure.

    ![A caixa de diálogo Entrada do Azure](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Depois que você estiver conectado, a caixa de diálogo **Selecionar Assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Escolha o botão **Selecionar**.

    ![A caixa de diálogo Selecionar Assinaturas](./media/hdinsight-apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. Na guia **Azure Explorer**, expanda **HDInsight** para ver os clusters Spark do HDInsight em sua assinatura.
   
    ![Clusters Spark do HDInsight no Azure Explorer](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Para exibir os recursos (por exemplo, contas de armazenamento) associados ao cluster, você poderá expandir ainda mais um nó de nome de cluster.
   
    ![Um nó de nome de cluster expandido](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar um aplicativo Scala Spark em um cluster HDInsight Spark

1. Inicie o IDEA do IntelliJ e crie um projeto. Na caixa de diálogo **Novo Projeto** , faça o seguinte: 

   a. Selecione **HDInsight** > **Spark no HDInsight (Scala)**.

   b. Na lista **Ferramenta de build**, selecione uma das seguintes opções, de acordo com suas necessidades:

      * **Maven**, para obter suporte ao assistente de criação de projetos Scala
      * **SBT**, para gerenciar as dependências e a compilação no projeto Scala

    ![A caixa de diálogo Novo Projeto](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

2. Selecione **Avançar**.

3. O assistente de criação de projetos Scala detecta automaticamente se você instalou o plug-in Scala. Selecione **Instalar**.

   ![Verificação do plug-in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

4. Para baixar o plug-in Scala, selecione **OK**. Siga as instruções para reiniciar o IntelliJ. 

   ![A caixa de diálogo Instalação do plug-in Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

5. Na janela **Novo Projeto**, faça o seguinte:  

    ![Selecionando o SDK do Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Insira um nome e o local do projeto.

   b. Na lista suspensa **SDK do Projeto**, selecione **Java 1.8** para o cluster Spark 2.x ou selecione **Java 1.7** para o cluster Spark 1.x.

   c. Na lista suspensa **Versão do Spark**, o assistente de criação de projeto Scala integra a versão apropriada do SDK do Spark e do SDK do Scala. Se a versão do cluster do Spark for inferior a 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark 2.x**. Esse exemplo usa o **Spark 2.0.2 (Scala 2.11.8)**.

6. Selecione **Concluir**.

7. O projeto do Spark cria automaticamente um artefato para você. Para exibir o artefato, faça o seguinte:

   a. No menu **Arquivo**, escolha **Estrutura do Projeto**.

   b. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** para exibir o artefato padrão criado. Você também pode criar seu próprio artefato selecionando o sinal de mais (**+**).

      ![Informações de artefato na caixa de diálogo](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
8. Adicione o código-fonte do aplicativo seguindo estas etapas:

   a. No Gerenciador de Projetos, clique com o botão direito do mouse em **src**, aponte para **Novo** e escolha **Classe do Scala**.
      
      ![Comandos para criar uma classe Scala do Explorador do Projeto](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. Na caixa de diálogo **Criar Nova Classe do Scala**, forneça um nome, selecione **Objeto** na caixa **Tipo** e selecione **OK**.
      
      ![Criar caixa de diálogo Nova Classe Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. No arquivo **MyClusterApp.scala** , cole o código a seguir. O código lê os dados no HVAC.csv (disponível em todos os clusters Spark do HDInsight), recupera as linhas com apenas um dígito na sétima coluna no arquivo CSV e grava a saída em **/HVACOut** no contêiner padrão de armazenamento do cluster.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

9. Execute o aplicativo em um cluster Spark do HDInsight seguindo estas etapas:

   a. No Explorador de Projetos, clique com o botão direito do mouse no nome do projeto e selecione **Enviar Aplicativo Spark para HDInsight**.
      
      ![O comando Enviar Aplicativo Spark para HDInsight](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. Você recebe uma solicitação para inserir suas credenciais de assinatura do Azure. Na caixa de diálogo **Envio do Spark**, forneça os valores a seguir e escolha **Enviar**.
      
      * Para **clusters Spark (somente no Linux)**, selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.

      * Selecione um artefato do projeto IntelliJ ou uma opção do disco rígido.

      * Na caixa **Nome da classe principal**, escolha o botão de reticências (**...** ), selecione a classe principal no código-fonte do aplicativo e selecione **OK**.

        ![A caixa de diálogo Selecionar Classe Principal](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * Como o código do aplicativo neste exemplo não exige argumentos de linha de comando nem JARs ou arquivos de referência, você pode deixar as caixas restantes em branco. Depois de fornecer todas as informações, a caixa de diálogo deve ser semelhante à imagem a seguir.
        
        ![A caixa de diálogo Envio do Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. A guia **Envio do Spark** na parte inferior da janela deve começar a exibir o progresso. Você também pode interromper o aplicativo escolhendo o botão vermelho na janela **Envio do Spark**.
      
      ![A janela Envio do Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Para saber como acessar a saída do trabalho, confira a seção "Acessar e gerenciar clusters Spark do HDInsight usando o Kit de Ferramentas do Azure para IntelliJ" mais adiante neste artigo.

## <a name="run-or-debug-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar ou depurar um aplicativo Scala Spark em um cluster Spark do HDInsight
Também recomendamos outra forma de enviar o aplicativo Spark ao cluster. Você pode fazer isso definido os parâmetros no IDE **Executar/Depurar configurações**. Para saber mais, confira [Depurar aplicativos Spark remotamente em um cluster HDInsight com o kit de ferramentas do Azure para IntelliJ por meio do SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Acessar e gerenciar clusters Spark do HDInsight usando o Kit de Ferramentas do Azure para IntelliJ
Você pode executar várias operações usando o Kit de Ferramentas do Azure para IntelliJ.

### <a name="access-the-job-view"></a>Acessar a exibição do trabalho
1. No Azure Explorer, expanda **HDInsight**, expanda o nome do cluster Spark e escolha **Trabalhos**.  

    ![Nó de exibição de trabalho](./media/hdinsight-apache-spark-intellij-tool-plugin/job-view-node.png)

2. No painel direito, a guia **Exibição de Trabalho do Spark** exibe todos os aplicativos que foram executados no cluster. Selecione o nome do aplicativo do qual você deseja ver mais detalhes.

    ![Detalhes do aplicativo](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Para exibir informações básicas do trabalho em execução, passe o mouse sobre o gráfico de trabalhos. Para exibir o gráfico de estágios e as informações geradas pelos trabalhos, escolha um nó no gráfico de trabalhos.

    ![Detalhes do estágio do trabalho](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Para exibir logs usados frequentemente, como *Stderr do Driver*, *Stdout do Driver* e *Informações do diretório*, escolha a guia **Log**.

    ![Detalhes do log](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Você também pode exibir a interface do usuário de histórico do Spark e a interface do usuário do YARN (no nível do aplicativo) selecionando um link na parte superior da janela.

### <a name="access-the-spark-history-server"></a>Acessar o servidor de histórico do Spark
1. No Azure Explorer, expanda o **HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e selecione **Abrir IU do Histórico Spark**. 

2. Quando for solicitado, insira as credenciais do administrador do cluster, que você especificou ao configurar o cluster.

3. No painel do servidor de histórico do Spark, é possível usar o nome do aplicativo para procurar o que você acabou de executar. No código anterior, você definiu o nome do aplicativo usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Portanto, o nome do aplicativo Spark é **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Iniciar o portal do Ambari
1. No Azure Explorer, expanda **HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e selecione **Abrir Portal de Gerenciamento do Cluster (Ambari)**. 

2. Quando solicitado, insira as credenciais de administrador para o cluster. Você especificou essas credenciais durante o processo de configuração do cluster.

### <a name="manage-azure-subscriptions"></a>Gerenciar assinaturas do Azure
Por padrão, o Kit de Ferramentas do Azure para IntelliJ listam os clusters Spark de todas as suas assinaturas do Azure. Se for necessário, você poderá especificar as assinaturas que deseja acessar. 

1. No Azure Explorer, clique com o botão direito do mouse no nó-raiz **Azure** e selecione **Gerenciar Assinaturas**. 

2. Na caixa de diálogo, desmarque as caixas de seleção ao lado das assinaturas que você não deseja acessar e escolha **Fechar**. Você também poderá escolher **Sair** se quiser sair da sua assinatura do Azure.

## <a name="run-a-spark-scala-application-locally"></a>Executar um aplicativo Scala Spark localmente
Você pode usar o Kit de Ferramentas do Azure para IntelliJ para executar aplicativos Spark Scala localmente em sua estação de trabalho. Normalmente, os aplicativos não precisam acessar recursos de cluster, como contêineres de armazenamento, e você pode executá-los e testá-los localmente.

### <a name="prerequisite"></a>Pré-requisito
Durante a execução do aplicativo Scala Spark local em um computador Windows, você pode receber uma exceção, conforme explicado em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque WinUtils.exe está ausente no Windows. 

Para resolver esse erro, [baixe o executável](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) em um local como **C:\WinUtils\bin**. Em seguida, adicione uma variável de ambiente **HADOOP_HOME** e defina o valor da variável para **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Executar um aplicativo Scala Spark local
1. Inicie o IDEA do IntelliJ e crie um projeto. 

2. Na caixa de diálogo **Novo Projeto** , faça o seguinte:
   
    a. Escolha **HDInsight** > **Exemplo de Execução Local do Spark no HDInsight (Scala)**.

    b. Na lista **Ferramenta de build**, selecione uma das seguintes opções, de acordo com suas necessidades:

      * **Maven**, para obter suporte ao assistente de criação de projetos Scala
      * **SBT**, para gerenciar as dependências e a compilação no projeto Scala

    ![A caixa de diálogo Novo Projeto](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

3. Selecione **Avançar**.
 
4. Na próxima janela, faça o seguinte:
   
    a. Insira um nome e o local do projeto.

    b. Na lista suspensa **SDK do Projeto**, escolha uma versão do Java que seja posterior à versão 1.7.

    c. Na lista suspensa **Versão do Spark**, escolha a versão do Scala que você deseja usar: Scala 2.11.x para Spark 2.0 ou Scala 2.10.x para Spark 1.6.

    ![A caixa de diálogo Novo Projeto](./media/hdinsight-apache-spark-intellij-tool-plugin/Create-local-project.PNG)

5. Selecione **Concluir**.

6. O modelo adiciona um código de exemplo (**LogQuery**) na pasta **src** que pode ser executada localmente em seu computador.
   
    ![Local do LogQuery](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)

7. Clique com o botão direito do mouse no aplicativo **LogQuery** e escolha **Executar 'LogQuery'**. Na guia **Executar** na parte inferior, você verá uma saída como a seguinte:
   
   ![Resultado da execução local do aplicativo Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Converter aplicativos IntelliJ IDEA existentes a fim de usar o Kit de Ferramentas do Azure para IntelliJ
Você pode converter os aplicativos Scala Spark existentes criados no IDEA do IntelliJ para serem compatíveis com o Kit de Ferramentas do Azure para IntelliJ. Em seguida, você pode usar o plug-in para enviar os aplicativos a um cluster Spark do HDInsight.

1. Para um aplicativo Scala Spark existente criado no IDEA do IntelliJ, abra o arquivo .iml associado.

2. Há um elemento **module** no nível de raiz, como o seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Edite o elemento para adicionar `UniqueKey="HDInsightTool"` , de modo que o elemento **module** seja semelhante ao seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Salve as alterações. Seu aplicativo deve ser compatível com o Kit de Ferramentas do Azure para IntelliJ. Você pode testar isso clicando com o botão direito do mouse no nome do projeto no Gerenciador de Projetos. Agora, o menu pop-up tem a opção **Enviar Aplicativo Spark ao HDInsight**.

## <a name="troubleshooting"></a>Solucionar problemas

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Erro na execução local: *Use um tamanho de heap maior*
No Spark 1.6, se você estiver usando um SDK Java de 32 bits durante a execução local, poderá encontrar os seguintes erros:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Esses erros acontecem porque o tamanho do heap não é grande o suficiente para Spark ser executado. O Spark requer pelo menos 471 MB. (Para saber mais, confira [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081).) Uma solução simples é usar um SDK do Java de 64 bits. Você também pode alterar as configurações da JVM no IntelliJ adicionando as seguintes opções:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Adicionando opções à caixa "Opções de VM" no IntelliJ](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>Perguntas frequentes
Para enviar um aplicativo ao Azure Data Lake Store, escolha o modo **Interativo** durante o processo de entrada no Azure. Se você selecionar o modo **Automatizado**, obterá um erro.

![interative-signin](./media/hdinsight-apache-spark-intellij-tool-plugin/interative-signin.png)

Isso já está resolvido. Você pode escolher um Cluster do Azure Data Lake para enviar seu aplicativo com qualquer método de entrada.

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos
Atualmente, não há suporte para exibir saídas do Spark diretamente.

Se você tiver sugestões ou comentários, ou se encontrar problemas ao usar esse plug-in, envie-nos um email para hdivstool@microsoft.com.

## <a name="seealso"></a>Próximas etapas
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>Demonstração
* Criar projeto do Scala (vídeo): [Criar aplicativos Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Usar o Kit de Ferramentas do Azure para IntelliJ para depurar aplicativos Spark remotamente no cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criando e executando aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio da VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar o kit de ferramentas do Azure para IntelliJ a fim de depurar aplicativos Spark remotamente por meio do SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usar ferramentas do HDInsight para IntelliJ com a área restrita do Hortonworks](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse para criar aplicativos Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gerenciando recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

