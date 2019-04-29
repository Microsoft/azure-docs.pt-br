---
title: Atividade Wait no Azure Data Factory | Microsoft Docs
description: A atividade Wait pausa a execução do pipeline pelo período especificado.
services: data-factory
documentationcenter: ''
author: shlo
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/12/2018
ms.author: shlo
ms.openlocfilehash: 66d79bc1597cd8f3c7e01eb8227eb7c91ba04d1d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764744"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Executar a atividade wait no Azure Data Factory
Quando você usa uma atividade de espera em um pipeline, o pipeline aguarda o período de tempo especificado antes de continuar com a execução de atividades subsequentes. 

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | DESCRIÇÃO | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
Nome | Nome da atividade `Wait`. | Cadeia de caracteres | Sim
tipo | Deve ser definido para **Wait**. | Cadeia de caracteres | Sim
waitTimeInSeconds | O número de segundos que o pipeline aguarda antes de continuar o processamento. | Número inteiro | Sim

## <a name="example"></a>Exemplo

> [!NOTE]
> Esta seção fornece definições de JSON e comandos de exemplo do PowerShell para executar o pipeline. Para obter instruções com instruções passo a passo para criar um pipeline do Data Factory usando definições de JSON e do Azure PowerShell, consulte o [tutorial: criar um Data Factory usando o Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Pipeline com atividade Wait
Neste exemplo, o pipeline tem duas atividades: **Until** e **Wait**. A atividade Wait é configurada para esperar por um segundo. O pipeline executa a atividade da Web em um loop com um tempo de espera de um segundo após cada execução. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade de Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
