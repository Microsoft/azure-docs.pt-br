---
title: Executar pipelines de Azure Machine Learning
description: Saiba como executar seus pipelines de Azure Machine Learning em seus pipelines de Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
ms.date: 07/16/2020
ms.openlocfilehash: db1816baf0ebc476a132b344d8db3cdbdd170e50
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373922"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Executar pipelines de Azure Machine Learning no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Execute seus pipelines de Azure Machine Learning como uma etapa em seus pipelines de Azure Data Factory. A atividade Machine Learning executar pipeline permite cenários de previsão de lote, como a identificação de possíveis padrões de empréstimo, a determinação de sentimentos e a análise de padrões de comportamento do cliente.

O vídeo abaixo apresenta uma introdução e uma demonstração de seis minutos desse recurso.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Propriedades de tipo

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
name | Nome da atividade no pipeline | String | Sim
type | O tipo de atividade é ' AzureMLExecutePipeline ' | String | Sim
linkedServiceName | Serviço vinculado para Azure Machine Learning | Referência de serviço vinculado | Sim
mlPipelineId | ID do pipeline de Azure Machine Learning publicado | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Sim
experimentoname | Nome de teste de execução do histórico da execução do pipeline de Machine Learning | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não
mlPipelineParameters | Pares de chave, valor a serem passados para o ponto de extremidade do pipeline de Azure Machine Learning publicado. As chaves devem corresponder aos nomes dos parâmetros de pipeline definidos no pipeline de Machine Learning publicado | Objeto com pares chave-valor (ou expressão com o objeto ResultType) | Não
mlParentRunId | A ID de execução do pipeline Azure Machine Learning pai | Cadeia de caracteres (ou expressão com um resultType de cadeia de caracteres) | Não
continueOnStepFailure | Se deseja continuar a execução de outras etapas no pipeline de Machine Learning executar se uma etapa falhar | booleano | Não

> [!NOTE]
> Para preencher os itens suspensos em Machine Learning nome e ID do pipeline, o usuário precisa ter permissão para listar pipelines ML. O ADF UX chama as APIs do AzureMLService diretamente usando as credenciais do usuário conectado.  

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras:

* [Executar atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [U-SQL activity](transform-data-using-data-lake-analytics.md) (Atividade do U-SQL)
* [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive)
* [Pig activity](transform-data-using-hadoop-pig.md) (Atividade do Pig)
* [MapReduce activity](transform-data-using-hadoop-map-reduce.md) (Atividade do MapReduce)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md) (Atividade de streaming do Hadoop)
* [Spark activity](transform-data-using-spark.md) (Atividade do Spark)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Stored procedure activity](transform-data-using-stored-procedure.md) (Atividade de procedimento armazenado)
