---
title: 'Tutorial: Analisar dados do Apache Spark usando o Power BI no Azure HDInsight '
description: Tutorial - Use o Microsoft Power BI para visualizar dados armazenados do Apache Spark em clusters HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/16/2019
ms.openlocfilehash: d5296fe19cef9e8881d39bd9e59eb4c40d049959
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296198"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Tutorial: Analisar dados do Apache Spark usando o Power BI no HDInsight

Neste tutorial, você aprende a usar o [Microsoft Power BI](https://powerbi.microsoft.com/) para visualizar dados em um cluster do Apache Spark no [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

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

1. A partir do notebook Jupyter, cole o código a seguir e pressione **SHIFT+ENTER**. O código verifica a existência das tabelas.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    A saída se parece com isso:

    ![Mostrar tabelas no Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Se você fechou o bloco de anotações antes de iniciar este tutorial, `hvactemptable` é limpo e, portanto, não é incluído na saída.  Somente as tabelas do Hive que são armazenadas no metastore (indicado por **False** na coluna **isTemporary**) podem ser acessadas por meio das ferramentas de BI. Neste tutorial, você se conecta à tabela **hvac** que você criou.

2. Cole o código a seguir em uma célula vazia e pressione **SHIFT+ENTER**. O código verifica os dados na tabela.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    A saída se parece com isso:

    ![Mostrar linhas da tabela hvac no Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. No menu **Arquivo** do notebook, selecione **Fechar e Interromper**. Feche o bloco de anotações para liberar os recursos.

## <a name="visualize-the-data"></a>Visualizar os dados

Nesta seção, use o Power BI para criar painéis, relatórios e visualizações a partir dos dados do cluster Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Criar um relatório no Power BI Desktop

As primeiras etapas ao trabalhar com Spark são conectar-se ao cluster no Power BI Desktop, carregar dados do cluster e criar uma visualização básica com base nesses dados.

> [!NOTE]  
> O conector demonstrado neste artigo está atualmente em visualização. Forneça qualquer comentário que você tenha por meio do site da [Comunidade do Power BI](https://community.powerbi.com/) ou [Ideias do Power BI](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Abra o Power BI Desktop. Feche a tela inicial de inicialização, se estiver aberta.

2. Na guia **Página Inicial**, navegue até **Obter Dados** > **Mais..** .

    ![Coloque os dados no Power BI Desktop do Apache Spark do HDInsight](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Coloque os dados no Power BI do Apache Spark BI")

3. Insira `Spark` na caixa de pesquisa, selecione **Azure HDInsight Spark** e, em seguida, selecione **Conectar**.

    ![Colocar dados no Power BI do Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Colocar dados no Power BI do Apache Spark BI")

4. Insira a URL do cluster (no formato `mysparkcluster.azurehdinsight.net`) na caixa de texto **Servidor**.

5. No **modo de Conectividade de dados**, selecione **DirectQuery**. Depois, selecione **OK**.

    Você pode usar qualquer um dos modos de conectividade de dados com o Spark. Se você usar o DirectQuery, as alterações serão refletidas em relatórios sem atualizar todo o conjunto de dados. Se você importar dados, você precisará atualizar o conjunto de dados para ver as alterações. Para obter mais informações sobre como e quando usar o DirectQuery, consulte [Usando DirectQuery no Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Insira as informações de conta de logon do HDInsight e, em seguida, selecione **Conectar**. O nome da conta padrão é *admin*.

7. Selecione a tabela `hvac`, aguarde para ver uma visualização dos dados e selecione **Carregar**.

    ![Nome de usuário e senha do cluster do Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nome de usuário e senha do cluster do Spark")

    O Power BI Desktop agora tem todas as informações necessárias para se conectar ao cluster do Spark e carregar os dados da tabela `hvac`. A tabela e as respectivas colunas são exibidas no painel **Campos**.

8. Visualize a variação entre a temperatura almejada e a temperatura real para cada compilação:

    1. No painel **VISUALIZAÇÕES**, selecione **Gráfico de Área**.

    2. Arraste o campo **BuildingID** para **Eixo**e arraste os campos **ActualTemp** e **TargetTemp** para **Valor**.

        ![Criar visualizações de dados Spark usando o Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Criar visualizações de dados Spark usando o Apache Spark BI")

        O diagrama se parecerá com isso:

        ![Criar visualizações de dados Spark usando o Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Criar visualizações de dados Spark usando o Apache Spark BI")

        Por padrão, a visualização mostra a soma de **ActualTemp** e **TargetTemp**. Selecione a seta para baixo ao lado de **ActualTemp** e **TragetTemp** no painel de visualizações, você pode ver que **Sum** está selecionado.

    3. Selecione as setas para baixo ao lado de **ActualTemp** e **TragetTemp** no painel de visualizações, selecione **Average** para obter uma média de temperatura real e temperatura alvo para cada compilação.

        ![Criar visualizações de dados Spark usando o Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Criar visualizações de dados Spark usando o Apache Spark BI")

        A sua visualização de dados deve ser semelhante àquela que aparece na captura de tela. Mova o cursor sobre a visualização para obter dicas de ferramenta com dados relevantes.

        ![Criar visualizações de dados Spark usando o Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Criar visualizações de dados Spark usando o Apache Spark BI")

9. Navegue até **Arquivo** > **Salvar**, digite o nome `BuildingTemperature` para o arquivo e selecione **Salvar**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicar o relatório para o serviço do Power BI (opcional)

O serviço do Power BI permite que você compartilhe os relatórios e painéis pela sua organização. Nesta seção, primeiro você publica o conjunto de dados e o relatório. Em seguida, você pode fixar o relatório em um painel. Painéis são normalmente usados para se concentrar em um subconjunto de dados em um relatório; você tem apenas uma visualização em seu relatório, mas ainda é útil percorrer as etapas.

1. Abra o Power BI Desktop.
2. A partir da guia **Início**, clique em **Publicar**.

    ![Publicar do Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publicar do Power BI Desktop")

2. Selecione um workspace para o qual publicar seu conjunto de dados e para o qual fazer relatórios e, em seguida, clique em **Selecionar**. Na imagem a seguir, o **Meu Workspace** padrão está selecionado.

    ![Selecione o workspace para o qual publicar seu conjunto de dados e para o qual fazer relatórios](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Selecione o workspace para o qual publicar seu conjunto de dados e para o qual fazer relatórios") 

3. Depois que a publicação for bem-sucedida, clique em **Abrir 'BuildingTemperature.pbix' no Power BI**.

    ![Publicação realizada com êxito, clique para inserir credenciais](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publicação realizada com êxito, clique para inserir credenciais") 

4. No serviço do Power BI, clique em **Inserir credenciais**.

    ![Inserir as credenciais no serviço do Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Inserir as credenciais no serviço do Power BI")

5. Clique em **Editar credenciais**.

    ![Editar as credenciais no serviço do Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Editar as credenciais no serviço do Power BI")

6. Insira as informações de conta de logon do HDInsight e, em seguida, clique em **Entrar**. O nome da conta padrão é *admin*.

    ![Entrar no cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Entrar no cluster Spark")

7. No painel esquerdo, acesse **Workspaces** > **Meu workspace** > **RELATÓRIOS** e, em seguida, clique em **BuildingTemperature**.

    ![Relatório listado em relatórios no painel esquerdo](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Relatório listado em relatórios no painel esquerdo")

    Você também verá a **BuildingTemperature** listada sob **DATASETS** no painel à esquerda.

    O visual criado no Power BI Desktop agora está disponível no serviço do Power BI. 

8. Focalize o cursor sobre a visualização e, em seguida, clique no ícone de fixação no canto superior direito.

    ![Relatório no serviço do Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Relatório no serviço do Power BI")

9. Selecione "Novo painel", digite o nome `Building temperature` e, em seguida, clique em **Fixar**.

    ![Fixar no novo painel](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Fixar no novo painel")

10. No relatório, clique em **Ir para o painel**. 

O visual é fixado no painel – você pode adicionar outros elementos visuais no relatório e fixá-los no mesmo painel. Para saber mais sobre relatórios e painéis, confira [Relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) e [Painéis no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Limpar recursos

Após concluir o tutorial, convém excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Para excluir um cluster, confira [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md) (Excluir um cluster HDInsight usando o navegador, o PowerShell ou a CLI do Azure).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o [Microsoft Power BI](https://powerbi.microsoft.com/) para visualizar dados em um cluster do Apache Spark no [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Avance para o próximo artigo para ver como os dados que você registrou no Spark podem ser removidos em uma ferramenta de análise de BI, assim como Power BI.

> [!div class="nextstepaction"]
> [Executar um trabalho de streaming do Apache Spark](apache-spark-eventhub-streaming.md)