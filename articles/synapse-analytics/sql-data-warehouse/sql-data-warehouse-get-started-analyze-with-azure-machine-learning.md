---
title: Analisar dados com o Azure Machine Learning
description: Use o Azure Machine Learning para compilar um modelo de aprendizado de máquina preditivo com base nos dados armazenados no Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 76a154d3a137017f374247308a3980d598698246
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678652"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analisar dados com o Azure Machine Learning

Este tutorial usa o [Designer de Azure Machine Learning](../../machine-learning/concept-designer.md) para criar um modelo de aprendizado de máquina preditiva. O modelo é baseado nos dados armazenados no Azure Synapse. O cenário para o tutorial é prever se é provável que um cliente compre uma bicicleta ou não para que a Adventure Works, a loja de bicicletas, possa criar uma campanha de marketing direcionada.

## <a name="prerequisites"></a>Pré-requisitos

Para realizar este tutorial, você precisa:

* um pool do SQL pré-carregado com dados de exemplo de AdventureWorksDW. Para provisionar esse pool SQL, consulte [criar um pool SQL](create-data-warehouse-portal.md) e optar por carregar os dados de exemplo. Se você já tiver um data warehouse, mas não tiver dados de exemplo, poderá [carregar dados de exemplo manualmente](./load-data-from-azure-blob-storage-using-copy.md).
* um espaço de trabalho do Azure Machine Learning. Siga [este tutorial](../../machine-learning/how-to-manage-workspace.md) para criar um novo.

## <a name="get-the-data"></a>Obter os dados

Os dados usados estão na exibição dbo. vTargetMail no AdventureWorksDW. Para usar o datastore neste tutorial, os dados são exportados primeiro para Azure Data Lake Storage conta, uma vez que o Azure Synapse não oferece suporte atualmente a DataSets. Azure Data Factory pode ser usado para exportar dados do data warehouse para Azure Data Lake Storage usando a [atividade de cópia](../../data-factory/copy-activity-overview.md). Use a seguinte consulta para importação:

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Depois que os dados estiverem disponíveis no Azure Data Lake Storage, os repositórios de dados no Azure Machine Learning serão usados para [se conectar aos serviços de armazenamento do Azure](../../machine-learning/how-to-access-data.md). Siga as etapas abaixo para criar um repositório de armazenamento e um conjunto de uma correspondente:

