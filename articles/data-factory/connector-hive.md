---
title: Copiar dados do Hive usando o Azure Data Factory
description: Saiba como copiar dados do Hive para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: jingwang
ms.openlocfilehash: 4207c4ddfcbab325b1ae119dcd200af30fc59f58
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844925"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>Copiar e transformar dados do hive usando o Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Hive. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do hive tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um Hive para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, não é necessário instalar manualmente qualquer driver usando esse conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector de Hive.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado de Hive:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Hive** | Sim |
| host | Endereço IP ou nome do host do servidor Hive, separados por '; ' para vários hosts (somente quando o Service DiscoveryMode está habilitado).  | Sim |
| porta | A porta TCP usada pelo servidor Hive para ouvir conexões de cliente. Se você conectar ao Microsoft Azure HDInsights, especifique a porta como 443. | Sim |
| serverType | O tipo do servidor Hive. <br/>Os valores permitidos são: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Não |
| thriftTransportProtocol | O protocolo de transporte a ser usado na camada de Thrift. <br/>Os valores permitidos são: **Binário**, **SASL**, **HTTP** | Não |
| authenticationType | O método de autenticação usado para acessar o servidor do Hive. <br/>Os valores permitidos são: **Anonymous**, **username**, **UsernameAndPassword**, **WindowsAzureHDInsightService**. Não há suporte para a autenticação Kerberos agora. | Sim |
| serviceDiscoveryMode | verdadeiro para indicar o uso do serviço ZooKeeper, falso não.  | Não |
| zooKeeperNameSpace | O namespace no ZooKeeper no qual os2 nós do Hive Server são adicionados.  | Não |
| useNativeQuery | Especifica se o driver usa consultas HiveQL nativas ou as converte em um formato equivalente em HiveQL.  | Não |
| Nome de Usuário | O nome de usuário que você usa para acessar o servidor do Hive.  | Não |
| password | A senha correspondente ao usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não |
| httpPath | A URL parcial correspondente ao servidor do Hive.  | Não |
| enableSsl | Especifica se as conexões com o servidor são criptografadas usando TLS. O valor padrão é false.  | Não |
| trustedCertPath | O caminho completo do arquivo. pem que contém certificados de AC confiáveis para verificar o servidor ao se conectar via TLS. Essa propriedade só pode ser definida ao usar TLS no IR auto-hospedado. O valor padrão é o arquivo de cacerts.pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se deve usar um certificado de autoridade de certificação do repositório de confiança de sistema ou de um arquivo PEM especificado. O valor padrão é false.  | Não |
| allowHostNameCNMismatch | Especifica se deve ser necessário um nome de certificado TLS/SSL emitido pela autoridade de certificação para corresponder ao nome de host do servidor ao se conectar por TLS. O valor padrão é false.  | Não |
| allowSelfSignedServerCert | Especifica se deve permitir os certificados autoassinados do servidor. O valor padrão é false.  | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos](#prerequisites). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |
| storageReference | Uma referência ao serviço vinculado da conta de armazenamento usada para preparar dados no fluxo de dados de mapeamento. Isso é necessário somente ao usar o serviço vinculado do hive no fluxo de dados de mapeamento | Não |

**Exemplo:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados de HTTP.

Para copiar dados de Hive, defina a propriedade type do conjunto de dados como **HiveObject**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **hiveobject** | Sim |
| esquema | Nome do esquema. |Não (se "query" na fonte da atividade for especificada)  |
| tabela | Nome da tabela. |Não (se "query" na fonte da atividade for especificada)  |
| tableName | Nome da tabela, incluindo a parte do esquema. Essa propriedade é compatível com versões anteriores. Para uma nova carga de trabalho, use `schema` e `table`. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte Hive.

### <a name="hivesource-as-source"></a>HiveSource como fonte

Para copiar dados de Hive, defina o tipo de fonte na atividade de cópia como **HiveSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **HiveSource** | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

Há suporte para o conector do hive como uma fonte de conjunto de dados [embutida](data-flow-source.md#inline-datasets) no mapeamento de fluxos. Leia usando uma consulta ou diretamente de uma tabela Hive no HDInsight. Os dados do hive são preparados em uma conta de armazenamento como arquivos parquet antes de serem transformados como parte de um fluxo de dados. 

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades com suporte por uma origem do hive. Você pode editar essas propriedades na guia **Opções de origem** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Repositório | O repositório deve ser `hive` | yes |  `hive` | store | 
| Formatar | Se você está lendo de uma tabela ou consulta | yes | `table` ou `query` | format |
| Nome do esquema | Se estiver lendo de uma tabela, o esquema da tabela de origem |  Sim, se o formato for `table` | Cadeia de caracteres | schemaName |
| Nome da tabela | Se estiver lendo de uma tabela, o nome da tabela |   Sim, se o formato for `table` | Cadeia de caracteres | tableName |
| Consulta | Se o formato for `query` , a consulta de origem no serviço vinculado do hive | Sim, se o formato for `query` | Cadeia de caracteres | Consulta |
| Em etapas | A tabela do hive sempre será preparada. | yes | `true` | em etapas |
| Contêiner de armazenamento | Contêiner de armazenamento usado para preparar dados antes de ler do hive ou gravar no hive. O cluster do hive deve ter acesso a esse contêiner. | yes | Cadeia de caracteres | storageContainer |
| Banco de dados de preparo | O esquema/banco de dados ao qual a conta de usuário especificada no serviço vinculado tem acesso. Ela é usada para criar tabelas externas durante o preparo e descartadas posteriormente | no | `true` ou `false` | stagingDatabaseName |
| Scripts anteriores do SQL | Código SQL a ser executado na tabela Hive antes de ler os dados | no | Cadeia de caracteres | preSQLs |

#### <a name="source-example"></a>Exemplo de origem

Veja abaixo um exemplo de configuração de origem do hive:

![Exemplo de origem do hive](media/data-flow/hive-source.png "[Exemplo de origem do hive")

Essas configurações são traduzidas para o seguinte script de fluxo de dados:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>Limitações conhecidas

* Tipos complexos, como matrizes, mapas, structs e uniões, não têm suporte para leitura. 
* O conector do hive só dá suporte a tabelas Hive no Azure HDInsight da versão 4,0 ou superior (Apache Hive 3.1.0)

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
