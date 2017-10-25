---
title: Copiar dados para/do Armazenamento de Tabelas do Azure usando o Data Factory.| Microsoft Docs
description: Saiba como copiar dados de armazenamentos de origem com suporte para o Armazenamento de Tabelas do Azure ou do Armazenamento de Tabelas para armazenamentos de coletor com suporte, usando o Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 2fa03b82750585454430da0c29392db57b20d3c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-table-using-azure-data-factory"></a>Copiar dados para ou das Tabelas do Azure usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-azure-table-connector.md)
> * [Versão 2 – Versão prévia](connector-azure-table-storage.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados para e da Tabela do Azure. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está em GA (disponibilidade geral), consulte [Conector do Armazenamento de Tabelas do Azure na V1](v1/data-factory-azure-table-connector.md).

## <a name="supported-scenarios"></a>Cenários com suporte

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para a Tabela do Azure ou copiar dados da Tabela do Azure para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector de Tabela do Azure dá suporte à cópia de dados usando as autenticações de **chave de conta** e de **SAS de serviço** (Assinatura de Acesso Compartilhado).

## <a name="get-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou o modelo do Azure Resource Manager. Confira o [Tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Armazenamento de Tabelas do Azure.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

### <a name="using-account-key"></a>Usando chave de conta

Você pode criar um serviço vinculado do Armazenamento do Azure usando a chave de conta, que fornece ao data factory o acesso global ao Armazenamento do Azure. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AzureStorage** |Sim |
| connectionString | Especifique as informações necessárias para se conectar ao armazenamento do Azure para a propriedade connectionString. Marque esse campo como SecureString. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

**Exemplo:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-sas-authentication"></a>Usando a autenticação SAS de Serviço

Você também pode criar um serviço vinculado do Armazenamento do Azure usando uma SAS (Assinatura de Acesso Compartilhado), que fornece ao data factory o acesso restrito/com limite de tempo a todos os recursos ou a recursos específicos no armazenamento.

Uma SAS (Assinatura de Acesso Compartilhado) fornece acesso delegado aos recursos da sua conta de armazenamento. Isso permite conceder a um cliente permissões limitadas a objetos na sua conta de armazenamento por um período especificado e com um conjunto determinado de permissões, sem precisar compartilhar suas chaves de acesso de conta. A SAS é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a SAS, o cliente só precisa passar a SAS ao construtor apropriado ou ao método apropriado. Para obter informações detalhadas sobre SAS, consulte [Assinaturas de Acesso Compartilhado: entendendo o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> O Azure Data Factory agora dá suporte somente a **SAS do serviço**, mas não SAS de conta. Consulte os [Tipos de Assinaturas de Acesso Compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) para obter detalhes sobre esses dois tipos e como criá-los. A URL de SAS que pode ser gerada no Portal do Azure ou no Gerenciador de Armazenamento é uma SAS de conta, que não tem suporte.
>

Para usar a autenticação de SAS de Serviço, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AzureStorage** |Sim |
| sasUri | Especificar o URI de Assinatura de Acesso Compartilhado para os recursos de Armazenamento do Azure, como blob, contêiner ou tabela. Marque esse campo como SecureString. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

**Exemplo:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Ao criar um **URI de SAS**, considerando os seguintes pontos:

- Defina as **permissões** apropriadas de leitura/gravação em objetos com base em como o serviço vinculado (leitura, gravação, leitura/gravação) será usado no data factory.
- Defina o **Tempo de expiração** adequadamente. Certifique-se de que o acesso aos objetos de Armazenamento do Azure não expira dentro do período ativo do pipeline.
- O URI deve ser criado no nível de tabela correto com base na necessidade.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados da Tabela do Azure.

Para copiar dados para/da Tabela do Azure, defina a propriedade type do conjunto de dados como **AzureTable**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **AzureTable** |Sim |
| tableName |Nome da tabela na instância do Banco de Dados da Tabela do Azure à qual o serviço vinculado se refere. |Sim |

**Exemplo:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Esquema do Data Factory

Para armazenamentos de dados sem esquema, como a Tabela do Azure, o serviço do Data Factory infere o esquema usando uma das seguintes maneiras:

1. Se você especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o serviço do Data Factory respeitará essa estrutura do esquema. Nesse caso, se uma linha não contiver um valor de uma coluna, um valor nulo será fornecido para ele.
2. Se você não especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o Data Factory inferirá o esquema usando a primeira linha dos dados. Nesse caso, se a primeira linha não contém o esquema completo, algumas colunas não estão presentes no resultado da operação de cópia.

Portanto, para fontes de dados sem esquema, a prática recomendada é especificar a estrutura de dados usando a propriedade **structure** .

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor da Tabela do Azure.

### <a name="azure-table-as-source"></a>Tabela do Azure como fonte

Para copiar dados da Tabela do Azure, defina o tipo de fonte na atividade de cópia como **AzureTableSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **AzureTableSource** |Sim |
| AzureTableSourceQuery |Utiliza a consulta personalizada de tabela do Azure para ler os dados. Veja exemplos na próxima seção. |Não |
| azureTableSourceIgnoreTableNotFound |Indique se assimilar a exceção da tabela não existe.<br/>Os valores permitidos são: **True** e **False** (padrão). |Não |

### <a name="azuretablesourcequery-examples"></a>Exemplos do azureTableSourceQuery

Se a coluna Tabela do Azure é do tipo cadeia de caracteres:

```json
"azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', <datetime parameter>)"
```

Se a coluna Tabela do Azure é do tipo datetime:

```json
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', <datetime parameter>, <datetime parameter>)"
```

### <a name="azure-table-as-sink"></a>Tabela do Azure como coletor

Para copiar dados da Tabela do Azure, defina o tipo de fonte na atividade de cópia como **AzureTableSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **AzureTableSink** |Sim |
| azureTableDefaultPartitionKeyValue |Valor de chave de partição padrão que pode ser utilizado pelo coletor. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são usados como chaves de partição. Se não especificado, "AzureTableDefaultPartitionKeyValue" será utilizado como a chave da partição. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores são usados como chaves de linha. Se não especificado, um GUID é usado para cada linha. |Não |
| azureTableInsertType |O modo para inserir dados na tabela do Azure. Essa propriedade controla se linhas existentes na tabela de saída com a partição correspondente e as chaves de linha terão seus valores substituídos ou mesclados. <br/><br/>Os valores permitidos são: **merge** (padrão), **replace**. <br/><br> Essa configuração se aplica ao nível de linha e não ao nível de tabela e nenhuma das opções excluirá as linhas na tabela de saída que não existirem na entrada. Para saber mais sobre como essas configurações (mesclagem e substituição) funcionam, consulte os tópicos [Inserir ou Mesclar Entidade](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Inserir ou Substituir Entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Não |
| writeBatchSize |Insere dados na tabela do Azure quando o writeBatchSize ou writeBatchTimeout for atingido.<br/>Os valores permitidos são: inteiro (número de linhas) |Não (o padrão é 10000) |
| writeBatchTimeout |Insere dados na tabela do Azure quando o writeBatchSize ou writeBatchTimeout for atingido.<br/>Os valores permitidos são: período. Exemplo: "00:20:00" (20 minutos) |Não (o padrão é 90 segundos – tempo limite padrão do cliente de armazenamento) |

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

Antes de poder usar a coluna de destino como o azureTablePartitionKeyName, será necessário mapear uma coluna de origem para uma coluna de destino usando a propriedade "translator".

No exemplo a seguir, a coluna de origem DivisionID é mapeada para a coluna de destino DivisionID.

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

A "DivisionID" é especificada como a chave de partição.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapeamento de tipo de dados para Tabela do Azure

Ao copiar dados de/para a Tabela do Azure, os seguintes mapeamentos são usados de tipos de dados de Tabela do Azure para tipos de dados intermediários do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

Quando dados forem movidos para e da Tabela do Azure, os seguintes [mapeamentos definidos pelo serviço Tabela do Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) serão usados nos tipos OData da Tabela do Azure para o tipo .NET e vice-versa.

| Tipo de dados de Tabela do Azure | Tipo de dados provisório do Data Factory | Detalhes |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Uma matriz de bytes de até 64 KB. |
| Edm.Boolean |bool |Um valor booliano. |
| Edm.DateTime |DateTime |Um valor de 64 bits expressado como Tempo Universal Coordenado (UTC). O intervalo de data e hora com suporte começa à 00:00 de 1º de janeiro de 1601 D.C. (C.E.), UTC. O intervalo termina em 31 de dezembro de 9999. |
| Edm.Double |double |Um valor de ponto flutuante de 64 bits. |
| Edm.Guid |Guid |Um identificador global exclusivo de 128 bits. |
| Edm.Int32 |Int32 |Um inteiro de 32 bits. |
| Edm.Int64 |Int64 |Um inteiro de 64 bits. |
| Edm.String |Cadeia de caracteres |Um valor codificado em UTF-16. Valores de cadeia de caracteres podem ter até 64 KB. |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).