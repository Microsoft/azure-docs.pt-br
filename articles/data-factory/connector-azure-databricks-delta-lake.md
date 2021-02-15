---
title: Copiar dados de e para Azure Databricks o Delta Lake
description: Saiba como copiar dados de e para Azure Databricks o Delta Lake usando uma atividade de cópia em um pipeline de Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/24/2020
ms.openlocfilehash: bdf71276d59dec9a19e29ae7f49cb92a0512c05a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364232"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>Copiar dados de e para Azure Databricks o Delta Lake usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Azure Databricks Delta Lake. Ele se baseia na [atividade de cópia no artigo Azure data Factory](copy-activity-overview.md) , que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este Azure Databricks conector Delta Lake tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com uma tabela de [matriz de origem/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Em geral, Azure Data Factory dá suporte ao Delta Lake com os seguintes recursos para atender às suas diversas necessidades.

- A atividade de cópia dá suporte Azure Databricks conector Delta Lake para copiar dados de qualquer armazenamento de dados de origem com suporte para Azure Databricks tabela Delta Lake e da tabela Delta Lake para qualquer armazenamento de dados de coletor com suporte. Ele aproveita o cluster do databricks para executar a movimentação de dados, consulte detalhes na [seção pré-requisitos](#prerequisites).
- O [fluxo de dados de mapeamento](concepts-data-flow-overview.md) dá suporte ao [formato Delta](format-delta.md) genérico no armazenamento do Azure como fonte e coletor para ler e gravar arquivos Delta para ETL sem código e é executado em Azure Integration Runtime gerenciados.
- [As atividades do databricks](transform-data-databricks-notebook.md) dão suporte à orquestração do ETL centrado em código ou da carga de trabalho de aprendizado de máquina sobre o Delta Lake.

## <a name="prerequisites"></a>Pré-requisitos

Para usar este Azure Databricks o conector Delta Lake, você precisa configurar um cluster no Azure Databricks.

- Para copiar dados para o Delta Lake, a atividade de cópia invoca Azure Databricks cluster para ler dados de um armazenamento do Azure, que é a fonte original ou uma área de preparação para onde Data Factory grava em primeiro lugar os dados de origem por meio de cópia em etapas internas. Saiba mais no [Delta Lake como o coletor](#delta-lake-as-sink).
- Da mesma forma, para copiar dados do Delta Lake, a atividade de cópia invoca Azure Databricks cluster para gravar dados em um armazenamento do Azure, que é o seu coletor original ou uma área de preparo de onde Data Factory continuará gravando dados no coletor final por meio de cópia em etapas internas. Saiba mais no [Delta Lake como a origem](#delta-lake-as-source).

O cluster do databricks precisa ter acesso ao blob do Azure ou Azure Data Lake Storage Gen2 conta, tanto o contêiner de armazenamento/sistema de arquivos usado para origem/coletor/preparo quanto para o sistema de contêiner/arquivo no qual você deseja gravar as tabelas Delta Lake.

- Para usar **Azure data Lake Storage Gen2**, você pode configurar uma **chave de acesso da conta de armazenamento** ou da entidade de **serviço** no cluster do databricks como parte da configuração do Apache Spark. Siga as etapas em [acessar diretamente com a entidade de serviço](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20) ou [o acesso diretamente usando a chave de acesso da conta de armazenamento](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-using-the-storage-account-access-key).

- Para usar o **armazenamento de BLOBs do Azure**, você pode configurar uma **chave de acesso da conta de armazenamento** ou um **token SAS** no cluster do databricks como parte da configuração do Apache Spark. Siga as etapas em [acessar o armazenamento de BLOBs do Azure usando a API RDD](/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api).

Durante a execução da atividade de cópia, se o cluster configurado tiver sido encerrado, Data Factory o iniciará automaticamente. Se você criar pipeline usando a interface do usuário de criação Data Factory, para operações como visualização de dados, você precisa ter um cluster ativo, Data Factory não iniciará o cluster em seu nome.

#### <a name="specify-the-cluster-configuration"></a>Especificar a configuração do cluster

1. Na lista suspensa **modo de cluster** , selecione **padrão**.

2. Na lista suspensa **Databricks Runtime versão** , selecione uma versão de tempo de execução do databricks.

3. Ative a [otimização automática](/azure/databricks/delta/optimizations/auto-optimize) adicionando as seguintes propriedades à sua [configuração do Spark](/azure/databricks/clusters/configure#spark-config):

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. Configure seu cluster dependendo de suas necessidades de integração e de dimensionamento.

Para obter detalhes de configuração do cluster, consulte [Configurar clusters](/azure/databricks/clusters/configure).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que definem Data Factory entidades específicas para um Azure Databricks conector Delta Lake.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para um Azure Databricks serviço vinculado do Delta Lake.

| Propriedade    | Descrição                                                  | Obrigatório |
| :---------- | :----------------------------------------------------------- | :------- |
| type        | A propriedade Type deve ser definida como **AzureDatabricksDeltaLake**. | Sim      |
| domínio      | Especifique a URL do espaço de trabalho Azure Databricks, por exemplo, `https://adb-xxxxxxxxx.xx.azuredatabricks.net` . |          |
| clusterId   | Especifique a ID do cluster de um cluster existente. Deve ser um cluster interativo já criado. <br>Você pode encontrar a ID do cluster de um cluster interativo no workspace do Databricks -&gt; Clusters -&gt; Nome do Cluster Interativo -&gt; Configuração -&gt; Marcas. [Saiba mais](/azure/databricks/clusters/configure#cluster-tags). |          |
| accessToken | O token de acesso é necessário para que o Data Factory autentique-se no Azure Databricks. O token de acesso precisa ser gerado a partir do workspace do Databricks. Etapas mais detalhadas para encontrar o token de acesso podem ser encontradas [aqui](/azure/databricks/dev-tools/api/latest/authentication#generate-token). |          |
| connectVia  | O [Integration Runtime](concepts-integration-runtime.md) que é usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usará o tempo de execução de integração do Azure padrão. | Não       |

**Exemplo:**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). 

As propriedades a seguir têm suporte para o conjunto de Azure Databricks do Delta Lake.

| Propriedade  | Descrição                                                  | Obrigatório                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | A propriedade Type do conjunto de conjuntos deve ser definida como **AzureDatabricksDeltaLakeDataset**. | Sim                         |
| Banco de Dados | Nome do banco de dados. |Não para fonte, sim para coletor  |
| table | Nome da tabela Delta. |Não para fonte, sim para coletor  |

**Exemplo:**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor do Azure Databricks Delta Lake.

### <a name="delta-lake-as-source"></a>Delta Lake como fonte

Para copiar dados do Azure Databricks Delta Lake, há suporte para as propriedades a seguir na seção **origem** da atividade de cópia.

| Propriedade                     | Descrição                                                  | Obrigatório |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | A propriedade Type da fonte da atividade de cópia deve ser definida como **AzureDatabricksDeltaLakeSource**. | Sim      |
| Consulta          | Especifique a consulta SQL para ler os dados. Para o controle de viagem de tempo, siga o padrão abaixo:<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | Não       |
| exportSettings | Configurações avançadas usadas para recuperar dados da tabela Delta. | Não       |
| ***Em `exportSettings` :*** |  |  |
| tipo | O tipo de comando de exportação, definido como **AzureDatabricksDeltaLakeExportCommand**. | Sim |
| dateFormat | Formate o tipo de data como cadeia de caracteres com um formato de data. Formatos de data personalizados seguem os formatos no [padrão DateTime](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Se não for especificado, ele usará o valor padrão `yyyy-MM-dd` . | Não |
| timestampFormat | Formate o tipo TIMESTAMP como cadeia de caracteres com um formato de carimbo de hora. Formatos de data personalizados seguem os formatos no [padrão DateTime](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Se não for especificado, ele usará o valor padrão `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | Não |

#### <a name="direct-copy-from-delta-lake"></a>Cópia direta do Delta Lake

Se o armazenamento e o formato de dados do coletor atenderem aos critérios descritos nesta seção, você poderá usar a atividade de cópia para copiar diretamente de Azure Databricks tabela Delta para o coletor. Data Factory verifica as configurações e falha na execução da atividade de cópia se os critérios a seguir não forem atendidos:

- O **serviço vinculado do coletor** é o [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md) ou [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md). A credencial da conta deve ser pré-configurada na configuração do cluster Azure Databricks, saiba mais em [pré-requisitos](#prerequisites).

- O **formato de dados do coletor** é de **parquet**, **texto delimitado** ou **Avro** com as seguintes configurações e aponta para uma pasta em vez de arquivo.

    - Para o formato **parquet** , o codec de compactação é **None**, **encaixado** ou **gzip**.
    - Para o formato de **texto delimitado** :
        - `rowDelimiter` é qualquer caractere único.
        - `compression` pode ser **None**, **bzip2**, **gzip**.
        - `encodingName` Não há suporte para UTF-7.
    - Para o formato **Avro** , o codec de compactação é **nenhum**, **desinflado** ou **instantâneo**.

- Na origem da atividade de cópia, `additionalColumns` não está especificado.
- Se a cópia de dados para um texto delimitado, no coletor da atividade de cópia, `fileExtension` precisa ser ". csv".
- No mapeamento da atividade de cópia, a conversão de tipo não está habilitada.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>Cópia em etapas do Delta Lake

Quando o formato ou armazenamento de dados do coletor não corresponde aos critérios de cópia direta, conforme mencionado na última seção, habilite a cópia em etapas interna usando uma instância do armazenamento do Azure provisória. O recurso de cópia preparada também oferece melhor rendimento. Data Factory exporta dados do Azure Databricks Delta Lake para o armazenamento de preparo, depois copia os dados para o coletor e, por fim, limpa os dados temporários do armazenamento de preparo. Confira [cópia em etapas](copy-activity-performance-features.md#staged-copy) para obter detalhes sobre como copiar dados usando o preparo.

Para usar esse recurso, crie um [serviço vinculado do armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#linked-service-properties) ou [Azure data Lake Storage Gen2 serviço vinculado](connector-azure-data-lake-storage.md#linked-service-properties) que se refere à conta de armazenamento como o preparo provisório. Em seguida, especifique as `enableStaging` `stagingSettings` Propriedades e na atividade de cópia.

>[!NOTE]
>A credencial de conta de armazenamento de preparo deve ser pré-configurada em Azure Databricks configuração de cluster, saiba mais em [pré-requisitos](#prerequisites).

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>Delta Lake como coletor

Para copiar dados para Azure Databricks Delta Lake, as propriedades a seguir têm suporte na seção **coletor** de atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| :------------ | :----------------------------------------------------------- | :------- |
| type          | A propriedade Type do coletor da atividade de cópia, definida como **AzureDatabricksDeltaLakeSink**. | Sim      |
| preCopyScript | Especifique uma consulta SQL para que a atividade de cópia seja executada antes de gravar dados na tabela Delta do databricks em cada execução. Você pode usar essa propriedade para limpar os dados pré-carregados ou adicionar uma tabela Truncate ou uma instrução de aspiração. | Não       |
| importSettings | Configurações avançadas usadas para gravar dados na tabela Delta. | Não |
| ***Em `importSettings` :*** |                                                              |  |
| tipo | O tipo de comando de importação, definido como **AzureDatabricksDeltaLakeImportCommand**. | Sim |
| dateFormat | Formatar cadeia de caracteres para tipo de data com um formato de data. Formatos de data personalizados seguem os formatos no [padrão DateTime](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Se não for especificado, ele usará o valor padrão `yyyy-MM-dd` . | Não |
| timestampFormat | Formatar cadeia de caracteres para tipo TIMESTAMP com um formato de carimbo de data/hora. Formatos de data personalizados seguem os formatos no [padrão DateTime](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Se não for especificado, ele usará o valor padrão `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | Não |

#### <a name="direct-copy-to-delta-lake"></a>Cópia direta para o Delta Lake

Se o armazenamento e o formato de dados de origem atenderem aos critérios descritos nesta seção, você poderá usar a atividade de cópia para copiar diretamente da origem para Azure Databricks Delta Lake. Azure Data Factory verifica as configurações e falha na execução da atividade de cópia se os critérios a seguir não forem atendidos:

- O **serviço vinculado de origem** é o [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md) ou [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md). A credencial da conta deve ser pré-configurada na configuração do cluster Azure Databricks, saiba mais em [pré-requisitos](#prerequisites).

- O **formato de dados de origem** é de **parquet**, **texto delimitado** ou **Avro** com as seguintes configurações e aponta para uma pasta em vez de arquivo.

    - Para o formato **parquet** , o codec de compactação é **None**, **encaixado** ou **gzip**.
    - Para o formato de **texto delimitado** :
        - `rowDelimiter` é padrão, ou qualquer caractere único.
        - `compression` pode ser **None**, **bzip2**, **gzip**.
        - `encodingName` Não há suporte para UTF-7.
    - Para o formato **Avro** , o codec de compactação é **nenhum**, **desinflado** ou **instantâneo**.

- Na fonte da atividade de cópia: 

    - `wildcardFileName` contém somente curinga `*` , mas não `?` , e `wildcardFolderName` não é especificado.
    - `prefix`, `modifiedDateTimeStart`, `modifiedDateTimeEnd` e `enablePartitionDiscovery` não foram especificados.
    - `additionalColumns` não está especificado.

- No mapeamento da atividade de cópia, a conversão de tipo não está habilitada.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>Cópia em etapas para o Delta Lake

Quando o formato ou armazenamento de dados de origem não corresponder aos critérios de cópia direta, conforme mencionado na última seção, habilite a cópia em etapas interna usando uma instância de armazenamento do Azure provisória. O recurso de cópia preparada também oferece melhor rendimento. Data Factory converte automaticamente os dados para atender aos requisitos de formato de dados no armazenamento de preparo e, em seguida, carrega os dados no Delta Lake a partir daí. Por fim, ele limpa os dados temporários do armazenamento. Confira [cópia em etapas](copy-activity-performance-features.md#staged-copy) para obter detalhes sobre como copiar dados usando o preparo.

Para usar esse recurso, crie um [serviço vinculado do armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#linked-service-properties) ou [Azure data Lake Storage Gen2 serviço vinculado](connector-azure-data-lake-storage.md#linked-service-properties) que se refere à conta de armazenamento como o preparo provisório. Em seguida, especifique as `enableStaging` `stagingSettings` Propriedades e na atividade de cópia.

>[!NOTE]
>A credencial de conta de armazenamento de preparo deve ser pré-configurada em Azure Databricks configuração de cluster, saiba mais em [pré-requisitos](#prerequisites).

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
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

## <a name="monitoring"></a>Monitoramento

Azure Data Factory fornece a mesma [experiência de monitoramento da atividade de cópia](copy-activity-monitoring.md) que outros conectores. Além disso, como o carregamento de dados de/para o Delta Lake está em execução no cluster Azure Databricks, você pode exibir ainda mais [logs detalhados do cluster](/azure/databricks/clusters/clusters-manage#--view-cluster-logs) e [monitorar o desempenho](/azure/databricks/clusters/clusters-manage#--monitor-performance).

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para obter mais informações sobre as propriedades, consulte [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores por atividade de cópia em Data Factory, consulte [armazenamentos de dados e formatos com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
