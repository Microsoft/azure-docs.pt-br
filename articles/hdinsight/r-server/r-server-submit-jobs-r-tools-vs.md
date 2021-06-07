---
title: Enviar trabalhos a partir das Ferramentas do R para Visual Studio - Azure HDInsight
description: Envie trabalhos de R em sua máquina local do Visual Studio para um cluster HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: ee984de22052076618728fbacfc31b73c18c073a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864679"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Enviar trabalhos a partir das Ferramentas do R para Visual Studio

[Ferramentas R para Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) é uma extensão gratuita e de código-fonte aberto para as edições Community (gratuita), Professional e Enterprise do [Visual Studio 2017](https://www.visualstudio.com/downloads/), e [Visual Studio 2015 Atualização 3](https://go.microsoft.com/fwlink/?LinkId=691129) ou superior. O RTVS não está disponível para o [Visual Studio 2019](/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?preserve-view=true&view=vs-2019).

RTVS aprimora seu fluxo de trabalho de R, oferecendo ferramentas como o [janela de R Interativo](/visualstudio/rtvs/interactive-repl) (REPL), intellisense (conclusão de código), [plotar visualização](/visualstudio/rtvs/visualizing-data) por meio de bibliotecas de R como ggplot2 e ggviz, [depuração de código de R](/visualstudio/rtvs/debugging)e muito mais.

## <a name="set-up-your-environment"></a>Configure seu ambiente

1. Instalar [Ferramentas do R para Visual Studio](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png" alt-text="Instalando o RTVS no Visual Studio de 2017" border="true":::

2. Selecione a carga de trabalho *ciência de dados e aplicativos analíticos*, selecione as opções **suporte a idioma R**, **suporte de runtime para o desenvolvimento de R**, e **Microsoft R Client**.

3. Você precisa ter as chaves públicas e privadas para autenticação de SSH.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Instale o [ML Server](/previous-versions/machine-learning-server/install/r-server-install-windows) em seu computador. ML Server fornece as [`RevoScaleR`](/machine-learning-server/r-reference/revoscaler/revoscaler) `RxSpark` funções e.

5. Instale [PuTTY](https://www.putty.org/) para fornecer um contexto de computação para executar `RevoScaleR` funções de seu cliente local para seu cluster HDInsight.

6. Você tem a opção de aplicar as Configurações da Ciência de Dados para o ambiente do Visual Studio, que fornece um novo layout para seu workspace para as ferramentas de R.
   1. Para salvar suas configurações atuais do Visual Studio, use o comando **Ferramentas > Importar e exportar configurações** e, em seguida, selecione **Exportar configurações de ambiente selecionadas** e especifique um nome de arquivo. Para restaurar essas configurações, use o mesmo comando e selecione **Importar configurações de ambiente selecionadas**.

   2. Vá para o item de menu **Ferramentas R**, selecione **Configurações da Ciência de Dados...**.

       :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png" alt-text="Configurações da Ciência de Dados do Visual Studio" border="true":::

      > [!NOTE]  
      > Usando a abordagem na etapa 1, você pode também salvar e restaurar o layout de cientista de dados personalizado, em vez de repetir o comando **Configurações da Ciência de Dados**.

## <a name="execute-local-r-methods"></a>Executar métodos locais de R

1. Crie seu cluster de ML Services do HDInsight.
2. Instale a [extensão RTVS](/visualstudio/rtvs/installation).
3. Faça o download do [arquivo zip de exemplos](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Abra `examples/Examples.sln` para iniciar a solução no Visual Studio.
5. Abra o `1-Getting Started with R.R` arquivo na `A first look at R` pasta da solução.
6. Iniciando na parte superior do arquivo, pressione Ctrl + Enter para enviar cada linha, uma de cada vez, à janela de R Interativo. Algumas linhas podem levar algum tempo já que instalam pacotes.
    * Como alternativa, você pode selecionar todas as linhas no arquivo de R (Ctrl + A), em seguida, execute todos os (Ctrl + Enter) ou selecione o ícone Executar em interativo na barra de ferramentas.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png" alt-text="Execução interativa do Visual Studio" border="true":::

7. Depois de executar todas as linhas no script, você verá uma saída semelhante a este:

    :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png" alt-text="Ferramentas de R do espaço de trabalho do Visual Studio" border="true":::

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Enviar trabalhos para um cluster ML Services do HDInsight

Usando um Microsoft ML Server/Microsoft R Client de um computador com Windows equipado com PuTTY, você pode criar um contexto de computação que executará funções distribuídas `RevoScaleR` de seu cliente local para seu cluster HDInsight. Use `RxSpark` para criar o contexto de computação, especificando seu nome de usuário, o nó de borda do cluster do Apache Hadoop, os switches SSH e assim por diante.

1. O endereço do nó de borda dos serviços ML no HDInsight é `CLUSTERNAME-ed-ssh.azurehdinsight.net` onde `CLUSTERNAME` é o nome do seu cluster de serviços ml.

1. Cole o código a seguir na janela de R Interativo no Visual Studio, alterando os valores das variáveis de programa de instalação para coincidir com seu ambiente.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png" alt-text="Apache Spark Configurando o contexto" border="true":::

1. Execute os comandos a seguir na janela de R Interativo:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Você deverá ver um resultado semelhante ao seguinte:

    :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png" alt-text="Execução bem-sucedida do comando de rx" border="true":::
um
1. Verifique se o arquivo `rxHadoopCopy` foi copiados com êxito `people.json` da pasta de dados de exemplo para a `/user/RevoShare/newUser` pasta recém-criada:

    1. No painel do cluster ML Services do HDInsight no Azure, selecione **contas de armazenamento** no menu à esquerda.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png" alt-text="Contas de armazenamento do Azure HDInsight" border="true":::

    2. Selecione a conta de armazenamento padrão para o cluster, anotando o nome do contêiner/diretório.

    3. Selecione **Contêineres** no menu à esquerda no painel de conta de armazenamento.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png" alt-text="Contêineres de armazenamento do Azure HDInsight" border="true":::

    4. Selecione nome do contêiner do cluster, navegue até a pasta **Usuário** (você talvez precise clicar *Carregar mais* na parte inferior da lista), em seguida, selecione *RevoShare*, em seguida, **newUser**. O `people.json` arquivo deve ser exibido no `newUser` pasta.

        :::image type="content" source="./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png" alt-text="Local da pasta de arquivos copiados do HDInsight" border="true":::

1. Depois que você terminar de usar o contexto atual do Apache Spark, deverá pará-lo. Você não pode executar simultaneamente vários contextos.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Próximas etapas

* [Opções de contexto de computação para o ML Services no HDInsight](r-server-compute-contexts.md)
* [Combinar ScaleR e SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) fornece um exemplo de previsões de atraso de voo aérea.