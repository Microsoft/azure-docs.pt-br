---
title: Definir Atividade Variável no Azure Data Factory
description: Saiba como usar a atividade Definir Variável para definir o valor de uma variável existente definida em um pipeline do Data Factory
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/07/2020
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.openlocfilehash: 113829dd35c14b5efae39c55a8085dcd2c1ecef4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786151"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Definir Atividade Variável no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a atividade Definir Variável para definir o valor de uma variável existente do tipo Cadeia de caracteres, Booliano ou Matriz definido em um pipeline do Data Factory.

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
name | Nome da atividade no pipeline | sim
descrição | Texto descrevendo o que a atividade realiza | não
type | Deve ser definido como **SetVariable** | sim
value | Valor de objeto de expressão ou literal de cadeia de caracteres ao qual a variável é atribuída | sim
variableName | O nome da variável definida por essa atividade | sim

## <a name="incrementing-a-variable"></a>Incrementando uma variável

Um cenário comum envolvendo variáveis no Azure Data Factory é usar uma variável como um iterador dentro de uma atividade until ou foreach. Em uma atividade set variable, você não pode fazer referência à variável que está sendo definida no campo `value`. Para solucionar essa limitação, defina uma variável temporária e, em seguida, crie uma segunda atividade set variable. A segunda atividade set variable define o valor do iterador para a variável temporária. 

Abaixo está um exemplo desse padrão:

![Incrementar a variável](media/control-flow-set-variable-activity/increment-variable.png "Incrementar variável")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre uma atividade de fluxo de controle relacionada com suporte pelo Data Factory: 

- [Acrescentar Atividade Variável](control-flow-append-variable-activity.md)
