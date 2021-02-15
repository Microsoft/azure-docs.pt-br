---
title: Registrar em log experimentos e métricas de ML
titleSuffix: Azure Machine Learning
description: Habilite o log em suas execuções de treinamento de ML para monitorar as métricas de execução em tempo real e para ajudar a diagnosticar erros e avisos.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9e5f64d9ef61a272da488ad70e690db4c07ddccc
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625070"
---
# <a name="enable-logging-in-ml-training-runs"></a>Habilitar log em execuções de treinamento de ML


O SDK do Python do Azure Machine Learning permite que você registre em log informações em tempo real usando o pacote de log padrão do Python e a funcionalidade específica do SDK. Você pode fazer logon localmente e enviar os logs para o seu workspace no portal.

Os logs podem ajudar você a diagnosticar erros e avisos ou acompanhar métricas de desempenho como parâmetros e desempenho do modelo. Neste artigo, você aprenderá a habilitar o log nos seguintes cenários:

> [!div class="checklist"]
> * Sessões de treinamento interativo
> * Envio de trabalhos de treinamento por meio do ScriptRunConfig
> * Configurações do `logging` nativo do Python
> * Log em fontes adicionais


> [!TIP]
> Este artigo mostra como monitorar o processo de treinamento do modelo. Se você estiver interessado em monitorar o uso de recursos e os eventos do Azure Machine Learning, como cotas, execuções de treinamento concluídas ou implantações de modelo concluídas, confira [Monitoramento do Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Tipos de dados

Registre em log vários tipos de dados, incluindo valores escalares, listas, tabelas, imagens, diretórios, entre outros. Para obter mais informações e exemplos de código Python para diferentes tipos de dados, confira a [página de referência da Classe de execução](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py).

## <a name="interactive-logging-session"></a>Sessão de log interativo

As sessões de log interativo normalmente são usadas em ambientes de notebook. O método [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) inicia uma sessão de log interativo. Qualquer métrica registrada em log durante a sessão é adicionada ao registro de execução no experimento. O método [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) encerra as sessões e marca a execução como concluída.

## <a name="scriptrun-logs"></a>Logs do ScriptRun

Nesta seção, você aprenderá a adicionar o código de log dentro de execuções criadas durante a configuração do ScriptRunConfig. Use a classe [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) para encapsular scripts e ambientes de execuções repetíveis. Use também essa opção para mostrar um widget de visual do Jupyter Notebooks para monitoramento.

Este exemplo executa uma limpeza de parâmetro em valores alfa e captura os resultados usando o método [run.log()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truelog-name--value--description----).

1. Crie um script de treinamento que inclua a lógica de log, `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Envie o script ```train.py``` para execução em um ambiente gerenciado pelo usuário. Toda a pasta de script é enviada para treinamento.

   [! Notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = src)]


   [! Notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = Run)]

    O parâmetro `show_output` ativa o log detalhado, o que permite visualizar os detalhes do processo de treinamento, bem como as informações sobre os recursos remotos ou os destinos de computação. Use o código a seguir para ativar o log detalhado ao enviar o experimento.

```python
run = exp.submit(src, show_output=True)
```

É possível usar o mesmo parâmetro na função `wait_for_completion` na execução resultante.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Log nativo do Python

Alguns logs do SDK poderão conter um erro que instrui você a definir o nível de registros em log como DEBUG. Para definir o nível de log, adicione o seguinte código ao script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Fontes de log adicionais

O Azure Machine Learning também pode registrar em log informações de outras fontes durante o treinamento, como execuções de machine learning automatizado ou contêineres do Docker que executam os trabalhos. Esses logs não são documentados, mas se você enfrentar problemas e entrar em contato com o Suporte da Microsoft, eles poderão usar esses logs na solução de problemas.

Para obter informações sobre como registrar métricas em log no designer do Azure Machine Learning, confira [Como registrar métricas em log no designer](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Blocos de anotações de exemplo

Os seguintes blocos de anotações demonstram conceitos neste artigo:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

Confira estes artigos para saber mais sobre como usar o Azure Machine Learning:

* Saiba como [registrar métricas em log no designer do Azure Machine Learning](how-to-track-designer-experiments.md).

* Veja um exemplo de como registrar o melhor modelo e implantá-lo no tutorial [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).
