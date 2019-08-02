---
title: Copiar dados de e para o Oracle usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de armazenamentos de origem com suporte para um banco de dados Oracle ou do Oracle para armazenamentos de coletor com suporte, usando o Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: jingwang
ms.openlocfilehash: 04f623a889a87c325b1f53e3b39656ca4b703961
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509239"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copiar dados de e para o Oracle usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-oracle-connector.md)
> * [Versão atual](connector-oracle.md)

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para copiar dados de e para um banco de dados Oracle. Ele amplia o artigo [Visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um banco de dados Oracle para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer repositório de dados de fonte com suporte para um banco de dados Oracle. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector Oracle dá suporte a:

- As seguintes versões do banco de dados Oracle:
  - Oracle 12c R1 (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)
- Cópia de dados usando **básicas** ou **OID** autenticações.
- Cópia paralela de origem Oracle. Ver [cópia do Oracle paralela](#parallel-copy-from-oracle) seção com detalhes.

> [!Note]
> Não há suporte para servidor proxy do Oracle.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de e para um banco de dados Oracle que não esteja acessível publicamente, você precisará configurar um Integration Runtime auto-hospedado. Para obter mais informações sobre o integration, runtime consulte [integration runtime auto-hospedado](create-self-hosted-integration-runtime.md). O integration runtime fornece um driver interno do Oracle. Portanto, você não precisa instalar manualmente um driver quando você copia dados de e para Oracle.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector Oracle.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Oracle.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Oracle**. | Sim |
| connectionString | Especifica as informações necessárias para se conectar à instância do Banco de Dados Oracle. <br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar uma senha no Azure Key Vault e extrair a configuração `password` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) que oferece mais detalhes. <br><br>**Tipo de conexão compatível**: você pode usar **Oracle SID** ou **Oracle Service Name** para identificar o banco de dados:<br>-Se você usar a SID:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Se você usar Service Name:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime auto-hospedado ou o Integration Runtime do Azure (se seu armazenamento de dados estiver publicamente acessível). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

>[!TIP]
>Se você atingir o erro "ORA-01025: parâmetro UPI fora do intervalo" e o Oracle for versão 8i, adicione `WireProtocolMode=1` à cadeia de conexão e tente novamente.

**Para habilitar a criptografia na conexão do Oracle**, há duas opções:

1.  Para usar **criptografia DES triplo (3DES) e criptografia AES** , no lado do servidor Oracle, vá para Oracle Advanced Security (OAS) e defina as configurações de criptografia, consulte os detalhes [aqui](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). O conector ADF Oracle negocia automaticamente o método de criptografia para usar aquele que você configura no OAS ao estabelecer a conexão com o Oracle.

2.  Para usar **SSL**, siga as etapas abaixo:

    1.  Obter informações do certificado SSL. Obter as informações de certificado codificado em DER do seu certificado SSL e salvar a saída (---Begin Certificate... End Certificate---) como um arquivo de texto.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exemplo:** extrair informações de certificado de DERcert.cer; em seguida, salvar a saída em cert.tx

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Crie o repositório de chaves ou truststore. O comando a seguir cria o arquivo truststore com ou sem uma senha no formato PKCS 12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exemplo:** cria um arquivo de truststore PKCS12 denominado MyTrustStoreFile com uma senha

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Coloque o arquivo truststore no computador do IR auto-hospedado, por exemplo, no C:\MyTrustStoreFile.
    4.  Em ADF, configure a cadeia de conexão do Oracle com `EncryptionMethod=1` e o correspondente `TrustStore` / `TrustStorePassword`valor, por exemplo, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Exemplo:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a senha no Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
            "password": { 
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
## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Oracle.

Para copiar dados do e para o Oracle, defina a propriedade type do conjunto de dados como **OracleTable**. Há suporte para as seguintes propriedades.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como **OracleTable**. | Sim |
| tableName |O nome da tabela no banco de dados Oracle à qual o serviço vinculado se refere. | Sim |

**Exemplo:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor Oracle.

### <a name="oracle-as-a-source-type"></a>Oracle como um tipo de fonte

> [!TIP]
>
> Saiba mais sobre [cópia do Oracle paralela](#parallel-copy-from-oracle) seção sobre como carregar dados do Oracle com eficiência usando o particionamento de dados.

Para copiar dados do Oracle, defina o tipo de fonte na atividade de cópia como **OracleSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **OracleSource**. | Sim |
| oracleReaderQuery | Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`.<br>Quando você habilita a carga particionada, você precisa associar parâmetros de partição interna correspondente em sua consulta. Veja exemplos na [cópia do Oracle paralela](#parallel-copy-from-oracle) seção. | Não |
| partitionOptions | Especifica os opções usadas para carregar dados do Oracle de particionamento de dados. <br>Permitir que os valores são: **None** (padrão), **PhysicalPartitionsOfTable** e **DynamicRange**.<br>Quando a opção de partição está habilitada (não ' None'), também configure **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** definir na atividade de cópia, por exemplo, como 4, que determina o grau de paralelo carreguem dados simultaneamente do Oracle banco de dados. | Não |
| partitionSettings | Especifique o grupo, as configurações de particionamento de dados. <br>Se aplicam quando a opção de partição não é `None`. | Não |
| partitionNames | A lista de partições físicas que precisam ser copiados. <br>Se aplicam quando a opção de partição é `PhysicalPartitionsOfTable`. Se você usar a consulta para recuperar dados de origem, de gancho `?AdfTabularPartitionName` na cláusula WHERE. Consulte o exemplo [cópia do Oracle paralela](#parallel-copy-from-oracle) seção. | Não |
| partitionColumnName | Especifique o nome da coluna de origem **no tipo de inteiro** que será usado por meio do particionamento de intervalo para a cópia paralela. Se não for especificado, a chave primária da tabela será automaticamente detectado e usado como a coluna de partição. <br>Se aplicam quando a opção de partição é `DynamicRange`. Se você usar a consulta para recuperar dados de origem, de gancho `?AdfRangePartitionColumnName` na cláusula WHERE. Consulte o exemplo [cópia do Oracle paralela](#parallel-copy-from-oracle) seção. | Não |
| partitionUpperBound | Valor máximo da coluna de partição para copiar dados. <br>Se aplicam quando a opção de partição é `DynamicRange`. Se você usar a consulta para recuperar dados de origem, de gancho `?AdfRangePartitionUpbound` na cláusula WHERE. Consulte o exemplo [cópia do Oracle paralela](#parallel-copy-from-oracle) seção. | Não |
| PartitionLowerBound | Valor mínimo da coluna de partição para copiar dados. <br>Se aplicam quando a opção de partição é `DynamicRange`. Se você usar a consulta para recuperar dados de origem, de gancho `?AdfRangePartitionLowbound` na cláusula WHERE. Consulte o exemplo [cópia do Oracle paralela](#parallel-copy-from-oracle) seção. | Não |

**Exemplo: copiar dados usando uma consulta básica sem partição**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ver mais exemplos na [cópia do Oracle paralela](#parallel-copy-from-oracle) seção.

### <a name="oracle-as-a-sink-type"></a>Oracle como um tipo de coletor

Para copiar dados para o Oracle, defina o tipo de coletor na atividade de cópia como **OracleSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | O tipo de propriedade do coletor de atividade de cópia deve ser definido como **OracleSink**. | Sim |
| writeBatchSize | Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize.<br/>Os valores permitidos são inteiro (número de linhas). |Não (o padrão é 10.000) |
| writeBatchTimeout | Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são período. Um exemplo é 00:30:00 (30 minutos). | Não |
| preCopyScript | Especifique uma consulta SQL para a atividade de cópia, a ser executada antes de gravar dados no Oracle em cada execução. Você pode usar essa propriedade para limpar os dados previamente carregados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Cópia paralela do Oracle

Conector do Oracle de fábrica de dados fornece para copiar dados do Oracle em paralelo com o excelente desempenho de particionamento de dados internos. Você pode encontrar as opções de particionamento de dados na atividade de cópia -> fonte Oracle:

![Opções de partição](./media/connector-oracle/connector-oracle-partition-options.png)

Quando você habilita a cópia particionada, o data factory executa consultas em paralelo em relação à sua fonte Oracle para carregar dados por partições. O grau de paralelo é configurado e controlado por meio **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** definindo na atividade de cópia. Por exemplo, se você definir `parallelCopies` como quatro, fábrica de dados simultaneamente gera e executa quatro consultas com base em sua opção de partição especificada e configurações, cada parte de recuperação de dados do banco de dados Oracle.

São sugeridas para habilitar cópia paralela com especialmente quando você carregar grandes quantidades de dados do banco de dados Oracle de particionamento de dados. A seguir estão as configurações sugeridas para cenários diferentes:

| Cenário                                                     | Configurações sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carregamento completo de uma tabela grande com partições físicas          | **Opção de partição**: Partições físicas da tabela. <br><br/>Durante a execução, o data Factory automaticamente detecta as partições físicas e copia os dados por partições. |
| Carregamento completo de uma tabela grande sem partições físicas, embora com uma coluna de inteiro para o particionamento de dados | **Opções de partição**: Partição de intervalo dinâmico.<br>**Coluna de partição**: Especifique a coluna usada para particionar dados. Se não for especificada, o principal de coluna de chave é usada. |
| Carregar grandes quantidades de dados usando uma consulta personalizada, abaixo com partições físicas | **Opção de partição**: Partições físicas da tabela.<br>**Consulta**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Nome da partição**: Especifique os nomes de partição para copiar dados do. Se não for especificado, o ADF detectará automaticamente as partições físicas na tabela especificada no conjunto de dados Oracle.<br><br>Durante a execução, a substituição de fábrica de dados `?AdfTabularPartitionName` com o nome real da partição e o enviará ao Oracle. |
| Carregar grandes quantidades de dados usando uma consulta personalizada, abaixo sem partições físicas ao mesmo tempo com uma coluna de inteiro para o particionamento de dados | **Opções de partição**: Partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: Especifique a coluna usada para particionar dados. Você pode particionar em relação à coluna com tipo de dados inteiro.<br>**Limite superior de partição** e **limite inferior de partição**: Especifique se você deseja filtrar em relação à coluna de partição para recuperar apenas os dados entre o intervalo inferior e superior.<br><br>Durante a execução, data factory replace `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, e `?AdfRangePartitionLowbound` com o nome real da coluna e valor intervalos para cada partição e enviar para o Oracle. <br>Por exemplo, se sua coluna de partição "ID" definido com o limite inferior como 1 e o limite superior como 80, com o conjunto de cópia paralela como 4, o ADF recuperar dados por 4 partições com a ID entre [1,20], [21, 40], [41, 60] e [61, 80]. |

**Exemplo: consulta com a partição física**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Exemplo: consulta com a partição de intervalo dinâmico**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Mapeamento de tipo de dados para o Oracle

Ao copiar dados do e para o Oracle, os seguintes mapeamentos são usados de tipos de dados do Oracle para tipos de dados provisórios do Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do Oracle | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(só tem suporte no Oracle 10g e superior) |
| CHAR |String |
| CLOB |String |
| DATE |Datetime |
| FLOAT |Decimal, String (se precisão > 28) |
| INTEGER |Decimal, String (se precisão > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, String (se precisão > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Os tipo de dados INTERVAL YEAR TO MONTH e INTERVAL DAY TO SECOND não têm suporte.


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
