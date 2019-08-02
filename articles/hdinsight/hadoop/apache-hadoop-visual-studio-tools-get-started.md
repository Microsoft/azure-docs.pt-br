---
title: Conectar-se ao Apache Hadoop usando as Ferramentas do Data Lake para Visual Studio - Azure HDInsight
description: Saiba como instalar e usar o Data Lake Tools para Visual Studio para se conectar a clusters do Apache Hadoop no HDInsight do Azure e, em seguida, executar consultas do Hive.
keywords: ferramentas do hadoop, consulta hive, visual studio, hadoop do visual studio
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 485f3a4b6a5fde532229873fe1f3feaa30ece523
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450207"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Use Data Lake Tools para Visual Studio para se conectar ao Azure HDInsight e executar consultas do Apache Hive

Saiba como usar [Microsoft Azure Data Lake e Stream Analytics Tools para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (também chamadas ferramentas do Data Lake) para conectar-se ao Apache Hadoop clusters no [Azure HDInsight](../hdinsight-hadoop-introduction.md) e enviar consultas Hive.  

Para obter mais informações sobre como usar o HDInsight, consulte [Introdução ao HDInsight](../hdinsight-hadoop-introduction.md) e [Começar a usar o HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Para obter mais informações sobre como conectar um cluster do Apache Storm, consulte [Desenvolver topologias C# para Apache Storm no HDInsight usando Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

As Ferramentas do Data Lake para Visual Studio podem ser usadas para acessar o Data Lake Analytics e o HDInsight. Para obter informações sobre as Ferramentas do Data Lake, confira [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo e usar as ferramentas do Data Lake para Visual Studio, você precisa dos seguintes itens:

* Um cluster Azure HDInsight. Para criar um cluster Azure HDInsight, consulte [Introdução ao uso do Apache Hadoop no Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Para executar consultas do Hive interativas, é necessário ter um cluster de [Consulta interativa do HDInsight](../interactive-query/apache-interactive-query-get-started.md).  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 ou mais recente).  O [Visual Studio Community edition](https://visualstudio.microsoft.com/vs/community/) é gratuito.  Consulte também [instalar o Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [2019 do Visual Studio](https://visualstudio.microsoft.com/downloads/). Há variações de interface leve com o Visual Studio de 2019.

  > [!IMPORTANT]  
  > Não há suporte para ferramentas do data Lake para Visual Studio 2013.

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar Ferramentas do Data Lake para Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 ou Visual Studio de 2019  
  Durante a instalação, verifique se você incluir pelo menos as cargas de trabalho **desenvolvimento do Azure** ou **processamento e armazenamento de dados**.  

  Para instalações existentes, na barra de menus, navegue até **ferramentas** > **obter ferramentas e recursos...**  para abrir o instalador do Visual Studio.  Em seguida, selecione pelo menos cargas de trabalho **desenvolvimento do Azure** ou **processamento e armazenamento de dados**.

  ![Captura de tela do instalador do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 e 2015  
  [Baixar ferramentas do Data Lake](https://www.microsoft.com/download/details.aspx?id=49504). Escolha a versão das Ferramentas do Data Lake que correspondem à versão do Visual Studio.  

> [!NOTE]  
> Atualmente, somente a versão em inglês das Ferramentas do Data Lake para Visual Studio está disponível.

## <a name="update-data-lake-tools-for-visual-studio"></a>Atualizar as ferramentas do Data Lake para Visual Studio  

1. Abra o Visual Studio.

2. Na barra de menus, navegue até **ferramentas** > **extensões e atualizações...** .

3. Dos **extensões e atualizações** janela, expanda **atualizações** à esquerda.

4. Se uma atualização estiver disponível, **do Azure Data Lake e ferramentas de análise de Stream** aparecerá na janela principal.  Selecione **Atualização**.

> [!NOTE]  
> Você pode usar somente as Ferramentas do Data Lake versão 2.3.0.0 ou posterior para se conectar aos clusters de Consulta Interativa e executar consultas interativas Hive.

## <a name="connect-to-azure-subscriptions"></a>Conectar-se a assinaturas do Azure
Você pode usar as ferramentas do Data Lake para Visual Studio para se conectar a clusters HDInsight, executar algumas operações básicas de gerenciamento e executar consultas Hive.

> [!NOTE]  
> Para obter informações sobre como se conectar a um cluster Hadoop genérico, confira [Escrever e enviar consultas Hive usando o Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).

Para conectar-se à sua assinatura do Azure:

1. Abra o Visual Studio.

2. Na barra de menus, navegue até **modo de exibição** > **Gerenciador de servidores**.

3. No Gerenciador de servidores, clique com botão direito **Azure**, selecione **conectar-se a assinatura do Microsoft Azure...** e conclua o processo de entrada.

4. No Gerenciador de servidores, será exibida uma lista de clusters existentes do HDInsight. Se não tiver nenhum cluster, você poderá criar um usando o portal do Azure, o Azure PowerShell ou o SDK do HDInsight. Para saber mais, veja [Criar clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Captura de tela da lista de clusters das Ferramentas do Data Lake para Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Lista de clusters das Ferramentas do Data Lake para Visual Studio no Gerenciador de Servidores")

5. Expanda um cluster do HDInsight. **Bancos de dados do hive**, uma conta de armazenamento padrão, contas de armazenamento vinculadas, e **Log de serviço do Hadoop** aparecem. Você pode expandir mais as entidades.

Depois de se conectar à sua assinatura do Azure, você pode executar as seguintes tarefas.

Conectar-se ao portal do Azure pelo Visual Studio:

1. No Server Explorer, navegue até **Azure** > **HDInsight** e selecione seu cluster.

2. Um cluster HDInsight com o botão direito e selecione **gerenciar Cluster no portal do Azure [sic]** .

Para fazer perguntas e/ou fornecer comentários do Visual Studio:

1. No Server Explorer, navegue até **Azure** > **HDInsight**.

2. Clique com botão direito **HDInsight** e selecione **Fórum do MSDN** para fazer perguntas, ou **fornecer comentários** para enviar comentários.

## <a name="link-a-cluster"></a>Vincular um cluster
Você pode vincular um cluster clicando **HDInsight** , em seguida, selecione **vincular um HDInsight Cluster**. Insira **Url de Conexão**, **nome de usuário** e **senha**, clique em **Avançar** , em seguida, **concluir**, o cluster deve ser listado no nó do HDInsight com êxito.

![Captura de tela de ferramentas do Data Lake para a caixa de diálogo do Visual Studio link cluster](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Clique com botão direito no cluster vinculado, selecione **editar**, o usuário pode atualizar as informações de cluster. A adição de um cluster HDInsight só dá suporte a Hive por enquanto.

![Captura de tela de ferramentas do Data Lake para Visual Studio atualização de cluster de link](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Explorar recursos vinculados
No Gerenciador de Servidores, pode-se visualizar a conta de armazenamento padrão e quaisquer contas de armazenamento vinculadas. Se você expandir a conta de armazenamento padrão, visualizará os contêineres na conta de armazenamento. Tanto a conta de armazenamento padrão como o contêiner padrão estão marcados. Clique com o botão direito do mouse em qualquer um dos contêineres para ver o conteúdo do contêiner.

![Captura de tela da lista de recursos vinculados das Ferramentas do Data Lake para Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Listar recursos vinculados")

Depois de abrir um contêiner, você poderá usar os botões abaixo para carregar, excluir e baixar blobs:

![Captura de tela das operações de blobs das Ferramentas do Data Lake para Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Carregar, excluir e baixar blobs no Gerenciador de Servidores")

## <a name="run-interactive-apache-hive-queries"></a>Executar consultas interativas do Apache Hive
O [Apache Hive](https://hive.apache.org) é uma infraestrutura do data warehouse criada no Hadoop. O Hive é usado para análise, consultas e resumo de dados. Você pode usar as Ferramentas do Data Lake para Visual Studio a fim de executar consultas Hive pelo Visual Studio. Para obter mais informações sobre o Hive, consulte [Usar Apache Hive com HDInsight](hdinsight-use-hive.md).

A [Consulta Interativa](../interactive-query/apache-interactive-query-get-started.md) usa [Hive em LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) no Apache Hive 2.1. A Consulta Interativa traz interatividade a consultas de estilo data warehouse complexas em conjuntos de dados grandes e armazenados. A execução de consultas Hive na Consulta Interativa é muito mais rápida em comparação com os trabalhos em lotes tradicionais do Hive. 

> [!NOTE]  
> Você só pode executar consultas interativas do Hive quando se conecta a um cluster de [Consulta Interativa HDInsight](../interactive-query/apache-interactive-query-get-started.md).

Use também as Ferramentas do Data Lake para Visual Studio a fim de ver o que está em um trabalho do Hive. As Ferramentas do Data Lake para Visual Studio coletam e revelam logs Yarn de determinados trabalhos do Hive.

No Server Explorer, navegue até **Azure** > **HDInsight** e selecione seu cluster.  Esse será o ponto de partida no Gerenciador de servidores para as seções a seguir.

### <a name="view-hivesampletable"></a>Modo de exibição hivesampletable
Todos os clusters de HDInsight tem uma tabela Hive de exemplo chamada `hivesampletable`.  

Em seu cluster, navegue até **bancos de dados Hive** > **padrão** > **hivesampletable**.

* Para exibir `hivesampletable` esquema:  
Expandir **hivesampletable**.

* Para exibir `hivesampletable` dados:  
Clique com botão direito **hivesampletable**e selecione **exibir as primeiras 100 linhas**.  Isso é o equivalente a executar a seguinte consulta Hive usando o driver ODBC do Hive:

   `SELECT * FROM hivesampletable LIMIT 100`

  É possível personalizar a contagem de linhas.

  ![Captura de tela de uma consulta de esquema do Hive do HDInsight para Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Resultados da consulta Hive")

### <a name="create-hive-tables"></a>Criar tabelas Hive
Você pode usar a GUI ou usar consultas Hive para criar uma tabela Hive. Para obter informações sobre o uso de consultas do Hive, consulte [Executar consultas do Apache Hive](#run.queries).

1. Em seu cluster, navegue até **bancos de dados Hive** > **padrão**.

2. Clique com botão direito **padrão**e selecione **Create Table**.

3. Configure a tabela conforme desejado.  

4. Selecione **Criar Tabela** para enviar o trabalho e criar uma nova tabela Hive.

    ![Captura de tela da janela Criar Tabela das Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Criar tabela do Hive")

### <a name="run.queries"></a>Criar e executar consultas do Hive
Você tem duas opções para criar e executar consultas do Hive:

* Criar consultas locais
* Criar um aplicativo Hive

Para criar e executar consultas ad hoc:

1. Clique com botão direito do cluster onde você deseja executar a consulta e selecione **escrever uma consulta Hive**.  

2. Insira a seguinte consulta de Hive:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    O editor do Hive é compatível com o IntelliSense. Agora as Ferramentas do Data Lake para Visual Studio dão suporte à obtenção de metadados remotos quando você edita o script do Hive. Por exemplo, se você digitar `SELECT * FROM`, o IntelliSense listará todos os nomes de tabela sugeridos. Quando um nome de tabela for especificado, o IntelliSense listará os nomes de coluna. As ferramentas dão suporte a quase todas as instruções DML Hive, subconsultas e UDFs internos.

    ![Captura de tela de um exemplo 1 de IntelliSense das Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![Captura de tela de um exemplo 2 de IntelliSense das Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > O IntelliSense sugere apenas os metadados dos clusters selecionados na Barra de Ferramentas do HDInsight.

3. Escolha o modo de execução:

    * **Interativo**  

      Certifique-se **interativo** está selecionado e, em seguida, selecione **Execute**.

      ![Captura de tela da consulta e executar](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **Batch**  

      Certifique-se **lote** está selecionado e, em seguida, selecione **enviar**.  Se você selecionar a opção de envio avançado, configure **nome do trabalho**, **argumentos**, **configurações adicionais**, e **diretório de Status**para o script.

      ![Captura de tela da consulta e lote](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![Captura de tela de uma consulta Hive do HDInsight Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Enviar consultas")

      > [!NOTE]  
      > Você não pode enviar os lotes de clusters de consulta interativa.  Você deve usar o modo interativo.

Para criar e executar uma solução de Hive:

1. Na barra de menus, navegue até **arquivo** > **New** > **projeto...** .

2. No painel esquerdo, navegue até **Installed** > **Azure Data Lake** > **HIVE (HDInsight)** .  

3. No painel central, selecione **Aplicativo Hive**. Insira as propriedades e selecione **OK**.

    ![Captura de tela de um novo projeto Hive das Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Criar aplicativos Hive pelo Visual Studio")

4. No **Gerenciador de Soluções**, clique duas vezes em **Script.hql** para abri-lo.

### <a name="view-job-summary-and-output"></a>Exibir o resumo do trabalho e saída

O resumo do trabalho varia ligeiramente entre **lote** e **interativo** modo.

![Resumo do trabalho](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "resumo do trabalho de Hive")

Use o **Refresh** botão para atualizar o status até que o status do trabalho muda para **concluído**.  

* Para obter os detalhes de trabalho do **lote** modo, selecione os links na parte inferior para ver **consulta do trabalho**, **saída do trabalho**, **log do trabalho**, ou **Log do yarn**.

* Para obter os detalhes de trabalho do **interativo** modo, consulte guias **saída** e **saída do HiveServer2**.

  ![detalhes do trabalho](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "detalhes do trabalho do Hive")

### <a name="view-job-graph"></a>Exibir o gráfico do trabalho

Atualmente, grafos de trabalho só são mostrados para trabalhos do Hive que usam o Tez como o mecanismo de execução.  Para saber mais sobre como habilitar o Tez, consulte [Usar o Apache Hive no HDInsight](hdinsight-use-hive.md).  Consulte também [usar o Apache Tez, em vez de reduzir o mapa](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Para ver todos os operadores dentro do vértice, clique duas vezes nos vértices do gráfico do trabalho. Você também pode apontar para um operador específico e ver mais detalhes sobre o operador.

O grafo de trabalho pode não aparecer, mesmo se o Tez é especificado como o mecanismo de execução, se nenhum aplicativo Tez é iniciado.  Isso pode acontecer porque o trabalho não contém instruções DML ou as instruções DML podem retornar sem iniciar um aplicativo Tez. Por exemplo, `SELECT * FROM table1` não iniciará o aplicativo Tez.

![Grafo do trabalho](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "resumo do trabalho de Hive")


### <a name="task-execution-detail"></a>Detalhes de execução da tarefa

O grafo de trabalho, você pode selecionar **detalhes de execução da tarefa** para obter informações para trabalhos do Hive estruturadas e visualizadas. Você também pode obter mais detalhes sobre o trabalho. Se ocorrerem problemas de desempenho, você poderá usar o modo de exibição para obter mais detalhes sobre o problema. Por exemplo, você pode obter informações sobre como funciona cada tarefa e informações detalhadas sobre cada tarefa (leitura/gravação de dados, agendamento/início/término e assim por diante). Use as informações para ajustar as configurações de trabalho ou a arquitetura do sistema com base nas informações visualizadas.

![Captura de tela da janela de exibição da execução de tarefas das Ferramentas do Visual Studio para o Data Lake ](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Modo de Exibição de Execução de Tarefa")


### <a name="view-hive-jobs"></a>Exibir trabalhos Hive
Você pode exibir consultas de trabalho, saída do trabalho, logs de trabalho e logs do Yarn para trabalhos do Hive.

Na versão mais recente das ferramentas, você pode ver o que está dentro de trabalhos do Hive coletando e identificando logs do Yarn. Um log do Yarn pode ajudar você a investigar problemas de desempenho. Para saber mais sobre como o HDInsight coleta logs do Yarn, confira [Acessar logs de aplicativo do HDInsight programaticamente](../hdinsight-hadoop-access-yarn-app-logs.md).

Para exibir trabalhos do Hive:

1. Um cluster HDInsight com o botão direito e selecione **Exibir trabalhos**. Uma lista dos trabalhos do Hive executados no cluster aparecerá.  

2. Selecione um trabalho. Na janela **Resumo do Trabalho de Hive**, selecione uma das seguintes opções:
    - **Consulta do Trabalho**
    - **Saída do trabalho**
    - **Log de trabalhos**  
    - **Log do Yarn**

    ![Captura de tela da janela Exibir trabalhos do Hive das Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Exibir trabalhos do Hive")


## <a name="run-apache-pig-scripts"></a>Executar scripts do Pig do Apache

1. Na barra de menus, navegue até **arquivo** > **New** > **projeto...** .

2. No painel esquerdo, navegue até **Installed** > **Azure Data Lake** > **Pig (HDInsight)** .  

3. No painel central, selecione **aplicativo Pig**. Insira as propriedades e selecione **OK**.

4. Na **Gerenciador de soluções**, clique duas vezes em **pig** para abrir o script.

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos
* Foi corrigido um problema no qual os resultados que são iniciados com valores nulos não são mostrados. Se você estiver bloqueado em relação a esse problema, contate a equipe de suporte.
* O script HQL criado pelo Visual Studio é codificado, dependendo da configuração de região local do usuário. O script não executa corretamente se você carrega o script em um cluster como um arquivo binário.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o pacote Ferramentas do Data Lake para Visual Studio para conectar-se aos clusters HDInsight pelo Visual Studio. Você também aprendeu a executar uma consulta do Hive. Para obter mais informações, consulte estes artigos:

* [Executar consultas do Apache Hive usando ferramentas do Data Lake para Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Usar o Hive do Hadoop no HDInsight](hdinsight-use-hive.md)
* [Introdução ao uso do Apache Hadoop no HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Enviar trabalhos do Apache Hadoop no HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analisar dados do Twitter com Apache Hadoop no HDInsight](../hdinsight-analyze-twitter-data.md)

