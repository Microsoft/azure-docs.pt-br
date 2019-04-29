---
title: Mapeamento de esquema na atividade de cópia | Microsoft Docs
description: Saiba mais sobre como a atividade de cópia no Azure Data Factory mapeia esquemas e tipos de dados dos dados de origem para os dados do coletor ao copiar dados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 99798b35419ec9574c99aaba42803fbeeb1555f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615632"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapeamento de esquema na atividade de cópia
Este artigo descreve como a atividade de cópia do Azure Data Factory faz o mapeamento de esquema e de tipo de dados dos dados de origem para os dados do coletor ao executar a cópia dos dados.

## <a name="column-mapping"></a>Mapeamento de coluna

O mapeamento de colunas é aplicado ao copiar dados entre dados em formato de tabela. Por padrão, atividade de cópia **mapeia dados de origem para o coletor por nomes de coluna**, a menos que o [mapeamento de coluna explícito](#explicit-column-mapping) esteja configurado. Mais especificamente, a atividade de cópia:

1. Lê os dados da origem e determina o esquema de origem

    * Para fontes de dados com um esquema predefinido no armazenamento de dados/formato de arquivo, por exemplo, bancos de dados/arquivos com metadados (Avro/ORC/Parquet/Texto com cabeçalho), o esquema de origem é extraído do resultado da consulta ou dos metadados do arquivo.
    * Para fontes de dados com esquema flexível, por exemplo, Tabela do Azure/Cosmos DB, o esquema de origem é inferido do resultado da consulta. Você pode substituí-lo ao configurar "structure" no conjunto de dados.
    * Para o arquivo de texto sem cabeçalho, os nomes de coluna padrão são gerados com o padrão "Prop_0", "Prop_1",... Você pode substituí-lo configurando "structure" no conjunto de dados.
    * Para a origem do Dynamics, você precisa fornecer as informações de esquema na seção "structure" do conjunto de dados.

2. Aplica o mapeamento de coluna explícito se especificado.

3. Grava os dados no coletor

    * Para armazenamentos de dados com um esquema predefinido, os dados são gravados nas colunas com o mesmo nome.
    * Para os armazenamentos de dados sem esquema fixo e para formatos de arquivo, os nomes de coluna/metadados serão gerados com base no esquema de origem.

### <a name="explicit-column-mapping"></a>Mapeamento de coluna explícito

Você pode especificar **columnMappings** na seção **typeProperties** da atividade de cópia para fazer o mapeamento de coluna explícito. Nesse cenário, a seção "structure" é necessária para os conjuntos de dados de entrada e de saída. O mapeamento de coluna dá suporte ao **mapeamento de todas as colunas ou de um subconjunto de colunas na “structure” do conjunto de dados de origem para todas as colunas na “structure” do conjunto de dados do coletor**. Veja a seguir condições de erro que resultam em uma exceção:

* O resultado da consulta do armazenamento de dados de origem não tem um nome de coluna especificado na seção “structure” do conjunto de dados de entrada.
* O armazenamento de dados do coletor (se estiver com o esquema predefinido) não tem um nome de coluna especificado na seção “structure” do conjunto de dados de saída.
* Menos colunas ou mais colunas na seção "structure" do conjunto de dados do coletor do que o especificado no mapeamento.
* Mapeamento duplicado.

#### <a name="explicit-column-mapping-example"></a>Exemplo de mapeamento de coluna explícito

Neste exemplo, a tabela de entrada tem uma estrutura e ela aponta para uma tabela em um banco de dados SQL local.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Neste exemplo, a tabela de saída tem uma estrutura e ela aponta para uma tabela em um Banco de Dados SQL do Azure.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

O JSON a seguir define uma atividade de cópia em um pipeline. As colunas da origem são mapeadas para colunas no coletor (**columnMappings**) utilizando a propriedade **translator**.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings": 
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Se você está utilizando a sintaxe de `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` para especificar o mapeamento de coluna, ainda há suporte no estado em que se encontra.

**Fluxo de mapeamento de coluna:**

![Fluxo de mapeamento de coluna](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="schema-mapping"></a>Mapeamento de esquema

O mapeamento de esquema é aplicado ao copiar dados entre dados em formato hierárquico e dados em forma de tabela, por exemplo, copie do MongoDB/REST para o arquivo de texto e copie do SQL para a API do Azure Cosmos DB para MongoDB. As propriedades a seguir têm suporte na seção `translator` da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do tradutor da atividade de cópia deve ser definida como: **TabularTranslator** | Sim |
| schemaMapping | Uma coleção de pares chave-valor, que representa a relação de mapeamento **do lado do código-fonte para o coletor lado**.<br/>- **Chave:** fonte representa. Para **origem tabular**, especifique o nome da coluna conforme definido na estrutura do conjunto de dados; para **origem hierárquica**, especifique a expressão de caminho JSON para cada campo extrair e mapear.<br/>- **Valor:** coletor representa. Para **coletor tabular**, especifique o nome da coluna conforme definido na estrutura do conjunto de dados; para **coletor hierárquica**, especifique a expressão de caminho JSON para cada campo extrair e mapear. <br/> No caso de dados hierárquicos, campos sob o objeto raiz, o caminho JSON começa com root $; para os campos dentro da matriz escolhidos pela `collectionReference` propriedade, o caminho JSON começa do elemento de matriz.  | Sim |
| collectionReference | Se você quiser fazer uma iteração e extrair dados de objetos **dentro de um campo de matriz** com o mesmo padrão e converter para por linha por objeto, especifique o caminho JSON da matriz para realizar a aplicação cruzada. Essa propriedade só terá suporte quando os dados hierárquicos forem a origem. | Não  |

**Exemplo: copiar do MongoDB para o SQL:**

Por exemplo, se você tiver o documento do MongoDB com o seguinte conteúdo: 

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

e você deseja copiá-lo para uma tabela do Azure SQL no formato a seguir, ao nivelar os dados de dentro da matriz *(order_pd e order_price)* e fazer uma união cruzada com as informações de raiz comuns *(número, data e cidade)*:

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Configure a regra de mapeamento de esquema como o seguinte exemplo JSON de atividade de cópia:

```json
{
    "name": "CopyFromMongoDBToSqlAzure",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number", 
                "orderDate": "$.date", 
                "order_pd": "prod", 
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mapeamento de tipo de dados

A atividade de cópia executa o mapeamento dos tipos de origem para os tipos de coletor com a abordagem de duas etapas a seguir:

1. Converter de tipos de origem nativos para tipos de dados provisórios do Azure Data Factory
2. Converter de tipos de dados provisórios do Azure Data Factory para o tipo de coletor nativo

Você pode encontrar o mapeamento entre o tipo nativo para o tipo provisório na seção "Mapeamento de tipo de dados" em cada tópico de conector.

### <a name="supported-data-types"></a>Tipos de dados com suporte

O Data Factory dá suporte aos seguintes tipos de dados provisórios: você pode especificar os valores a seguir ao configurar informações de tipo na configuração da [estrutura do conjunto de dados](concepts-datasets-linked-services.md#dataset-structure):

* Byte[]
* Boolean
* DateTime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* Cadeia de caracteres
* Timespan

### <a name="explicit-data-type-conversion"></a>Conversão de tipo de dados explícita

Ao copiar dados para os armazenamentos de dados com esquema fixo, por exemplo, SQL Server/Oracle, quando a origem e o coletor têm um tipo diferente na mesma coluna, a conversão de tipo explícita deve ser declarada no lado da origem:

* Para a origem do arquivo, por exemplo, CSV/Avro, a conversão de tipo deve ser declarada por meio da estrutura de origem com a lista de colunas completa (nome da coluna do lado da origem e tipo do lado do coletor)
* Para a origem relacional (por exemplo, SQL/Oracle), a conversão de tipo deve ser obtida com a conversão de tipo explícita na instrução de consulta.

## <a name="when-to-specify-dataset-structure"></a>Quando especificar a seção “structure” no conjunto de dados

Nos cenários abaixo, a seção “structure” no conjunto de dados é necessária ao:

* Aplicar a [conversão de tipo de dados explícita](#explicit-data-type-conversion) para as origens de arquivo durante a cópia (conjunto de dados de entrada)
* Aplicar o [mapeamento de coluna explícito](#explicit-column-mapping) durante a cópia (conjuntos de dados de entrada e saída)
* Copiar da origem do Dynamics 365/CRM (conjunto de dados de entrada)
* Copiar para o Cosmos DB como um objeto aninhado quando a origem não são arquivos JSON (conjunto de dados de saída)

Nos cenários abaixo, a seção “structure” no conjunto de dados é sugerida ao:

* Copiar do arquivo de texto sem cabeçalho (conjunto de dados de entrada). Você pode especificar os nomes de coluna para o alinhamento do arquivo de texto com as colunas de coletor correspondentes para salvar a configuração do mapeamento de coluna explícito.
* Copiar dados dos armazenamentos de dados com esquema flexível, por exemplo, Tabela do Azure/Cosmos DB (conjunto de dados de entrada), para garantir os dados (colunas) esperados sendo copiados em vez de deixar a atividade de cópia inferir o esquema com base nas linhas superiores durante cada execução da atividade.


## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Tolerância a falhas da atividade de cópia](copy-activity-fault-tolerance.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)
