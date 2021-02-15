---
title: Usar comandos e recursos de notebook integrado nos notebooks Python do Azure Cosmos DB (versão prévia)
description: Saiba como usar comandos e recursos internos para realizar operações comuns usando os notebooks do Python internos do Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: b89fcf32ed033f359b4db601e36cc69bb899944d
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165817"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-python-notebooks-preview"></a>Usar comandos e recursos de notebook integrado nos notebooks Python do Azure Cosmos DB (versão prévia)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Os notebooks Jupyter internos do Azure Cosmos DB permitem que você analise e visualize os dados do portal do Azure. Este artigo descreve como usar os recursos e comandos do notebook integrado para realizar operações comuns nos notebooks Python.

## <a name="install-a-new-package"></a>Instalar um novo pacote
Depois de habilitar o suporte para notebook nas contas do Azure Cosmos, você pode abrir um novo notebook e instalar um pacote.

Em uma nova célula de código, insira e execute o código a seguir, substituindo ``PackageToBeInstalled`` pelo pacote do Python desejado.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled --user
```
Esse pacote estará disponível para uso em qualquer notebook, no espaço de trabalho da conta do Azure Cosmos. 

> [!TIP]
> Se o seu notebook exigir um pacote personalizado, recomendamos que você adicione uma célula no notebook para instalar o pacote, pois os pacotes serão removidos se você [redefinir o espaço de trabalho](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Executar uma consulta SQL

Você pode usar o comando mágico ``%%sql`` para executar uma [consulta SQL](sql-query-getting-started.md) em qualquer contêiner em sua conta. Use a sintaxe:

```python
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Substitua ``{database_id}`` e ``{container_id}`` pelo nome do banco de dados e do contêiner em sua conta do Cosmos. Se os argumentos ``--database`` e ``--container`` não forem fornecidos, a consulta será executada no [banco de dados e no contêiner padrão](#set-default-database-for-queries).
- Você pode executar qualquer consulta SQL que seja válida no Azure Cosmos DB. O texto da consulta deve estar em uma nova linha.

Por exemplo: 
```python
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Execute ```%%sql?``` em uma célula para ver a documentação de ajuda do comando mágico SQL no notebook.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Executar uma consulta SQL e uma saída para um dataframe do Pandas

Você pode enviar os resultados de uma consulta ``%%sql`` para um [dataframe do Pandas](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame). Use a sintaxe: 

```python
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Substitua ``{database_id}`` e ``{container_id}`` pelo nome do banco de dados e do contêiner em sua conta do Cosmos. Se os argumentos ``--database`` e ``--container`` não forem fornecidos, a consulta será executada no [banco de dados e no contêiner padrão](#set-default-database-for-queries).
- Substitua ``{outputDataFrameVar}`` pelo nome da variável do dataframe que conterá os resultados.
- Você pode executar qualquer consulta SQL que seja válida no Azure Cosmos DB. O texto da consulta deve estar em uma nova linha. 

Por exemplo:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```python
df_cosmos.head(10)

    Action    ItemRevenue    Country/Region    Item
0    Viewed    9.00    Tunisia    Black Tee
1    Viewed    19.99    Antigua and Barbuda    Flannel Shirt
2    Added    3.75    Guinea-Bissau    Socks
3    Viewed    3.75    Guinea-Bissau    Socks
4    Viewed    55.00    Czech Republic    Rainjacket
5    Viewed    350.00    Iceland    Cosmos T-shirt
6    Added    19.99    Syrian Arab Republic    Button-Up Shirt
7    Viewed    19.99    Syrian Arab Republic    Button-Up Shirt
8    Viewed    33.00    Tuvalu    Red Top
9    Viewed    14.00    Cabo Verde    Flip Flop Shoes
```
## <a name="set-default-database-for-queries"></a>Definir banco de dados padrão para consultas
Você pode definir os comandos ```%%sql``` de banco de dados padrão que serão usados para o notebook. Substitua ```{database_id}``` pelo nome do seu banco de dados.

```python
%database {database_id}
```
Execute ```%database?``` em uma célula para ver a documentação no notebook.

## <a name="set-default-container-for-queries"></a>Definir contêiner padrão para consultas
Você pode definir os comandos ```%%sql``` de contêiner padrão que serão usados para o notebook. Substitua ```{container_id}``` pelo nome do contêiner.

```python
%container {container_id}
```
Execute ```%container?``` em uma célula para ver a documentação no notebook.

## <a name="upload-json-items-to-a-container"></a>Carregar itens JSON em um contêiner
Você pode usar o comando mágico ``%%upload`` para carregar dados de um arquivo JSON em um contêiner específico do Azure Cosmos. Use o comando a seguir para carregar os itens:

```python
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Substitua ``{database_id}`` e ``{container_id}`` pelo nome do banco de dados e do contêiner em sua conta do Azure Cosmos. Se os argumentos ``--database`` e ``--container`` não forem fornecidos, a consulta será executada no [banco de dados e no contêiner padrão](#set-default-database-for-queries).
- Substitua ``{url_location_of_file}`` pela localização do arquivo JSON. O arquivo deve ser uma matriz de objetos JSON válidos e deve ser possível acessá-lo pela Internet pública.

Por exemplo:

```python
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Com as estatísticas de saída, você pode calcular a RU/s efetiva usado para carregar os itens. Por exemplo, se 25.000 RUs foram consumidos em 38 segundos, a RU/s efetiva é 25.000 RUs/38 segundos = 658 RU/s.

Você pode salvar arquivos (como arquivos CSV ou JSON) no espaço de trabalho local do bloco de anotações. Recomendamos que você adicione uma célula em seu bloco de anotações para salvar arquivos. Você pode exibir esses arquivos do terminal integrado no ambiente do bloco de anotações. Você pode usar o comando "ls" para exibir os arquivos salvos. No entanto, esses arquivos serão removidos se você redefinir o espaço de trabalho. Portanto, é melhor usar o armazenamento persistente, como o GitHub ou uma conta de armazenamento, em vez do espaço de trabalho local.

## <a name="run-another-notebook-in-current-notebook"></a>Executar outro notebook no notebook atual 
Você pode usar o comando mágico ``%%run`` para executar outro notebook no espaço de trabalho do notebook atual. Use a sintaxe:

```python
%%run ./path/to/{notebookName}.ipynb
```
Substitua ``{notebookName}`` pelo nome do notebook que você deseja executar. O notebook deve estar no atual espaço de trabalho “Meus Notebooks”. 

## <a name="use-built-in-nteract-data-explorer"></a>Usar o explorador de dados interno nteract
Você pode usar o [explorador de dados interno nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) para filtrar e visualizar um dataframe. Para habilitar esse recurso, defina a opção ``pd.options.display.html.table_schema`` como ``True`` e ``pd.options.display.max_rows`` como o valor desejado (você pode definir ``pd.options.display.max_rows`` como ``None`` para mostrar todos os resultados).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
:::image type="content" source="media/use-notebook-features-and-commands/nteract-built-in-chart.png" alt-text="Explorador de dados nteract":::

## <a name="use-the-built-in-python-sdk"></a>Usar o SDK do Python interno
A versão 4 da [API do SDK do Python para SQL do Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) está instalada e incluída no ambiente de notebook da conta do Azure Cosmos.

Use a instância ``cosmos_client`` interna para executar qualquer operação do SDK. 

Por exemplo:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Consulte [Exemplos de SDK do Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> O SDK do Python interno só é compatível com contas de API do SQL (Core). Para outras APIs, você precisará [instalar o driver do Python relevante](#install-a-new-package) que corresponda à API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Criar uma instância do ``cosmos_client``
Para obter mais flexibilidade, você pode criar uma instância personalizada do ``cosmos_client`` para:

- Personalizar a [política de conexão](/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?preserve-view=true&view=azure-python-preview).
- Executar operações em uma conta do Azure Cosmos diferente daquela em que você está.

Você pode acessar a cadeia de conexão e a chave primária da conta atual por meio das [variáveis de ambiente](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=COSMOS.ENDPOINT, credential=COSMOS.KEY, connection_policy=custom_connection_policy)

```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Acessar as variáveis de ambiente de ponto de extremidade e chave primária da conta
Você pode acessar o ponto de extremidade e a chave internos da conta em que seu notebook está.

```python
endpoint = COSMOS.ENDPOINT
primary_key = COSMOS.KEY
```
> [!IMPORTANT]
> As variáveis ``COSMOS.ENDPOINT`` e ``COSMOS.KEY`` só se aplicam à API do SQL. Para outras APIs, localize o ponto de extremidade e a chave na folha **cadeias de conexão** ou **chaves** da sua conta do Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Restaurar espaço de trabalho de notebook
Para restaurar as configurações padrão do espaço de trabalho de notebooks, selecione **Restaurar espaço de trabalho** na barra de comandos. Isso removerá todos os pacotes personalizados instalados e reiniciará o servidor Jupyter. Seus notebooks, arquivos e recursos de Cosmos do Azure não serão afetados.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Restaurar espaço de trabalho de notebook":::

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os benefícios dos [Notebooks do Jupyter do Azure Cosmos DB](cosmosdb-jupyter-notebooks.md).
- Saiba mais sobre a [API do SDK do Python para SQL do Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)