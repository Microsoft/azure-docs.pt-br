---
title: Tutorial – Azure Toolkit for IntelliJ (aplicativo Spark)
description: Tutorial – Usar o Azure Toolkit for IntelliJ para desenvolver aplicativos Spark escritos em Scala e enviá-los a um Pool do Apache Spark sem servidor.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: a4512a8f75d59f617058a50846d7578a90b9d062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675813"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>Tutorial: Criar um aplicativo Apache Spark com o IntelliJ usando um workspace do Azure Synapse

Este tutorial mostra como usar o plug-in do Azure Toolkit for IntelliJ para desenvolver aplicativos Apache Spark escritos em [Scala](https://www.scala-lang.org/) e depois enviá-los diretamente do IDE (ambiente de desenvolvimento integrado) IntelliJ para um Pool do Apache Spark sem servidor. Você pode usar o plug-in destas maneiras:

- Desenvolver e enviar um aplicativo Scala Spark em um Pool do Spark.
- Acessar seus recursos de pools do Spark.
- Desenvolver e executar um aplicativo Scala Spark localmente.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> - Usar o plug-in Azure Toolkit for IntelliJ
> - Desenvolver aplicativos do Apache Spark
> - Enviar aplicativo para pools do Spark

## <a name="prerequisites"></a>Pré-requisitos

- [Versão da Comunidade do IntelliJ IDEA](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Plug-in do Azure Toolkit 3.27.0-2019.2 – Instalar do [repositório de plug-ins do IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation)
- [JDK (Versão 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Plugin Scala – Instalar do [repositório de plug-ins do IntelliJ](../../hdinsight/spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).
- O seguinte pré-requisito se aplica apenas a usuários do Windows:

  Durante a execução do aplicativo Scala Spark local em um computador Windows, você pode receber uma exceção, conforme explicado em [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorre porque WinUtils.exe está ausente no Windows.
  Para resolver esse erro, baixe o [executável WinUtils](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe), salvando-o em uma localização como **C:\WinUtils\bin**. Em seguida, adicione uma variável de ambiente **HADOOP_HOME** e defina o valor da variável para **C\WinUtils**.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Criar um aplicativo Scala Spark para um Pool do Spark

1. Inicie o IntelliJ IDEA e selecione **Criar novo projeto** para abrir a janela **Novo projeto**.
2. Selecione **Apache Spark/HDInsight** no painel esquerdo.
3. Selecione **Projeto Spark com Exemplos (Scala)** na janela principal.
4. Na lista suspensa **Ferramenta de build**, selecione um dos seguintes tipos:

   - **Maven** para obter suporte ao assistente de criação de projetos Scala.
   - **SBT** para gerenciar as dependências e para criar no projeto Scala.

    ![A caixa de diálogo Novo Projeto no IntelliJ IDEA](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Selecione **Avançar**.
6. Na janela **Novo Projeto**, forneça as seguintes informações:

    | Propriedade | Descrição |
    | ----- | ----- |
    |Nome do projeto| Insira um nome. Este tutorial usa `myApp`.|
    |Local do&nbsp;projeto| Insira a localização desejada para salvar o projeto.|
    |SDK do projeto| Isso poderá ficar em branco no primeiro uso do IDEA. Selecione **Novo...** e navegue até o JDK.|
    |Versão do Spark|O assistente de criação integra a versão apropriada para o SDK do Spark e o SDK do Scala. O Synapse só dá suporte ao **Spark 2.4.0**.|
    |||

    ![Selecionar o SDK do Apache Spark](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Selecione **Concluir**. Pode levar alguns minutos antes que o projeto fique disponível.
8. O projeto do Spark cria automaticamente um artefato para você. Para ver o artefato, execute as seguintes operações:

   a. Na barra de menus, navegue até **Arquivo** > **Estrutura do projeto...** .

   b. Na janela **Estrutura do Projeto**, selecione **Artefatos**.

   c. Selecione **Cancelar** depois de exibir o artefato.

    ![Informações do artefato na caixa de diálogo](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Localize **LogQuery** em **myApp** > **src** > **main** > **scala**> **sample**> **LogQuery**. Este tutorial usa **LogQuery** para executar.

   ![Comandos para criar uma classe Scala do Projeto](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Conectar-se aos seus pools do Spark

Entre na assinatura do Azure para se conectar aos seus pools do Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Entre em sua assinatura do Azure

1. Na barra de menus, navegue até **Exibição** > **Janelas de Ferramentas** > **Azure Explorer**.

   ![O IntelliJ IDEA mostra o Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. No Azure Explorer, clique com o botão direito do mouse no nó **Azure** e, em seguida, selecione **Entrar**.

   ![Clique com o botão direito do mouse no Azure Explorer pelo IntelliJ IDEA](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. Na caixa de diálogo **Entrar no Azure**, escolha **Logon do Dispositivo** e selecione **Entrar**.

    ![Entrada no Azure pelo IntelliJ IDEA](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. Na caixa de diálogo **Logon no Dispositivo do Azure**, selecione **Copiar e Abrir**.

   ![Logon no dispositivo do Azure pelo IntelliJ IDEA](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. Na interface do navegador, cole o código e selecione **Avançar**.

   ![Caixa de diálogo de inserção de código da Microsoft para HDI](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Insira suas credenciais do Azure e feche o navegador.

   ![Caixa de diálogo de inserção de email da Microsoft para HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Depois que você estiver conectado, a caixa de diálogo **Selecionar Assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Selecione sua assinatura e escolha **Selecionar**.

    ![A caixa de diálogo Selecionar Assinaturas](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. No **Azure Explorer**, expanda **Apache Spark no Synapse** para ver os workspaces que estão em suas assinaturas.

    ![IntelliJ IDEA exibição principal do Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Para ver os pools do Spark, você pode expandir ainda mais um workspace.

    ![Contas de armazenamento do Azure Explorer](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Executar remotamente um aplicativo Scala Spark em um Pool do Spark

Depois de criar um aplicativo Scala, você poderá executá-lo remotamente.

1. Abra a janela **Configurações de Execução/Depuração** selecionando o ícone.

    ![O comando Enviar Aplicativo Spark para HDInsight 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. Na janela da caixa de diálogo **Configurações de Execução/Depuração**, selecione **+** e escolha **Apache Spark no Azure Synapse**.

    ![O comando Enviar Aplicativo Spark para HDInsight 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. Na janela **Configurações de Execução/Depuração**, forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Pools do Spark|Selecione os pools do Spark nos quais você deseja executar o aplicativo.|
    |Selecione um Artefato para enviar|Deixe a configuração padrão.|
    |Nome da classe principal|O valor padrão é a classe principal do arquivo selecionado. Você pode alterar a classe selecionando as reticência ( **...** ) e escolhendo outra classe.|
    |Configurações de trabalho|Você pode alterar os valores e as chaves padrão. Para obter mais informações, confira [API REST do Apache Livy](http://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumentos de linha de comando|Você pode inserir argumentos separados por espaço para a classe principal se necessário.|
    |Arquivos Referenciados e Jars Referenciados|você pode inserir os caminhos para os Jars e os arquivos referenciados, se houver. Você também pode procurar arquivos no sistema de arquivos virtual do Azure, que atualmente dá suporte apenas ao cluster ADLS Gen2. Para obter mais informações: [Configuração do Apache Spark]https://spark.apache.org/docs/2.4.5/configuration.html#runtime-environment) e [Como carregar recursos no cluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md).|
    |Armazenamento de Upload de Trabalho|Expanda para revelar opções adicionais.|
    |Tipo de armazenamento|Selecione **Usar o blob do Azure para carregar** ou **Usar a conta de armazenamento padrão do cluster para carregar** na lista suspensa.|
    |Conta de Armazenamento|Insira sua conta de armazenamento.|
    |Chave de Armazenamento|Insira sua chave de armazenamento.|
    |Contêiner de armazenamento|Selecione seu contêiner de armazenamento na lista suspensa depois que **Conta de Armazenamento** e **Chave de Armazenamento** tiverem sido inseridas.|

    ![A caixa de diálogo Envio do Spark 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Selecione o ícone **SparkJobRun** para enviar seu projeto para o Pool do Spark selecionado. A guia **Trabalho do Spark Remoto no Cluster** exibe o andamento da execução do trabalho na parte inferior. Você pode parar o aplicativo selecionando o botão vermelho.

    ![A janela Envio do Apache Spark](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![A caixa de diálogo Envio do Spark 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Depurar/executar localmente aplicativos do Apache Spark

Você pode seguir as instruções abaixo para configurar a execução e a depuração locais para seu trabalho do Apache Spark.

### <a name="scenario-1-do-local-run"></a>Cenário 1: Fazer execução local

1. Abra a caixa de diálogo **Configurações de Execução/Depuração** e selecione o sinal de mais ( **+** ). Em seguida, selecione a opção **Apache Spark no Synapse**. Insira as informações em **Nome** e **Nome de classe principal** para salvar.

    ![Execução do IntelliJ configurações de depuração execução local 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - As variáveis de ambiente e a localização de WinUtils.exe são apenas para usuários do Windows.
    - Variáveis de ambiente: A variável de ambiente do sistema poderá ser detectada automaticamente se você a tiver definido antes e não precisar adicioná-la manualmente.
    - [Local de WinUtils.exe](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): Você pode especificar a localização de WinUtils selecionando o ícone de pasta à direita.

2. Depois, selecione o botão de execução local.

    ![Execução do IntelliJ configurações de depuração execução local 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Depois que a execução local for concluída, se o script incluir a saída, você poderá verificar o arquivo de saída em **data** > **__default__**.

    ![Resultado da execução local do projeto do IntelliJ 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Cenário 2: fazer depuração local

1. Abra o script **LogQuery** e defina pontos de interrupção.
2. Selecione o ícone **Depuração local** para fazer esse procedimento.

    ![Resultado da execução local do projeto do IntelliJ 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Acessar e gerenciar o workspace do Synapse

Você pode executar operações diferentes no Azure Explorer no Azure Toolkit for IntelliJ. Na barra de menus, navegue até **Exibição** > **Janelas de Ferramentas** > **Azure Explorer**.

### <a name="launch-workspace"></a>Iniciar o workspace

1. No Azure Explorer, navegue até **Apache Spark no Synapse** e, em seguida, expanda-o.

    ![IntelliJ IDEA exibição principal do Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Clique com o botão direito do mouse em um workspace e selecione **Iniciar workspace**. O site será aberto.

    ![Spark detalhes do aplicativo de exibição de trabalho 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Spark detalhes do aplicativo de exibição de trabalho 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Console do Spark

Você pode executar o Console Local do Spark (Scala) ou o Console de Sessão Interativa do Spark Livy (Scala).

### <a name="spark-local-console-scala"></a>Console local do Spark (Scala)

Verifique se você satisfez o pré-requisito do WINUTILS.EXE.

1. Na barra de menus, navegue até **Executar** > **Editar Configurações...** .
2. Na janela **Configurações de Execução/Depuração**, no painel esquerdo, navegue até **Apache Spark no Synapse** >  **[Spark no Synapse] myApp**.
3. Na janela principal, selecione a guia **Executar Localmente**.
4. Forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Variáveis de ambiente|Verifique se o valor de HADOOP_HOME está correto.|
    |Localização de WINUTILS.exe|Verifique se o caminho está correto.|

    ![Configuração do conjunto de console local](./media/intellij-tool-synapse/local-console-synapse01.png)

5. De Projeto, navegue até **myApp** > **src** > **main** > **scala** > **myApp**.
6. Na barra de menus, navegue até **Ferramentas** > **Console do Spark** > **Executar Console Local do Spark (Scala)** .
7. Em seguida, duas caixas de diálogo poderão ser exibidas para perguntar se você deseja corrigir automaticamente as dependências. Em caso afirmativo, selecione **Autocorreção**.

    ![IntelliJ IDEA Spark caixa de diálogo correção automática1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark caixa de diálogo correção automática2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. O console deve ser semelhante à imagem abaixo. Na janela do console, digite `sc.appName` e pressione ENTER. O resultado será exibido. Você pode interromper o console local selecionando o botão vermelho.

    ![IntelliJ IDEA resultado do console local](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Console de sessão interativa do Spark Livy (Scala)

Ele é compatível apenas com o IntelliJ 2018.2 e 2018.3.

1. Na barra de menus, navegue até **Executar** > **Editar Configurações...** .

2. Na janela **Configurações de Execução/Depuração**, no painel esquerdo, navegue até **Apache Spark no Synapse** >  **[Spark no Synapse] myApp**.

3. Na janela principal, selecione a guia **Executar Remotamente no Cluster**.

4. Forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Nome da classe principal| Selecione o nome de classe Main.| 
    |Pools do Spark|Selecione os pools do Spark nos quais você deseja executar o aplicativo.|
    ||

    ![Configuração do conjunto de console interativo](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. De Projeto, navegue até **myApp** > **src** > **main** > **scala** > **myApp**.

6. Na barra de menus, navegue até **Ferramentas** > **Console do Spark** > **Executar Console de Sessão Interativa do Spark Livy (Scala)** .
7. O console deve ser semelhante à imagem abaixo. Na janela do console, digite `sc.appName` e pressione ENTER. O resultado será exibido. Você pode interromper o console local selecionando o botão vermelho.

    ![IntelliJ IDEA resultado do Console Interativo](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Enviar seleção ao console do Spark

Talvez você queira ver o resultado do script enviando código ao console local ou ao Console de Sessão Interativa do Livy (Scala). Para fazer isso, você pode realçar o código no arquivo do Scala e clicar com o botão direito do mouse em **Enviar Seleção ao Console do Spark**. O código selecionado será enviado para o console e será executado. O resultado será exibido após o código no console. O console verificará os erros existentes.

   ![Enviar seleção ao console do Spark](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Próximas etapas

- [Azure Synapse Analytics](../overview-what-is.md)
- [Criar um Pool do Apache Spark para um workspace do Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)