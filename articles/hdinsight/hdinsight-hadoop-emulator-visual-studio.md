---
title: Ferramentas do Data Lake para Visual Studio com a Área Restrita da Hortonworks – Azure HDInsight
description: Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio com a área restrita da Hortonworks em execução em uma VM local. Com essas ferramentas, você pode criar e executar trabalhos Hive e Pig na área restrita e exibir a saída e o histórico do trabalho.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: 8172da3f573f78365cbe76f435a4817e64bc136c
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63762751"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Usar as Ferramentas do Azure Data Lake para Visual Studio com a Área Restrita da Hortonworks

O Azure Data Lake inclui ferramentas para trabalhar com clusters Apache Hadoop genéricos. Este documento fornece as etapas necessárias para usar as ferramentas do Data Lake com a Área Restrita da Hortonworks em execução em uma máquina virtual local.

O uso da Hortonworks Sandbox permite que você trabalhe com o Hadoop localmente em seu ambiente de desenvolvimento. Após você desenvolver uma solução e querer implantá-la em escala, mude para um cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* A Área Restrita da Hortonworks em execução em uma máquina virtual em seu ambiente de desenvolvimento. Este documento foi criado e testado com a área restrita em execução no Oracle VirtualBox. Para obter informações sobre como configurar a área restrita, confira o documento [Introdução a uma área restrita Hortonworks](hadoop/apache-hadoop-emulator-get-started.md) .

* Visual Studio 2013, Visual Studio 2015 ou Visual Studio 2017 (qualquer edição).

