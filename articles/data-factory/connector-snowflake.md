---
title: Copiar e transformar dados em floco de neve
description: Saiba como copiar e transformar dados em floco de neve usando Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: 816c9ae25034382763e18ea61055a2a18ccc03d6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388831"
---
# <a name="copy-and-transform-data-in-snowflake-by-using-azure-data-factory"></a>Copie e transforme dados em floco de neve usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de e para floco de neve e usar o fluxo de dados para transformar dados em floco de neve. Para obter mais informações sobre Data Factory, consulte o [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do floco de neve tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com uma tabela de [matriz de origem/coletor com suporte](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Para a atividade de cópia, este conector de floco de neve dá suporte às seguintes funções:

- Copie dados do floco de neve que utiliza a cópia do floco de neve [no comando [Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) para obter o melhor desempenho.
- Copie dados para flocos de neve que aproveitam a cópia do floco de neve [no comando [Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) para obter o melhor desempenho. Ele dá suporte a flocos de neve no Azure. 

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que definem Data Factory entidades específicas para um conector floco de neve.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para um serviço vinculado a floco de neve.

| Propriedade         | Descrição                                                  | Obrigatório |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | A propriedade Type deve ser definida como **floco de neve**.              | Sim      |
| connectionString | Especifica as informações necessárias para se conectar à instância de floco de neve. Você pode optar por colocar a senha ou a cadeia de conexão inteira em Azure Key Vault. Consulte os exemplos abaixo da tabela, bem como as credenciais de [armazenamento no artigo Azure Key Vault](store-credentials-in-key-vault.md) , para obter mais detalhes.<br><br>Algumas configurações típicas:<br>- **Nome da conta:** O  [nome da conta completa](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) da sua conta do floco de neve (incluindo segmentos adicionais que identificam a região e a plataforma de nuvem), por exemplo, xy12345. leste-US-2. Azure.<br/>- **Nome de usuário:** O nome de logon do usuário para a conexão.<br>- **Senha:** A senha do usuário.<br>- **Banco de dados:** O banco de dados padrão a ser usado uma vez conectado. Deve ser um banco de dados existente para o qual a função especificada tenha privilégios.<br>- **Depósito:** O depósito virtual a ser usado uma vez conectado. Deve ser um depósito existente para o qual a função especificada tenha privilégios.<br>- **Função:** A função de controle de acesso padrão a ser usada na sessão floco de neve. A função especificada deve ser uma função existente que já tenha sido atribuída ao usuário especificado. A função padrão é PUBLIC. | Sim      |
| connectVia       | O [Integration Runtime](concepts-integration-runtime.md) que é usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usará o tempo de execução de integração do Azure padrão. | Não       |

**Exemplo:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
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

**Senha no Azure Key Vault:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). 

As propriedades a seguir têm suporte para o conjunto de itens floco de neve.

| Propriedade  | Descrição                                                  | Obrigatório                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | A propriedade Type do conjunto de conjuntos deve ser definida como **floco de neve**. | Sim                         |
| esquema | Nome do esquema. Observe que o nome do esquema diferencia maiúsculas de minúsculas no ADF. |Não para fonte, sim para coletor  |
| tabela | Nome da tabela/exibição. Observe que o nome da tabela diferencia maiúsculas de minúsculas no ADF. |Não para fonte, sim para coletor  |

**Exemplo:**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do floco de neve.

### <a name="snowflake-as-the-source"></a>Floco de neve como a origem

O conector floco de neve utiliza a cópia do floco de Tróia [no comando [Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) para obter o melhor desempenho.

Se o armazenamento de dados do coletor e o formato tiverem suporte nativo do comando de cópia floco de neve, você poderá usar a atividade de cópia para copiar diretamente do floco de neve para o coletor. Para obter detalhes, consulte [cópia direta do floco de neve](#direct-copy-from-snowflake). Caso contrário, use a [cópia de preparo interna do floco de neve](#staged-copy-from-snowflake).

Para copiar dados do floco de neve, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia.

| Propriedade                     | Descrição                                                  | Obrigatório |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | A propriedade Type da fonte da atividade de cópia deve ser definida como **floco de neve**. | Sim      |
| Consulta          | Especifica a consulta SQL para ler dados do floco de neve. Se os nomes do esquema, tabela e colunas contiverem letras minúsculas, citar o identificador de objeto na consulta, por exemplo, `select * from "schema"."myTable"` .<br>Não há suporte para a execução do procedimento armazenado. | Não       |
| exportSettings | Configurações avançadas usadas para recuperar dados do floco de neve. Você pode configurar aqueles com suporte no comando copiar para que Data Factory passará quando você invocar a instrução. | Não       |
| ***Em `exportSettings` :*** |  |  |
| tipo | O tipo de comando de exportação, definido como **SnowflakeExportCopyCommand**. | Sim |
| additionalCopyOptions | Opções de cópia adicionais, fornecidas como um dicionário de pares chave-valor. Exemplos: MAX_FILE_SIZE, substituir. Para obter mais informações, consulte [Opções de cópia do floco de neve](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions). | Não |
| additionalFormatOptions | Opções de formato de arquivo adicionais que são fornecidas para copiar comando como um dicionário de pares chave-valor. Exemplos: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Para obter mais informações, consulte [Opções de tipo de formato floco de neve](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions). | Não |

#### <a name="direct-copy-from-snowflake"></a>Cópia direta do floco de neve

Se o armazenamento e o formato de dados do coletor atenderem aos critérios descritos nesta seção, você poderá usar a atividade de cópia para copiar diretamente de floco de neve para o coletor. Data Factory verifica as configurações e falha na execução da atividade de cópia se os critérios a seguir não forem atendidos:

- O **serviço vinculado do coletor** é o [**armazenamento de BLOBs do Azure**](connector-azure-blob-storage.md) com autenticação de **assinatura de acesso compartilhado** . Se desejar copiar dados diretamente para Azure Data Lake Storage Gen2 no formato com suporte a seguir, você poderá criar um serviço vinculado de blob do Azure com a autenticação SAS em sua conta de ADLS Gen2, para evitar o uso [da cópia em etapas do floco de neve](#staged-copy-from-snowflake).

- O **formato de dados do coletor** é de **parquet**, **texto delimitado** ou **JSON** com as seguintes configurações:

    - Para o formato **parquet** , o codec de compactação é **None**, **snapshot** ou **LZO**.
    - Para o formato de **texto delimitado** :
        - `rowDelimiter` é **\r\n** ou qualquer caractere único.
        - `compression` pode ser **sem compactação**, **gzip**, **bzip2** ou **deflate**.
        - `encodingName` é deixado como padrão ou definido como **utf-8**.
        - `quoteChar` é **aspas duplas**, aspas **simples** ou **cadeia de caracteres vazia** (sem caractere de aspas).
    - Para o formato **JSON** , a cópia direta só dá suporte ao caso de a tabela floco de neve de origem ou o resultado da consulta tem apenas uma única coluna e o tipo de dados dessa coluna é **Variant**, **Object** ou **array**.
        - `compression` pode ser **sem compactação**, **gzip**, **bzip2** ou **deflate**.
        - `encodingName` é deixado como padrão ou definido como **utf-8**.
        - `filePattern` no coletor de atividade de cópia é deixado como padrão ou definido como **setOfObjects**.
- Na origem da atividade de cópia, `additionalColumns` não está especificado.
- O mapeamento de coluna não foi especificado.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MYTABLE",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Cópia em etapas do floco de neve

Quando o formato ou armazenamento de dados do coletor não for nativamente compatível com o comando de cópia do floco de neve, conforme mencionado na última seção, habilite a cópia em etapas interna usando uma instância provisória do armazenamento de BLOBs do Azure. O recurso de cópia preparada também oferece melhor rendimento. Data Factory exporta dados do floco de neve para o armazenamento de preparo, depois copia os dados para o coletor e, por fim, limpa os dados temporários do armazenamento de preparo. Confira [cópia em etapas](copy-activity-performance-features.md#staged-copy) para obter detalhes sobre como copiar dados usando o preparo.

Para usar esse recurso, crie um [serviço vinculado do armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#linked-service-properties) que se refere à conta de armazenamento do Azure como o preparo provisório. Em seguida, especifique as `enableStaging` `stagingSettings` Propriedades e na atividade de cópia.

> [!NOTE]
> O serviço vinculado do armazenamento de BLOBs do Azure de preparo deve usar a autenticação de assinatura de acesso compartilhado, conforme exigido pelo comando de cópia floco de neve. 

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="snowflake-as-sink"></a>Floco de neve como coletor

O conector do floco de neve utiliza a cópia do floco de neve [no comando [Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) para obter o melhor desempenho. Ele dá suporte à gravação de dados em flocos de neve no Azure.

Se o armazenamento e o formato de dados de origem tiverem suporte nativo pelo comando de cópia floco de neve, você poderá usar a atividade de cópia para copiar diretamente da origem para o floco de neve. Para obter detalhes, consulte [cópia direta para floco de neve](#direct-copy-to-snowflake). Caso contrário, use [a cópia de preparo interna para floco de neve](#staged-copy-to-snowflake).

Para copiar dados para floco de neve, as propriedades a seguir têm suporte na seção **coletor** de atividade de cópia.

| Propriedade          | Descrição                                                  | Obrigatório                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | A propriedade Type do coletor da atividade de cópia, definida como **SnowflakeSink**. | Sim                                           |
| preCopyScript     | Especifique uma consulta SQL para que a atividade de cópia seja executada antes de gravar dados em floco de neve em cada execução. Use essa propriedade para limpar os dados pré-carregados. | Não                                            |
| importSettings | Configurações avançadas usadas para gravar dados em floco de neve. Você pode configurar aqueles com suporte no comando copiar para que Data Factory passará quando você invocar a instrução. | Não |
| ***Em `importSettings` :*** |                                                              |  |
| tipo | O tipo de comando de importação, definido como **SnowflakeImportCopyCommand**. | Sim |
| additionalCopyOptions | Opções de cópia adicionais, fornecidas como um dicionário de pares chave-valor. Exemplos: ON_ERROR, FORCE LOAD_UNCERTAIN_FILES. Para obter mais informações, consulte [Opções de cópia do floco de neve](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions). | Não |
| additionalFormatOptions | Opções de formato de arquivo adicionais fornecidas para o comando de cópia, fornecidas como um dicionário de pares chave-valor. Exemplos: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Para obter mais informações, consulte [Opções de tipo de formato floco de neve](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions). | Não |

#### <a name="direct-copy-to-snowflake"></a>Cópia direta para floco de neve

Se o armazenamento e o formato de dados de origem atenderem aos critérios descritos nesta seção, você poderá usar a atividade de cópia para copiar diretamente da origem para o floco de neve. Azure Data Factory verifica as configurações e falha na execução da atividade de cópia se os critérios a seguir não forem atendidos:

- O **serviço vinculado de origem** é o [**armazenamento de BLOBs do Azure**](connector-azure-blob-storage.md) com autenticação de **assinatura de acesso compartilhado** . Se desejar copiar dados diretamente de Azure Data Lake Storage Gen2 no seguinte formato com suporte, você poderá criar um serviço vinculado de blob do Azure com autenticação SAS em sua conta de ADLS Gen2, para evitar o uso  [de cópia em etapas para floco de neve](#staged-copy-to-snowflake).

- O **formato de dados de origem** é **parquet**, **texto delimitado** ou **JSON** com as seguintes configurações:

    - Para o formato **parquet** , o codec de compactação é **None** ou **encaixado**.

    - Para o formato de **texto delimitado** :
        - `rowDelimiter` é **\r\n** ou qualquer caractere único. Se o delimitador de linha não for "\r\n", `firstRowAsHeader` precisará ser **falso** e `skipLineCount` não for especificado.
        - `compression` pode ser **sem compactação**, **gzip**, **bzip2** ou **deflate**.
        - `encodingName` é deixado como padrão ou definido como "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "BIG5", "EUC-JP", "EUC-KR", "GB18030", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255".
        - `quoteChar` é **aspas duplas**, aspas **simples** ou **cadeia de caracteres vazia** (sem caractere de aspas).
    - Para o formato **JSON** , a cópia direta dá suporte apenas ao caso em que a tabela floco de neve do coletor tem apenas uma única coluna e o tipo de dados dessa coluna é **Variant**, **Object** ou **array**.
        - `compression` pode ser **sem compactação**, **gzip**, **bzip2** ou **deflate**.
        - `encodingName` é deixado como padrão ou definido como **utf-8**.
        - O mapeamento de coluna não foi especificado.

- Na fonte da atividade de cópia: 

   -  `additionalColumns` não está especificado.
   - Se sua origem for uma pasta, `recursive` será definida como true.
   - `prefix`, `modifiedDateTimeStart`, `modifiedDateTimeEnd` e `enablePartitionDiscovery` não foram especificados.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Cópia em etapas para floco de neve

Quando o formato ou armazenamento de dados de origem não é compatível nativamente com o comando de cópia do floco de neve, conforme mencionado na última seção, habilite a cópia em etapas interna usando uma instância provisória do armazenamento de BLOBs do Azure. O recurso de cópia preparada também oferece melhor rendimento. Data Factory converte automaticamente os dados para atender aos requisitos de formato de dados do floco de neve. Em seguida, ele invoca o comando de cópia para carregar dados em floco de neve. Finalmente, ele limpa seus dados temporários do armazenamento de blobs. Confira [cópia em etapas](copy-activity-performance-features.md#staged-copy) para obter detalhes sobre como copiar dados usando o preparo.

Para usar esse recurso, crie um [serviço vinculado do armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#linked-service-properties) que se refere à conta de armazenamento do Azure como o preparo provisório. Em seguida, especifique as `enableStaging` `stagingSettings` Propriedades e na atividade de cópia.

> [!NOTE]
> O serviço vinculado do armazenamento de BLOBs do Azure de preparo precisa usar a autenticação de assinatura de acesso compartilhado, conforme exigido pelo comando de cópia floco de neve.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

Ao transformar dados no fluxo de dados de mapeamento, você pode ler e gravar em tabelas em floco de neve. Para obter mais informações, confira [transformação de origem](data-flow-source.md) e [transformação do coletor](data-flow-sink.md) nos fluxos de dados de mapeamento. Você pode optar por usar um conjunto de linhas de floco de neve ou um conjunto de uma [embutido](data-flow-source.md#inline-datasets) como fonte e tipo de coletor.

### <a name="source-transformation"></a>Transformação de origem

A tabela abaixo lista as propriedades com suporte pela fonte floco de neve. Você pode editar essas propriedades na guia **Opções de origem** . O conector utiliza a [transferência de dados interna](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)de floco de neve.

| Nome | Descrição | Necessária | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabela | Se você selecionar tabela como entrada, o fluxo de dados irá buscar todos os dados da tabela especificada no conjunto de informações do floco de neve ou nas opções de origem ao usar o conjunto de dados embutido. | Não | String | *(somente para conjunto de linhas embutido)*<br>tableName<br>schemaName |
| Consulta | Se você selecionar consulta como entrada, insira uma consulta para buscar dados do floco de neve. Essa configuração substitui qualquer tabela que você escolheu no conjunto de um.<br>Se os nomes do esquema, tabela e colunas contiverem letras minúsculas, citar o identificador de objeto na consulta, por exemplo, `select * from "schema"."myTable"` . | Não | String | Consulta |

#### <a name="snowflake-source-script-examples"></a>Exemplos de script de origem de floco de neve

Quando você usa o conjunto de dados de floco de neve como um tipo de origem, o script de fluxo associado é:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'select * from MYTABLE',
    format: 'query') ~> SnowflakeSource
```

Se você usar o conjunto de dados embutido, o script de fluxo do data associado será:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'select * from MYTABLE',
    store: 'snowflake') ~> SnowflakeSource
```

### <a name="sink-transformation"></a>Transformação de coletor

A tabela abaixo lista as propriedades com suporte pelo coletor de floco de neve. Você pode editar essas propriedades na guia **configurações** . Ao usar o conjunto de linhas embutido, você verá configurações adicionais, que são iguais às propriedades descritas na seção [Propriedades do conjunto](#dataset-properties) de cores. O conector utiliza a [transferência de dados interna](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)de floco de neve.

| Nome | Descrição | Necessária | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Método Update | Especifique quais operações são permitidas em seu destino de floco de neve.<br>Para atualizar, upsertr ou excluir linhas, uma [transformação ALTER Row](data-flow-alter-row.md) é necessária para marcar linhas para essas ações. | Sim | `true` ou `false` | pode ser excluído <br/>Insertable <br/>atualizável <br/>upsertable |
| Colunas de chaves | Para atualizações, upserts e exclusões, é necessário selecionar uma coluna de chave ou colunas para determinar qual linha alterar. | Não | Array | chaves |
| Ação tabela | Determina se deve-se recriar ou remover todas as linhas da tabela de destino antes da gravação.<br>- **Nenhum**: nenhuma ação será feita para a tabela.<br>- **Recriar**: a tabela será descartada e recriada. Necessário ao criar uma tabela dinamicamente.<br>- **Truncar**: todas as linhas da tabela de destino serão removidas. | Não | `true` ou `false` | recriar<br/>truncate |

#### <a name="snowflake-sink-script-examples"></a>Exemplos de script de coletor de floco de neve

Quando você usa o conjunto de dados de floco de neve como tipo de coletor, o script de fluxo associado é:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:false,
    keys:['movieId'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

Se você usar o conjunto de dados embutido, o script de fluxo do data associado será:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    tableName: 'table',
    schemaName: 'schema',
    deletable: true,
    insertable: true,
    updateable: true,
    upsertable: false,
    store: 'snowflake',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para obter mais informações sobre as propriedades, consulte [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores por atividade de cópia em Data Factory, consulte [armazenamentos de dados e formatos com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
