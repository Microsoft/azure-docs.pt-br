---
title: Copiar dados do Cassandra usando o Azure Data Factory
description: Saiba como copiar dados do Cassandra para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: a3cd3c3ae28ae302e9469a71d00054152a9b5fb5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383697"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Copiar dados do Cassandra usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Versão atual](connector-cassandra.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados Cassandra. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do Cassandra tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados Cassandra para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Cassandra dá suporte:

- Cassandra **versões 2.x e 3.x**.
- À cópia de dados usando a autenticação **Básica** ou **Anônima**.

>[!NOTE]
>Para a atividade em execução no Integration Runtime Auto-hospedado, o Cassandra 3.x tem suporte desde o IR versão 3.7 e acima.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Integration Runtime fornece um driver do Cassandra interno, portanto, não será necessário instalar manualmente nenhum driver ao copiar dados do/para o Cassandra.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao Cassandra.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Cassandra:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type |A propriedade type deve ser definida como: **Cassandra** |Sim |
| host |Um ou mais endereços IP ou nomes de host dos servidores Cassandra.<br/>Especifique uma lista separada por vírgulas de endereços IP ou nomes de host para se conectar simultaneamente a todos os servidores. |Sim |
| porta |A porta TCP usada pelo servidor Cassandra para ouvir conexões de cliente. |Não (o padrão é 9042) |
| authenticationType | Tipo de autenticação usado para se conectar ao banco de dados Cassandra.<br/>Os valores permitidos são: **básico** e **anônimo**. |Sim |
| Nome de Usuário |Especifique o nome de usuário da conta de usuário. |Sim, se authenticationType for definida como Básica. |
| password |Especifique a senha para a conta de usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim, se authenticationType for definida como Básica. |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos](#prerequisites). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

>[!NOTE]
>Atualmente, a conexão a Cassandra usando TLS não é suportada.

**Exemplo:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
            "authenticationType": "Basic",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Cassandra.

Para copiar dados do Cassandra, defina a propriedade type do conjunto de dados como **CassandraTable**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **CassandraTable** | Sim |
| keyspace |Nome do keyspace ou do esquema no banco de dados Cassandra. |Não (se a "consulta" para "CassandraSource" estiver especificada) |
| tableName |Nome da tabela no banco de dados Cassandra. |Não (se a "consulta" para "CassandraSource" estiver especificada) |

**Exemplo:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia


Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte Cassandra.

### <a name="cassandra-as-source"></a>Cassandra como fonte

Para copiar dados do Cassandra, defina o tipo de fonte na atividade de cópia como **CassandraSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **CassandraSource** | Sim |
| Consulta |Utiliza a consulta personalizada para ler os dados. Consulta SQL-92 ou consulta CQL. Veja [Referência ao CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao usar a consulta SQL, especifique **keyspace name.table name** para representar a tabela que deseja consultar. |Não (se "tableName" e "keyspace" no conjunto de dados estiverem especificados). |
| consistencyLevel |O nível de consistência especifica quantas réplicas devem responder a uma solicitação de leitura antes de retornar dados ao aplicativo cliente. O Cassandra verifica o número especificado de réplicas de dados atender à solicitação de leitura. Confira [Configuring data consistency (Configurando a consistência de dados)](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter detalhes.<br/><br/>Os valores permitidos são: **ONE**, **TWO**, **THREE**, **QUORUM**, **ALL**, **LOCAL_QUORUM**, **EACH_QUORUM** e **LOCAL_ONE**. |Não (o padrão é `ONE`) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
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
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Mapeamento de tipo de dados para o Cassandra

Ao copiar dados do Cassandra, os seguintes mapeamentos são usados de tipos de dados do Cassandra para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados Cassandra | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| ASCII |String |
| bigint |Int64 |
| BLOB |Byte[] |
| BOOLEAN |Boolean |
| DECIMAL |Decimal |
| DOUBLE |Double |
| FLOAT |Único |
| INET |String |
| INT |Int32 |
| TEXT |String |
| timestamp |Datetime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |String |
| VARINT |Decimal |

> [!NOTE]
> Para tipos de coleção (mapa, conjunto, lista, etc.), consulte a seção [Trabalhar com coleções usando tabela virtual](#work-with-collections-using-virtual-table) .
>
> Não há suporte para tipos definidos pelo usuário.
>
> O comprimento da coluna Binário e os comprimentos da coluna Cadeia de Caracteres não pode ser maior que 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com coleções usando tabela virtual

O Azure Data Factory usa um driver ODBC interno para se conectar ao banco de dados Cassandra e copiar dados dele. Para tipos de coleção, incluindo mapa, conjunto e lista, o driver normaliza novamente os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver colunas de coleção, o driver vai gerar as seguintes tabelas virtuais:

* Uma **tabela base**, que contém os mesmos dados da tabela real, exceto nas colunas de coleção. A tabela base usa o mesmo nome da tabela real que ela representa.
* Uma **tabela virtual** para cada coluna de coleção, que expande os dados aninhados. As tabelas virtuais que representam coleções são nomeadas usando o nome da tabela real, um separador "*vt*" e o nome da coluna.

As tabelas virtuais se referem aos dados na tabela real, permitindo que o driver acesse dados desordenados. Confira a seção Exemplo para obter detalhes. Você pode acessar o conteúdo das coleções de Cassandra consultando e unindo as tabelas virtuais.

### <a name="example"></a>Exemplo

Por exemplo, a "ExampleTable" a seguir é uma tabela de banco de dados Cassandra que contém uma coluna de chave primária de inteiro chamada "pk_int", uma coluna de texto chamada valor, uma coluna de lista, uma coluna de mapa e uma coluna de conjunto (chamada "StringSet").

| pk_int | Valor | List | Mapeamento | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valor de exemplo 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"valor de exemplo 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

O driver geraria várias tabelas virtuais para representar essa tabela única. As colunas de chave estrangeira nas tabelas virtuais fazem referência às colunas de chave primário na tabela real e indicam à qual linha da tabela real a linha da tabela virtual corresponde.

A primeira tabela virtual é a tabela base chamada "ExampleTable" e é mostrada na tabela a seguir: 

| pk_int | Valor |
| --- | --- |
| 1 |"valor de exemplo 1" |
| 3 |"valor de exemplo 3" |

A tabela base contém os mesmos dados da tabela de banco de dados original, exceto para as coleções, que são omitidas da tabela e expandidas em outras tabelas virtuais.

As tabelas a seguir mostram as tabelas virtuais que normalizam novamente os dados nas colunas Lista, Mapa e StringSet. As colunas com nomes que terminam com "_index" ou "_key" indicam a posição dos dados na lista ou no mapa original. As colunas com nomes que terminam com "_value" contêm os dados expandidos da coleção.

**Tabela "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabela "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |Um |
| 1 |S2 |b |
| 3 |S1 |t |

**Tabela "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |Um |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
