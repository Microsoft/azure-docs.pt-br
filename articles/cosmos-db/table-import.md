---
title: Migrar dados existentes para a conta da API de Tabela no Azure Cosmos DB
description: Saiba como migrar ou importar dados locais ou de nuvem para a conta da API de Tabela do Azure no Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e876ca028532bb3721146e90a91d68c4c12bf79f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096068"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Migrar seus dados para a conta de API de Tabela do Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Este tutorial fornece instruções sobre como importar dados para usar com o a [API de Tabela](table-introduction.md) do Azure Cosmos DB. Se você tiver dados no armazenamento de Tabelas do Azure, será possível usar a Ferramenta de Migração de Dados ou o AzCopy para importar os dados para a API de Tabela do Azure Cosmos DB. Se você tiver dados armazenados em uma conta da API de Tabela do Azure Cosmos DB (versão prévia), deverá usar a ferramenta de Migração de Dados para migrar seus dados. 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Importar dados com a ferramenta de migração de dados
> * Importar dados com o AzCopy
> * Migrar da API de Tabela (versão prévia) para a API de Tabela 

## <a name="prerequisites"></a>Pré-requisitos

* **Aumentar a taxa de transferência:** a duração da sua migração de dados depende da taxa de transferência que você configurar para um contêiner individual ou um conjunto de contêineres. Certifique-se de aumentar a taxa de transferência para migrações de dados maiores. Depois de concluir a migração, diminua a taxa de transferência para economizar custos. Para obter mais informações sobre como aumentar a produtividade no portal do Azure, consulte Níveis de desempenho e tipos de preço no Azure Cosmos DB.

* **Criar recursos do Azure Cosmos DB:** antes de começar a migrar dados, crie previamente todas as suas tabelas no portal do Azure. Se você estiver migrando para uma conta do Azure Cosmos DB com produtividade de nível de banco de dados, não deixe de fornecer uma chave de partição ao criar as tabelas do Azure Cosmos DB.

## <a name="data-migration-tool"></a>Ferramenta de migração de dados

A ferramenta de Migração de Dados do Azure Cosmos DB da linha de comando (dt.exe) pode ser usada para importar os dados de armazenamento de Tabela do Azure existentes para uma conta disponível ao público em geral da API de Tabela ou migrar dados de uma conta da API de Tabela (versão prévia) para uma conta de disponível ao público em geral da API de Tabela. Outras fontes não são atualmente compatíveis. A ferramenta de migração de dados baseada na interface do usuário atualmente não oferece suporte para contas de API de tabela. 

Para executar uma migração de dados de tabela, conclua as seguintes tarefas:

