---
title: Acompanhamento de MLflow para experimentos de Machine Learning
titleSuffix: Azure Machine Learning
description: Configure o MLflow com Azure Machine Learning para registrar métricas e artefatos de modelos de ML e implante seus modelos de ML como um serviço Web.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 02684ba91c207357e15684870a6fa0ceab3e17ff
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520958"
---
# <a name="train-and-track-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Treinar e acompanhar modelos de ML com MLflow e Azure Machine Learning (visualização)

Neste artigo, saiba como habilitar o URI de acompanhamento do MLflow e a API de log, coletivamente conhecido como [acompanhamento de MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), para se conectar Azure Machine Learning como o back-end de seus experimentos de MLflow. 

Os recursos com suporte incluem: 

+ Acompanhe e registre as métricas de experimento e os artefatos em seu [espaço de trabalho Azure Machine Learning](./concept-azure-machine-learning-architecture.md#workspace). Se você já usa o Acompanhamento do MLflow para os experimentos, o espaço de trabalho fornece um local centralizado, seguro e escalável para armazenar métricas e modelos de treinamento.

+ Envie trabalhos de treinamento com [projetos do MLflow](https://www.mlflow.org/docs/latest/projects.html) com suporte de back-end Azure Machine Learning (versão prévia). Você pode enviar trabalhos localmente com rastreamento de Azure Machine Learning ou migrar suas execuções para a nuvem, como por meio de uma [computação Azure Machine Learning](./how-to-create-attach-compute-cluster.md).

+ Acompanhe e gerencie modelos no MLflow e no registro de modelo de Azure Machine Learning.

O [MLflow](https://www.mlflow.org) é uma biblioteca de open-source para gerenciar o ciclo de vida dos experimentos de aprendizado de máquina. O acompanhamento de MLFlow é um componente de MLflow que registra e rastreia suas métricas de execução de treinamento e artefatos de modelo, independentemente do ambiente de seu experimento, localmente em seu computador, em um destino de computação remota, em uma máquina virtual ou [em um cluster Azure Databricks](how-to-use-mlflow-azure-databricks.md). 

>[!NOTE]
> Como uma biblioteca de software livre, o MLflow muda com frequência. Dessa forma, a funcionalidade disponibilizada por meio da integração de Azure Machine Learning e MLflow deve ser considerada uma versão prévia e não tem suporte total da Microsoft.

O diagrama a seguir ilustra isso com o Acompanhamento do MLflow, você acompanha as métricas de execução de um experimento e armazena os artefatos de modelo no workspace do Azure Machine Learning.

![mlflow com o diagrama do Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Este documento oferece informações principalmente para cientistas de dados e desenvolvedores que querem monitorar o processo de treinamento de modelo. Se você for um administrador interessado em monitorar o uso de recursos e os eventos do Azure Machine Learning, como cotas, execuções de treinamento concluídas ou implantações de modelo concluídas, consulte [Monitoramento do Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparar clientes do MLflow e do Azure Machine Learning

 A tabela a seguir resume os diferentes clientes que podem usar Azure Machine Learning e seus respectivos recursos de função.

 O Acompanhamento do MLflow oferece funcionalidades de registro de métricas e armazenamento de artefatos que estão disponíveis apenas por meio do [SDK do Python do Azure Machine Learning](/python/api/overview/azure/ml/intro).

| Funcionalidade | Implantação de & de rastreamento de MLflow | SDK do Python do Azure Machine Learning |  CLI do Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Gerenciar workspace |   | ✓ | ✓ | ✓ |
| Usar armazenamentos de dados  |   | ✓ | ✓ | |
| Registrar métricas      | ✓ | ✓ |   | |
| Carregar artefatos | ✓ | ✓ |   | |
| Métricas de exibição     | ✓ | ✓ | ✓ | ✓ |
| Gerenciar a computação   |   | ✓ | ✓ | ✓ |
| Implantar modelos    | ✓ | ✓ | ✓ | ✓ |
|Monitorar o desempenho do modelo||✓|  |   |
| Detectar descompasso de dados |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Pré-requisitos

* Instale o pacote `azureml-mlflow`. 
    * Esse pacote automaticamente traz a `azureml-core` [Azure Machine Learning SDK do Python](/python/api/overview/azure/ml/install), que fornece a conectividade para MLflow acessar seu espaço de trabalho.
* [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).
    * Veja quais [permissões de acesso você precisa para executar suas operações MLflow com seu espaço de trabalho](how-to-assign-roles.md#mlflow-operations).

## <a name="track-local-runs"></a>Acompanhar execuções locais

O acompanhamento do MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos das execuções locais no workspace do Azure Machine Learning.

Importe as classes `mlflow` e [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) para acessar o URI de acompanhamento do MLflow e configurar o espaço de trabalho.

No código a seguir, o método `get_mlflow_tracking_uri()` atribui um endereço de URI de acompanhamento exclusivo ao espaço de trabalho, `ws` e `set_tracking_uri()` aponta o URI de acompanhamento do MLflow para esse endereço.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>O URI de acompanhamento é válido até uma hora ou menos. Se você reiniciar o script após algum tempo ocioso, use a API get_mlflow_tracking_uri para obter um novo URI.

Defina o nome do experimento do MLflow com `set_experiment()` e inicie a execução de treinamento com `start_run()`. Use `log_metric()` para ativar a API de registro do MLflow e começar a registrar as métricas de execução de treinamento.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Acompanhar execuções remotas

As execuções remotas permitem treinar os modelos em computações mais eficazes, como máquinas virtuais habilitadas para GPU ou clusters de Computação do Machine Learning. Consulte [usar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md) para saber mais sobre diferentes opções de computação.

O acompanhamento do MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos das execuções remotas no workspace do Azure Machine Learning. Qualquer execução com o código de rastreamento MLflow em ti terá as métricas registradas automaticamente no espaço de trabalho. 

O ambiente Conda de exemplo a seguir inclui `mlflow` e `azureml-mlflow` como pacotes Pip. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - numpy
```

Em seu script, configure seu ambiente de computação e de execução de treinamento com a [`Environment`](/python/api/azureml-core/azureml.core.environment.environment) classe. Em seguida, construa  [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) com sua computação remota como o destino de computação.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Com essa configuração de computação e execução de treinamento, use o método `Experiment.submit()` para enviar uma execução. Esse método define automaticamente o URI de acompanhamento do MLflow e direciona o registro do MLflow para o espaço de trabalho.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Treinar com projetos do MLflow

Os [projetos MLflow](https://mlflow.org/docs/latest/projects.html) permitem que você organize e descreva seu código para permitir que outros cientistas de dados (ou ferramentas automatizadas) o executem. Os projetos do MLflow com Azure Machine Learning permitem que você acompanhe e gerencie suas execuções de treinamento em seu espaço de trabalho. 

Este exemplo mostra como enviar projetos MLflow localmente com o rastreamento de Azure Machine Learning.

Instale o `azureml-mlflow` pacote para usar o acompanhamento do MLflow com o Azure Machine Learning em seus experimentos localmente. Seus experimentos podem ser executados por meio de um editor de Jupyter Notebook ou de código.

```shell
pip install azureml-mlflow
```

Importe as classes `mlflow` e [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) para acessar o URI de acompanhamento do MLflow e configurar o espaço de trabalho.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Defina o nome do experimento do MLflow com `set_experiment()` e inicie a execução de treinamento com `start_run()`. Em seguida, use `log_metric()` para ativar a API de log MLflow e começar a registrar suas métricas de execução de treinamento.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Crie o objeto de configuração de back-end para armazenar as informações necessárias para a integração, como o destino de computação e qual tipo de ambiente gerenciado usar.

```python
backend_config = {"USE_CONDA": False}
```
Adicione o `azureml-mlflow` pacote como uma dependência Pip ao seu arquivo de configuração de ambiente para acompanhar métricas e artefatos de chave em seu espaço de trabalho. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Envie a execução local e verifique se você definiu o parâmetro `backend = "azureml" ` . Com essa configuração, você pode enviar execuções localmente e obter o suporte adicional de rastreamento de saída automático, arquivos de log, instantâneos e erros impressos em seu espaço de trabalho. 

Exiba suas execuções e métricas no [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Exibir métricas e artefatos no espaço de trabalho

As métricas e os artefatos no registro do MLflow são mantidos no espaço de trabalho. Para exibi-los a qualquer momento, navegue até o espaço de trabalho e localize o experimento por nome no espaço de trabalho em [Azure Machine Learning Studio](https://ml.azure.com).  Ou execute o código abaixo. 

```python
run.get_metrics()
```

## <a name="manage-models"></a>Gerenciar modelos 

Registre e acompanhe seus modelos com o [registro de modelo de Azure Machine Learning](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) que dá suporte ao registro de modelo MLflow. Os modelos de Azure Machine Learning são alinhados com o esquema de modelo MLflow, facilitando a exportação e a importação desses modelos em diferentes fluxos de trabalho. Os metadados relacionados ao MLflow, como a ID de execução, também são marcados com o modelo registrado para rastreamento. Os usuários podem enviar execuções de treinamento, registrar e implantar modelos produzidos por meio de execuções do MLflow. 

Se você quiser implantar e registrar seu modelo pronto de produção em uma única etapa, consulte [implantar e registrar modelos MLflow](how-to-deploy-mlflow-models.md).

Para registrar e exibir um modelo de uma execução, use as seguintes etapas:

1. Quando a execução for concluída, chame o `register_model()` método.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Exiba o modelo registrado em seu espaço de trabalho com o [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

    No exemplo a seguir, o modelo registrado `my-model` tem metadados de acompanhamento de MLflow marcados. 

    ![registrar-mlflow-modelo](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Selecione a guia **artefatos** para ver todos os arquivos de modelo que se alinham com o esquema de modelo MLflow (Conda. YAML, MLmodel, Model. PKL).

    ![modelo-esquema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Selecione MLmodel para ver o arquivo MLmodel gerado pela execução.

    ![MLmodel-esquema](./media/how-to-use-mlflow/mlmodel-view.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Se você não planeja usar as métricas registradas e os artefatos em seu espaço de trabalho, a capacidade de excluí-los individualmente não estará disponível no momento. Em vez disso, exclua o grupo de recursos que contém a conta de armazenamento e o espaço de trabalho, para que você não incorra nenhum encargo:

1. No portal do Azure, selecione **Grupos de recursos** no canto esquerdo.

   ![Exclusão no portal do Azure](./media/how-to-use-mlflow/delete-resources.png)

1. Selecione o grupo de recursos criado na lista.

1. Selecione **Excluir grupo de recursos**.

1. Insira o nome do grupo de recursos. Em seguida, selecione **Excluir**.

## <a name="example-notebooks"></a>Blocos de anotações de exemplo

O [MLflow com notebooks do Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) demonstra e amplia os conceitos apresentados neste artigo.

> [!NOTE]
> Um repositório controlado pela comunidade de exemplos que usam o mlflow pode ser encontrado em https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Próximas etapas

* [Implante modelos com MLflow](how-to-deploy-mlflow-models.md).
* Monitore os modelos de produção para [descompasso de dados](./how-to-enable-data-collection.md).
* [Acompanhe as execuções de Azure Databricks com MLflow](how-to-use-mlflow-azure-databricks.md).
* [Gerenciar os modelos](concept-model-management-and-deployment.md).