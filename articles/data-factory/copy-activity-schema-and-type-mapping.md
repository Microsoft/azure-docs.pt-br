---
title: "Mapeamento de esquema na atividade de cópia | Microsoft Docs"
description: "Saiba mais sobre como a atividade de cópia no Azure Data Factory mapeia esquemas e tipos de dados dos dados de origem para os dados do coletor ao copiar dados."
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
ms.date: 09/15/2017
ms.author: jingwang
ms.openlocfilehash: 459c792028d3eede059814324597811b24e65ac2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="schema-mapping-in-copy-activity"></a>Mapeamento de esquema na atividade de cópia
Este artigo descreve como a atividade de cópia do Azure Data Factory faz o mapeamento de esquema e de tipo de dados dos dados de origem para os dados do coletor ao realizar a cópia dos dados.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Documentação do Data Factory versão 1](v1/data-factory-introduction.md).


## <a name="column-mapping"></a>Mapeamento de coluna

Por padrão, atividade de cópia **mapeia dados de origem para o coletor por nomes de coluna**, a menos que o [mapeamento de coluna explícito](#explicit-column-mapping) esteja configurado. Mais especificamente, a atividade de cópia:

1. Lê os dados da origem e determina o esquema de origem

    * Para fontes de dados com um esquema predefinido no armazenamento de dados/formato de arquivo, por exemplo, bancos de dados/arquivos com metadados (Avro/ORC/Parquet/Texto com cabeçalho), o esquema de origem é extraído do resultado da consulta ou dos metadados do arquivo.
    * Para fontes de dados com esquema flexível, por exemplo, Tabela do Azure/Cosmos DB, o esquema de origem é inferido do resultado da consulta. Você pode substituí-lo fornecendo "structure" no conjunto de dados.
    * Para o arquivo de texto sem cabeçalho, os nomes de coluna padrão são gerados com o padrão "Prop_0", "Prop_1",... Você pode substituí-lo fornecendo "structure" no conjunto de dados.
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
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    }
}
```

**Fluxo de mapeamento de coluna:**

![Fluxo de mapeamento de coluna](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="data-type-mapping"></a>Mapeamento de tipo de dados

A atividade de cópia executa o mapeamento dos tipos de origem para os tipos de coletor com a abordagem de duas etapas a seguir:

1. Converter de tipos de origem nativos para tipos de dados provisórios do Azure Data Factory
2. Converter de tipos de dados provisórios do Azure Data Factory para o tipo de coletor nativo

Você pode encontrar o mapeamento entre o tipo nativo para o tipo provisório na seção "Mapeamento de tipo de dados" em cada tópico de conector.

### <a name="supported-data-types"></a>Tipos de dados com suporte

O Data Factory dá suporte aos seguintes tipos de dados provisórios: você pode especificar abaixo dos valores ao fornecer informações de tipo em [estrutura do conjunto de dados](concepts-datasets-linked-services.md#dataset-structure) configuração:

* Byte[]
* Booliano
* Datetime
* Datetimeoffset
* Decimal
* Duplo
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

* Copiar do arquivo de texto sem cabeçalho (conjunto de dados de entrada). Você pode especificar os nomes de coluna para o arquivo de texto alinhando com as colunas correspondentes do coletor, para poupar do fornecimento de mapeamento de coluna explícito.
* Copiar dados dos armazenamentos de dados com esquema flexível, por exemplo, Tabela do Azure/Cosmos DB (conjunto de dados de entrada), para garantir os dados (colunas) esperados sendo copiados em vez de deixar a atividade de cópia inferir o esquema com base nas linhas superiores durante cada execução da atividade.


## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Tolerância a falhas da atividade de cópia](copy-activity-fault-tolerance.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)