1. Baixe a ferramenta de migração no [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Execute `dt.exe` usando os argumentos de linha de comando para seu cenário. `dt.exe` usa um comando no seguinte formato:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

As opções com suporte para esse comando são:

* **/ErrorLog:** Opcional. Nome do arquivo CSV para redirecionar falhas de transferência de dados
* **/OverwriteErrorLog:** Opcional. Substituir arquivo de log de erros
* **/ProgressUpdateInterval:** opcional, o padrão é 00:00:01. Intervalo de tempo para atualizar o andamento da transferência de dados na tela
* **/ErrorDetails:** opcional, o padrão é None. Especifica que as informações de erro detalhadas devem ser exibidas para os seguintes erros: Nenhum, Crítico, Todos
* **/EnableCosmosTableLog:** Opcional. direcione o log para uma conta de tabela do Cosmos. Se definido, o padrão será a cadeia de conexão da conta de destino, a menos que /CosmosTableLogConnectionString também seja fornecido. Isso será útil se várias instâncias de DT estiverem em execução simultaneamente.
* **/CosmosTableLogConnectionString:** Opcional. ConnectionString para direcionar o log para uma conta de tabela do Cosmos remota.

### <a name="command-line-source-settings"></a>Configurações de fonte de linha de comando

Use as seguintes opções de fonte ao definir Armazenamento de Tabela do Azure ou API de Tabela (versão prévia) como fonte da migração.

* **/s:AzureTable:** lê dados do armazenamento de Tabelas do Azure
* **/s.ConnectionString:** cadeia de conexão para o ponto de extremidade da tabela. Pode ser recuperado do portal do Azure
* **/s.LocationMode:** opcional, o padrão é PrimaryOnly. Especifica qual modo de localização usar ao se conectar ao armazenamento de Tabela do Azure: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
* **/s.Table:** nome da tabela do Azure
* **/s.InternalFields:** definido como Todos para a migração de tabela, pois RowKey e PartitionKey são necessários para a importação.
* **/s.Filter:** Opcional. Cadeia de caracteres de filtro a ser aplicada
* **/s.Projection:** Opcional. Lista de colunas a selecionar

Para recuperar a cadeia de conexão da fonte ao importar do armazenamento de Tabela do Azure, abra o portal do Azure e clique em **Contas de armazenamento** > **Conta** > **Chaves de acesso** e, em seguida, use o botão copiar para copiar a **Cadeia de conexão**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Captura de tela que mostra as opções Contas de armazenamento > Conta > Chaves de acesso e realça o botão copiar.":::

Para recuperar a cadeia de conexão da fonte ao importar de uma conta de API de Tabela do Azure Cosmos DB, abra o portal do Azure, clique em **Azure Cosmos DB** > **Conta** > **Cadeia de conexão** e use o botão copiar para copiar a **Cadeia de conexão**.

:::image type="content" source="./media/table-import/cosmos-connection-string.png" alt-text="Captura de tela das opções de origem do HBase":::

[Exemplo de comando de Armazenamento de Tabela do Azure](#azure-table-storage)

[Exemplo de comando da API de Tabela do Azure Cosmos DB (versão prévia)](#table-api-preview)

### <a name="command-line-target-settings"></a>Configurações de destino de linha de comando

Use as seguintes opções de destino ao definir a API de Tabela do Azure Cosmos DB como destino da migração.

* **/t:TableAPIBulk:** carrega dados na tabela do Azure CosmosDB em lotes
* **/t.ConnectionString:** cadeia de conexão para o ponto de extremidade da tabela
* **/t.TableName:** especifica o nome da tabela na qual gravar
* **/t.Overwrite:** opcional, o padrão é false. Especifica se os valores existentes devem ser substituídos
* **/t.MaxInputBufferSize:** opcional, o padrão é 1GB. Estimativa aproximada de bytes de entrada a colocar em buffer antes de liberar dados para o coletor
* **/t.Throughput:** opcional, padrões de serviço se não especificado. Especifica a taxa de transferência a ser configurada para a tabela
* **/t.MaxBatchSize:** opcional, o padrão é 2MB. Especifique o tamanho do lote em bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Comando de exemplo: a fonte é o armazenamento de Tabelas do Azure

Aqui está um exemplo de linha de comando mostrando como importar do armazenamento de Tabela do Azure para a API de Tabela:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Comando de exemplo: a fonte é a API de Tabela do Azure Cosmos DB (versão prévia)

Aqui está um exemplo de linha de comando para importar da API de Tabela (versão prévia) para a API de Tabela disponível ao público em geral:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrar dados usando o AzCopy

Outra opção para migrar dados de armazenamento de tabela do Azure para a API de Tabela do Azure Cosmos DB é usar o utilitário de linha de comando AzCopy. Para usar o AzCopy, primeiro exporte seus dados conforme descrito em [Exportar dados do armazenamento de tabela](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage), depois importe os dados para o Azure Cosmos DB conforme descrito em [API de Tabela do Azure Cosmos DB](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Ao executar a importação para o Azure Cosmos DB, consulte o exemplo a seguir. Observe que o valor de /Dest usa cosmosdb e não core.

Exemplo de comando de importação:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrar da API de Tabela (versão prévia) para a API de Tabela

> [!WARNING]
> Se você quiser aproveitar imediatamente os benefícios das tabelas disponíveis ao público em geral, migre suas tabelas da versão prévia conforme especificado nesta seção; caso contrário, vamos executar migrações automáticas para clientes da versão prévia nas próximas semanas, observe, no entanto, que as tabelas da versão prévia migradas automaticamente terão certas restrições nelas que não há nas tabelas recém-criadas.

A API de Tabela já está disponível ao público em geral. Há diferenças entre as versões prévia e disponível ao público em geral das tabelas tanto no código que é executado na nuvem como no código que é executado no cliente. Portanto, não é recomendável tentar combinar um cliente do SDK de versão prévia com uma conta da API de Tabela disponível ao público em geral e vice-versa. Os clientes da versão prévia da API de Tabela que desejem continuar a usar as tabelas existentes, mas em um ambiente de produção precisam migrar da versão prévia para o ambiente disponível ao público em geral ou aguardar a migração automática. Se você esperar a migração automática, será notificado sobre as restrições nas tabelas migradas. Após a migração, você poderá criar novas tabelas em sua conta existente sem restrições (somente tabelas migradas terão restrições).

Para migrar da API de Tabela (versão prévia) para a API de Tabela disponível ao público em geral:

1. Crie uma nova conta do Azure Cosmos DB e defina seu tipo de API como Tabela do Azure, conforme descrito em [Criar uma conta de banco de dados](create-table-dotnet.md#create-a-database-account).

2. Altere os clientes para usar uma versão de lançamento dos [SDKs da API de Tabela](table-sdk-dotnet.md).

3. Migre os dados do cliente das tabelas de versão prévia para tabelas disponíveis ao público em geral usando a ferramenta de Migração de Dados. Instruções sobre como usar a ferramenta de migração de dados para esse fim são descritas em [Ferramenta de Migração de Dados](#data-migration-tool). 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Importar dados com a ferramenta de migração de dados
> * Importar dados com o AzCopy
> * Migrar da API de Tabela (versão prévia) para a API de Tabela

Agora você pode seguir para o próximo tutorial e saber como consultar dados usando a API de Tabela do Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar os dados?](../cosmos-db/tutorial-query-table.md)