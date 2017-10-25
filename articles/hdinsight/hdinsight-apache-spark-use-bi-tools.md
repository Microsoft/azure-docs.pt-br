---
title: "Spark BI usando ferramentas de visualização de dados no Azure HDInsight | Microsoft Docs"
description: "Usar ferramentas de visualização de dados para análise usando Apache Spark BI em clusters HDInsight"
keywords: "apache spark bi, spark bi, visualização de dados spark, spark business intelligence"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: nitinme
ms.openlocfilehash: 869a000909813e607620c47ef802b4043e37dfa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI usando ferramentas de visualização de dados com o Azure HDInsight

Saiba como usar ferramentas de visualização de dados, como o Power BI e o Tableau para analisar um conjunto de dados brutos de exemplo usando o Apache Spark BI em clusters HDInsight.

> [!NOTE]
> A conectividade com as ferramentas BI descritas neste artigo não tem suporte no Spark 2.1 no modo Preview do Azure HDInsight 3.6. Somente as versões 1.6 e 2.0 do Spark (HDInsight 3.4 e 3.5, respectivamente) têm suporte.
>

Este tutorial também está disponível como um notebook Jupyter em um cluster do Spark HDInsight. A experiência de bloco de anotações permite executar os trechos de código Python no próprio bloco de anotações. Para executar o tutorial em um bloco de anotações, crie um cluster Spark, inicie um bloco de anotações do Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) e execute o bloco de anotações **Usar ferramentas de BI com Apache Spark no HDInsight.ipynb** na pasta **Python**.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="hivetable"></a>Preparar dados para visualização de dados o Spark

Nesta seção, usamos o bloco de anotações do [Jupyter](https://jupyter.org) de um cluster Spark no HDInsight para executar trabalhos que processam os dados brutos de exemplo e os salvam como uma tabela. Os dados de exemplo são um arquivo .csv (hvac.csv) disponível em todos os clusters por padrão. Depois que os dados são salvos como uma tabela, na próxima seção, usamos ferramentas de BI para conectar à tabela e realizar visualizações de dados.

> [!NOTE]
> Se executar as etapas neste artigo depois de concluir as instruções em [Executar consultas interativas em um cluster HDInsight Spark](hdinsight-apache-spark-load-data-run-query.md), você poderá pular para a Etapa 8 abaixo.
>

1. No [portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   

2. Na folha do cluster Spark, clique em **Painel do Cluster** e em **Notebook Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

   > [!NOTE]
   > Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Crie um notebook. Clique em **Novo** e em **PySpark**.

    ![Criar um notebook Jupyter para o Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/create-jupyter-notebook-for-spark-bi.png "Criar um notebook Jupyter para o Apache Spark BI")

4. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.

    ![Fornecer um nome para o notebook do Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/jupyter-notebook-name-for-spark-bi.png "Fornecer um nome para o notebook do Apache Spark BI")

5. Por ter criado um notebook usando o kernel PySpark, não será necessário criar nenhum contexto explicitamente. Os contextos do Spark e do Hive são criados automaticamente para você ao executar a primeira célula do código. Você pode começar importando os tipos obrigatórios para este cenário. Para fazer isso, coloque o cursor na célula e pressione **SHIFT + ENTER**.

        from pyspark.sql import *

6. Carregar dados de exemplo em uma tabela temporária. Quando você cria um cluster Spark no HDInsight, o arquivo de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associada em **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Em uma célula vazia, cole o trecho a seguir e pressione **SHIFT+ENTER**. Esse trecho registra os dados em uma tabela chamada **hvac**.

        # Create an RDD from sample data
        hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))

        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