1. Inicie o Azure Machine Learning Studio de portal do Azure ou entre no [Azure Machine Learning Studio](https://ml.azure.com/).

1. Clique em **repositórios de armazenamento** no painel esquerdo na seção **gerenciar** e, em seguida, clique em **novo repositório de armazenamento**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Captura de tela do painel esquerdo da interface Azure Machine Learning":::

1. Forneça um nome para o repositório de armazenamento, selecione o tipo como ' armazenamento de BLOBs do Azure ', forneça o local e as credenciais. Em seguida, clique em **Criar**.

1. Em seguida, clique em **conjuntos** de itens no painel esquerdo na seção **ativos** . Selecione **criar conjunto** de um com a opção **do repositório de armazenamento**.

1. Especifique o nome do conjunto de e selecione o tipo a ser **tabular**. Em seguida, clique em **Avançar** para avançar.

1. Na **seção selecionar ou criar um repositório de armazenamento**, selecione a opção **repositório de armazenamento criado anteriormente**. Selecione o repositório de armazenamento que foi criado anteriormente. Clique em avançar e especifique as configurações de caminho e arquivo. Certifique-se de especificar o cabeçalho da coluna se os arquivos contiverem um.

1. Por fim, clique em **criar** para criar o conjunto de um.

## <a name="configure-designer-experiment"></a>Configurar experimento do designer

Em seguida, siga as etapas abaixo para a configuração do designer:

1. Clique na guia **Designer** no painel esquerdo na seção **autor** .

1. Selecione **módulos pré-criados fáceis de usar** para criar um novo pipeline.

1. No painel configurações à direita, especifique o nome do pipeline.

1. Além disso, selecione um cluster de computação de destino para todo o teste no botão configurações para um cluster provisionado anteriormente. Feche o painel Configurações.

## <a name="import-the-data"></a>Importar os dados

1. Selecione os **conjuntos** de subtab no painel esquerdo abaixo da caixa de pesquisa.

1. Arraste o conjunto de um que você criou anteriormente para a tela.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="Captura de tela do módulo do conjunto de um no Canvas.":::

## <a name="clean-the-data"></a>Limpar os dados

Para limpar os dados, remova as colunas que não são relevantes para o modelo. Siga as etapas abaixo:

1. Selecione os **módulos** subtab no painel esquerdo.

1. Arraste o módulo **Selecionar colunas no conjunto de dados** em **Transformação de Dados < Manipulação** para a tela. Conecte este módulo ao módulo **DataSet** .

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="Captura de tela do módulo seleção de coluna na tela." lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. Clique no módulo para abrir o painel Propriedades. Clique em Editar coluna para especificar quais colunas você deseja descartar.

1. Exclua duas colunas: CustomerAlternateKey e GeographyKey. Clique em **Salvar**

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="Captura de tela mostrando colunas que são removidas.":::

## <a name="build-the-model"></a>Criar o modelo

Os dados são divididos em 80-20:80% para treinar um modelo de aprendizado de máquina e 20% para testar o modelo. Algoritmos de "duas classes" são usados nesse problema de classificação binária.

1. Arraste o módulo **dividir dados** para a tela.

1. No painel Propriedades, insira 0,8 para a **fração de linhas no primeiro conjunto de registros de saída**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="Captura de tela mostrando a taxa de divisão de 0,8.":::

1. Arraste o módulo **Árvore de Decisão Aumentada de duas classes** na tela.

1. Arraste o módulo **treinar modelo** até a tela. Especifique entradas conectando-o à **árvore de decisão aumentada de duas classes** (algoritmo ml) e **dividir dados** (dados para treinar o algoritmo).

1. Para modelo de modelo de treinamento, na opção de **coluna de rótulo** no painel Propriedades, selecione Editar coluna. Selecione a coluna **BikeBuyer** como a coluna a prever e selecione **salvar**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="Captura de tela mostrando a coluna de rótulo, BikeBuyer, selecionada.":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="Captura de tela mostrando o módulo modelo de treinamento conectado a Two-Class árvore de decisão aumentada e módulos de dados divididos.":::

## <a name="score-the-model"></a>Pontuar o modelo

Agora, teste como o modelo é executado nos dados de teste. Dois algoritmos diferentes serão comparados para ver qual deles tem um desempenho melhor. Siga as etapas abaixo:

1. Arraste o módulo **Modelo de Pontuação** para a tela e conecte-o aos módulos **Modelo de Treinamento** e **Dados de Divisão**.

1. Arraste a **perceptron média de Bayes de duas classes** para a tela do experimento. Você vai comparar a forma como esse algoritmo é executado em comparação com a árvore de decisão aumentada Two-Class.

1. Copie e cole os módulos **modelo de treinamento** e **modelo de Pontuação** na tela.

1. Arraste o módulo **Avaliar Modelo** na tela para comparar os dois algoritmos.

1. Clique em **Enviar** para configurar a execução do pipeline.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="Captura de tela de todos os módulos restantes no Canvas." lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. Quando a execução for concluída, clique com o botão direito do mouse no módulo **modelo** de avaliação e clique em **Visualizar resultados da avaliação**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="Captura de tela dos resultados.":::

As métricas fornecidas são a curva ROC, o diagrama de comparação de precisão e recolhimento e a curva de comparação de precisão. Examine essas métricas para ver que o primeiro modelo foi executado melhor do que o segundo. Para ver o que o primeiro modelo prevê, clique com o botão direito do mouse no módulo modelo de Pontuação e clique em Visualizar conjunto de linha de conjuntos de marcação para visualizar os resultados previstos.

Você verá mais duas colunas adicionadas ao seu conjunto de testes.

* Probabilidades Pontuadas: a probabilidade de que um cliente é um comprador de bicicleta.
* Rótulos Pontuados: a classificação feita pelo modelo – comprador de bicicleta (1) ou não (0). Esse limite de probabilidade para a rotulagem é definido como 50% e pode ser ajustado.

Compare a coluna BikeBuyer (real) com os rótulos pontuados (previsão) para ver como o modelo foi bem executado. Em seguida, você pode usar esse modelo para fazer previsões para novos clientes. Você pode [publicar esse modelo como um serviço Web](../../machine-learning/tutorial-designer-automobile-price-deploy.md) ou gravar resultados de volta para o Azure Synapse.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Azure Machine Learning, consulte [introdução ao Machine Learning no Azure](../../machine-learning/overview-what-is-azure-ml.md).

Saiba mais sobre Pontuação interna na data warehouse, [aqui](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true).