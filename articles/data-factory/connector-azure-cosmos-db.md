---
title: Copiar e transformar dados no Azure Cosmos DB (API do SQL)
description: Saiba como copiar dados de e para Azure Cosmos DB (API do SQL) e transformar dados em Azure Cosmos DB (API do SQL) usando Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: d42f30ebd72dca81255ddc02a9440db19979536d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608054"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Copiar e transformar dados no Azure Cosmos DB (API do SQL) usando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-documentdb-connector.md)
> * [Versão atual](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Azure Cosmos DB (API SQL) e usar o Fluxo de Dados para transformar dados no Azure Cosmos DB (API SQL). Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).



>[!NOTE]
>Este conector dá suporte apenas à API do SQL Cosmos DB. Para a API do MongoDB, confira o [conector para API do Azure Cosmos DB para MongoDB](connector-azure-cosmos-db-mongodb-api.md). Não há suporte para outros tipos de API.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Esse conector de Azure Cosmos DB (API do SQL) tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor compatível](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Para a atividade de cópia, esse conector de Azure Cosmos DB (API do SQL) dá suporte a:

- Copiar dados para e da [API SQL](../cosmos-db/introduction.md) do Azure Cosmos DB.
- Gravar no Azure Cosmos DB como **insert** ou **upsert**.
- Importar e exportar documentos JSON no estado em que se encontram, ou copiar dados de ou para um conjunto de dados tabular. Exemplos incluem um Banco de Dados SQL e um arquivo CSV. Para copiar documentos como estão para ou de arquivos JSON ou de outra coleção de Azure Cosmos DB, consulte [importar e exportar documentos JSON](#import-and-export-json-documents).

O Data Factory integra-se com a [biblioteca de executor em massa do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para fornecer o melhor desempenho quando você escreve para o Azure Cosmos DB.

> [!TIP]
> O [vídeo de Migração de Dados](https://youtu.be/5-SRNiC_qOU) orienta você pelas etapas de copiar dados do Armazenamento de Blobs do Azure para o Azure Cosmos DB. O vídeo também descreve considerações de ajuste de desempenho para a ingestão de dados para o Azure Cosmos DB em geral.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory específicas para o Azure Cosmos DB (API do SQL).

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Azure Cosmos DB (API do SQL):

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **type** deve ser definida como **CosmosDb**. | Sim |
| connectionString |Especifique as informações necessárias para se conectar ao banco de dados do Azure Cosmos DB.<br />**Observação**: você deve especificar informações de banco de dados na cadeia de conexão conforme mostrado nos exemplos a seguir. <br/> Você também pode colocar a chave de conta no Azure Key Vault e efetuar pull da configuração `accountKey` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você poderá usar o Azure Integration Runtime ou um tempo de execução da integração auto-hospedada (se o armazenamento de dados estiver localizado em uma rede privada). Se essa propriedade não for especificada, o Azure Integration Runtime padrão será usado. |Não |

**Exemplo**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a chave da conta no Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para definição de conjuntos de dados, consulte [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md).

As propriedades a seguir têm suporte para o conjunto de Azure Cosmos DB (API do SQL): 

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** do conjunto de conjuntos deve ser definida como **CosmosDbSqlApiCollection**. |Sim |
| collectionName |O nome da coleção de documentos do Azure Cosmos DB. |Sim |

Se você usar o conjunto de dados do tipo "DocumentDbCollection", ainda terá suporte como está para compatibilidade com versões anteriores para a atividade de cópia e pesquisa, não há suporte para o fluxo de dados. Você é sugerido para usar o novo modelo no futuro.

**Exemplo**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Esta seção fornece uma lista das propriedades compatíveis com fonte e coletor do Azure Cosmos DB (API do SQL). Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (API do SQL) como origem

Para copiar dados do Azure Cosmos DB (API do SQL), defina o tipo **source** na Atividade de Cópia como **DocumentDbCollectionSource**. 

As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** da fonte da atividade de cópia deve ser definida como **CosmosDbSqlApiSource**. |Sim |
| Consulta |Especifique a consulta do Azure Cosmos DB para ler dados.<br/><br/>Exemplo:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificada, esta instrução SQL será executada: `select <columns defined in structure> from mycollection` |
| preferredRegions | A lista preferencial de regiões às quais se conectar ao recuperar dados de Cosmos DB. | Não |
| pageSize | O número de documentos por página do resultado da consulta. O padrão é "-1", que significa usar o tamanho de página dinâmico do lado do serviço até 1000. | Não |
| detectDatetime | Se é para detectar DateTime dos valores de cadeia de caracteres nos documentos. Os valores permitidos são: **true** (padrão), **false**. | Não |

Se você usar a origem do tipo "DocumentDbCollectionSource", ainda terá suporte como está para compatibilidade com versões anteriores. É recomendável usar o novo modelo no futuro, que fornece recursos mais avançados para copiar dados de Cosmos DB.

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ao copiar dados de Cosmos DB, a menos que você queira [exportar documentos JSON no estado em que se encontram](#import-and-export-json-documents), a prática recomendada é especificar o mapeamento na atividade de cópia. Data Factory honra o mapeamento especificado na atividade-se uma linha não contiver um valor para uma coluna, um valor nulo será fornecido para o valor da coluna. Se você não especificar um mapeamento, o Data Factory inferirá o esquema usando a primeira linha dos dados. Se a primeira linha não contiver o esquema completo, algumas colunas estarão ausentes no resultado da operação de atividade.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (API do SQL) como coletor

Para copiar dados para o Azure Cosmos DB (API do SQL), defina o tipo **sink** na Atividade de Cópia como **DocumentDbCollectionSink**. 

As seguintes propriedades são suportadas na seção Copy Activity **sink**:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **Type** do coletor da atividade de cópia deve ser definida como **CosmosDbSqlApiSink**. |Sim |
| writeBehavior |Descreve como gravar dados no Azure Cosmos DB. Valores permitidos são **insert** e **upsert**.<br/><br/>O comportamento de **upsert** é substituir o documento se um documento com a mesma ID já existir; caso contrário, inserir o documento.<br /><br />**Observação**: o Data Factory gerará automaticamente uma ID para um documento se uma ID não for especificada no documento original ou pelo mapeamento de coluna. Isso significa que, para **upsert** funcionar conforme esperado, o documento deve ter uma ID. |Não<br />(o padrão é **insert**) |
| writeBatchSize | O Data Factory usa a [biblioteca de executor em massa do Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) para gravar dados no Azure Cosmos DB. A propriedade **writeBatchSize** controla o tamanho dos documentos que o ADF fornece à biblioteca. Você pode tentar aumentar o valor de **writeBatchSize** para melhorar o desempenho e diminuir o valor se o tamanho do documento for grande - veja as dicas abaixo. |Não<br />(o padrão é **10.000**) |
| disableMetricsCollection | Data Factory coleta métricas como Cosmos DB RUs para otimização e recomendações de desempenho de cópia. Se você estiver preocupado com esse comportamento, especifique `true` para desativá-lo. | Não (o padrão é `false`) |
| maxConcurrentConnections |O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não |


>[!TIP]
>Para importar documentos JSON no estado em que se encontram, consulte a seção [importar ou exportar documentos JSON](#import-and-export-json-documents) ; para copiar de dados em formato de tabela, consulte [migrar do banco de dado relacional para Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>O Cosmos DB limita o tamanho da solicitação única para 2MB. A fórmula é Tamanho da Solicitação = Tamanho Único do Documento * Tamanho do Lote de Gravação. Se você digitar o erro dizendo **"O tamanho da solicitação é muito grande"**, **reduza o `writeBatchSize`valor** na configuração do coletor de cópia.

Se você usar a origem do tipo "DocumentDbCollectionSink", ainda terá suporte como está para compatibilidade com versões anteriores. É recomendável usar o novo modelo no futuro, que fornece recursos mais avançados para copiar dados de Cosmos DB.

**Exemplo**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Mapeamento de esquema

Para copiar dados de Azure Cosmos DB para o coletor tabular ou vice-versa, consulte [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

Ao transformar dados no fluxo de dados de mapeamento, você pode ler e gravar em coleções no Cosmos DB. Para obter mais informações, confira [transformação de origem](data-flow-source.md) e [transformação do coletor](data-flow-sink.md) nos fluxos de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

As configurações específicas para Azure Cosmos DB estão disponíveis na guia **Opções de origem** da transformação origem. 

**Incluir colunas do sistema:** Se true, ```id``` , ```_ts``` e outras colunas do sistema serão incluídas nos metadados do fluxo de dados do CosmosDB. Ao atualizar as coleções, é importante incluí-las para que você possa obter a ID de linha existente.

**Tamanho da página:** O número de documentos por página do resultado da consulta. O padrão é "-1", que usa a página dinâmica do serviço até 1000.

**Taxa de transferência:** Defina um valor opcional para o número de RUs que você gostaria de aplicar à sua coleção CosmosDB para cada execução desse fluxo de dados durante a operação de leitura. O mínimo é 400.

**Regiões preferenciais:** Escolha as regiões de leitura preferenciais para esse processo.

#### <a name="json-settings"></a>Configurações de JSON

**Documento único:** Selecione esta opção se o ADF for tratar o arquivo inteiro como um único documento JSON.

**Nomes de colunas sem aspas:** Selecione esta opção se os nomes de coluna no JSON não estiverem entre aspas.

**Tem comentários:** Use essa seleção se seus documentos JSON tiverem comentários nos dados.

**Entre aspas simples:** Isso deve ser selecionado se as colunas e valores em seu documento estiverem entre aspas simples.

**Escape de barra invertida:** Se estiver usando barras invertidas em caracteres de escape em seu JSON, escolha essa opção.

### <a name="sink-transformation"></a>Transformação de coletor

As configurações específicas para Azure Cosmos DB estão disponíveis na guia **configurações** da transformação coletor.

**Método Update:** determina quais operações são permitidas no destino do banco de dados. O padrão é permitir apenas inserções. Para atualizar, fazer upsert ou excluir linhas, uma transformação alter-row é necessária para marcar as linhas para essas ações. Para atualizações, upserts e exclusões, é necessário selecionar uma coluna de chave ou colunas para determinar qual linha alterar.

**Ação de coleta:** Determina se a coleção de destino deve ser recriada antes da gravação.
* Nenhum: nenhuma ação será feita para a coleção.
* Recriar: a coleção será descartada e recriada

**Tamanho do lote**: um número inteiro que representa quantos objetos estão sendo gravados para Cosmos DB coleção em cada lote. Normalmente, a partir do tamanho de lote padrão é suficiente. Para ajustar esse valor, observe:

- O Cosmos DB limita o tamanho da solicitação única para 2MB. A fórmula é "tamanho da solicitação = tamanho de documento único * tamanho do lote". Se você clicar em erro dizendo "o tamanho da solicitação é muito grande", reduza o valor do tamanho do lote.
- Quanto maior o tamanho do lote, o ADF de taxa de transferência melhor pode atingir, embora você aloque o RUs suficiente para capacitar sua carga de trabalho.

**Chave de partição:** Insira uma cadeia de caracteres que represente a chave de partição para sua coleção. Exemplo: ```/movies/title```

**Taxa de transferência:** Defina um valor opcional para o número de RUs que você gostaria de aplicar à sua coleção CosmosDB para cada execução desse fluxo de dados. O mínimo é 400.

**Orçamento de taxa de transferência de gravação:** Um inteiro que representa o RUs que você deseja alocar para essa operação de gravação de fluxo de dados, fora da taxa de transferência total alocada para a coleção.

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importar e exportar documentos JSON

Use esse conector do Azure Cosmos DB (API do SQL) para facilmente:

* Copiar documentos entre duas coleções do Azure Cosmos DB no estado em que se encontram.
* Importar documentos JSON de várias fontes para o Azure Cosmos DB, incluindo o armazenamento de BLOBs do Azure, Azure Data Lake Storage e outros repositórios baseados em arquivo compatíveis com o Azure Data Factory.
* Exportar documentos JSON de uma coleção do Azure Cosmos DB para vários repositórios baseados em arquivo.

Para obter uma cópia independente de esquema:

* Quando você usa a ferramenta Copiar Dados, selecione a opção **Exportar no estado em que se encontram para arquivos JSON ou uma coleção do Cosmos DB**.
* Ao usar a criação de atividade, escolha o formato JSON com o repositório de arquivos correspondente para a origem ou o coletor.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrar do banco de dados relacional para Cosmos DB

Ao migrar de um banco de dados relacional, por exemplo, SQL Server para Azure Cosmos DB, a atividade de cópia pode facilmente mapear dados tabulares de documentos JSON de origem para mesclar em Cosmos DB. Em alguns casos, talvez você queira recriar o modelo de dados para otimizá-lo para os casos de uso NoSQL de acordo com a [modelagem de dados no Azure Cosmos DB](../cosmos-db/modeling-data.md), por exemplo, para desnormalizar os dados inserindo todos os subitens relacionados em um documento JSON. Para tal caso, consulte [Este artigo](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) com um passo-a mais sobre como obtê-lo usando Azure data Factory atividade de cópia.

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados compatíveis com a Atividade de Cópia como fontes e coletores no Azure Data Factory, confira [armazenamentos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).