* O [SDK do Azure para .NET](https://azure.microsoft.com/downloads/) 2.7.1 ou posterior.

* As [Ferramentas do Azure Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Configurar senhas para a área restrita

Certifique-se de que a Área Restrita da Hortonworks está em execução. Siga as etapas do documento [Introdução à área restrita Hortonworks](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords). Essas etapas configuram a senha para a conta `root` de SSH e para a conta `admin` do Apache Ambari. Essas senhas serão usadas ao conectar-se do Visual Studio à área restrita.

## <a name="connect-the-tools-to-the-sandbox"></a>Conectar as ferramentas à área restrita

1. Abra o Visual Studio e selecione **Exibir**, **Gerenciador de Servidores**.

2. No **Gerenciador de Servidores**, clique com o botão direito do mouse na entrada **HDInsight** e selecione **Conectar ao Emulador do HDInsight**.

    ![Captura de tela do Gerenciador de Servidores, com a opção Conectar-se ao Emulador do HDInsight realçada](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Na caixa de diálogo **Conectar-se ao Emulador do HDInsight**, digite a senha que você configurou para o Ambari.

    ![Captura de tela da caixa de diálogo, com a caixa de texto de senha realçada](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Selecione **Avançar** para continuar.

4. Use o campo **Senha** para inserir a senha configurada para a conta `root`. Mantenha os outros campos com os valores padrão.

    ![Captura de tela da caixa de diálogo, com a caixa de texto de senha realçada](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Selecione **Avançar** para continuar.

5. Aguarde que a validação dos serviços seja concluída. Em alguns casos, a validação pode falhar e solicitar a atualização da configuração. Se a validação falhar, selecione o botão **Atualizar** e aguarde a conclusão da configuração e da verificação do serviço.

    ![Captura de tela da caixa de diálogo, com o botão Atualizar realçado](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]  
    > O processo de atualização usa o Ambari para modificar a configuração da Área Restrita da Hortonworks esperada pelas ferramentas do Data Lake para Visual Studio.

6. Após a conclusão da validação, selecione **Concluir** para concluir a configuração.
    ![Captura de tela da caixa de diálogo, com o botão Concluir realçado](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]  
     > Dependendo da velocidade de seu ambiente de desenvolvimento e da quantidade de memória alocada para a máquina virtual, talvez demore vários minutos para configurar e validar os serviços.

Após a execução dessas etapas, você terá uma entrada de **cluster local do HDInsight** no Gerenciador de Servidores, na seção **HDInsight**.

## <a name="write-an-apache-hive-query"></a>Escrever uma consulta do Apache Hive

O Hive fornece uma linguagem de consulta do tipo SQL (HiveQL) para trabalhar com os dados estruturados. Use as etapas a seguir para saber como executar consultas sob demanda no cluster local.

1. No **Gerenciador de Servidores**, clique com o botão direito do mouse na entrada do cluster local que você adicionou anteriormente e, em seguida, selecione **Escrever uma Consulta do Hive**.

    ![Captura de tela do Gerenciador de Servidores, com a opção Escrever uma Consulta do Hive realçada](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Uma nova janela de consulta é exibida. Nela, você pode gravar e enviar rapidamente uma consulta ao cluster local.

2. Na nova janela de consulta, insira o seguinte comando:

        select count(*) from sample_08;

    Para executar a consulta, selecione **Enviar** na parte superior da janela. Deixe os outros valores (**Lote** e nome do servidor) com os valores padrão.

    ![Captura de tela da janela de consulta, com o botão Enviar realçado](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Você também pode usar o menu suspenso próximo a **Enviar** para selecionar **Avançado**. Opções avançadas permitem que você forneça opções adicionais ao enviar o trabalho.

    ![Captura de tela da caixa de diálogo Enviar Script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Após você enviar a consulta, o status do trabalho será exibido. O status do trabalho exibe informações sobre o trabalho conforme ele é processado pelo Hadoop. **Estado do Trabalho** fornece o status do trabalho. O estado é atualizado periodicamente ou você pode usar o ícone de atualização para atualizar o estado manualmente.

    ![Captura de tela da caixa de diálogo Exibir Trabalho, com o Estado do Trabalho realçado](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Após o **Estado do Trabalho** mudar para **Concluído**, um DAG (grafo direcionado acíclico) é exibido. Este diagrama descreve o caminho de execução determinado por Tez durante o processamento da consulta de Hive. O Tez é o mecanismo padrão de execução para Hive no cluster local.

    > [!NOTE]  
    > O Apache Tez também é o padrão quando você usa clusters do HDInsight baseados em Linux. Ele não é o padrão em HDInsight baseado no Windows. Para usá-lo, você deve adicionar a linha `set hive.execution.engine = tez;` ao início de sua consulta de Hive.

    Use o link **Saída de Trabalho** para exibir a saída. Nesse caso, ela é 823, o número de linhas na tabela sample_08. Você pode exibir informações de diagnóstico sobre o trabalho usando os links **Log do Trabalho** e **Baixar Log do YARN**.

4. Você também pode executar trabalhos do Hive de forma interativa alterando o campo **Lote** para **Interativo**. Em seguida, selecione **Executar**.

    ![Captura de tela dos botões Interativo e Executar realçados](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Uma consulta interativa transfere o log de saída gerado durante o processamento para a janela **Saída do HiveServer2**.

    > [!NOTE]  
    > Essas são as mesmas informações disponíveis pelo link **Log de Trabalho** após a conclusão de um trabalho.

    ![Captura de tela do log de saída](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Criar um projeto do Hive

Você também pode criar um projeto que contém vários scripts do Hive. Use um projeto quando você tiver scripts relacionados ou desejar armazenar scripts em um sistema de controle de versão.

1. No Visual Studio, selecione **Arquivo**, **Novo** e **Projeto**.

2. Na lista de projetos, expanda **Modelos**, **Azure Data Lake** e selecione **HIVE (HDInsight)**. Na lista de modelos, selecione **Amostra do Hive**. Insira um nome e um local e selecione **OK**.

    ![Captura de tela da janela Novo Projeto com Azure Data Lake, HIVE, Amostra do Hive e OK realçados](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

O projeto **Amostra do Hive** contém dois scripts, **WebLogAnalysis.hql** e **SensorDataAnalysis.hql**. Você pode enviar esses scripts usando o mesmo botão **Enviar** na parte superior da janela.

## <a name="create-an-apache-pig-project"></a>Criar um projeto Apache Pig

Enquanto o Hive fornece uma linguagem semelhante ao SQL para trabalhar com os dados estruturados, o Pig funciona executando transformações nos dados. O Pig fornece uma linguagem (Pig Latin) que permite que você desenvolva um pipeline de transformações. Siga as etapas a seguir para usar o Pig com o cluster local:

1. Abra o Visual Studio, selecione **Arquivo**, **Novo** e **Projeto**. Na lista de projetos, expanda **Modelos**, **Azure Data Lake** e selecione **Pig (HDInsight)**. Na lista de modelos, selecione **Aplicativo Pig**. Insira um nome e local e selecione **OK**.

    ![Captura de tela da janela Novo Projeto com Azure Data Lake, Pig, Aplicativo Pig e OK realçados](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Insira o texto a seguir como conteúdo do arquivo **script.pig** que foi criado com este projeto.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Embora o Pig use uma linguagem diferente do Hive, o modo de execução dos trabalhos é consistente entre as duas linguagens por meio do botão **Enviar**. A seleção da lista suspensa ao lado de **Enviar** exibe uma caixa de diálogo de envio avançado para o Pig.

    ![Captura de tela da caixa de diálogo Enviar Script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. O status e a saída do trabalho também são exibidos, assim como ocorre com uma consulta do Hive.

    ![Captura de tela de um trabalho de Pig concluído](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Exibir trabalhos

As ferramentas do Data Lake também permitem que você exiba facilmente as informações sobre os trabalhos que foram executados no Hadoop. Use as etapas a seguir para ver os trabalhos que foram executados no cluster local.

1. No **Gerenciador de Servidores**, clique com o botão direito do mouse no cluster local e selecione **Exibir Trabalhos**. Uma lista de trabalhos que foram enviados para o cluster será exibida.

    ![Captura de tela do Gerenciador de Servidores, com a opção Exibir Trabalhos realçada](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Na lista de trabalhos, selecione um para exibir os detalhes do trabalho.

    ![Captura de tela do Navegador de Trabalho, com um dos trabalhos realçado](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    As informações exibidas são semelhantes as que você vê depois de executar uma consulta de Hive ou Pig, incluindo links para exibir as informações de saída e de log.

3. Você também pode modificar e reenviar o trabalho a partir daqui.

## <a name="view-hive-databases"></a>Exibir bancos de dados do Hive

1. No **Gerenciador de Servidores**, expanda a entrada **Cluster local do HDInsight** e, em seguida, expanda **Bancos de Dados Hive**. Os bancos de dados **Padrão** e **xademo** no cluster local serão exibidos. A expansão de um banco de dados revela as tabelas contidas no banco de dados.

    ![Captura de tela do Gerenciador de Servidores, com os bancos de dados realçados](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. A expansão de uma tabela exibe as colunas dessa tabela. Para exibir os dados rapidamente, clique com o botão direito do mouse em uma tabela e selecionar **Exibir as 100 Primeiras Linhas**.

    ![Captura de tela do Gerenciador de Servidores, com a tabela expandida e a opção Exibir as 100 Primeiras Linhas selecionada](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Propriedades do banco de dados e da tabela

Você pode exibir as propriedades de um banco de dados ou tabela. Selecionar **Propriedades** mostra detalhes do item selecionado na janela Propriedades. Por exemplo, veja as informações mostradas na seguinte captura de tela:

![Captura de tela da janela Propriedades](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Criar uma tabela

Para criar uma nova tabela, clique com o botão direito do mouse em um banco de dados e selecione **Criar Tabela**.

![Captura de tela do Gerenciador de Servidores, com a opção Criar Tabela realçada](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Em seguida, você pode criar a tabela usando um formulário. Na parte inferior da captura de tela a seguir, você pode ver o HiveQL bruto que será usado para criar a tabela.

![Captura de tela do formulário usado para criar uma tabela](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Próximos passos

* [Learning the ropes of the Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Tutorial do Apache Hadoop – Introdução ao HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
