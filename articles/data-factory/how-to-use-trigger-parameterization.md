---
title: Passar informações do gatilho para o pipeline
description: Saiba como referenciar metadados de gatilho no pipeline
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 57024d6cb23ab273aa69bf59f4ec436a6f873a05
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193390"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>Metadados de gatilho de referência em execuções de pipeline

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como os metadados de gatilho, como a hora de início do gatilho, podem ser usados na execução do pipeline.

Às vezes, o pipeline precisa entender e ler metadados do gatilho que o invoca. Por exemplo, com a execução do gatilho de janela em cascata, com base na hora de início e de término da janela, o pipeline processará diferentes fatias ou pastas de dados. Em Azure Data Factory, usamos parametrização e a [variável do sistema](control-flow-system-variables.md) para passar metadados do gatilho para o pipeline.

Esse padrão é especialmente útil para o [gatilho de janela em cascata](how-to-create-tumbling-window-trigger.md), em que Trigger fornece a hora de início e término da janela.

> [!NOTE]
> O tipo de gatilho diferente fornece informações de metadados diferentes. Para obter mais informações, consulte [variável do sistema](control-flow-system-variables.md)

## <a name="data-factory-ui"></a>IU do Data Factory

Esta seção mostra como passar informações de metadados do gatilho para o pipeline, dentro da interface do usuário Azure Data Factory.

1. Ir para a **tela de criação** e editar um pipeline

1. Clique na tela em branco para exibir as configurações de pipeline. Não selecione nenhuma atividade. Talvez seja necessário efetuar pull do painel de configuração na parte inferior da tela, pois ele pode ter sido recolhido

1. Selecione a seção **parâmetros** e selecione **+ novo** para adicionar parâmetros

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="Captura de tela de configuração de pipeline mostrando como definir parâmetros no pipeline.":::

1. Adicione gatilhos ao pipeline clicando em **+ gatilho**.

1. Crie ou anexe um gatilho ao pipeline e clique em **OK**

1. Na página a seguir, preencha o gatilho meta data para cada parâmetro. Use o formato definido na [variável do sistema](control-flow-system-variables.md) para recuperar informações de gatilho. Você não precisa preencher as informações de todos os parâmetros, apenas aqueles que presumirão disparar valores de metadados. Por exemplo, aqui atribuímos a hora de início da execução do gatilho para *parameter_1*.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="Captura de tela da página de definição do gatilho mostrando como passar informações de gatilho para parâmetros de pipeline.":::

1. Para usar os valores no pipeline, utilize os parâmetros _@pipeline (). Parameters. ParameterName_, __não__ a variável do sistema, nas definições de pipeline. Por exemplo, em nosso caso, para ler a hora de início do gatilho, vamos referenciar @pipeline () .Parameters.parameter_1.

## <a name="json-schema"></a>JSON schema

Para transmitir informações de gatilho para execuções de pipeline, o gatilho e o JSON de pipeline precisam ser atualizados com a seção de _parâmetros_ .

### <a name="pipeline-definition"></a>Definição de pipeline

Na seção **Propriedades** , adicione definições de parâmetro à seção de **parâmetros**

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>Definição do gatilho

Na seção **pipelines** , atribua valores de parâmetro na seção **parâmetros** . Você não precisa preencher as informações de todos os parâmetros, apenas aqueles que presumirão disparar valores de metadados.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>Usar informações de gatilho no pipeline

Para usar os valores no pipeline, utilize os parâmetros _@pipeline (). Parameters. ParameterName_, __não__ a variável do sistema, nas definições de pipeline.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações detalhadas sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
