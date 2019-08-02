---
title: Mover dados do Cassandra usando o Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados de um banco de dados Cassandra local usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5b098aaf2df5e04983aa53563d5e0203f3287b42
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839956"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Mover dados de um banco de dados Cassandra local usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-onprem-cassandra-connector.md)
> * [Versão 2 (versão atual)](../connector-cassandra.md)

> [!NOTE]
> Este artigo se aplica à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Conector do Cassandra na V2](../connector-cassandra.md).

Esse artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados de um banco de dados Cassandra local. Ele se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um armazenamento de dados local do Cassandra para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de repositórios de dados com suporte como coletores da atividade de cópia, confira a tabela [Repositórios de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Atualmente, o data factory dá suporte apenas à movimentação de dados de um armazenamento de dados Cassandra para outros armazenamentos de dados, mas não à movimentação de dados de outros armazenamentos de dados para um armazenamento de dados Cassandra.

## <a name="supported-versions"></a>Versões com suporte
O conector Cassandra dá suporte às seguintes versões do Cassandra: 2.x e 3.x. Para a atividade em execução no Integration Runtime Auto-hospedado, o Cassandra 3.x tem suporte desde o IR versão 3.7 e acima.

## <a name="prerequisites"></a>Pré-requisitos
Para que o serviço Azure Data Factory consiga se conectar ao seu banco de dados Cassandra local, é necessário instalar o Gateway de Gerenciamento de Dados no mesmo computador que hospeda o banco de dados ou em um computador separado para evitar a concorrência por recursos com o banco de dados. O Gateway de Gerenciamento de Dados é um componente que conecta as fontes de dados locais aos serviços de nuvem de uma maneira segura e gerenciada. Confira o artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para obter todos os detalhes sobre o Gateway de Gerenciamento de Dados. Consulte o artigo [Mover dados de pontos locais para a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções detalhadas sobre como configurar um pipeline de dados para o gateway a fim de mover dados.

Você deverá usar o gateway para se conectar a um banco de dados Cassandra mesmo se o banco de dados estiver hospedado na nuvem, por exemplo, em uma VM IaaS do Azure. Você pode ter o gateway na mesma VM que hospeda o banco de dados ou em uma VM diferente, desde que o gateway possa se conectar o banco de dados.

Quando você instala o gateway, ele instala automaticamente um driver ODBC do Microsoft Cassandra usado para se conectar ao banco de dados Cassandra. Portanto, não é necessário instalar nenhum driver manualmente no computador do gateway ao copiar dados do banco de dados Cassandra.

> [!NOTE]
> Consulte [Solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para ver dicas sobre como solucionar os problemas relacionados à conexão/gateway.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com atividade de cópia que mova dados de um armazenamento de dados local Cassandra usando diferentes ferramentas/APIs.

- A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Consulte [Tutorial: criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.
- Você também pode usar as ferramentas abaixo para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo do Resource Manager**, **.NET API**, e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída.

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON. Para obter um exemplo com definições de JSON para entidades do Data Factory que são usadas para copiar dados do armazenamento de dados Cassandra local, confira a seção [Exemplo de JSON: Copiar dados do Cassandra para o Blob do Azure](#json-example-copy-data-from-cassandra-to-azure-blob) neste artigo.

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas para um armazenamento de dados Cassandra:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição de elementos JSON específicos para o serviço vinculado Cassandra.

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **OnPremisesCassandra** |Sim |
| host |Um ou mais endereços IP ou nomes de host dos servidores Cassandra.<br/><br/>Especifique uma lista separada por vírgulas de endereços IP ou nomes de host para se conectar simultaneamente a todos os servidores. |Sim |
| port |A porta TCP usada pelo servidor Cassandra para ouvir conexões de cliente. |Não, valor padrão: 9042 |
| authenticationType |Básica, ou Anônima |Sim |
| username |Especifique o nome de usuário da conta de usuário. |Sim, se authenticationType for definida como Básica. |
| password |Especifique a senha para a conta de usuário. |Sim, se authenticationType for definida como Básica. |
| gatewayName |O nome do gateway que é usado para se conectar ao servidor Cassandra local. |Sim |
| encryptedCredential |Credencial criptografada pelo gateway. |Não |

>[!NOTE]
>Atualmente, não há suporte para a conexão ao Cassandra usando SSL.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **CassandraTable** tem as seguintes propriedades

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| keyspace |Nome do keyspace ou do esquema no banco de dados Cassandra. |Sim (se a **consulta** para **CassandraSource** não estiver definida). |
| tableName |Nome da tabela no banco de dados Cassandra. |Sim (se a **consulta** para **CassandraSource** não estiver definida). |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

Por outro lado, as propriedades disponíveis na seção typeProperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Quando a fonte é do tipo **CassandraSource**, as seguintes propriedades estão disponíveis na seção typeProperties:

| Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. |Consulta SQL-92 ou consulta CQL. Veja [Referência ao CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao usar a consulta SQL, especifique **keyspace name.table name** para representar a tabela que deseja consultar. |Não (se tableName e keyspace no conjunto de dados estiverem definidos). |
| consistencyLevel |O nível de consistência especifica quantas réplicas devem responder a uma solicitação de leitura antes de retornar dados ao aplicativo cliente. O Cassandra verifica o número especificado de réplicas de dados atender à solicitação de leitura. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Confira [Configuring data consistency (Configurando a consistência de dados)](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter detalhes. |Nº O valor padrão é ONE. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Exemplo JSON: Copiar dados do Cassandra para o Blob do Azure
Este exemplo fornece as definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [do Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ele mostra como copiar dados de um banco de dados Cassandra local para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.

> [!IMPORTANT]
> Este exemplo fornece snippets de JSON. Ele não inclui instruções passo a passo para criar o data factory. Confira o artigo [Mover dados entre fontes locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo.

O exemplo tem as seguintes entidades de data factory:

* Um serviço vinculado do tipo [OnPremisesCassandra](#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [CassandraTable](#dataset-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* O [pipeline](data-factory-create-pipelines.md) com a Atividade de Cópia que usa [CassandraSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Serviço vinculado Cassandra:**

Este exemplo usa o serviço vinculado **Cassandra** . Confira a seção [Serviço vinculado Cassandra](#linked-service-properties) para ver as propriedades compatíveis com esse serviço vinculado.

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Serviço vinculado de armazenamento do Azure:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Conjunto de dados de entrada do Cassandra:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Configurar **external** como **true** informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: horas, intervalo: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Atividade de cópia em um pipeline com origem Cassandra e coletor de Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo **source** está definido como **CassandraSource** e o tipo **sink** está definido como **BlobSink**.

Confira [Propriedades do tipo RelationalSource](#copy-activity-properties) para obter a lista de propriedades permitidas pelo RelationalSource.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```

### <a name="type-mapping-for-cassandra"></a>Mapeamento de tipo para Cassandra
| Tipo Cassandra | Tipo baseado no .NET |
| --- | --- |
| ASCII |String |
| BIGINT |Int64 |
| BLOB |Byte[] |
| Boolean |Boolean |
| Decimal |Decimal |
| Double |Double |
| FLOAT |Simples |
| INET |String |
| INT |Int32 |
| TEXT |String |
| TIMESTAMP |DateTime |
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
>

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com coleções usando tabela virtual
O Azure Data Factory usa um driver ODBC interno para se conectar ao banco de dados Cassandra e copiar dados dele. Para tipos de coleção, incluindo mapa, conjunto e lista, o driver normaliza novamente os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver colunas de coleção, o driver vai gerar as seguintes tabelas virtuais:

* Uma **tabela base**, que contém os mesmos dados da tabela real, exceto nas colunas de coleção. A tabela base usa o mesmo nome da tabela real que ela representa.
* Uma **tabela virtual** para cada coluna de coleção, que expande os dados aninhados. As tabelas virtuais que representam coleções são nomeadas usando o nome da tabela real, um separador "*vt*" e o nome da coluna.

As tabelas virtuais se referem aos dados na tabela real, permitindo que o driver acesse dados desordenados. Confira a seção Exemplo para obter detalhes. Você pode acessar o conteúdo das coleções de Cassandra consultando e unindo as tabelas virtuais.

Você pode usar o [Assistente de Cópia](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) para exibir intuitivamente a lista de tabelas no banco de dados Cassandra (incluindo as tabelas virtuais) e visualizar os dados internos. Também é possível construir uma consulta no Assistente de Cópia e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, "ExampleTable" a seguir é uma tabela de banco de dados Cassandra que contém uma coluna de chave primária de inteiro chamada "pk_int", uma coluna de texto chamado valor, uma coluna de lista, uma coluna de mapa e uma coluna de conjunto (chamada "StringSet").

| pk_int | Valor | Listar | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valor de exemplo 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"valor de exemplo 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

O driver geraria várias tabelas virtuais para representar essa tabela única. As colunas de chave estrangeira nas tabelas virtuais fazem referência às colunas de chave primário na tabela real e indicam à qual linha da tabela real a linha da tabela virtual corresponde.

A primeira tabela virtual é a tabela base chamada "ExampleTable" e é mostrada na tabela a seguir. A tabela base contém os mesmos dados da tabela de banco de dados original, exceto para as coleções, que são omitidas da tabela e expandidas em outras tabelas virtuais.

| pk_int | Valor |
| --- | --- |
| 1 |"valor de exemplo 1" |
| 3 |"valor de exemplo 3" |

As tabelas a seguir mostram as tabelas virtuais que normalizam novamente os dados nas colunas Lista, Mapa e StringSet. As colunas com nomes que terminam com "_index" ou "_key" indicam a posição dos dados na lista ou mapa original. As colunas com nomes que terminam com "_value" contêm os dados expandidos da coleção.

#### <a name="table-exampletablevtlist"></a>Tabela "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletablevtmap"></a>Tabela "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |O |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletablevtstringset"></a>Tabela "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |O |
| 1 |b |
| 1 |C |
| 3 |O |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para saber mais sobre mapeamento de colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapeamento de colunas de conjunto de dados no Azure Data Factory).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja de que maneira for, você precisa garantir que os mesmos dados sejam lidos não importa quantas vezes uma fatia seja executada. Confira [Leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.
