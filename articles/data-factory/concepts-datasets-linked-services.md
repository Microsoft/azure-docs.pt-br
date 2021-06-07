---
title: Conjunto de dados
description: Saiba mais sobre conjuntos de dados no Data Factory. Os conjuntos de dados representam os dados de entrada/saída.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/24/2020
ms.openlocfilehash: 167fb0014f2f0a9e7a2530fe276289f94347146e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785777"
---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de dados no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-create-datasets.md)
> * [Versão atual](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Este artigo descreve o que são conjuntos de dados, como eles são definidos no formato JSON e como são usados em pipelines do Azure Data Factory.

Se estiver conhecendo o Azure Data Factory agora, consulte [Introdução ao Azure Data Factory](introduction.md) para obter uma visão geral.

## <a name="overview"></a>Visão geral
Uma fábrica de dados pode ter um ou mais pipelines. Um **pipeline** é um agrupamento lógico de **atividades** que juntas executam uma tarefa. As atividades em um pipeline definem ações para executar em seus dados. Por outro lado, um **conjunto de dados** é uma exibição nomeada de dados que simplesmente aponta ou faz referência aos dados que você deseja usar em suas **atividades** como entradas e saídas. Conjuntos de dados identificam dados em armazenamentos de dados diferentes, como tabelas, arquivos, pastas e documentos. Por exemplo, um conjunto de dados de Blob do Azure especifica o contêiner de blobs e a pasta no armazenamento de Blobs dos quais a atividade deve ler os dados.

Antes de criar um conjunto de dados, crie um [**serviço vinculado**](concepts-linked-services.md) para vincular seu armazenamento de dados ao data factory. Serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para o Data Factory para se conectar a recursos externos. Pense dessa maneira: o conjunto de dados representa a estrutura dos dados nos armazenamentos de dados vinculados e o serviço vinculado define a conexão à fonte de dados. Por exemplo, um serviço vinculado do Armazenamento do Azure vincula uma conta de armazenamento ao data factory. Um conjunto de dados de Blob do Azure representa o contêiner de blob e a pasta na conta de Armazenamento do Azure que contém os blobs de entrada a serem processados.

Veja abaixo um cenário de exemplo. Para copiar dados do armazenamento de BLOBs para um banco de dado SQL, você cria dois serviços vinculados: armazenamento de BLOBs do Azure e banco de dados SQL do Azure. Em seguida, crie dois conjuntos de dados: conjunto de dados de texto delimitado (que se refere ao serviço vinculado do armazenamento de BLOBs do Azure, supondo que você tenha arquivos de texto como fonte) e o conjunto de dados da tabela SQL do Azure (que se refere ao serviço vinculado do banco de dados SQL). O armazenamento de BLOBs do Azure e os serviços vinculados do banco de dados SQL do Azure contêm cadeias de conexão que Data Factory usa em tempo de execução para se conectar ao armazenamento do Azure e ao banco de dados SQL do Azure O conjunto de dados de texto delimitado especifica o contêiner de BLOB e a pasta de BLOB que contém os blobs de entrada no armazenamento de BLOBs, juntamente com as configurações relacionadas ao formato. O conjunto de dados de Tabela do SQL do Azure especifica a tabela do SQL no Banco de Dados SQL no qual os dados serão copiados.

O seguinte diagrama mostra a relação entre pipeline, atividade, conjunto de dados e serviço vinculado no Data Factory:

![Relação entre pipeline, atividade e conjunto de dados, serviços vinculados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Conjunto de dados do JSON
Um conjunto de dados no Data Factory é definido no formato JSON a seguir:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
A tabela a seguir descreve as propriedades no JSON acima:

Propriedade | Descrição | Obrigatório |
-------- | ----------- | -------- |
name | Nome do conjunto de dados. Consulte [Azure Data Factory – Regras de nomenclatura](naming-rules.md). |  Sim |
type | Tipo de conjunto de dados. Especifique um dos tipos com suporte pelo Data Factory (por exemplo: DelimitedText, AzureSqlTable). <br/><br/>Para obter detalhes, consulte [Tipos de conjunto de dados](#dataset-type). | Sim |
esquema | Esquema do conjunto de dados representa a forma e o tipo de dado físico. | Não |
typeProperties | As propriedades de tipo são diferentes para cada tipo. Para obter detalhes sobre os tipos com suporte e suas propriedades, consulte [Tipo de conjunto de dados](#dataset-type). | Sim |

Ao importar o esquema do conjunto de uma, selecione o botão **importar esquema** e escolha Importar da origem ou de um arquivo local. Na maioria dos casos, você importará o esquema diretamente da fonte. Mas se já tiver um arquivo de esquema local (um arquivo Parquet ou CSV com cabeçalhos), você poderá instruir o Data Factory a basear-se no esquema nesse arquivo.

Na atividade de cópia, os conjuntos de valores são usados na origem e no coletor. O esquema definido em DataSet é opcional como referência. Se você quiser aplicar o mapeamento de coluna/campo entre a origem e o coletor, consulte [esquema e mapeamento de tipo](copy-activity-schema-and-type-mapping.md).

No Fluxo de Dados, os conjuntos de dados são usados em transformações da fonte e do coletor. Os conjuntos de dados definem os esquemas de dados básicos. Se os dados não tiverem um esquema, use o descompasso de esquema para a fonte e o coletor. Os metadados dos conjuntos de dados aparecem na transformação da fonte como a projeção da fonte. A projeção na transformação da fonte representa os dados do Fluxo de Dados com nomes e tipos definidos.

## <a name="dataset-type"></a>Tipo de conjunto de dados

O Azure Data Factory dá suporte a muitos tipos diferentes de conjuntos de dados, dependendo dos armazenamentos que você usa. Você pode encontrar a lista de armazenamentos de dados com suporte pelo Data Factory do artigo [visão geral do conector](connector-overview.md) . Clique em um armazenamento de dados para saber como criar um serviço vinculado e um conjunto de dado para ele.

Por exemplo, para um conjunto de texto delimitado, o tipo DataSet é definido como **DelimitedText** , conforme mostrado no exemplo de JSON a seguir:

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

## <a name="create-datasets"></a>Criar conjuntos de dados
Você pode criar conjuntos de dados usando uma dessas ferramentas ou SDKs: [API do .NET](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [API REST](quickstart-create-data-factory-rest-api.md), modelo do Azure Resource Manager e Portal do Azure

## <a name="current-version-vs-version-1-datasets"></a>Conjuntos de dados da versão atual vs. versão 1

A seguir, são apresentadas algumas diferenças entre os conjuntos de dados do Data Factory e Data Factory versão 1:

- A propriedade externa não é tem suporte na versão atual. Ela é substituída por um [gatilho](concepts-pipeline-execution-triggers.md).
- Não há suporte para as propriedades de disponibilidade e política na versão atual. A hora de início de um pipeline depende de [gatilhos](concepts-pipeline-execution-triggers.md).
- Não há suporte para conjuntos de dados com escopo (conjuntos de dados definidos em um pipeline) na versão atual.

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes tutoriais para obter instruções passo a passo para criar pipelines e conjuntos de dados usando uma destas ferramentas ou SDKs.

- [Início rápido: criar um data factory usando o .NET](quickstart-create-data-factory-dot-net.md)
- [Início rápido: criar um data factory usando o PowerShell](quickstart-create-data-factory-powershell.md)
- [Início rápido: criar um data factory usando a API REST](quickstart-create-data-factory-rest-api.md)
- [Início rápido: criar um data factory usando o portal do Azure](quickstart-create-data-factory-portal.md)
