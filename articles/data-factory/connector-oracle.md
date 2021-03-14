---
title: Copiar dados de e para o Oracle usando o Azure Data Factory
description: Saiba como copiar dados de armazenamentos de origem com suporte para um Oracle Database ou do Oracle para repositórios de coletor com suporte, usando Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/20/2021
ms.author: jingwang
ms.openlocfilehash: ebafac024593767e884be908acbf0efb9ead50e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703296"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copiar dados de e para o Oracle usando o Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-oracle-connector.md)
> * [Versão atual](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para um banco de dado Oracle. Ele se baseia na [visão geral da atividade de cópia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Oracle tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados Oracle para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer repositório de dados de fonte com suporte para um banco de dados Oracle. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector Oracle oferece suporte a:

- As seguintes versões de um banco de dados Oracle:
    - Oracle 19C R1 (19,1) e superior
    - Oracle 18C R1 (18,1) e superior
    - Oracle 12c R1 (12,1) e superior
    - Oracle 11g R1 (11,1) e superior
    - Oracle 10g R1 (10,1) e superior
    - Oracle 9i R2 (9,2) e superior
    - Oracle 8i R3 (8.1.7) e superior
    - Oracle Database serviço Cloud Exadata
- Cópia paralela de uma origem do Oracle. Consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) para obter detalhes.

> [!Note]
> Não há suporte para servidor proxy do Oracle.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

O integration runtime fornece um driver interno do Oracle. Portanto, você não precisa instalar manualmente um driver quando você copia dados de e para Oracle.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector Oracle.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

O serviço vinculado do Oracle oferece suporte às seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como **Oracle**. | Sim |
| connectionString | Especifica as informações necessárias para se conectar à instância do Banco de Dados Oracle. <br/>Você também pode colocar uma senha em Azure Key Vault e extrair a `password` configuração da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais em Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. <br><br>**Tipo de conexão com suporte**: você pode optar por usar a **Oracle SID** ou o **Oracle Service Name** para identificar o banco de dados:<br>-Se você usar a SID:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Se você usar Service Name:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Para opções avançadas de conexão nativa da Oracle, você pode optar por adicionar uma entrada em [arquivo tnsnames. ORA](http://www.orafaq.com/wiki/Tnsnames.ora) no servidor Oracle e, no serviço vinculado do Oracle do ADF, escolha usar o tipo de conexão do nome do serviço Oracle e configure o nome do serviço correspondente. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos](#prerequisites). Se não especificado, o Azure Integration Runtime padrão será usado. |Não |

>[!TIP]
>Se você receber um erro, "ORA-01025: parâmetro UPI fora do intervalo" e a versão do Oracle for 8i, adicione `WireProtocolMode=1` à sua cadeia de conexão. Em seguida, tente novamente.

Se você tiver várias instâncias do Oracle para o cenário de failover, poderá criar o serviço vinculado do Oracle e preencher o host primário, a porta, o nome de usuário, a senha, etc. e adicionar uma nova "**Propriedades de conexão adicionais**" com o nome de propriedade como `AlternateServers` e o valor como `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)` -não perca os colchetes e preste atenção aos dois-pontos ( `:` ) como separador. Por exemplo, o seguinte valor de servidores alternativos define dois servidores de banco de dados alternativos para failover de conexão: `(HostName=AccountingOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)` .

Mais propriedades de conexão que você pode definir na cadeia de conexão por seu caso:

| Propriedade | Descrição | Valores permitidos |
|:--- |:--- |:--- |
| ArraySize |O número de bytes que o conector pode buscar em uma única viagem de ida e volta da rede. Por exemplo, `ArraySize=‭10485760‬` .<br/><br/>Valores maiores aumentam a taxa de transferência, reduzindo o número de vezes para buscar dados na rede. Valores menores aumentam o tempo de resposta, pois há menos de um atraso aguardando o servidor transmitir dados. | Um número inteiro de 1 a 4294967296 (4 GB). O valor padrão é `60000`. O valor 1 não define o número de bytes, mas indica alocar espaço para exatamente uma linha de dados. |

Para habilitar a criptografia na conexão do Oracle, há duas opções:

-   Para usar **criptografia triple-DES (3DES) e criptografia AES (AES)**, no lado do servidor Oracle, vá para Oracle Advanced Security (OAS) e defina as configurações de criptografia. Para obter detalhes, consulte esta [documentação da Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). O conector do AAD (estrutura de desenvolvimento de aplicativos) da Oracle negocia automaticamente o método de criptografia para usar aquele que você configurar no OAS ao estabelecer uma conexão com o Oracle.

-   Para usar o **TLS**:

    1.  Obtenha as informações do certificado TLS/SSL. Obtenha as informações de certificado codificado em Distinguished Encoding Rules (DER) do seu certificado TLS/SSL e salve a saída (-----iniciar certificado... End Certificate---) como um arquivo de texto.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exemplo:** Extraia informações de certificado de DERcert. cer e salve a saída em cert.txt.

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
    
    2.  Crie o `keystore` ou o `truststore` . O comando a seguir cria o `truststore` arquivo, com ou sem uma senha, no formato PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exemplo:** Crie um `truststore` arquivo PKCS12, chamado MyTrustStoreFile, com uma senha.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Coloque o `truststore` arquivo no computador ir auto-hospedado. Por exemplo, coloque o arquivo em C:\MyTrustStoreFile.
    4.  Em Azure data Factory, configure a cadeia de conexão Oracle com `EncryptionMethod=1` e o `TrustStore` / `TrustStorePassword` valor correspondente. Por exemplo, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Exemplo:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
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

Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Oracle. Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira [Conjuntos de dados](concepts-datasets-linked-services.md). 

Para copiar dados de e para o Oracle, defina a propriedade Type do conjunto como `OracleTable` . Há suporte para as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como `OracleTable` . | Sim |
| esquema | Nome do esquema. |Não para fonte, Sim para o coletor  |
| tabela | Nome da tabela/exibição. |Não para fonte, Sim para o coletor  |
| tableName | Nome da tabela/exibição com esquema. Essa propriedade é compatível com versões anteriores. Para uma nova carga de trabalho, use `schema` e `table`. | Não para fonte, Sim para o coletor |

**Exemplo:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor Oracle. Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja [Pipelines](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle como fonte

>[!TIP]
>Para carregar dados do Oracle com eficiência usando o particionamento de dados, saiba mais em [cópia paralela do Oracle](#parallel-copy-from-oracle).

Para copiar dados do Oracle, defina o tipo de fonte na atividade de cópia como `OracleSource` . As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como `OracleSource` . | Sim |
| oracleReaderQuery | Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`.<br>Ao habilitar a carga particionada, você precisa vincular quaisquer parâmetros de partição internos correspondentes em sua consulta. Para obter exemplos, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |
| partitionOptions | Especifica as opções de particionamento de dados usadas para carregar dados do Oracle. <br>Os valores permitidos são: **None** (padrão), **PhysicalPartitionsOfTable** e **DynamicRange**.<br>Quando uma opção de partição está habilitada (ou seja, não `None` ), o grau de paralelismo para carregar dados simultaneamente de um banco de dado Oracle é controlado pela [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) configuração na atividade de cópia. | Não |
| partitionSettings | Especifique o grupo de configurações para o particionamento de dados. <br>Aplicar quando a opção de partição não estiver `None` . | Não |
| partições | A lista de partições físicas que precisam ser copiadas. <br>Aplicar quando a opção de partição for `PhysicalPartitionsOfTable`. Se você usar uma consulta para recuperar os dados de origem, conecte `?AdfTabularPartitionName` na cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |
| partitionColumnName | Especifique o nome da coluna de origem **no tipo de inteiro** que será usado pelo particionamento de intervalo para cópia paralela. Se não for especificado, a chave primária da tabela será detectada automaticamente e usada como a coluna de partição. <br>Aplicar quando a opção de partição for `DynamicRange`. Se você usar uma consulta para recuperar os dados de origem, conecte-se  `?AdfRangePartitionColumnName` à cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |
| partitionUpperBound | O valor máximo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange`. Se você usar uma consulta para recuperar os dados de origem, conecte `?AdfRangePartitionUpbound` na cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |
| partitionLowerBound | O valor mínimo da coluna de partição para copiar dados. <br>Aplicar quando a opção de partição for `DynamicRange`. Se você usar uma consulta para recuperar os dados de origem, conecte `?AdfRangePartitionLowbound` na cláusula WHERE. Para obter um exemplo, consulte a seção [cópia paralela do Oracle](#parallel-copy-from-oracle) . | Não |

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

### <a name="oracle-as-sink"></a>Oracle como coletor

Para copiar dados para o Oracle, defina o tipo de coletor na atividade de cópia como `OracleSink` . As propriedades a seguir têm suporte na seção **sink** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do coletor da atividade de cópia deve ser definida como `OracleSink` . | Sim |
| writeBatchSize | Insere dados na tabela SQL quando o tamanho do buffer atinge `writeBatchSize` .<br/>Os valores permitidos são inteiro (número de linhas). |Não (o padrão é 10.000) |
| writeBatchTimeout | O tempo de espera para a operação de inserção em lotes a ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são período. Um exemplo é 00:30:00 (30 minutos). | Não |
| preCopyScript | Especifique uma consulta SQL para que a atividade de cópia seja executada antes de gravar dados no Oracle em cada execução. Você pode usar essa propriedade para limpar os dados previamente carregados. | Não |

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

O conector do Data Factory Oracle fornece particionamento de dados interno para copiar dados do Oracle em paralelo. Você pode encontrar opções de particionamento de dados na guia **origem** da atividade de cópia.

![Captura de tela das opções de partição](./media/connector-oracle/connector-oracle-partition-options.png)

Quando você habilita a cópia particionada, o Data Factory executa consultas paralelas em sua origem do Oracle para carregar dados por partições. O grau paralelo é controlado pela configuração do [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) na atividade de cópia. Por exemplo, se você definir `parallelCopies` como quatro, data Factory gera e executa quatro consultas de maneira simultânea com base na opção de partição e nas configurações especificadas, e cada consulta recupera uma parte dos dados do Oracle Database.

É recomendável habilitar a cópia paralela com o particionamento de dados, especialmente quando você carrega grandes quantidades de dados do seu Oracle Database. Veja a seguir as configurações sugeridas para cenários diferentes. Ao copiar dados para o armazenamento de dados baseado em arquivo, recomendamos gravá-los em uma pasta como vários arquivos (apenas especifique o nome da pasta) para obter um desempenho melhor do que gravar em um arquivo.

| Cenário                                                     | Configurações sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga total de uma tabela grande, com partições físicas.          | **Opção de partição**: partições físicas da tabela. <br><br/>Durante a execução, Data Factory detecta automaticamente as partições físicas e copia os dados por partições. |
| Carga total de uma tabela grande, sem partições físicas, enquanto com uma coluna de inteiros para o particionamento de dados. | **Opções de partição**: partição de intervalo dinâmico.<br>**Coluna de partição**: especifique a coluna usada para particionar dados. Se não for especificado, a coluna de chave primária será usada. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, com partições físicas. | **Opção de partição**: partições físicas da tabela.<br>**Consulta**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Nome da partição**: Especifique os nomes de partição dos quais copiar dados. Se não for especificado, Data Factory detectará automaticamente as partições físicas na tabela que você especificou no conjunto de Oracle.<br><br>Durante a execução, Data Factory substitui `?AdfTabularPartitionName` pelo nome real da partição e envia para o Oracle. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, sem partições físicas, enquanto com uma coluna de inteiros para o particionamento de dados. | **Opções de partição**: partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna de partição**: especifique a coluna usada para particionar dados. Você pode particionar em relação à coluna com tipo de dados Integer.<br>Limite **superior** da partição e limite **inferior da partição**: especifique se deseja filtrar a coluna de partição para recuperar dados somente entre o intervalo inferior e superior.<br><br>Durante a execução, Data Factory substitui `?AdfRangePartitionColumnName` , `?AdfRangePartitionUpbound` e `?AdfRangePartitionLowbound` pelo nome real da coluna e os intervalos de valores de cada partição e envia ao Oracle. <br>Por exemplo, se a coluna de partição "ID" for definida com o limite inferior como 1 e o limite superior como 80, com conjunto de cópia paralela como 4, Data Factory recuperará dados por 4 partições. Suas IDs estão entre [1, 20], [21, 40], [41, 60] e [61, 80], respectivamente. |

> [!TIP]
> Ao copiar dados de uma tabela não particionada, você pode usar a opção de partição "intervalo dinâmico" para particionar em uma coluna de inteiros. Se os dados de origem não tiverem esse tipo de coluna, você poderá aproveitar [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) função na consulta de origem para gerar uma coluna e usá-la como coluna de partição.

**Exemplo: consulta com partição física**

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

**Exemplo: consulta com a partição do intervalo dinâmico**

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

Quando você copia dados do e para o Oracle, os mapeamentos a seguir se aplicam. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados de Oracle | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(só tem suporte no Oracle 10g e superior) |
| CHAR |String |
| CLOB |String |
| DATE |Datetime |
| FLOAT |Decimal, cadeia de caracteres (se precisão > 28) |
| INTEGER |Decimal, cadeia de caracteres (se precisão > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER (p,s) |Decimal, Cadeia de caracteres (se p > 28) |
| NÚMERO sem precisão e escala |Double |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| timestamp |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Número |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Os tipo de dados INTERVAL YEAR TO MONTH e INTERVAL DAY TO SECOND não têm suporte.

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
