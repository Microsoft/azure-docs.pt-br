---
title: Métricas de log no designer
titleSuffix: Azure Machine Learning
description: Monitore seus experimentos do Azure ML designer. Habilite o registro em log usando o módulo executar script Python e exiba os resultados registrados no estúdio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 01/11/2021
ms.topic: conceptual
ms.custom: designer
ms.openlocfilehash: b940f5c9bd14bcec404827daaef666da802d969b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98065245"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Habilitar o log em pipelines do designer de Azure Machine Learning


Neste artigo, você aprende a adicionar o código de registro em log a pipelines de designer. Você também aprenderá a exibir esses logs usando o portal da Web do Azure Machine Learning Studio.

Para obter mais informações sobre as métricas de log usando a experiência de criação do SDK, consulte [monitorar execuções e métricas de experimento do Azure ml](how-to-track-experiments.md).

## <a name="enable-logging-with-execute-python-script"></a>Habilitar log com script de execução de Python

Use o módulo [Executar script Python](./algorithm-module-reference/execute-python-script.md) para habilitar o registro em pipelines do designer. Embora você possa registrar qualquer valor com esse fluxo de trabalho, é especialmente útil registrar métricas do módulo __modelo de avaliação__ para controlar o desempenho do modelo entre as execuções.

O exemplo a seguir mostra como registrar em log o erro de quadrado médio de dois modelos treinados usando os módulos avaliar modelo e executar script Python.

1. Conecte um módulo __Executar script Python__ à saída do módulo __modelo de avaliação__ .

    ![Conectar o módulo Executar script Python ao módulo Avaliar Modelo](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Cole o código a seguir no editor de código de __script Python de execução__ para registrar o erro de média absoluta para seu modelo treinado. Você pode usar um padrão semelhante para registrar qualquer outro valor no designer:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model. The following line should be deleted if you only connect one Score Module to the` left port of Evaluate Model module.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
Esse código usa o SDK do Python Azure Machine Learning para registrar valores. Ele usa Run.get_context () para obter o contexto da execução atual. Em seguida, ele registra valores nesse contexto com o método Run. Parent. log (). Ele usa `parent` para registrar valores na execução do pipeline pai em vez da execução do módulo.

Para obter mais informações sobre como usar o SDK do Python para registrar valores, consulte [habilitar o registro em log nas execuções de treinamento do Azure ml](how-to-track-experiments.md).

## <a name="view-logs"></a>Exibir logs

Depois que a execução do pipeline for concluída, você poderá ver o *Mean_Absolute_Error* na página experimentos.

1. Navegue até a seção **experimentos** .
1. Selecione seu experimento.
1. Selecione a execução em seu experimento que você deseja exibir.
1. Selecione **Métricas**.

    ![Exibir métricas de execução no estúdio](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar os logs no designer. Para as próximas etapas, consulte estes artigos relacionados:


* Saiba como solucionar problemas de pipelines de designer, consulte [Debug & solucionar problemas de pipelines de ml](how-to-debug-pipelines.md#azure-machine-learning-designer).
* Saiba como usar o SDK do Python para registrar métricas na experiência de criação do SDK, consulte [habilitar o log nas execuções de treinamento do Azure ml](how-to-track-experiments.md).
* Saiba como usar o [script Python em execução](./algorithm-module-reference/execute-python-script.md) no designer.
