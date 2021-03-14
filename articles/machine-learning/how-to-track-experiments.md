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
ms.openlocfilehash: 3d970193bd8d73baeac89fb45da4c8a3d81cbde4
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518392"
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

Registre em log vários tipos de dados, incluindo valores escalares, listas, tabelas, imagens, diretórios, entre outros. Para obter mais informações e exemplos de código Python para diferentes tipos de dados, confira a [página de referência da Classe de execução](/python/api/azureml-core/azureml.core.run%28class%29).

### <a name="logging-run-metrics"></a>Métricas de execução de log 

Use os métodos a seguir nas APIs de log para influenciar as visualizações de métricas. Observe os [limites de serviço](https://docs.microsoft.com/azure/machine-learning/resource-limits-quotas-capacity#metrics) para essas métricas registradas. 

|Valor conectado|Código de exemplo| Formato no portal|
|----|----|----|
|Registrar uma matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|gráfico de linhas de variável único|
|Registre um único valor numérico com o mesmo nome de métrica usado repetidamente (como em um loop for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Gráfico de linhas de variável-único|
|Faça uma linha com colunas numéricas 2 repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de linhas de duas variáveis|
|Tabela de log com 2 colunas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de linhas de duas variáveis|
|Imagem de log|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Use este método para registrar um arquivo de imagem ou um gráfico matplotlib na execução. Essas imagens estarão visíveis e comparáveis no registro de execução|

### <a name="logging-with-mlflow"></a>Registrando em log com MLflow
Use MLFlowLogger para registrar métricas.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="interactive-logging-session"></a>Sessão de log interativo

As sessões de log interativo normalmente são usadas em ambientes de notebook. O método [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) inicia uma sessão de log interativo. Qualquer métrica registrada em log durante a sessão é adicionada ao registro de execução no experimento. O método [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) encerra as sessões e marca a execução como concluída.

## <a name="scriptrun-logs"></a>Logs do ScriptRun

Nesta seção, você aprenderá a adicionar o código de log dentro de execuções criadas durante a configuração do ScriptRunConfig. Use a classe [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig) para encapsular scripts e ambientes de execuções repetíveis. Use também essa opção para mostrar um widget de visual do Jupyter Notebooks para monitoramento.

Este exemplo executa uma limpeza de parâmetro em valores alfa e captura os resultados usando o método [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

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
