---
title: 'Tutorial: Criar um notebook no Azure Cosmos DB para analisar e visualizar os dados'
description: 'Tutorial: Saiba como usar notebooks Jupyter internos para importar dados para o Azure Cosmos DB, analisar os dados e visualizar a saída.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: beb58922172a045242f4f9bcaf647b3cfc8b5551
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380790"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Tutorial: Criar um notebook no Azure Cosmos DB para analisar e visualizar os dados
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve como usar notebooks Jupyter internos para importar dados de varejo de exemplo para o Azure Cosmos DB. Você verá como usar os comandos magic do SQL e do Azure Cosmos DB para executar consultas, analisar os dados e visualizar os resultados.

## <a name="prerequisites"></a>Pré-requisitos

* [Habilitar notebooks em uma conta do Azure Cosmos](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Criar os recursos e importar dados
 
Nesta seção, você criará o banco de dados do Azure Cosmos e o contêiner e importará os dados de varejo para o contêiner.

1. Navegue até a conta do Azure Cosmos e abra o **Data Explorer.**

1. Acesse a guia **Notebooks**, selecione `…` ao lado de **Meus Notebooks** e crie um **Notebook**. Selecione **Python 3** como o kernel padrão.

   :::image type="content" source="./media/create-notebook-visualize-data/create-new-notebook.png" alt-text="Crie um notebook":::

1. Depois que um notebook for criado, será possível renomeá-lo para algo como **VisualizeRetailData.ipynb.**

1. Em seguida, você criará um banco de dados chamado "RetailDemo" e um contêiner chamado "WebsiteData" para armazenar os dados de varejo. Você pode usar /CartID como a chave de partição. Copie e cole o código a seguir para uma nova célula em seu notebook e o execute:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Para executar uma célula, selecione `Shift + Enter` ou selecione a célula e escolha a opção **Executar Célula Ativa** na barra de navegação do Data Explorer.

   :::image type="content" source="./media/create-notebook-visualize-data/run-active-cell.png" alt-text="Executar a célula ativa":::

   O banco de dados e o contêiner são criados em sua conta atual do Azure Cosmos. O contêiner é provisionado com 400 RU/s. Você verá a saída a seguir depois que o banco de dados e o contêiner forem criados. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   Também é possível atualizar a guia **Dados** e ver os recursos recém-criados:

   :::image type="content" source="media/create-notebook-visualize-data/refresh-data-tab.png" alt-text="Atualize a guia dados para ver o novo contêiner":::

1. Em seguida, você importará os dados de varejo de exemplo para o contêiner do Azure Cosmos. Veja o formato de um item dos dados de varejo:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   Para a finalidade do tutorial, os dados de varejo de exemplo são armazenados no Armazenamento de Blobs do Azure. É possível importá-los para o contêiner do Azure Cosmos passando o código a seguir para uma nova célula. É possível confirmar que os dados foram importados com êxito executando uma consulta para selecionar o número de itens.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Quando você executa a consulta anterior, ela retorna a seguinte saída:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Obtenha seus dados para um DataFrame

Antes de executar consultas para analisar os dados, é possível ler os dados do contêiner para um [DataFrame do Pandas](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) para análise. Use o seguinte comando magic do SQL para ler os dados em um DataFrame:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Para saber mais, confira o artigo [comandos e recursos de notebook interno no Azure Cosmos DB](use-python-notebook-features-and-commands.md). Você executará a consulta- `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c`. Os resultados serão salvos em um DataFrame do Pandas chamado df_cosmos. Cole o seguinte comando em uma nova célula de notebook e o execute:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

Em uma nova célula de notebook, execute o código a seguir para ler os 10 primeiros itens da saída:

```python
# See a sample of the result
df_cosmos.head(10)
```

:::image type="content" source="./media/create-notebook-visualize-data/run-query-get-top10-items.png" alt-text="Executar consulta para obter os 10 primeiros itens":::

## <a name="run-queries-and-analyze-your-data"></a>Executar as consultas e analisar seus dados

Nesta seção, você executará algumas consultas nos dados recuperados.

* **Consulta1:** execute uma consulta Agrupar por no DataFrame para obter a soma da receita de vendas total para cada país/região e exiba cinco itens nos resultados. Em uma nova célula do notebook, execute o seguinte código:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/total-sales-revenue-output.png" alt-text="Saída da receita de vendas total":::

* **Consulta2:** para obter uma lista dos cinco principais itens comprados, abra uma nova célula de notebook e execute o seguinte código:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/top5-purchased-items.png" alt-text="Cinco itens mais comprados":::

## <a name="visualize-your-data"></a>Visualize seus dados  

1. Agora que temos nossos dados na receita do contêiner do Azure Cosmos, você pode visualizar seus dados com uma biblioteca de visualização da sua escolha. Neste tutorial, usaremos a biblioteca Bokeh. Abra uma nova célula de notebook e execute o seguinte código para instalar a biblioteca Bokeh. Depois que todos os requisitos forem atendidos, a biblioteca será instalada.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Em seguida, prepare-se para plotar os dados em um mapa. Ingresse os dados no Azure Cosmos DB com informações de país/região localizadas no Armazenamento de Blobs do Azure e converta o resultado em formato GeoJSON. Copie o seguinte código para uma nova célula de notebook e o execute.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country/region information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries/regions dataframe with our data in Azure Cosmos DB, joining on country/region code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Visualize a receita de vendas de diferentes países/regiões em um mapa mundial executando o seguinte código em uma nova célula de notebook:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   A saída exibe o mapa mundial com diferentes cores. As cores mais escuras para mais claras representam os países/regiões com maior renda para os países com menor renda.

   :::image type="content" source="./media/create-notebook-visualize-data/countries-revenue-map-visualization.png" alt-text="Visualização do mapa da receita de países/regiões":::

1. Vamos ver outro caso de visualização de dados. O contêiner WebsiteData tem um registro de usuários que exibiram um item, o adicionaram ao carrinho e o compraram. Vamos plotar a taxa de conversão de itens adquiridos. Execute o código a seguir em uma nova célula para visualizar a taxa de conversão para cada item:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png" alt-text="Visualizar taxa de conversão de compra":::

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre comandos de notebook do Python, confira o artigo [como usar comandos e recursos de notebook interno no Azure Cosmos DB](use-python-notebook-features-and-commands.md).
