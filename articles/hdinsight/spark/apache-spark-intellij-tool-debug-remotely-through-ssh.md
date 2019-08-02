---
title: 'Azure Toolkit for IntelliJ: Depurar aplicativos Spark remotamente por meio do SSH '
description: Instruções passo a passo sobre como usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para depurar aplicativos remotamente nos clusters do HDInsight por meio do SSH
keywords: depurar o intellij remotamente, depuração remota do intellij, ssh, intellij, hdinsight, depurar o intellij, depuração
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 0bca6c16124f886d9df9e88e651f3f7450f51a1a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876362"
---
# <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Depurar aplicativos Apache Spark local ou remotamente em um cluster HDInsight com Azure Toolkit for IntelliJ por meio do SSH

Este artigo fornece orientação passo-a-passo sobre como usar as Ferramentas do HDInsight no [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) para depurar aplicativos remotamente em um cluster do HDInsight. Para depurar seu projeto, você também poderá exibir o vídeo [Depurar aplicativos do HDInsight Spark com o Kit de Ferramentas do Azure para IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ).

**Pré-requisitos**
* **Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ**. Essa ferramenta faz parte do Kit de Ferramentas do Azure para IntelliJ. Para mais informações, consulte [Instalar o Kit de Ferramentas do Azure para IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). E o **Kit de Ferramentas do Azure para IntelliJ**. Use esse kit de ferramentas para criar aplicativos do Apache Spark para um cluster do HDInsight. Para obter mais informações, siga as instruções em [Use o Azure Toolkit for IntelliJ para criar aplicativos Apache Spark para um cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **Serviço SSH do HDInsight com gerenciamento de nome de usuário e senha**. Para obter mais informações, consulte [Conectar o HDInsight (Apache Hadoop) usando SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [Usar túnel SSH para acessar a interface do usuário da Web do Ambari, JobHistory, NameNode, Apache Oozie e outras interfaces do usuário da Web](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Saiba como realizar a execução e depuração local
### <a name="scenario-1-create-a-spark-scala-application"></a>Cenário 1: Criar um aplicativo Spark Scala 

1. Inicie o IDEA do IntelliJ e crie um projeto. Na caixa de diálogo **Novo Projeto**, faça o seguinte:

   a. Selecione **Azure Spark/HDInsight**. 

   b. Selecione um modelo Java ou Scala com base em sua preferência. Selecione entre as seguintes opções:

   - **Projeto do Spark (Java)**

   - **Projeto do Spark (escala)**

   - **Projeto do Spark com amostras (escala)**

   - **Projeto do Spark com amostras de depuração de tarefa de falha (versão prévia) (escala)**

     Este exemplo usa um **projeto do Spark com modelo de amostras (escala)** .

   c. Na lista **Ferramenta de build**, selecione uma das seguintes opções, de acordo com suas necessidades:

   - **Maven**, para obter suporte ao assistente de criação de projetos Scala

   - **SBT**, para gerenciar as dependências e a compilação no projeto Scala 

     ![Criar um projeto de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Selecione **Avançar**.     
 
1. Na janela **Novo Projeto** a seguir, faça o seguinte:

   ![Selecione o SDK do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Insira um nome de projeto e o local do projeto.

   b. Na lista suspensa **SDK do Projeto**, selecione **Java 1.8** para o cluster **Spark 2.x** ou selecione **Java 1.7** para o cluster **Spark 1.x**.

   c. Na lista suspensa **Versão do Spark**, o assistente de criação de projeto Scala integra a versão correta do SDK do Spark e do SDK do Scala. Se a versão do cluster do Spark for inferior à versão 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark 2.x.** Esse exemplo usa o **Spark 2.0.2 (Scala 2.11.8)** .

   d. Selecione **Concluir**.

1. Selecione **src** > **main** > **scala** para abrir seu código no projeto. Este exemplo usa o script **SparkCore_wasbloTest**.

### <a name="prerequisite-for-windows"></a>Pré-requisito para o Windows
Durante a execução do aplicativo Scala Spark local em um computador Windows, você pode receber uma exceção, conforme explicado em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque WinUtils.exe está ausente no Windows. 

Para resolver esse erro, [baixe o executável](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) em um local como **C:\WinUtils\bin**. Em seguida, adicione uma variável de ambiente **HADOOP_HOME** e defina o valor da variável para **C\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Cenário 2: Realizar execução local
1. Abra o script **SparkCore_wasbloTest**, clique com o botão direito do mouse no editor de script e, em seguida, selecione a opção **Executar 'XXX [Trabalho do Spark]'** para realizar a execução local.
1. Assim que a execução local estiver concluída, você pode ver o arquivo de saída salvo no Explorador de projeto atual **dados** >  **__padrão__** .

    ![Resultado da execução local](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
1. Nossas ferramentas definiram a configuração de execução local automaticamente quando você executar a execução local e depuração local. Abra a configuração **[Spark no hdinsight] xxx** no canto superior direito, você pode ver o **[Spark no hdinsight] xxx** já criado em **Apache Spark no HDInsight**. Mude para a guia **Executar localmente**.

    ![Configuração de execução local](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Variáveis de ambiente](#prerequisite-for-windows): Se a variável de ambiente do sistema **HADOOP_HOME** já foi definida como **C:\WinUtils**, isso detectará automaticamente que não será necessário adicionar manualmente.
    - [Local de WinUtils.exe](#prerequisite-for-windows): Se a variável de ambiente do sistema ainda não foi definida, você poderá localizar o local clicando no botão.
    - Basta escolher uma das duas opções, e elas não são necessárias em MacOS e Linux.
1. Você também pode definir a configuração manualmente antes de executar a depuração e execução local. Na captura de tela acima, selecione o sinal de adição ( **+** ). Em seguida, selecione a opção **Apache Spark no HDInsight** . Insira as informações para **Nome**, **Nome da classe principal** para salvar, em seguida, clique no botão de execução local.

### <a name="scenario-3-perform-local-debugging"></a>Cenário 3: Executar depuração local
1. Abra o script **SparkCore_wasbloTest** e defina os pontos de interrupção.
1. Clique com o botão direito do mouse no editor de scripts e selecione a opção **depurar ' [Spark no HDInsight] xxx '** para executar a depuração local.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Saiba como realizar a execução e depuração remota
### <a name="scenario-1-perform-remote-run"></a>Cenário 1: Realizar execução remota

1. Para acessar o menu **Editar Configurações**, selecione o ícone no canto superior direito. Nesse menu, você poderá criar ou editar as configurações de depuração remota.

   ![Editar configurações](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. Na caixa de diálogo **Configurações de Execução/Depuração**, selecione o sinal de mais ( **+** ). Em seguida, selecione a opção **Apache Spark no HDInsight** .

   ![Adicionar nova configuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. Mude para a guia **Executar Remotamente em Cluster**. Insira as informações em **Nome**, **Cluster do Spark** e **Nome da classe principal**. Em seguida, clique em **Configuração avançada (depuração remota)** . Nossas ferramentas oferecem suporte à depuração com **Executores**. O **numExectors**, o valor padrão é 5. É melhor não definir como maior que 3.

   ![Executar configurações de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Na parte **Configuração avançada (depuração remota)** , selecione **Habilitar depuração remota do Spark**. Insira o nome de usuário do SSH e insira uma senha ou use um arquivo de chave privada. Se desejar executar a depuração remota, será necessário configurá-la. Não será necessário configurá-la se quiser usar apenas a execução remota.

   ![Habilitar depuração remota do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Agora, a configuração está salva com o nome fornecido. Para exibir os detalhes de configuração, selecione o nome da configuração. Para fazer alterações, selecione **Editar configurações**. 

1. Após concluir as definições de configurações, você poderá executar o projeto no cluster remoto ou realizar a depuração remota.
   
   ![Botão de execução remota](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

1. Clique no botão **Desconectar** para ocultar os logs de envio do painel esquerdo. Eles continuarão sendo executados em segundo plano.

   ![Botão de execução remota](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Cenário 2: Executar depuração remota
1. Configure pontos de interrupção e, em seguida, clique no ícone **Depuração remota**. A diferença com envio remoto é que o nome de usuário/senha de SSH devem ser configurados.

   ![Selecione o ícone de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Quando a execução do programa atingir o ponto de interrupção, você verá uma guia **Driver** e duas guias **Executor** no painel **Depurador**. Selecione o ícone **Retomar Programa** para continuar a execução do código, que alcança o próximo ponto de interrupção. Você precisará mudar para a guia **Executor** correta para localizar o executor de destino para depurar. Você pode visualizar os logs de execução na guia **Console** correspondente.

   ![Guia depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Cenário 3: Para realizar depuração remota e correção de bugs
1. Configure dois pontos de interrupção e, em seguida, selecione o ícone **Depurar** para iniciar o processo de depuração remota.

1. O código para no primeiro ponto de interrupção e as informações de parâmetro e de variáveis são mostradas no painel **Variáveis**. 

1. Selecione o ícone **Retomar Programa** para continuar. O código para no segundo ponto. A exceção é capturada conforme o esperado.

   ![Gerar erro](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. Selecione o ícone **Retomar Programa** novamente. A janela **Envio do HDInsight Spark** exibe um erro de “falha na execução do trabalho”.

   ![Envio de erro](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. Para atualizar dinamicamente o valor da variável usando a capacidade de depuração do IntelliJ, selecione **Depurar** novamente. O painel **Variáveis** é exibido novamente. 

1. Clique com o botão direito do mouse no destino na guia **Depurar** e, então, selecione **Definir valor**. Em seguida, insira um novo valor para a variável. Depois, selecione **Enter** para salvar o valor. 

   ![Definir valor](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

1. Selecione o ícone **Retomar Programa** para continuar a execução do programa. Neste momento, nenhuma exceção é detectada. Você pode ver que o projeto é executado com êxito, sem exceções.

   ![Depurar sem exceção](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Próximas etapas
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração
* Crie o projeto Scala (vídeo): [Criar aplicativos Scala do Apache Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Usar o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente em um cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de prédios usando dados HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Use o Azure Toolkit for IntelliJ para criar aplicativos do Apache Spark para um cluster do HDInsight](apache-spark-intellij-tool-plugin.md)
* [Use o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente por meio de VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos do Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o notebook Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
