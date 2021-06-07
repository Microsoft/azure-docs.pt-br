---
title: Power BI e pool de SQL sem servidor para analisar Azure Cosmos DB dados com o link Synapse
description: Saiba como criar um banco de dados do pool SQL sem servidor e exibições sobre o link do Synapse para Azure Cosmos DB, consultar os contêineres de Azure Cosmos DB e criar um modelo com Power BI sobre essas exibições.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: synapse-cosmos-db
ms.openlocfilehash: d84508a7629481a7138f1080c86f4a203d35894d
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626241"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-to-analyze-azure-cosmos-db-data-with-synapse-link"></a>Use Power BI e o pool de SQL Synapse sem servidor para analisar Azure Cosmos DB dados com o link do Synapse 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Neste artigo, você aprende a criar um banco de dados do pool SQL sem servidor e exibições sobre o link do Synapse para Azure Cosmos DB. Você consultará os contêineres de Azure Cosmos DB e criará um modelo com Power BI sobre essas exibições para refletir essa consulta.

Nesse cenário, você usará dados fictícios sobre as vendas de produtos de superfície em uma loja de varejo de parceiro. Você analisará a receita por loja com base na proximidade com grandes residências e o impacto de publicidade para uma semana específica. Neste artigo, você cria duas exibições chamadas **RetailSales** e **StoreDemographics** e uma consulta entre elas. Você pode obter os dados do produto de exemplo deste repositório [GitHub](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) .

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de criar os seguintes recursos antes de começar:

* [Crie uma conta de Azure Cosmos DB do tipo SQL (Core) ou MongoDB.](create-cosmosdb-resources-portal.md)

* Habilitar o link Synapse do Azure para sua [conta do cosmos do Azure](configure-synapse-link.md#enable-synapse-link)

* Crie um banco de dados dentro da conta do Azure Cosmos e dois contêineres que tenham o [repositório analítico habilitado.](configure-synapse-link.md#create-analytical-ttl)

* Carregue os dados de produtos nos contêineres de Cosmos do Azure conforme descrito neste bloco de anotações de [ingestão de dados em lotes](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) .

* [Crie um espaço de trabalho Synapse](../synapse-analytics/quickstart-create-workspace.md) chamado **SynapseLinkBI**.

* [Conecte o banco de dados Cosmos do Azure ao espaço de trabalho Synapse](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json).

## <a name="create-a-database-and-views"></a>Criar um banco de dados e exibições

No espaço de trabalho Synapse, acesse a guia **desenvolver** , selecione o **+** ícone e selecione **script SQL**.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Adicionar um script SQL ao espaço de trabalho do Synapse Analytics":::

Todo espaço de trabalho é fornecido com um ponto de extremidade SQL sem servidor. Depois de criar um script SQL, na barra de ferramentas na parte superior, conecte-se ao **interno**.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Habilitar o script SQL para usar o ponto de extremidade SQL sem servidor no espaço de trabalho":::

A criação de exibições nos bancos de dados **mestre** ou **padrão** não é recomendada nem tem suporte. Crie um novo banco de dados, chamado **RetailCosmosDB**, e um modo SQL sobre os contêineres habilitados para link Synapse. O comando a seguir mostra como criar um banco de dados:

```sql
-- Create database
Create database RetailCosmosDB
```

Em seguida, crie várias exibições em contêineres de Cosmos do Azure habilitados para o link Synapse. As exibições permitirão que você use o T-SQL para unir e consultar Azure Cosmos DB dados localizados em contêineres diferentes.  Certifique-se de selecionar o banco de dados **RetailCosmosDB** ao criar os modos de exibição.

Os scripts a seguir mostram como criar modos de exibição em cada contêiner. Para simplificar, vamos usar o recurso de [inferência de esquema automática](analytical-store-introduction.md#analytical-schema) de pools de SQL sem servidor em contêineres habilitados para link do Synapse:


### <a name="retailsales-view"></a>Exibição de RetailSales:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

Certifique-se de inserir sua região de Azure Cosmos DB e a chave primária no script SQL anterior. Todos os caracteres no nome da região devem estar em letras minúsculas, sem espaços. Ao contrário dos outros parâmetros do `OPENROWSET` comando, o parâmetro de nome do contêiner deve ser especificado sem aspas.

### <a name="storedemographics-view"></a>Exibição de StoreDemographics:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

Agora, execute o script SQL selecionando o comando **executar** .

## <a name="query-the-views"></a>Consultar as exibições

Agora que as duas exibições foram criadas, vamos definir a consulta para unir essas duas exibições da seguinte maneira:

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

Selecione **executar** que fornece a seguinte tabela como resultado:

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Resultados da consulta depois de unir as exibições StoreDemographics e RetailSales":::

## <a name="model-views-over-containers-with-power-bi"></a>Exibições de modelo em contêineres com Power BI

Em seguida, abra o Power BI área de trabalho e conecte-se ao ponto de extremidade SQL sem servidor usando as seguintes etapas:

1. Abra o aplicativo Power BI Desktop. Selecione **obter dados** e selecione **mais**.

1. Escolha **análise de Synapse do Azure (SQL DW)** na lista de opções de conexão.

1. Insira o nome do ponto de extremidade SQL onde o banco de dados está localizado. Insira `SynapseLinkBI-ondemand.sql.azuresynapse.net` dentro do campo **servidor** . Neste exemplo,  **SynapseLinkBI** é o nome do espaço de trabalho. Substitua-o se você tiver fornecido um nome diferente para seu espaço de trabalho. Selecione **consulta direta** para o modo de conectividade de dados e, em seguida, **OK**.

1. Selecione o método de autenticação preferencial, como o Azure AD.

1. Selecione o banco de dados **RetailCosmosDB** e os modos de exibição **RetailSales**, **StoreDemographics** .

1. Selecione **carregar** para carregar as duas exibições no modo consulta direta.

1. Selecione **modelo** para criar uma relação entre as duas exibições por meio da coluna **StoreID** .

1. Arraste a coluna **StoreID** da exibição **RetailSales** até a coluna **StoreID** na exibição **StoreDemographics** .

1. Selecione a relação muitos para um (*: 1) porque há várias linhas com a mesma ID de repositório na exibição **RetailSales** . **StoreDemographics** tem apenas uma linha de ID de repositório (é uma tabela de dimensões).

Agora, navegue até a janela de **relatório** e crie um relatório para comparar a importância relativa de tamanho doméstico com a receita média por loja com base na representação dispersão de receita e índice LargeHH:

1. Selecione **gráfico de dispersão**.

1. Arraste e solte **LargeHH** da exibição **StoreDemographics** para o eixo X.

1. Arraste e solte **receita** da exibição **RetailSales** no eixo Y. Selecione **média** para obter a média de vendas por produto por loja e por semana.

1. Arraste e solte o **productCode** da exibição **RetailSales** na legenda para selecionar uma linha de produto específica.
Depois de escolher essas opções, você deverá ver um grafo como a captura de tela a seguir:

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Relatório que compara a importância relativa do tamanho doméstico com a receita média por loja":::

## <a name="next-steps"></a>Próximas etapas

[Usar o T-SQL para consultar dados de Azure Cosmos DB usando o link Synapse do Azure](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

Usar o pool SQL sem servidor para [analisar os conjuntos de resultados abertos do Azure e visualizar o resultado no Azure Synapse Studio](../synapse-analytics/sql/tutorial-data-analyst.md)
