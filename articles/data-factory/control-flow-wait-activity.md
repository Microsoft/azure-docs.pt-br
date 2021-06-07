---
title: Atividade Wait no Azure Data Factory
description: A atividade Wait pausa a execução do pipeline pelo período especificado.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 9271723e68e5edd6805f0197bca9cdefc3c5e921
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100388321"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Executar atividade de espera no Azure Data Factory
Quando você usa uma atividade de espera em um pipeline, o pipeline aguarda o período de tempo especificado antes de continuar com a execução de atividades subsequentes. 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


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

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
name | Nome da atividade `Wait`. | String | Sim
type | Deve ser definido para **Wait**. | String | Sim
waitTimeInSeconds | O número de segundos que o pipeline aguarda antes de continuar o processamento. | Inteiro | Sim

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

- [Atividade Condição Se](control-flow-if-condition-activity.md)
- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
- [Atividade Until](control-flow-until-activity.md)
