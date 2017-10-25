---
title: "Atividade de obtenção de metadados no Azure Data Factory | Microsoft Docs"
description: "Saiba como é possível usar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado em um banco de dados SQL do Azure ou SQL Data Warehouse do Azure de um pipeline de Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: shlo
ms.openlocfilehash: 99182b2ed91f6d60f499be0078077bf52fe8b366
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>A atividade de obtenção de metadados no Azure Data Factory
A atividade GetMetadata pode ser usada para recuperar metadados de todos os dados no Azure Data Factory. Esta atividade tem suporte apenas data factories da versão 2. Ela pode ser usada nos seguintes cenários:

- Validar as informações de metadados de todos os dados
- Disparar um pipeline quando os dados estão prontos/disponíveis

A seguinte funcionalidade está disponível no fluxo de controle:
- A saída da atividade GetMetadata pode ser usada em expressões condicionais para executar a validação.
- Um pipeline pode ser disparado quando a condição é atendida por meio do loop Do-Until

A atividade GetMetadata utiliza um conjunto de dados como uma entrada necessária e gera informações de metadados disponíveis como a saída. Atualmente, há suporte apenas para o conjunto de dados do Blob d Azure. Os campos de metadados com suporte são o tamanho, a estrutura e a hora de lastModified.  

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Documentação do Data Factory V1](v1/data-factory-introduction.md).


## <a name="syntax"></a>Sintaxe

### <a name="get-metadata-activity-definition"></a>Definição da atividade de obtenção de metadados:
No exemplo a seguir, a atividade GetMetadata retorna metadados sobre os dados representados pelo MyDataset. 

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```
### <a name="dataset-definition"></a>Definição de conjunto de dados:

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>Saída
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>Propriedades de tipo
No momento, a atividade GetMetadata pode buscar os seguintes tipos de informações de metadados do conjunto de dados do Armazenamento do Azure.

Propriedade | Descrição | Valores Permitidos | Obrigatório
-------- | ----------- | -------------- | --------
fieldList | Lista os tipos de informações de metadados necessárias.  | <ul><li>tamanho</li><li>estrutura</li><li>lastModified</li></ul> |    Não<br/>Se estiver vazio, a atividade retornará todas as três informações de metadados com suporte. 
conjunto de dados | O conjunto de dados de referência cuja atividade de metadados deve ser recuperada pela atividade GetMetadata. <br/><br/>O tipo de conjunto de dados com suporte no momento é o Blob do Azure. Duas subpropriedades são: <ul><li><b>referenceName</b>: referência a um conjunto de dados do Blob do Azure existente</li><li><b>type</b>: como o conjunto de dados está sendo referenciado, é do tipo "DatasetReference"</li></ul> |    <ul><li>Cadeia de caracteres</li><li>DatasetReference</li></ul> | Sim

## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)