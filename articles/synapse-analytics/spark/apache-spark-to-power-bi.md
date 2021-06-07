---
title: Notebooks do Azure Synapse Studio
description: Este tutorial fornece uma visão geral de como criar um dashboard do Power BI usando o Apache Spark e um pool de SQL sem servidor.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 53fb256d3a0fba1cc10cafb239ff7f7bb21ac4eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98121066"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>Tutorial: Criar um relatório do Power BI usando o Apache Spark e o Azure Synapse Analytics

As organizações geralmente precisam processar grandes volumes de dados antes de atender aos principais stakeholders da empresa. Neste tutorial, você aprenderá a aproveitar as experiências integradas do Azure Synapse Analytics para processar dados usando o Apache Spark e, mais tarde, fornecer os dados aos usuários finais por meio do Power BI e do SQL sem servidor.

## <a name="before-you-begin"></a>Antes de começar
- [Workspace do Azure Synapse Analytics](../quickstart-create-workspace.md) com uma conta de armazenamento do ADLS Gen2 configurada como o armazenamento padrão. 
- Workspace do Power BI e Power BI Desktop para visualizar os dados. Para obter detalhes, confira [Criar um workspace do Power BI](/power-bi/service-create-the-new-workspaces) e [Instalar o Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Serviço vinculado para conectar seus workspaces do Azure Synapse Analytics e do Power BI. Para obter detalhes, confira [Vínculo a um workspace do Power BI](../quickstart-power-bi.md)
- Pool do Apache Spark sem servidor no workspace do Azure Synapse Analytics. Para obter detalhes, confira [Criar um pool do Apache Spark sem servidor](../quickstart-create-apache-spark-pool-studio.md)
  
## <a name="download-and-prepare-the-data"></a>Baixar e preparar os dados
Neste exemplo, você usará o Apache Spark para realizar uma análise nos dados de gorjeta das corridas de táxi de Nova York. Os dados estão disponíveis por meio dos [Conjuntos de Dados em Aberto no Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Esse subconjunto do conjunto de dados contém informações sobre as corridas de táxi amarelo, incluindo informações sobre cada corrida, a hora de início e de término e os locais, o custo e outros atributos interessantes.

1. Execute as linhas a seguir para criar um dataframe do Spark, colando o código em uma nova célula. Isso recupera os dados por meio da API do Conjunto de Dados em Aberto no Azure. A extração de todos esses dados gera cerca de 1,5 bilhão de linhas. O exemplo de código a seguir usa start_date e end_date para aplicar um filtro que retorna um único mês de dados.
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. Usando o SQL do Apache Spark, criaremos um banco de dados chamado NycTlcTutorial. Usaremos esse banco de dados para armazenar os resultados do nosso processamento de dados.
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. Em seguida, usaremos as operações de dataframe do Spark para processar os dados. No seguinte código, executaremos as seguintes transformações:
   1. A remoção de colunas que não são necessárias.
   2. A remoção de exceções/valores incorretos por meio da filtragem.
   3. A criação de recursos, como ```tripTimeSecs``` e ```tipped```, para análise adicional.
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. Por fim, salvaremos nosso dataframe usando o método ```saveAsTable``` do Apache Spark. Isso permitirá que você consulte a mesma tabela mais tarde e conecte-se a ela usando pools de SQL sem servidor.
  ```python
     taxi_df.write.mode("overwrite").saveAsTable("NycTlcTutorial.nyctaxi")
  ```
   
## <a name="query-data-using-serverless-sql-pools"></a>Consultar dados usando pools de SQL sem servidor
O Azure Synapse Analytics permite que os diferentes mecanismos computacionais do workspace compartilhem bancos de dados e tabelas entre os pools do Apache Spark sem servidor e o pool de SQL sem servidor. Isso é ativado por meio da funcionalidade de [gerenciamento de metadados compartilhados](../metadata/overview.md) do Azure Synapse. Como resultado, os bancos de dados criados pelo Spark e as tabelas com backup do Parquet correspondentes ficam visíveis no pool de SQL sem servidor do workspace.

Para consultar sua tabela do Apache Spark usando o pool de SQL sem servidor:
   1. Depois de salvar sua tabela do Apache Spark, alterne para a guia **Dados**.
   
   2. Em **Workspaces**, localize a tabela do Apache Spark recém-criada e escolha **Novo script SQL** e **Selecionar as PRIMEIRAS 100 linhas**. 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="Consulta SQL." border="true":::

   3. Você pode continuar refinando a consulta ou, até mesmo, visualizar os resultados usando as opções de gráficos do SQL.

## <a name="connect-to-power-bi"></a>Conectar o Power BI
Em seguida, conectaremos o pool de SQL sem servidor ao workspace do Power BI. Depois de conectar seu workspace, você poderá criar relatórios do Power BI diretamente no Azure Synapse Analytics, bem como no Power BI Desktop.

>[!Note]
> Antes de começar, você precisará configurar um serviço vinculado para seu [workspace do Power BI](../quickstart-power-bi.md) e baixar o [Power BI Desktop](/power-bi/service-create-the-new-workspaces).  

Para conectar o pool de SQL sem servidor ao workspace do Power BI:

1.  Procure a guia **Conjuntos de dados do Power BI** e selecione a opção para **+ Novo Conjunto de Dados**. No prompt, baixe o arquivo .pbids do banco de dados da Análise de SQL que deseja usar como fonte de dados. 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Conjuntos de dados do Power BI." border="true":::

2.  Abra o arquivo com o Power BI Desktop para criar um conjunto de dados. Depois de abrir o arquivo, conecte-se ao banco de dados do SQL Server usando a opção **Conta Microsoft** e **Importar**. 
   

## <a name="create-a-power-bi-report"></a>Criar um relatório do Power BI
1. No Power BI Desktop, agora você pode adicionar um gráfico de **principais influenciadores** ao relatório.
   
   1. Arraste a coluna média *tipAmount* para o eixo **Analisar**.
   
   2. Arraste as colunas **weekdayString**, **tripDistance** média e **tripTimeSecs** médio para o eixo **Explicar por**. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop." border="true":::

2. Na guia Página Inicial do Power BI Desktop, selecione **Publicar** e **Salvar** as alterações. Insira um nome de arquivo e salve esse relatório no *Workspace NycTaxiTutorial*.
   
3. Além disso, você também pode criar visualizações do Power BI no seu workspace do Azure Synapse Analytics. Para fazer isso, procure a guia **Desenvolver** no workspace do Azure Synapse e abra a guia do Power BI. Nela, selecione o relatório e continue criando visualizações adicionais. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Workspace do Azure Synapse Analytics." border="true":::

Para obter mais detalhes sobre como criar um conjunto de dados por meio do SQL sem servidor e se conectar ao Power BI, acesse este tutorial sobre [como se conectar ao Power BI Desktop](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md)

## <a name="next-steps"></a>Próximas etapas
Continue aprendendo mais sobre as funcionalidades de visualização de dados no Azure Synapse Analytics acessando os seguintes documentos e tutoriais:
   - [Visualizar dados com os pools do Apache Spark sem servidor](../spark/apache-spark-data-visualization-tutorial.md)
   - [Visão geral da visualização de dados com os pools do Apache Spark](../spark/apache-spark-data-visualization.md)