---
title: Migrar dados existentes para uma conta da API de Tabela no Azure Cosmos DB
description: Saiba como migrar ou importar dados locais ou de nuvem para uma conta da API de Tabela do Azure no Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 77f4c928db695bd4193ad46c93e0efbd16decf29
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443331"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Migrar seus dados para uma conta da API de Tabela do Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Este tutorial fornece instruções sobre como importar dados para usar com o a [API de Tabela](table-introduction.md) do Azure Cosmos DB. Se você tiver dados no Armazenamento de Tabelas do Azure, use a ferramenta de migração de dados ou o AzCopy para importar os dados para a API de Tabela do Azure Cosmos DB. 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Importar dados com a ferramenta de migração de dados
> * Importar dados com o AzCopy

## <a name="prerequisites"></a>Pré-requisitos

* **Aumentar a taxa de transferência:** a duração da sua migração de dados depende da taxa de transferência que você configurar para um contêiner individual ou um conjunto de contêineres. Certifique-se de aumentar a taxa de transferência para migrações de dados maiores. Depois de concluir a migração, diminua a taxa de transferência para economizar custos.

* **Criar recursos do Azure Cosmos DB:** antes de iniciar a migração dos dados, crie todas as tabelas no portal do Azure. Se você estiver fazendo a migração para uma conta do Azure Cosmos DB com produtividade no nível de banco de dados, forneça uma chave de partição ao criar as tabelas do Azure Cosmos DB.

## <a name="data-migration-tool"></a>Ferramenta de migração de dados

Use a ferramenta de migração de dados de linha de comando (dt.exe) do Azure Cosmos DB para importar os dados existentes do Armazenamento de Tabelas do Azure para uma conta da API de Tabela. 

Para migrar dados de tabela:

1. Baixe a ferramenta de migração no [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Execute `dt.exe` usando os argumentos de linha de comando para o seu cenário. `dt.exe` usa um comando no seguinte formato:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

As opções com suporte para esse comando são:

* **/ErrorLog:** Opcional. Nome do arquivo CSV usado para redirecionar falhas de transferência de dados.
* **/OverwriteErrorLog:** Opcional. Substitui o arquivo de log de erros.
* **/ProgressUpdateInterval:** opcional, o padrão é `00:00:01`. O intervalo de tempo de atualização do progresso da transferência de dados na tela.
* **/ErrorDetails:** opcional, o padrão é `None`. Especifica que as informações de erro detalhadas devem ser exibidas para os seguintes erros: `None`, `Critical` ou `All`.
* **/EnableCosmosTableLog:** Opcional. Direciona o log para uma conta de tabela do Azure Cosmos DB. Se definido, o padrão será a cadeia de conexão da conta de destino, a menos que `/CosmosTableLogConnectionString` também seja fornecido. Isso será útil se várias instâncias da ferramenta estiverem em execução simultânea.
* **/CosmosTableLogConnectionString:** Opcional. A cadeia de conexão usada para direcionar o log para uma conta de tabela remota do Azure Cosmos DB.

### <a name="command-line-source-settings"></a>Configurações de fonte de linha de comando

Use as opções de origem a seguir ao definir o Armazenamento de Tabelas do Azure como a origem da migração.

* **/s:AzureTable:** lê os dados do Armazenamento de Tabelas.
* **/s.ConnectionString:** cadeia de conexão para o ponto de extremidade da tabela. Você pode recuperar isso no portal do Azure.
* **/s.LocationMode:** opcional, o padrão é `PrimaryOnly`. Especifica qual modo de localização será usado ao se conectar ao Armazenamento de Tabelas do Azure: `PrimaryOnly`, `PrimaryThenSecondary`, `SecondaryOnly` ou `SecondaryThenPrimary`.
* **/s.Table:** nome da tabela do Azure.
* **/s.InternalFields:** definido como `All` para a migração de tabela, pois `RowKey` e `PartitionKey` são necessários para a importação.
* **/s.Filter:** Opcional. Cadeia de caracteres de filtro a ser aplicada.
* **/s.Projection:** Opcional. Lista de colunas a serem selecionadas:

Para recuperar a cadeia de conexão de origem na importação do Armazenamento de Tabelas, abra o portal do Azure. Selecione **Contas de armazenamento** > **Conta** > **Chaves de acesso** e copie a **Cadeia de conexão**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Captura de tela que mostra as opções Contas de armazenamento > Conta > Chaves de acesso e realça o ícone de cópia.":::

### <a name="command-line-target-settings"></a>Configurações de destino de linha de comando

Use as opções de destino a seguir ao definir a API de Tabela do Azure Cosmos DB como o destino da migração.

* **/t:TableAPIBulk:** carrega dados na API de Tabela do Azure Cosmos DB em lotes.
* **/t.ConnectionString:** a cadeia de conexão do ponto de extremidade da tabela.
* **/t.TableName:** especifica o nome da tabela para gravação.
* **/t.Overwrite:** opcional, o padrão é `false`. Especifica se os valores existentes devem ser substituídos.
* **/t.MaxInputBufferSize:** opcional, o padrão é `1GB`. Estimativa aproximada de bytes de entrada para buffer antes da liberação de dados para o coletor.
* **/t.Throughput:** opcional, padrões de serviço se não especificado. Especifica a taxa de transferência a ser configurada para a tabela.
* **/t.MaxBatchSize:** opcional, o padrão é `2MB`. Especifica o tamanho do lote em bytes.

### <a name="sample-command-source-is-table-storage"></a>Exemplo de comando: a origem é o Armazenamento de Tabelas

Veja um exemplo de linha de comando mostrando como fazer a importação do Armazenamento de Tabelas para a API de Tabela:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrar dados usando o AzCopy

Use também o utilitário de linha de comando AzCopy para migrar dados do Armazenamento de Tabelas para a API de Tabela do Azure Cosmos DB. Para usar o AzCopy, primeiro, exporte os dados conforme descrito em [Exportar dados do Armazenamento de Tabelas](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage). Em seguida, importe os dados para o Azure Cosmos DB conforme descrito em [API de Tabela do Azure Cosmos DB](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Veja o exemplo a seguir quando estiver fazendo a importação para o Azure Cosmos DB. Observe que o valor `/Dest` usa `cosmosdb`, não `core`.

Exemplo de comando de importação:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>Próximas etapas

Saiba como consultar dados usando a API de Tabela do Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar os dados?](../cosmos-db/tutorial-query-table.md)




