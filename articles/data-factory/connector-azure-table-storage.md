---
title: Copiar dados para e do Armazenamento de Tabelas do Azure usando o Data Factory | Microsoft Docs
description: Saiba como copiar dados de armazenamentos de origem com suporte para o Armazenamento de Tabelas do Azure ou do Armazenamento de Tabelas para armazenamentos de coletor com suporte, usando o Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 7ef8f80f44c921cc1f2524351c8acb78ebd713bf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57434786"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Copiar dados para e do Armazenamento de Tabelas do Azure usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-table-connector.md)
> * [Versão atual](connector-azure-table-storage.md)

Este artigo descreve como usar uma atividade de cópia no Azure Data Factory para copiar dados para e do Armazenamento de Tabelas do Azure. Ele amplia o artigo [visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da Atividade de Cópia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de qualquer armazenamento de dados fonte compatível para o armazenamento de tabelas. Você também pode copiar dados de um armazenamento de tabelas para qualquer armazenamento de dados de coletor compatível. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector de Tabela do Azure dá suporte à cópia de dados usando as autenticações de chave de conta e de assinatura de acesso compartilhado serviço.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Armazenamento de Tabelas.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

### <a name="use-an-account-key"></a>Usar uma chave de conta

Você pode criar um serviço vinculado do Armazenamento do Azure usando a chave de conta. Isso fornece ao data factory acesso global ao Armazenamento. Há suporte para as seguintes propriedades.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureTableStorage**. |Sim |
| connectionString | Especifique as informações necessárias para se conectar ao Armazenamento para a propriedade connectionString. <br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar a chave de conta no Azure Key Vault e efetuar pull da configuração `accountKey` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

>[!NOTE]
>Se você estiver usando o serviço vinculado do tipo "AzureStorage", ele ainda terá suporte no estado em que se encontra, enquanto você recebe a sugestão de usar esse novo tipo de serviço vinculado "AzureTableStorage" no futuro.

**Exemplo:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
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
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Você pode usá-la para conceder a um cliente permissões limitadas para objetos em sua conta de armazenamento por determinado tempo e com um conjunto específico de permissões. Não é preciso compartilhar as chaves de acesso da conta. A assinatura de acesso compartilhado é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a assinatura de acesso compartilhado, o cliente só precisa passar a assinatura de acesso compartilhado ao construtor ou método apropriado. Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [Assinaturas de acesso compartilhado: Entender o modelo de assinatura de acesso compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> O Data Factory agora dá suporte para **assinaturas de acesso compartilhado de serviço** e **assinaturas de acesso compartilhado de conta**. Para obter mais informações sobre esses dois tipos e como construí-los, consulte [Tipos de assinaturas compartilhadas](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). 

> [!TIP]
> Para gerar uma assinatura de acesso compartilhado de serviço para a conta de armazenamento, você pode executar os comandos a seguir do PowerShell. Substitua os espaços reservados e conceda a permissão necessária.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Para usar a autenticação de assinatura de acesso compartilhado, há suporte para as seguintes propriedades.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureTableStorage**. |Sim |
| sasUri | Especifique o URI da SAS do URI da assinatura de acesso compartilhado para a tabela. <br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar o token SAS no cofre de chaves do Azure para aproveitar a rotação automática e remover a parte do token. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

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
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

| Propriedade | DESCRIÇÃO | Obrigatório |
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
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Esquema do Data Factory

Para armazenamentos de dados sem esquema, como a Tabela do Azure, o Data Factory infere o esquema usando uma das seguintes maneiras:

* Se você especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o Data Factory respeitará essa estrutura do esquema. Nesse caso, se uma linha não contiver um valor de uma coluna, um valor nulo será fornecido para ele.
* Se você não especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o Data Factory inferirá o esquema usando a primeira linha dos dados. Nesse caso, se a primeira linha não contém o esquema completo, algumas colunas não estão presentes no resultado da operação de cópia.

Para fontes de dados sem esquema, a melhor prática é especificar a estrutura de dados usando a propriedade **structure**.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor da Tabela do Azure.

### <a name="azure-table-as-a-source-type"></a>Tabela do Azure como tipo de fonte

Para copiar dados da Tabela do Azure, defina o tipo de fonte na atividade de cópia como **AzureTableSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como **AzureTableSource**. |Sim |
| azureTableSourceQuery |Utiliza a consulta personalizada de armazenamento de tabelas para ler os dados. Veja os exemplos na seção a seguir. |Não  |
| azureTableSourceIgnoreTableNotFound |Indica se deve permitir que exceção da tabela não exista.<br/>Os valores permitidos são **True** e **False** (padrão). |Não  |

### <a name="azuretablesourcequery-examples"></a>Exemplos do azureTableSourceQuery

Se a coluna Tabela do Azure é do tipo datetime:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Se a coluna Tabela do Azure é do tipo string:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Caso use o parâmetro de pipeline, converta o valor datetime para o formato apropriado, de acordo com os exemplos anteriores.

### <a name="azure-table-as-a-sink-type"></a>Tabela do Azure como tipo sink

Para copiar dados da Tabela do Azure, defina o tipo de coletor na atividade de cópia como **AzureTableSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | O tipo de propriedade do coletor da atividade de cópia deve ser definida como **AzureTableSink**. |Sim |
| azureTableDefaultPartitionKeyValue |O valor de chave de partição padrão que pode ser utilizado pelo coletor. |Não  |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são usados como chaves de partição. Se não especificado, "AzureTableDefaultPartitionKeyValue" será utilizado como a chave da partição. |Não  |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores são usados como as chaves de linha. Se não especificado, um GUID é usado para cada linha. |Não  |
| azureTableInsertType |O modo para inserir dados na Tabela do Azure. Essa propriedade controla se linhas existentes na tabela de saída com a partição correspondente e as chaves de linha terão seus valores substituídos ou mesclados. <br/><br/>Os valores permitidos são **merge** (padrão) e **replace**. <br/><br> Essa configuração se aplica no nível de linha, não no nível de tabela. Nenhuma opção exclui linhas na tabela de saída que não existe na entrada. Para saber mais sobre como as configurações de mesclagem e substituição funcionam, consulte [Inserir ou mesclar entidade](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Inserir ou substituir entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Não  |
| writeBatchSize |Insere dados na Tabela do Azure quando writeBatchSize ou writeBatchTimeout for atingido.<br/>Os valores permitidos são inteiro (número de linhas). |Não (o padrão é 10.000) |
| writeBatchTimeout |Insere dados na Tabela do Azure quando writeBatchSize ou writeBatchTimeout for atingido.<br/>Os valores permitidos são timespan. Um exemplo é "00:20:00" (20 minutos). |Não (o padrão é 90 segundos, tempo limite padrão do cliente de armazenamento) |

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

Antes de poder usar a coluna de destino como o azureTablePartitionKeyName, será necessário mapear uma coluna de origem para uma coluna de destino usando a propriedade **"translator"**.

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

Quando dados forem movidos para e da Tabela do Azure, os seguintes [mapeamentos definidos pela Tabela do Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) serão usados nos tipos OData da Tabela do Azure para o tipo .NET e vice-versa.

| Tipo de dados de Tabela do Azure | Tipo de dados provisório do Data Factory | Detalhes |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Uma matriz de bytes de até 64 KB. |
| Edm.Boolean |bool |Um valor booliano. |
| Edm.DateTime |DateTime |Um valor de 64 bits expressado como Tempo Universal Coordenado (UTC). O intervalo de data e hora com suporte começa à meia-noite de 1º de janeiro de 1601 D.C. (C.E.), UTC. O intervalo termina em 31 de dezembro de 9999. |
| Edm.Double |double |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |Guid |Um identificador global exclusivo de 128 bits. |
| Edm.Int32 |Int32 |Um inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um inteiro de 64 bits. |
| Edm.String |Cadeia de caracteres |Um valor codificado em UTF-16. Valores de cadeia de caracteres podem ter até 64 KB. |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
