---
title: Copiar dados bidirecionalmente no Armazenamento de Tabelas do Azure
description: Saiba como copiar dados de armazenamentos de origem com suporte para o Armazenamento de Tabelas do Azure ou do Armazenamento de Tabelas para armazenamentos de coletor com suporte, usando o Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 5674d656385d356f596f96cd7851d2cc05839761
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597391"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Copiar dados para e do Armazenamento de Tabelas do Azure usando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-table-connector.md)
> * [Versão atual](connector-azure-table-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar uma atividade de cópia no Azure Data Factory para copiar dados para e do Armazenamento de Tabelas do Azure. Ele amplia o artigo [visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da Atividade de Cópia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Há suporte para este conector do Armazenamento de Tabelas do Azure nas seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de qualquer armazenamento de dados fonte compatível para o armazenamento de tabelas. Você também pode copiar dados de um armazenamento de tabelas para qualquer armazenamento de dados de coletor compatível. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector de Tabela do Azure dá suporte à cópia de dados usando as autenticações de chave de conta e de assinatura de acesso compartilhado serviço.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Armazenamento de Tabelas.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

### <a name="use-an-account-key"></a>Usar uma chave de conta

Você pode criar um serviço vinculado do Armazenamento do Azure usando a chave de conta. Isso fornece ao data factory acesso global ao Armazenamento. Há suporte para as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureTableStorage**. |Sim |
| connectionString | Especifique as informações necessárias para se conectar ao Armazenamento para a propriedade connectionString. <br/>Você também pode colocar a chave de conta no Azure Key Vault e efetuar pull da configuração `accountKey` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

>[!NOTE]
>Se você estiver usando o serviço vinculado do tipo "AzureStorage", ele ainda terá suporte no estado em que se encontra, enquanto você recebe a sugestão de usar esse novo tipo de serviço vinculado "AzureTableStorage" no futuro.

**Exemplo:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
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
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
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

### <a name="use-shared-access-signature-authentication"></a>Usar autenticação de assinatura de acesso compartilhado

Você também pode criar um serviço vinculado ao Armazenamento por meio de uma assinatura de acesso compartilhado. Isso fornece ao data factory acesso restrito/acesso total, com limite de tempo/recursos específicos no armazenamento.

Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Você pode usá-la para conceder a um cliente permissões limitadas para objetos em sua conta de armazenamento por determinado tempo e com um conjunto específico de permissões. Não é preciso compartilhar as chaves de acesso da conta. A assinatura de acesso compartilhado é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a assinatura de acesso compartilhado, o cliente só precisa passar a assinatura de acesso compartilhado ao construtor ou método apropriado. Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [Assinaturas de acesso compartilhado: Entender o modelo de assinatura de acesso compartilhado](../storage/common/storage-sas-overview.md).

> [!NOTE]
> O Data Factory agora dá suporte para **assinaturas de acesso compartilhado de serviço** e **assinaturas de acesso compartilhado de conta**. Para obter mais informações sobre assinaturas de acesso compartilhado, confira [Conceder acesso limitado a recursos de Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../storage/common/storage-sas-overview.md). 

> [!TIP]
> Para gerar uma assinatura de acesso compartilhado de serviço para a conta de armazenamento, você pode executar os comandos a seguir do PowerShell. Substitua os espaços reservados e conceda a permissão necessária.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Para usar a autenticação de assinatura de acesso compartilhado, há suporte para as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureTableStorage**. |Sim |
| sasUri | Especifique o URI da SAS do URI da assinatura de acesso compartilhado para a tabela. <br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar o token SAS no Azure Key Vault para aproveitar a rotação automática e remover a parte do token. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

>[!NOTE]
>Se você estiver usando o serviço vinculado do tipo "AzureStorage", ele ainda terá suporte no estado em que se encontra, enquanto você recebe a sugestão de usar esse novo tipo de serviço vinculado "AzureTableStorage" no futuro.

**Exemplo:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
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
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
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

Ao criar um URI de assinatura de acesso compartilhado, considere os seguintes pontos:

- Defina as permissões apropriadas de leitura/gravação em objetos com base em como o serviço vinculado (leitura, gravação, leitura/gravação) será usado no data factory.
- Defina o **Tempo de expiração** adequadamente. Certifique-se de que o acesso aos objetos de Armazenamento não expira dentro do período ativo do pipeline.
- O URI deve ser criado no nível de tabela correto com base na necessidade.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte do conjunto de dados da Tabela do Azure.

Para copiar dados para e da Tabela do Azure, defina a propriedade type do conjunto de dados como **AzureTable**. Há suporte para as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como **AzureTable**. |Sim |
| tableName |O nome da tabela na instância do banco de dados do armazenamento de Tabelas à qual o serviço vinculado se refere. |Sim |

**Exemplo:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Esquema do Data Factory

Para armazenamentos de dados sem esquema, como a Tabela do Azure, o Data Factory infere o esquema usando uma das seguintes maneiras:

* Se você especificar o mapeamento de coluna na atividade de cópia, Data Factory usará a lista de colunas do lado de origem para recuperar dados. Nesse caso, se uma linha não contiver um valor de uma coluna, um valor nulo será fornecido para ele.
* Se você não especificar o mapeamento de coluna na atividade de cópia, o Data Factory vai inferir o esquema usando a primeira linha nos dados. Nesse caso, se a primeira linha não contiver o esquema completo (por exemplo, algumas colunas têm valor nulo), algumas colunas estarão ausentes do resultado da operação de cópia.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor da Tabela do Azure.

### <a name="azure-table-as-a-source-type"></a>Tabela do Azure como tipo de fonte

Para copiar dados da Tabela do Azure, defina o tipo de fonte na atividade de cópia como **AzureTableSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como **AzureTableSource**. |Sim |
| AzureTableSourceQuery |Utiliza a consulta personalizada de armazenamento de tabelas para ler os dados.<br/>A consulta de origem é um mapa direto da `$filter` opção de consulta com suporte do armazenamento de tabelas do Azure, saiba mais sobre a sintaxe deste [documento](/rest/api/storageservices/querying-tables-and-entities#supported-query-options)e veja os exemplos na [seção exemplos de azureTableSourceQuery](#azuretablesourcequery-examples)a seguir. |Não |
| azureTableSourceIgnoreTableNotFound |Indica se deve permitir que exceção da tabela não exista.<br/>Os valores permitidos são **True** e **False** (padrão). |Não |

### <a name="azuretablesourcequery-examples"></a>Exemplos do azureTableSourceQuery

>[!NOTE]
>A operação de consulta de Tabela do Azure atinge o tempo limite em 30 segundos, conforme [imposto pelo serviço Tabela do Azure](/rest/api/storageservices/setting-timeouts-for-table-service-operations). Saiba como otimizar a consulta no artigo [Design para consulta](../storage/tables/table-storage-design-for-query.md).

No Azure Data Factory, caso deseje filtrar os dados em uma coluna do tipo datetime, veja este exemplo:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Caso deseje filtrar os dados em uma coluna do tipo cadeia de caracteres, veja este exemplo:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Caso use o parâmetro de pipeline, converta o valor datetime para o formato apropriado, de acordo com os exemplos anteriores.

### <a name="azure-table-as-a-sink-type"></a>Tabela do Azure como tipo sink

Para copiar dados da Tabela do Azure, defina o tipo de coletor na atividade de cópia como **AzureTableSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | O tipo de propriedade do coletor da atividade de cópia deve ser definida como **AzureTableSink**. |Sim |
| azureTableDefaultPartitionKeyValue |O valor de chave de partição padrão que pode ser utilizado pelo coletor. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são usados como chaves de partição. Se não especificado, "AzureTableDefaultPartitionKeyValue" será utilizado como a chave da partição. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores são usados como as chaves de linha. Se não especificado, um GUID é usado para cada linha. |Não |
| azureTableInsertType |O modo para inserir dados na Tabela do Azure. Essa propriedade controla se linhas existentes na tabela de saída com a partição correspondente e as chaves de linha terão seus valores substituídos ou mesclados. <br/><br/>Os valores permitidos são **merge** (padrão) e **replace**. <br/><br> Essa configuração se aplica no nível de linha, não no nível de tabela. Nenhuma opção exclui linhas na tabela de saída que não existe na entrada. Para saber mais sobre como as configurações de mesclagem e substituição funcionam, consulte [Inserir ou mesclar entidade](/rest/api/storageservices/Insert-Or-Merge-Entity) e [Inserir ou substituir entidade](/rest/api/storageservices/Insert-Or-Replace-Entity). |Não |
| writeBatchSize |Insere dados na Tabela do Azure quando writeBatchSize ou writeBatchTimeout for atingido.<br/>Os valores permitidos são inteiro (número de linhas). |Não (o padrão é 10.000) |
| writeBatchTimeout |Insere dados na Tabela do Azure quando writeBatchSize ou writeBatchTimeout for atingido.<br/>Os valores permitidos são timespan. Um exemplo é "00:20:00" (20 minutos). |Não (o padrão é 90 segundos, tempo limite padrão do cliente de armazenamento) |
| maxConcurrentConnections |O limite superior de conexões simultâneas estabelecidas com o armazenamento de dados durante a execução da atividade. Especifique um valor somente quando desejar limitar as conexões simultâneas.| Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Antes de poder usar a coluna de destino como o azureTablePartitionKeyName, será necessário mapear uma coluna de origem para uma coluna de destino usando a propriedade **"translator"** .

No exemplo a seguir, a coluna de origem DivisionID é mapeada para a coluna de destino DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" é especificada como a chave de partição.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapeamento de tipo de dados para Tabela do Azure

Ao copiar dados de e para a Tabela do Azure, os seguintes mapeamentos são usados de tipos de dados de Tabela do Azure para tipos de dados intermediários do Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

Quando dados forem movidos para e da Tabela do Azure, os seguintes [mapeamentos definidos pela Tabela do Azure](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) serão usados nos tipos OData da Tabela do Azure para o tipo .NET e vice-versa.

| Tipo de dados de Tabela do Azure | Tipo de dados provisório do Data Factory | Detalhes |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Uma matriz de bytes de até 64 KB. |
| Edm.Boolean |bool |Um valor booliano. |
| Edm.DateTime |Datetime |Um valor de 64 bits expressado como Tempo Universal Coordenado (UTC). O intervalo de data e hora com suporte começa à meia-noite de 1º de janeiro de 1601 D.C. (C.E.), UTC. O intervalo termina em 31 de dezembro de 9999. |
| Edm.Double |double |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |Guid |Um identificador global exclusivo de 128 bits. |
| Edm.Int32 |Int32 |Um inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um inteiro de 64 bits. |
| Edm.String |String |Um valor codificado em UTF-16. Valores de cadeia de caracteres podem ter até 64 KB. |

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).