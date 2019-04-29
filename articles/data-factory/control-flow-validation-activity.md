---
title: Atividade de validação no Azure Data Factory | Microsoft Docs
description: A atividade de validação não continuar a execução do pipeline até que ele valida o conjunto de dados anexado com determinados critérios especificados pelo usuário.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764315"
---
# <a name="validation-activity-in-azure-data-factory"></a>Atividade de validação no Azure Data Factory
Você pode usar uma validação em um pipeline para garantir que o pipeline continua apenas a execução depois que ele tiver validado o anexo existe referência de conjunto de dados, que ele atende aos critérios especificados ou tempo limite foi atingido.


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

Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
Nome | Nome da atividade 'Validação' | Cadeia de caracteres | Sim |
Tipo | Deve ser definido como **validação**. | Cadeia de caracteres | Sim |
dataset | Atividade irá impedir a execução até que ele foi validado essa referência de conjunto de dados existe e que ele atende aos critérios especificados ou tempo limite foi atingido. Conjunto de dados fornecido deve dar suporte a propriedade "MinimumSize" ou "ChildItems". | Referência de conjunto de dados | Sim |
Tempo limite | Especifica o tempo limite para a atividade ser executada. Se nenhum valor for especificado, o valor padrão é 7 dias ("7.00:00:00"). O formato é d.hh:mm:ss | Cadeia de caracteres | Não  |
Modo de suspensão | Um atraso em segundos entre tentativas de validação. Se nenhum valor for especificado, o valor padrão é 10 segundos. | Número inteiro | Não  |
childItems | Verifica se a pasta tem itens filhos. Pode ser definido para true: Valide a pasta existe e se ele tem itens. Bloqueia até que pelo menos um item está presente na pasta ou valor de tempo limite seja atingido.-false: Valide a pasta existe e que ele está vazio. Bloqueia até que a pasta está vazia ou tempo limite de valor é atingido. Se nenhum valor for especificado, a atividade bloqueará até a pasta existe ou até que o tempo limite for atingido. | Boolean | Não  |
minimumSize | Tamanho mínimo de um arquivo em bytes. Se nenhum valor for especificado, o valor padrão é 0 bytes | Número inteiro | Não  |


## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory:

- [Atividade de Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
