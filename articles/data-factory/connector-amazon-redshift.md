---
title: Copiar dados do Amazon Redshift usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como copiar dados do Amazon Redshift para armazenamentos de dados de coletor com suporte por meio do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 9e1dde57dc1903e87704bd55fb0b942b7cc349e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61261790"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Copiar dados do Amazon Redshift usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-amazon-redshift-connector.md)
> * [Versão atual](connector-amazon-redshift.md)


Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um Amazon Redshift. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do Amazon Redshift para qualquer repositório de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Amazon Redshift dá suporte a recuperação de dados do Redshift usando a consulta ou o suporte interno a UNLOAD do Redshift.

> [!TIP]
> Para obter o melhor desempenho ao copiar grandes quantidades de dados do Redshift, considere o uso de UNLOAD do Redshift interno por meio do Amazon S3. Consulte a seção [Usar UNLOAD para copiar dados do Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

* Se você estiver copiando dados para um armazenamento de dados local usando o [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md), conceda ao Integration Runtime (use o endereço IP do computador) o acesso ao cluster do Amazon Redshift. Veja [Autorizar o acesso ao cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) para obter instruções.
* Se você estiver copiando dados para um armazenamento de dados do Azure, veja [Intervalos de IP do data center do Azure](https://www.microsoft.com/download/details.aspx?id=41653) para obter os endereços IP de computação e os intervalos de SQL usados pelos data centers do Azure.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Amazon Redshift.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Amazon Redshift:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AmazonRedshift** | Sim |
| server |Endereço IP ou nome do host do servidor Amazon Redshift. |Sim |
| port |O número da porta TCP usada pelo servidor Amazon Redshift para ouvir conexões de cliente. |Não, o padrão é 5439 |
| database |Nome do banco de dados do Amazon Redshift. |Sim |
| username |Nome de usuário que tem acesso ao banco de dados. |Sim |
| password |Senha para a conta de usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

**Exemplo:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Amazon Redshift.

Para copiar dados do Amazon Redshift, defina a propriedade type do conjunto de dados como **RelationalTable**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **RelationalTable** | Sim |
| tableName | Nome da tabela no Amazon Redshift. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do Amazon Redshift.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift como fonte

Para copiar dados do Amazon Redshift, defina o tipo de fonte na atividade de cópia como **AmazonRedshiftSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **AmazonRedshiftSource** | Sim |
| query |Utiliza a consulta personalizada para ler os dados. Por exemplo: select * from MyTable. |Não (se "tableName" no conjunto de dados for especificado) |
| redshiftUnloadSettings | Grupo de propriedades ao usar UNLOAD do Amazon Redshift. | Não |
| s3LinkedServiceName | Refere-se a um Amazon S3 que será usado como um repositório provisório especificando um nome de serviço vinculado do tipo "AmazonS3". | Sim se estiver usando UNLOAD |
| bucketName | Indique o bucket S3 para armazenar os dados intermediários. Se não for fornecido, o serviço Data Factory o gerará automaticamente.  | Sim se estiver usando UNLOAD |

**Exemplo: Fonte Amazon Redshift na atividade de cópia usando UNLOAD**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Saiba mais sobre como usar o UNLOAD para copiar dados do Amazon Redshift com eficiência na próxima seção.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Usar UNLOAD para copiar dados do Amazon Redshift

[UNLOAD](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) é um mecanismo fornecido pelo Amazon Redshift, que pode descarregar os resultados de uma consulta para um ou mais arquivos no Amazon S3 (Serviço de Armazenamento Simples da Amazon). É a maneira recomendada pela Amazon para copiar um conjunto de dados grande do Redshift.

**Exemplo: copiar dados do Amazon Redshift para o SQL Data Warehouse do Azure usando UNLOAD, cópia em etapas e PolyBase**

Nesse caso de uso de exemplo, a atividade de cópia descarrega os dados do Amazon Redshift para o Amazon S3 conforme configurado em "redshiftUnloadSettings" e, em seguida, copia os dados do Amazon S3 para o Blob do Azure como especificado em "stagingSettings", por fim, usa o PolyBase para carregar os dados no SQL Data Warehouse. Todos os formatos provisórios são adequadamente tratados pela atividade de cópia.

![Redshift para o fluxo de trabalho de cópia do SQL DW](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapeamento de tipo de dados para o Amazon Redshift

Ao copiar dados do Amazon Redshift, os seguintes mapeamentos são usados de tipos de dados do Amazon Redshift para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do Amazon Redshift | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |String |
| CHAR |String |
| DATE |DateTime |
| DECIMAL |Decimal |
| DOUBLE PRECISION |Double |
| INTEGER |Int32 |
| REAL |Single |
| SMALLINT |Int16 |
| TEXT |String |
| TIMESTAMP |DateTime |
| VARCHAR |String |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
