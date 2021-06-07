---
title: 'Tutorial: Analisar dados do Apache Spark do Azure HDInsight com o Power BI'
description: Tutorial - Use o Microsoft Power BI para visualizar dados armazenados do Apache Spark em clusters HDInsight
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 3e0632b2ad1ac237d8899e9d3bdc7f1d3350fa76
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057925"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Tutorial: Analisar dados do Apache Spark usando o Power BI no HDInsight

Neste tutorial, você aprende a usar o Microsoft Power BI para visualizar dados em um cluster do Apache Spark no Azure HDInsight.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Visualizar dados de Spark usando o Power BI

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua o artigo [Tutorial: Carregar dados e executar consultas em um cluster Apache Spark no Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Opcional: [Assinatura de avaliação do Power BI](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Verificar os dados

O [Jupyter Notebook](https://jupyter.org/) que você criou no [tutorial anterior](apache-spark-load-data-run-query.md) inclui código para criar uma tabela `hvac`. Esta tabela é baseada no arquivo CSV disponível em todos os clusters do Spark do HDInsight em `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Use o procedimento a seguir para verificar os dados.

1. Do Jupyter Notebook, cole o código a seguir e pressione **SHIFT + ENTER**. O código verifica a existência das tabelas.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    A saída se parece com isso:

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-show-tables.png" alt-text="Mostrar tabelas no Spark" border="true":::

    Se você fechou o bloco de anotações antes de iniciar este tutorial, `hvactemptable` é limpo e, portanto, não é incluído na saída.  Somente as tabelas do Hive que são armazenadas no metastore (indicado por **False** na coluna **isTemporary**) podem ser acessadas por meio das ferramentas de BI. Neste tutorial, você se conecta à tabela **hvac** que você criou.

2. Cole o código a seguir em uma célula vazia e pressione **SHIFT+ENTER**. O código verifica os dados na tabela.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    A saída se parece com isso:

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-select-limit.png" alt-text="Mostrar linhas da tabela hvac no Spark" border="true":::

3. No menu **Arquivo** do notebook, selecione **Fechar e Interromper**. Feche o bloco de anotações para liberar os recursos.

## <a name="visualize-the-data"></a>Visualizar os dados

Nesta seção, use o Power BI para criar painéis, relatórios e visualizações a partir dos dados do cluster Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop

As primeiras etapas ao trabalhar com Spark são conectar-se ao cluster no Power BI Desktop, carregar dados do cluster e criar uma visualização básica com base nesses dados.

1. Abra o Power BI Desktop. Feche a tela inicial de inicialização, se estiver aberta.

2. Na guia **Página Inicial**, navegue até **Obter Dados** > **Mais..** .

    :::image type="content" source="./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png " alt-text="Obter dados para o Power BI Desktop do HDInsight Apache Spark" border="true":::er="true":::

3. Insira `Spark` na caixa de pesquisa, selecione **Azure HDInsight Spark** e, em seguida, selecione **Conectar**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png " alt-text="Obter dados para o Power BI do Apache Spark BI" border="true":::er="true":::

4. Insira a URL do cluster (no formato `mysparkcluster.azurehdinsight.net`) na caixa de texto **Servidor**.

5. No **modo de Conectividade de dados**, selecione **DirectQuery**. Depois, selecione **OK**.

    Você pode usar qualquer um dos modos de conectividade de dados com o Spark. Se você usar o DirectQuery, as alterações serão refletidas em relatórios sem atualizar todo o conjunto de dados. Se você importar dados, você precisará atualizar o conjunto de dados para ver as alterações. Para obter mais informações sobre como e quando usar o DirectQuery, consulte [Usando DirectQuery no Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Insira as informações de conta de logon do HDInsight e, em seguida, selecione **Conectar**. O nome da conta padrão é *admin*.

7. Selecione a tabela `hvac`, aguarde para ver uma visualização dos dados e selecione **Carregar**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png " alt-text="Nome de usuário e senha do cluster Spark" border="true":::d" border="true":::

    O Power BI Desktop agora tem todas as informações necessárias para se conectar ao cluster do Spark e carregar os dados da tabela `hvac`. A tabela e as respectivas colunas são exibidas no painel **Campos**.

8. Visualize a variação entre a temperatura almejada e a temperatura real para cada compilação:

    1. No painel **VISUALIZAÇÕES**, selecione **Gráfico de Área**.

    2. Arraste o campo **BuildingID** para **Eixo** e arraste os campos **ActualTemp** e **TargetTemp** para **Valor**.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png " alt-text="adicionar colunas de valor" border="true":::t="adicionar colunas de valor" border="true":::

        O diagrama se parecerá com isso:

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png " alt-text="soma do grafo de área" border="true":::lt-text="soma do grafo de área" border="true":::

        Por padrão, a visualização mostra a soma de **ActualTemp** e **TargetTemp**. Selecione a seta para baixo ao lado de **ActualTemp** e **TragetTemp** no painel de visualizações, você pode ver que **Sum** está selecionado.

    3. Selecione as setas para baixo ao lado de **ActualTemp** e **TragetTemp** no painel de visualizações, selecione **Average** para obter uma média de temperatura real e temperatura alvo para cada compilação.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png " alt-text="média de valores" border="true":::t="média de valores" border="true":::

        A sua visualização de dados deve ser semelhante àquela que aparece na captura de tela. Mova o cursor sobre a visualização para obter dicas de ferramenta com dados relevantes.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png " alt-text="grafo de área" border="true":::.png " alt-text="grafo de área" border="true":::

9. Navegue até **Arquivo** > **Salvar**, digite o nome `BuildingTemperature` para o arquivo e selecione **Salvar**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicar o relatório para o serviço do Power BI (opcional)

O serviço do Power BI permite que você compartilhe os relatórios e painéis pela sua organização. Nesta seção, primeiro você publica o conjunto de dados e o relatório. Em seguida, você pode fixar o relatório em um painel. Os dashboards normalmente são usados para se concentrar em um subconjunto de dados em um relatório. Você tem apenas uma visualização em seu relatório, mas ainda assim é útil percorrer as etapas.

1. Abra o Power BI Desktop.

1. Na guia **Página Inicial**, selecione **Publicar**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png " alt-text="Publicar do Power BI Desktop" border="true"::: Desktop" border="true":::

1. Selecione um workspace no qual o conjunto de dados e o relatório serão publicados e, em seguida, escolha **Selecionar**. Na imagem a seguir, o **Meu Workspace** padrão está selecionado.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png " alt-text="Selecione o workspace no qual publicar o conjunto de dados e o relatório" border="true":::ue":::

1. Depois que a publicação for bem-sucedida, selecione **Abrir 'BuildingTemperature.pbix' no Power BI**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png " alt-text="Publicação bem-sucedida, clique para inserir as credenciais" border="true":::er="true":::

1. No serviço do Power BI, selecione **Inserir credenciais**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png " alt-text="Inserir credenciais no serviço do Power BI" border="true":::" border="true":::

1. Selecione **Editar credenciais**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png " alt-text="Editar credenciais no serviço do Power BI" border="true":::e" border="true":::

1. Insira os dados da conta de logon do HDInsight e, em seguida, selecione **Entrar**. O nome da conta padrão é *admin*.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png " alt-text="Entrar no cluster Spark" border="true":::cluster Spark" border="true":::

1. No painel esquerdo, acesse **Workspaces** > **Meu Workspace** > **RELATÓRIOS** e, em seguida, selecione **BuildingTemperature**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png " alt-text="Relatório listado em relatórios no painel esquerdo" border="true":::order="true":::

    Você também verá a **BuildingTemperature** listada sob **DATASETS** no painel à esquerda.

    O visual criado no Power BI Desktop agora está disponível no serviço do Power BI.

1. Posicione o cursor sobre a visualização e, em seguida, selecione o ícone de pino no canto superior direito.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png " alt-text="Relatório no serviço do Power BI" border="true":::service" border="true":::

1. Selecione "Novo dashboard", insira o nome `Building temperature` e, em seguida, selecione **Fixar**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png " alt-text="Fixar no novo painel" border="true"::: no novo painel" border="true":::

1. No relatório, selecione **Ir para o dashboard**.

O visual é fixado no painel – você pode adicionar outros elementos visuais no relatório e fixá-los no mesmo painel. Para saber mais sobre relatórios e painéis, confira [Relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) e [Painéis no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Limpar os recursos

Após concluir o tutorial, convém excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Para excluir um cluster, confira [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md) (Excluir um cluster HDInsight usando o navegador, o PowerShell ou a CLI do Azure).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o Microsoft Power BI para visualizar dados em um cluster do Apache Spark no Azure HDInsight. Avance para o próximo artigo para ver que você pode criar uma aplicativo de aprendizado de máquina.

> [!div class="nextstepaction"]
> [Criar um aplicativo de aprendizado de máquina](./apache-spark-ipython-notebook-machine-learning.md)