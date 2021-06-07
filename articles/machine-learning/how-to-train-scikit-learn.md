---
title: Treine o scikit-Aprenda os modelos de aprendizado de máquina
titleSuffix: Azure Machine Learning
description: Saiba como Azure Machine Learning permite escalar horizontalmente um trabalho de treinamento scikit usando recursos de computação em nuvem elásticos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 807174fdbede2e4631b3ca1df7220904038da4c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518289"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Treine os modelos scikit-Aprenda em escala com Azure Machine Learning

Neste artigo, saiba como executar seus scripts de treinamento scikit-Learn com Azure Machine Learning.

Os scripts de exemplo neste artigo são usados para classificar imagens de flor de íris para criar um modelo de aprendizado de máquina baseado no [conjunto](https://archive.ics.uci.edu/ml/datasets/iris)de informações de íris do scikit.

Se você estiver treinando um modelo de aprendizado de máquina scikit-Learn do zero ou se estiver trazendo um modelo existente para a nuvem, poderá usar Azure Machine Learning para escalar trabalhos de treinamento de software livre usando recursos de computação em nuvem elásticos. Você pode criar, implantar, obter uma versão e monitorar modelos de nível de produção com Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:
 - Instância de computação do Azure Machine Learning - nenhum download ou instalação é necessária

    - Conclua o [tutorial: ambiente de instalação e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md)  para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
    - Na pasta de treinamento de exemplos no servidor do notebook, localize um bloco de anotações concluído e expandido navegando até este diretório: **como usar-azureml > ml-frameworks > scikit-learn > Train-hiperparameter-ajustar-deploy-com-sklearn** pasta.

 - Seu próprio servidor Jupyter Notebook

    - [Instale o SDK do Azure Machine Learning](/python/api/overview/azure/ml/install) (>= 1.13.0).
    - [Criar um arquivo de configuração de workspace](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Configurar o experimento

Esta seção configura o teste de treinamento carregando os pacotes do python necessários, inicializando um espaço de trabalho, definindo o ambiente de treinamento e preparando o script de treinamento.

### <a name="initialize-a-workspace"></a>Inicializar um workspace

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar. No SDK do Python, você pode acessar os artefatos do espaço de trabalho criando um [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) objeto.

Crie um objeto de espaço de trabalho a partir do `config.json` arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Preparar scripts

Neste tutorial, o script de treinamento **train_iris. py** já foi fornecido [aqui](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py). Na prática, você deve ser capaz de pegar qualquer script de treinamento personalizado como está e executá-lo com o Azure ML sem precisar modificar seu código.

Observações:
- O script de treinamento fornecido mostra como registrar algumas métricas em sua execução do Azure ML usando o `Run` objeto dentro do script.
- O script de treinamento fornecido usa dados de exemplo da  `iris = datasets.load_iris()` função.  Para usar e acessar seus próprios dados, confira [como treinar com conjuntos](how-to-train-with-datasets.md) de dados para disponibilizar a disponibilidade durante o treinamento.

### <a name="define-your-environment"></a>Definir seu ambiente

Para definir o [ambiente](concept-environments.md) do am do Azure que encapsula as dependências do script de treinamento, você pode definir um ambiente personalizado ou usar o e o ambiente organizado do Azure ml.

#### <a name="use-a-curated-environment"></a>Usar um ambiente organizado
Opcionalmente, o Azure ML fornece ambientes predefinidos e estruturados se você não quiser definir seu próprio ambiente. Para obter mais informações, consulte [aqui](resource-curated-environments.md).
Se você quiser usar um ambiente organizado, poderá executar o seguinte comando em vez disso:

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>Criar um ambiente personalizado

Você também pode criar seu próprio ambiente personalizado. Definir suas dependências de Conda em um arquivo YAML; Neste exemplo, o arquivo é nomeado `conda_dependencies.yml` .

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Crie um ambiente do Azure ML a partir desta especificação de ambiente Conda. O ambiente será empacotado em um contêiner do Docker no tempo de execução.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

Para obter mais informações sobre como criar e usar ambientes, consulte [criar e usar ambientes de software em Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurar e enviar sua execução de treinamento

### <a name="create-a-scriptrunconfig"></a>Criar um ScriptRunConfig
Crie um objeto ScriptRunConfig para especificar os detalhes da configuração de seu trabalho de treinamento, incluindo o script de treinamento, o ambiente a ser usado e o destino de computação para a execução.
Todos os argumentos para seu script de treinamento serão passados por meio da linha de comando, se especificado no `arguments` parâmetro.

O código a seguir configurará um objeto ScriptRunConfig para enviar seu trabalho para execução em seu computador local.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Se você quiser executar seu trabalho em um cluster remoto, poderá especificar o destino de computação desejado para o `compute_target` parâmetro de ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>Envie sua execução
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning executa scripts de treinamento copiando o diretório de origem inteiro. Se você tiver dados confidenciais que não deseja carregar, use um [arquivo. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem. Em vez disso, acesse os seus dados usando um [DataSet](how-to-train-with-datasets.md)ml do Azure.

### <a name="what-happens-during-run-execution"></a>O que acontece durante execução de execução
À medida que a execução é executada, ela passa pelos seguintes estágios:

- **Preparando**: uma imagem do Docker é criada de acordo com o ambiente definido. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenada em cache para execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso. Se um ambiente organizado for especificado, a imagem armazenada em cache que faz o backup desse ambiente organizado será usada.

- **Dimensionamento**: o cluster tentará escalar verticalmente se o cluster de ia do lote exigir mais nós para executar a execução do que está disponível no momento.

- **Em execução**: todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o `script` é executado. As saídas de stdout e a pasta **./logs** são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: a pasta **./Outputs** da execução é copiada para o histórico de execuções.

## <a name="save-and-register-the-model"></a>Salvar e registrar o modelo

Depois de treinar o modelo, você poderá salvá-lo e registrá-lo em seu workspace. O registro de modelo permite que você armazene e versione os modelos no workspace para simplificar o [gerenciamento e a implantação de modelos](concept-model-management-and-deployment.md).

Adicione o código a seguir ao seu script de treinamento, train_iris. py, para salvar o modelo. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registre o modelo em seu espaço de trabalho com o código a seguir. Ao especificar os parâmetros `model_framework` , `model_framework_version` e `resource_configuration` , a implantação do modelo sem código fica disponível. A implantação de modelo sem código permite que você implante diretamente seu modelo como um serviço Web do modelo registrado, e o [`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration) objeto define o recurso de computação para o serviço Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Implantação

O modelo que você acabou de registrar pode ser implantado exatamente da mesma forma que qualquer outro modelo registrado no Azure ML. A implantação "como" contém uma seção sobre como registrar modelos, mas você pode pular diretamente para [criar um destino de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implantação, já que você já tem um modelo registrado.

### <a name="preview-no-code-model-deployment"></a>Apresentação Implantação de modelo sem código

Em vez da rota de implantação tradicional, você também pode usar o recurso de implantação sem código (versão prévia) para scikit-learn. A implantação de modelo sem código tem suporte para todos os tipos de modelo scikit-Learn internos. Ao registrar seu modelo, conforme mostrado acima com os `model_framework` `model_framework_version` parâmetros, e `resource_configuration` , você pode simplesmente usar a [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) função estática para implantar seu modelo.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Observação: essas dependências são incluídas no contêiner de inferência scikit-Learn predefinido.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

A implantação [completa abrange](how-to-deploy-and-where.md) em Azure Machine Learning mais detalhadamente.


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinou e registrou um modelo scikit-Learn e aprendeu sobre as opções de implantação. Consulte estes outros artigos para saber mais sobre Azure Machine Learning.

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
