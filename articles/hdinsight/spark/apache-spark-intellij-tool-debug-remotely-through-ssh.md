---
title: 'Azure Toolkit for IntelliJ: depurar aplicativos Spark com SSH-HDInsight'
description: Instruções passo a passo sobre como usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ para depurar aplicativos remotamente nos clusters do HDInsight por meio do SSH
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: ccd642578c8c35ac6b5f23397788ad1e7f83a1f5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942603"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Depurar Apache Spark aplicativos em um cluster HDInsight com Azure Toolkit for IntelliJ por meio de SSH

Este artigo fornece orientações passo a passo sobre como usar as ferramentas do HDInsight no [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) para depurar aplicativos remotamente em um cluster HDInsight. Para depurar seu projeto, você também poderá exibir o vídeo [Depurar aplicativos do HDInsight Spark com o Kit de Ferramentas do Azure para IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Veja [Criar um cluster do Apache Spark](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Para usuários do Windows: enquanto você está executando o aplicativo local do Spark escalar em um computador Windows, você pode obter uma exceção, conforme explicado no [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque WinUtils.exe está ausente no Windows.

    Para resolver esse erro, baixe [Winutils.exe](https://github.com/steveloughran/winutils) em um local como **C:\WinUtils\bin**. Em seguida, adicione uma variável de ambiente **HADOOP_HOME** e defina o valor da variável para **C\WinUtils**.

* [INTELLIJ Idea](https://www.jetbrains.com/idea/download/#section=windows) (a Community Edition é gratuita).

* [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* [Plug-in escalar para IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Criar um aplicativo Spark Scala

1. Inicie o IntelliJ IDEA e selecione **Criar novo projeto** para abrir a janela **Novo projeto**.

1. Selecione **Apache Spark/HDInsight** no painel esquerdo.

1. Selecione **projeto do Spark com amostras (escala)** na janela principal.

1. Na lista suspensa **Ferramenta de build**, selecione uma das seguintes opções:

    * **Maven** para obter suporte ao assistente de criação de projetos Scala.
    * **SBT** para gerenciar as dependências e para criar no projeto Scala.

     ![IntelliJ criar novo projeto Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Selecione **Avançar**.

1. Na próxima janela **novo projeto** , forneça as seguintes informações:

    |Propriedade |Descrição |
    |---|---|
    |Nome do projeto|Insira um nome. Essa explicação usa `myApp` .|
    |Localização do projeto|Insira o local desejado para salvar o projeto.|
    |SDK do projeto|Se estiver em branco, selecione **novo...** e navegue até o JDK.|
    |Versão do Spark|O assistente de criação integra a versão apropriada para o SDK do Spark e o SDK do Scala. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark 2. x.**. Esse exemplo usa o **Spark 2.3.0 (Scala 2.11.8)** .|

   ![IntelliJ novo projeto selecione versão do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Selecione **Concluir**. Pode levar alguns minutos antes que o projeto fique disponível. Observe o canto inferior direito do progresso.

1. Expanda seu projeto e navegue até o exemplo src  >  **Main**  >  **escalar**  >  . Clique duas vezes em **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Realizar execução local

1. No script de **SparkCore_WasbIOTest** , clique com o botão direito do mouse no editor de scripts e selecione a opção **Executar ' SparkCore_WasbIOTest '** para executar a execução local.

1. Quando a execução local for concluída, você poderá ver o arquivo de saída salvo em seu padrão de **dados** do explorador de projeto atual  >  ****.

    ![Resultado da execução local do projeto do IntelliJ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Nossas ferramentas definiram a configuração de execução local automaticamente quando você executar a execução local e depuração local. Abra a configuração **[Spark no hdinsight] xxx** no canto superior direito, você pode ver o **[Spark no hdinsight] xxx** já criado em **Apache Spark no HDInsight**. Mude para a guia **Executar localmente**.

    ![Execução do IntelliJ configurações de depuração execução local](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Variáveis de ambiente](#prerequisites): se você já definiu a variável de ambiente do sistema **HADOOP_HOME** como **C:\WinUtils**, ela pode detectar automaticamente que não é necessário adicionar manualmente.
    - [Local de WinUtils.exe](#prerequisites): se você não tiver definido a variável de ambiente do sistema, poderá encontrar o local clicando em seu botão.
    - Basta escolher uma das duas opções, e elas não são necessárias em MacOS e Linux.

1. Você também pode definir a configuração manualmente antes de executar a depuração e execução local. Na captura de tela anterior, selecione o sinal de adição ( **+** ). Em seguida, selecione a opção **Apache Spark no HDInsight** . Insira as informações para **Nome**, **Nome da classe principal** para salvar, em seguida, clique no botão de execução local.

## <a name="perform-local-debugging"></a>Executar depuração local

1. Abra o script **SparkCore_wasbloTest** e defina os pontos de interrupção.

1. Clique com o botão direito do mouse no editor de scripts e selecione a opção **depurar ' [Spark no HDInsight] xxx '** para executar a depuração local.

## <a name="perform-remote-run"></a>Realizar execução remota

1. Navegue para **executar**  >  **Editar configurações...**. Nesse menu, você pode criar ou editar as configurações para depuração remota.

1. Na caixa de diálogo **Configurações de Execução/Depuração**, selecione o sinal de mais (**+**). Em seguida, selecione a opção **Apache Spark no HDInsight** .

   ![IntelliJ Adicionar nova configuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Alterne para **execução remota na guia cluster** . Insira informações para **nome**, **cluster do Spark** e **nome da classe principal**. Em seguida, clique em **Configuração avançada (depuração remota)**. Nossas ferramentas oferecem suporte à depuração com **Executores**. O **numExectors**, o valor padrão é 5. É melhor não definir como maior que 3.

   ![IntelliJ executar configurações de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Na parte **Configuração avançada (depuração remota)** , selecione **Habilitar depuração remota do Spark**. Insira o nome de usuário do SSH e insira uma senha ou use um arquivo de chave privada. Se desejar executar a depuração remota, será necessário configurá-la. Não será necessário configurá-la se quiser usar apenas a execução remota.

   ![Configuração avançada do IntelliJ habilitar a depuração remota do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Agora, a configuração está salva com o nome fornecido. Para exibir os detalhes de configuração, selecione o nome da configuração. Para fazer alterações, selecione **Editar configurações**.

1. Após concluir as definições de configurações, você poderá executar o projeto no cluster remoto ou realizar a depuração remota.

   ![Botão de execução remota do trabalho do Spark remoto do IntelliJ Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Clique no botão **Desconectar** para ocultar os logs de envio do painel esquerdo. Eles continuarão sendo executados em segundo plano.

   ![Resultado da execução remota do trabalho do Spark remoto do IntelliJ de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Executar depuração remota

1. Configure pontos de interrupção e, em seguida, clique no ícone **Depuração remota**. A diferença com envio remoto é que o nome de usuário/senha de SSH devem ser configurados.

   ![Ícone de depuração do trabalho do Spark remoto do IntelliJ Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Quando a execução do programa atingir o ponto de interrupção, você verá uma guia **Driver** e duas guias **Executor** no painel **Depurador**. Selecione o ícone **Retomar Programa** para continuar a execução do código, que alcança o próximo ponto de interrupção. Você precisará mudar para a guia **Executor** correta para localizar o executor de destino para depurar. Você pode visualizar os logs de execução na guia **Console** correspondente.

   ![Guia de depuração de trabalho do Spark remoto do IntelliJ Debug](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Para realizar depuração remota e correção de bugs

1. Configure dois pontos de interrupção e, em seguida, selecione o ícone **Depurar** para iniciar o processo de depuração remota.

1. O código para no primeiro ponto de interrupção e as informações de parâmetro e de variáveis são mostradas no painel **Variáveis**.

1. Selecione o ícone **Retomar Programa** para continuar. O código para no segundo ponto. A exceção é capturada conforme o esperado.

   ![Erro de geração de trabalho do Spark remoto do IntelliJ de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Selecione o ícone **Retomar Programa** novamente. A janela de **envio do HDInsight Spark** exibe um erro "falha na execução do trabalho".

   ![Envio de erro de trabalho do Spark remoto do IntelliJ de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Para atualizar dinamicamente o valor da variável usando a capacidade de depuração do IntelliJ, selecione **Depurar** novamente. O painel **Variáveis** é exibido novamente.

1. Clique com o botão direito do mouse no destino na guia **Depurar** e, então, selecione **Definir valor**. Em seguida, insira um novo valor para a variável. Depois, selecione **Enter** para salvar o valor.

   ![Valor do conjunto de trabalhos do Spark remoto do IntelliJ de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Selecione o ícone **Retomar Programa** para continuar a execução do programa. Neste momento, nenhuma exceção é detectada. Você pode ver que o projeto é executado com êxito, sem exceções.

   ![Trabalho do Spark remoto do IntelliJ de depuração sem exceção](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Próximas etapas

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
* [Use o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente por meio de VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos do Apache Spark](./apache-spark-eclipse-tool-plugin.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com notebooks Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)