---
title: Destinos de computação remotos do ML automatizado
titleSuffix: Azure Machine Learning
description: Saiba como criar modelos usando o aprendizado de máquina automatizado em um Azure Machine Learning de destino de computação remota com Azure Machine Learning
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.custom: how-to, automl
ms.date: 03/09/2020
ms.openlocfilehash: 402af581e0ca6de9936b78106840a4fe273069d8
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503446"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Treinar modelos com o aprendizado de máquina automatizado na nuvem



No Azure Machine Learning, você treina seu modelo em diferentes tipos de recursos de computação que gerencia. O destino de computação pode ser um computador local ou um recurso na nuvem.

Você pode facilmente escalar ou escalar horizontalmente seu experimento de aprendizado de máquina adicionando destinos de computação adicionais, como computação de Azure Machine Learning (AmlCompute). A AmlCompute é uma infraestrutura de computação gerenciada que permite ao usuário criar facilmente uma computação de nó único ou de vários nós.

Neste artigo, você aprenderá a criar um modelo usando o ML automatizado com AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>Como remoto difere do local?

Mais recursos estão disponíveis quando você usa um destino de computação remota.  Para obter mais detalhes, consulte [destinos de computação locais e remotos](concept-automated-ml.md#local-remote).

O tutorial "[treinar um modelo de classificação com o Machine Learning automatizado](tutorial-auto-train-models.md)" ensina a usar um computador local para treinar um modelo com ml automatizado. O fluxo de trabalho durante o treinamento local também se aplica aos destinos remotos. Para treinar remotamente, você primeiro cria um destino de computação remota, como AmlCompute. Em seguida, configura o recurso remoto e envia seu código para ele.

Este artigo mostra as etapas adicionais necessárias para executar um experimento de ML automatizado em um destino de AmlCompute remoto. O objeto de workspace, `ws`, do tutorial, é usado em todo o código aqui.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Criar recurso

Crie o [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29) destino em seu espaço de trabalho ( `ws` ) se ele ainda não existir.

**Tempo estimado**: a criação do destino AmlCompute leva aproximadamente 5 minutos.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Agora você pode usar o objeto `compute_target` como o destino de computação remota.

As restrições de nome de cluster incluem:
+ Deve ser menor do que 64 caracteres.
+ Não pode incluir nenhum dos seguintes caracteres: `\` ~! @ # $% ^ & * () = + _ [] {} \\ \\ |;: \' \\ ",  < > /?. `

## <a name="access-data-using-tabulardataset-function"></a>Acessar dados usando a função TabularDataset

Definido training_data como [`TabularDataset`](/python/api/azureml-core/azureml.data.tabulardataset) e o rótulo, que são passados para ml automatizado no [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) . O `TabularDataset` método `from_delimited_files` , por padrão, define o `infer_column_types` como true, o que inferirá o tipo de colunas automaticamente. 

Se você deseja definir manualmente os tipos de coluna, pode definir o `set_column_types` argumento para definir manualmente o tipo de cada coluna. No exemplo de código a seguir, os dados vêm do pacote sklearn.

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists('project_folder'):
    os.makedirs('project_folder')

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
```

## <a name="configure-experiment"></a>Configurar o experimento
Especifique as configurações para `AutoMLConfig`.  (Veja uma [lista completa de parâmetros](how-to-configure-auto-train.md#configure-experiment) e seus valores possíveis.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>Enviar o teste de treinamento

Agora, envie a configuração para selecionar automaticamente o algoritmo, os hiperparâmetros e treinar o modelo.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'Tutorial-automl-remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Você verá uma saída semelhante ao exemplo a seguir:

```output
Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
***********************************************************************************************
ITERATION: The iteration being evaluated.
PIPELINE:  A summary description of the pipeline being evaluated.
DURATION: Time taken for the current iteration.
METRIC: The result of computing score on the fitted pipeline.
BEST: The best observed score thus far.
***********************************************************************************************

 ITERATION     PIPELINE                               DURATION                METRIC      BEST
         2      Standardize SGD classifier            0:02:36                  0.954     0.954
         7      Normalizer DT                         0:02:22                  0.161     0.954
         0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
         4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
         1      Normalizer kNN                        0:02:44                  0.984     0.984
         9      Normalizer extra trees                0:03:15                  0.834     0.984
         5      Robust Scaler DT                      0:02:18                  0.736     0.984
         8      Standardize kNN                       0:02:05                  0.981     0.984
         6      Standardize SVM                       0:02:18                  0.984     0.984
        10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
        11      Standardize SGD classifier            0:02:24                  0.863     0.984
         3      Standardize gradient boosting         0:03:03                  0.971     0.984
        12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
        14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
        13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
        15      Robust Scaler kNN                     0:02:28                  0.904     0.989
        17      Standardize kNN                       0:02:22                  0.974     0.989
        16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
        18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
        19      Robust Scaler kNN                     0:02:32                  0.983     0.989
```

## <a name="explore-results"></a>Explorar os resultados

Você pode usar o mesmo [widget Jupyter](/python/api/azureml-widgets/azureml.widgets) , conforme mostrado no [tutorial de treinamento](tutorial-auto-train-models.md#explore-the-results) para ver um gráfico e uma tabela de resultados.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Aqui está uma imagem estática do widget.  No notebook, você pode clicar em qualquer linha na tabela para ver as propriedades de execução e os logs de saída para a execução.   Você também pode usar a lista suspensa acima do gráfico para exibir um gráfico de cada métrica disponível para cada iteração.

![tabela de widget](./media/how-to-auto-train-remote/table.png)
![gráfico de widget](./media/how-to-auto-train-remote/plot.png)

O widget exibirá uma URL que você pode usar para ver e explorar os detalhes individuais da execução.  

Se você não estiver em um notebook Jupyter, poderá exibir a URL da própria execução:

```
remote_run.get_portal_url()
```

As mesmas informações estão disponíveis no seu espaço de trabalho.  Para saber mais sobre esses resultados, confira [avaliar os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md).

## <a name="example"></a>Exemplo

O [Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) a seguir demonstra os conceitos neste artigo.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* Saiba [como definir as configurações para treinamento automático](how-to-configure-auto-train.md).
* Consulte [como](how-to-machine-learning-interpretability-automl.md) habilitar recursos de interpretação de modelo em experimentos de ml automatizados.