7. Verifique se a tabela foi criada com êxito. Você pode usar a mágica do `%%sql` para executar as consultas do Hive diretamente. Para saber mais sobre a palavra mágica `%%sql`, e outras palavras mágicas disponíveis com o kernel PySpark, confira [Kernels disponíveis em notebooks Jupyter com clusters HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SHOW TABLES

    Você vê uma saída como a mostrada abaixo:

        +---------------+-------------+
        |tableName      |isTemporary  |
        +---------------+-------------+
        |hvactemptable  |true        |
        |hivesampletable|false        |
        |hvac           |false        |
        +---------------+-------------+

    Somente as tabelas que têm falso na coluna **isTemporary** são tabelas hive que ficam armazenadas no metastore e podem ser acessadas por meio das ferramentas de BI. Neste tutorial, vamos nos conectar à tabela **hvac** criada.

8. Verifique se a tabela contém os dados pretendidos. Em uma célula vazia no bloco de anotações, copie o seguinte trecho e pressione **SHIFT+ENTER**.

        %%sql
        SELECT * FROM hvac LIMIT 10

9. Feche o bloco de anotações para liberar os recursos. Para isso, no menu **Arquivo** do bloco de anotações, clique em **Fechar e Interromper**.

## <a name="powerbi"></a>Usar o Power BI para visualização de dados do Spark

> [!NOTE]
> Esta seção é aplicável somente ao Spark 1.6 no HDInsight 3.4 e ao Spark 2.0 no HDInsight 3.5.
>
>

Depois de salvar os dados como uma tabela, você poderá usar o Power BI para conectar aos dados e visualizá-los para criar relatórios, painéis, etc.

1. Verifique se que você tem acesso ao Power BI. Você pode obter uma assinatura gratuita de visualização do Power BI em [http://www.powerbi.com/](http://www.powerbi.com/).

2. Entre no [Power BI](http://www.powerbi.com/).

3. Na parte inferior do painel esquerdo, clique em **Obter Dados**.

4. Na página **Obter Dados**, em **Importar ou Conectar-se a Dados**, para **Bancos de Dados**, clique em **Obter**.

    ![Colocar dados no Power BI para o Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Colocar dados no Power BI para o Apache Spark BI")

5. Na próxima tela, clique em **Spark no Azure HDInsight** e em **Conectar**. Quando solicitado, insira a URL do cluster (`mysparkcluster.azurehdinsight.net`) e as credenciais para se conectar a ele

    ![Conectar-se ao Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "Conectar-se ao Apache Spark BI")

    Depois que a conexão é estabelecida, o Power BI inicia a importação de dados do cluster Spark no HDInsight.

6. O Power BI importa os dados e adiciona um conjunto de dados **Spark** sob o título **Conjuntos de dados**. Clique no conjunto de dados para abrir uma nova planilha e visualizar os dados. Também é possível salvar a planilha como um relatório. Para salvar uma planilha, no menu **Arquivo**, clique em **Salvar**.

    ![Bloco do Apache Spark BI no painel do Power BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-tile-dashboard.png "Bloco do Apache Spark BI no painel do Power BI")
7. Observe que a lista **Campos**, à direita, lista a tabela **hvac** que você criou anteriormente. Expanda a tabela para ver seus campos, conforme você definiu anteriormente no bloco de anotações.

      ![Listar tabelas no painel do Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "Listar tabelas no painel do Apache Spark BI")

8. Crie uma visualização para mostrar a variação entre a temperatura almejada e a temperatura real para cada edifício. Para visualizar os dados, escolha **Gráfico de Áreas** (mostrado na caixa vermelha). Para definir o eixo, arraste e solte o campo **BuildingID** em **Eixo** e os campos **ActualTemp**/**TargetTemp** em **Valor**.

    ![Criar visualizações de dados Spark usando o Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Criar visualizações de dados Spark usando o Apache Spark BI")

9. Por padrão, a visualização mostra a soma de **ActualTemp** e **TargetTemp**. Para os campos, na lista suspensa, selecione **Média** para obter uma média das temperaturas almejada e real para ambos os prédios.

    ![Criar visualizações de dados Spark usando o Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Criar visualizações de dados Spark usando o Apache Spark BI")

10. A visualização de dados deve ser semelhante à que aparece na captura de tela. Mova o cursor sobre a visualização para obter dicas de ferramenta com dados relevantes.

    ![Criar visualizações de dados Spark usando o Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Criar visualizações de dados Spark usando o Apache Spark BI")

11. Clique em **Salvar** no menu superior e forneça um nome de relatório. Você também pode fixar o visual. Quando você fixa uma visualização, ela fica armazenada no painel para permitir o controle do valor mais recente rapidamente.

   Você pode adicionar quantas visualizações desejar para o mesmo conjunto de dados e fixá-las no painel para um instantâneo dos dados. Além disso, os clusters do Spark no HDInsight são conectados ao Power BI com conexão direta. Isso faz com que o Power BI tenha sempre os dados mais atualizadas de seu cluster para que você não precise agendar atualizações para o conjunto de dados.

## <a name="tableau"></a>Usar o Tableau Desktop para visualização de dados do Spark

> [!NOTE]
> Esta seção é aplicável somente para clusters do Spark 1.5.2 criados no Azure HDInsight.
>
>

1. Instale o [Tableau Desktop](http://www.tableau.com/products/desktop) no computador em que você está executando este tutorial do Apache Spark BI.

2. Verifique se o computador também tem o driver ODBC do Microsoft Spark instalado. Você pode instalar o driver clicando [aqui](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Inicie o Tableau Desktop. No painel esquerdo, na lista de servidores aos quais se conectar, clique em **Spark SQL**. Se Spark SQL não estiver listado por padrão no painel esquerdo, você poderá encontrá-lo ao clicar em **Mais Servidores**.
2. Na caixa de diálogo de conexão do Spark SQL, forneça os valores conforme mostrado na captura de tela e clique em **OK**.

    ![Conectar-se a um cluster do Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Conectar-se a um cluster do Apache Spark BI")

    O menu suspenso de autenticação listará o **Serviço Microsoft Azure HDInsight** como uma opção somente se você tiver instalado o [Driver ODBC do Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) no computador.
3. Na próxima tela, no menu suspenso **Esquema**, clique no ícone **Localizar** e clique em **padrão**.

    ![Localizar o esquema para o Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Localizar o esquema para o Apache Spark BI")
4. Para o campo **Tabela**, clique no ícone **Localizar** novamente para listar todas as tabelas Hive disponíveis no cluster. Você deve ver a tabela **hvac** criada anteriormente usando o bloco de anotações.

    ![Localizar tabela para o Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Localizar a tabela para o Apache Spark BI")
5. Arraste e solte a tabela para a caixa superior à direita. O Tableau importa os dados e exibe o esquema conforme destacado pela caixa vermelha.

    ![Adicionar tabelas ao Tableau para o Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Adicionar tabelas ao Tableau para o Apache Spark BI")
6. Clique na guia **Sheet1** na parte inferior esquerda. Faça uma visualização que mostre as temperaturas almejada e real para todos os edifícios para cada data. Arraste **Data** e **ID do Prédio** para **Colunas** e **Temp. real**/**Temp. almejada** para **Linhas**. Em **Marcas**, selecione **Área** para usar uma mapa de área para a visualização de dados do Spark.

     ![Adicionar campos para visualização de dados do Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Adicionar campos para visualização de dados do Spark")
7. Por padrão, os campos de temperatura são mostrados em agregado. Se você desejar mostrar a temperatura média em vez disso, pode fazer isso na lista suspensa, conforme mostrado abaixo.

    ![Obter a temperatura média para visualização de dados do Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Obter a temperatura média para visualização de dados do Spark")

8. Você também pode sobrepor um mapa de temperatura a outro para ter uma ideia melhor da diferença entre as temperaturas almejada e real. Mova o mouse para o canto do mapa da área inferior até ver a forma da alça realçada em um círculo vermelho. Arraste o mapa para o outro mapa na parte superior e solte o mouse ao ver a forma realçada no retângulo vermelho.

    ![Mesclar mapas para visualização de dados do Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Mesclar mapas para visualização de dados do Spark")

     A visualização de dados deve ser alterada conforme mostrado na captura de tela:

    ![Saída do Tableau para visualização de dados do Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Saída do Tableau para visualização de dados do Spark")
9. Clique em **Salvar** para salvar a planilha. Você pode criar painéis e adicionar uma ou mais planilhas a ele.

## <a name="next-steps"></a>Próximas etapas

Até agora, você aprendeu como criar um cluster, criar quadros de dados do Spark para consultar dados e, em seguida, acessar esses dados de ferramentas de BI. Agora você pode examinar as instruções sobre como gerenciar os recursos de cluster e depurar os trabalhos que são executados em um cluster HDInsight Spark.

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

