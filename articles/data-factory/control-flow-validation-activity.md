---
title: Atividade de validação no Azure Data Factory
description: A atividade de validação não continua a execução do pipeline até que ele valide o conjunto de dado anexado com determinados critérios que o usuário especifica.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: e00090fc31c129f35632d30b9a5ed6c39c13a414
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385244"
---
# <a name="validation-activity-in-azure-data-factory"></a>Atividade de validação no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Você pode usar uma validação em um pipeline para garantir que o pipeline continue a execução apenas depois de ter validado a referência do conjunto de entrada anexado, que ele atende aos critérios especificados ou que o tempo limite tenha sido atingido.


## <a name="syntax"></a>Sintaxe

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
name | Nome da atividade de ' validação ' | String | Sim |
type | Deve ser definido como  **validação**. | String | Sim |
dataset | A atividade bloqueará a execução até que tenha validado essa referência de conjunto de e que ele atenda aos critérios especificados ou que o tempo limite tenha sido atingido. O conjunto de conjuntos fornecido deve dar suporte à propriedade "MinimumSize" ou "ChildItems". | Referência de DataSet | Sim |
tempo limite | Especifica o tempo limite para a atividade ser executada. Se nenhum valor for especificado, o valor padrão será 7 dias ("7.00:00:00"). O formato é d. hh: mm: SS | String | Não |
sleep | Um atraso em segundos entre as tentativas de validação. Se nenhum valor for especificado, o valor padrão será 10 segundos. | Integer | Não |
childItems | Verifica se a pasta tem itens filhos. Pode ser definido como-true: valide se a pasta existe e se ela tem itens. Bloqueia até que pelo menos um item esteja presente na pasta ou o valor de tempo limite seja atingido.-false: valide se a pasta existe e se está vazia. Bloqueia até a pasta estar vazia ou até que o valor de tempo limite seja atingido. Se nenhum valor for especificado, a atividade será bloqueada até que a pasta exista ou até que o tempo limite seja atingido. | Boolean | Não |
minimumSize | Tamanho mínimo de um arquivo em bytes. Se nenhum valor for especificado, o valor padrão será 0 bytes | Integer | Não |


## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory:

- [Atividade Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
