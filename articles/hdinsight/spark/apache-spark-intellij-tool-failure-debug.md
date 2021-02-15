---
title: Depurar o trabalho do Spark com o kit de ferramentas do IntelliJ Azure (versão prévia) – HDInsight
description: Diretrizes usando as ferramentas do HDInsight no Azure Toolkit for IntelliJ para depurar aplicativos
keywords: depurar o intellij remotamente, depuração remota do intellij, ssh, intellij, hdinsight, depurar o intellij, depuração
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 5abbb26e7582d8ddabb73f3a178cf6d87e24db21
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942533"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Falha na depuração do trabalho Spark com Azure Toolkit for IntelliJ (versão prévia)

Este artigo fornece orientações passo a passo sobre como usar as ferramentas do HDInsight no [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) para executar aplicativos de **depuração de falha do Spark** .

## <a name="prerequisites"></a>Pré-requisitos

* [Kit de desenvolvimento Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Este tutorial usa o Java versão 8.0.202.
  
* IntelliJ IDEA. Este artigo usa a [INTELLIJ Idea Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Confira [Installing the Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation) (Instalação do Azure Toolkit for IntelliJ).

* Conecte-se ao cluster HDInsight. Consulte [conectar-se ao seu cluster HDInsight](apache-spark-intellij-tool-plugin.md).

* Gerenciador de Armazenamento do Microsoft Azure. Consulte [baixar Gerenciador de armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Criar um projeto com o modelo de depuração

Criar um projeto do Spark 2.3.2 para continuar a depuração de falha, escolher o arquivo de exemplo de depuração de tarefa de falha neste documento.

1. Abra o IntelliJ IDEA. Abra a janela **novo projeto** .

   a. Selecione **Azure Spark/HDInsight** no painel esquerdo.

   b. Selecione **projeto do Spark com falha de depuração da tarefa de falhas (visualização) (escala)** na janela principal.

     ![IntelliJ criar um projeto de depuração](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Selecione **Avançar**.

2. Na janela **Novo Projeto**, execute as seguintes etapas:

   ![IntelliJ novo projeto selecione versão do Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Insira um nome de projeto e o local do projeto.

   b. Na lista suspensa **SDK do projeto** , selecione **Java 1,8** para o cluster **Spark 2.3.2** .

   c. Na lista suspensa **versão do Spark** , selecione **Spark 2.3.2 (escala 2.11.8)**.

   d. Selecione **Concluir**.

3. Selecione **src**  >  **Main**  >  **escalar** para abrir seu código no projeto. Este exemplo usa o script **AgeMean_Div ()** .

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Executar um aplicativo de escala/Java Spark em um cluster HDInsight

Crie um aplicativo do Spark escala/Java e execute o aplicativo em um cluster Spark executando as seguintes etapas:

1. Clique em **Adicionar configuração** para abrir a janela **executar/depurar configurações** .

   ![HDI IntelliJ adicionar configuração](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. Na caixa de diálogo **Configurações de Execução/Depuração**, selecione o sinal de mais (**+**). Em seguida, selecione a opção **Apache Spark no HDInsight** .

   ![IntelliJ Adicionar nova configuração](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Alterne para **execução remota na guia cluster** . Insira informações para **nome**, **cluster do Spark** e **nome da classe principal**. Nossas ferramentas oferecem suporte à depuração com **Executores**. O **numExectors**, o valor padrão é 5, e você melhor não definiu mais que 3. Para reduzir o tempo de execução, você pode adicionar **Spark. yarn. maxAppAttempts** às **configurações de trabalho** e definir o valor como 1. Clique no botão **OK** para salvar a configuração.

   ![IntelliJ executar configurações de depuração novas](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Agora, a configuração está salva com o nome fornecido. Para exibir os detalhes de configuração, selecione o nome da configuração. Para fazer alterações, selecione **Editar configurações**.

5. Depois de concluir as configurações, você pode executar o projeto no cluster remoto.

   ![Botão de execução remota do trabalho do Spark remoto do IntelliJ Debug](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Você pode verificar a ID do aplicativo na janela saída.

   ![Resultado da execução remota do trabalho do Spark remoto do IntelliJ de depuração](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Baixar perfil de trabalho com falha

Se o envio do trabalho falhar, você poderá baixar o perfil de trabalho com falha no computador local para uma depuração adicional.

1. Abra **Gerenciador de armazenamento do Microsoft Azure**, localize a conta do HDInsight do cluster para o trabalho com falha, baixe os recursos de trabalho com falha do local correspondente: **\hdp\spark2-Events \\ . Spark \\ \<application ID> -falhas** em uma pasta local. A janela **atividades** mostrará o progresso do download.

   ![Falha no download do Gerenciador de Armazenamento do Azure](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Êxito no download de Gerenciador de Armazenamento do Azure](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Configurar o ambiente de depuração local e depurar em caso de falha

1. Abra o projeto original ou crie um novo projeto e associe-o ao código-fonte original. Somente a versão do Spark 2.3.2 tem suporte para a depuração de falha no momento.

1. Em IntelliJ IDEA, crie um arquivo de configuração de **depuração de falha do Spark** , selecione o arquivo FTD dos recursos de trabalho com falha baixados anteriormente para o campo local do contexto de falha do trabalho do **Spark** .

   ![configuração de falha de criar](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Clique no botão Executar local na barra de ferramentas, o erro será exibido na janela Executar.

   ![executar-falha-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![executar-falha-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Defina ponto de interrupção conforme o log indica, em seguida, clique no botão de depuração local para fazer a depuração local da mesma forma que seus projetos escalares/Java normais no IntelliJ.

1. Após a depuração, se o projeto for concluído com êxito, você poderá reenviar o trabalho com falha para o Spark no cluster HDInsight.

## <a name="next-steps"></a><a name="seealso"></a>Próximas etapas

* [Visão geral: depurar aplicativos Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demonstração

* Crie o projeto Scala (vídeo): [Crie aplicativos Scala do Spark do Apache](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Usar o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente em um cluster do HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: fazer análises de dados interativas usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para analisar a temperatura do edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](./apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Use o Azure Toolkit for IntelliJ para criar aplicativos do Apache Spark para um cluster do HDInsight](apache-spark-intellij-tool-plugin.md)
* [Use o Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente por meio de VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar ferramentas de HDInsight para IntelliJ com a área restrita do Hortonworks](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Use as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos do Apache Spark](./apache-spark-eclipse-tool-plugin.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com notebooks Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)