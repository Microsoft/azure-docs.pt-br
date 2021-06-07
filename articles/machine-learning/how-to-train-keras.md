---
title: Treinar modelos de Keras de aprendizado profundo
titleSuffix: Azure Machine Learning
description: Saiba como treinar e registrar um modelo de classificação de rede neural Keras profundo em execução no TensorFlow usando o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2b4af9dec2bf397ad2766c68d547eeac85a9a9a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518357"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Treine os modelos Keras em escala com Azure Machine Learning

Neste artigo, saiba como executar seus scripts de treinamento do Keras com Azure Machine Learning.

O código de exemplo neste artigo mostra como treinar e registrar um modelo de classificação Keras criado usando o back-end TensorFlow com Azure Machine Learning. Ele usa o popular [conjunto de MNIST](http://yann.lecun.com/exdb/mnist/) para classificar dígitos manuscritos usando uma DNN (rede neural profunda) criada usando a [biblioteca Keras Python](https://keras.io) em execução na parte superior do [TensorFlow](https://www.tensorflow.org/overview).

Keras é uma API de rede neural de alto nível capaz de executar a parte superior de outras estruturas de DNN populares para simplificar o desenvolvimento. Com o Azure Machine Learning, você pode expandir rapidamente os trabalhos de treinamento usando recursos de computação em nuvem elástico. Você também pode acompanhar suas execuções de treinamento, modelos de versão, implantar modelos e muito mais.

Se você estiver desenvolvendo um modelo de Keras do zero ou estiver trazendo um modelo existente para a nuvem, Azure Machine Learning poderá ajudá-lo a criar modelos prontos para produção.

> [!NOTE]
> Se você estiver usando a API Keras **TF. Keras** embutida em TensorFlow e não o pacote Keras autônomo, consulte em vez disso [treinar modelos de TensorFlow](how-to-train-tensorflow.md).

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:

- Instância de computação do Azure Machine Learning - nenhum download ou instalação é necessária

     - Conclua o [Tutorial: Configure o ambiente e o espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o exemplo de repositório.
    - Na pasta Samples no servidor do notebook, localize um bloco de anotações completo e expandido navegando até este diretório: **instruções-azureml > ml-frameworks > keras > Train-hiperparameter-ajuste-deploy-com-Keras** pasta.

 - Seu próprio servidor Jupyter Notebook

    - [Instale o SDK do Azure Machine Learning](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Criar um arquivo de configuração de workspace](how-to-configure-environment.md#workspace).
    - [Baixar os arquivos](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` de script de exemplo e `utils.py`

    Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) deste guia na página de exemplos do github. O notebook inclui seções expandidas que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurar o experimento

Esta seção configura o teste de treinamento carregando os pacotes python necessários, inicializando um espaço de trabalho, criando o filedataset para os dados de treinamento de entrada, criando o destino de computação e definindo o ambiente de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas Python necessárias.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializar um workspace

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar. No SDK do Python, você pode acessar os artefatos do espaço de trabalho criando um [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) objeto.

Crie um objeto de espaço de trabalho a partir do `config.json` arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Criar um conjunto de um arquivo

Um objeto `FileDataset` referencia um ou vários arquivos no armazenamento de dados do workspace ou em URLs públicas. Os arquivos podem ser de qualquer formato e a classe fornece a capacidade de baixar ou montar os arquivos no computador. Ao criar um `FileDataset`, você cria uma referência à localização da fonte de dados. Se você tiver aplicado todas as transformações ao conjunto de dados, elas também serão armazenadas no conjunto de dados. Os dados permanecem na localização existente, portanto, nenhum custo de armazenamento extra é gerado. Confira o guia de [instruções](./how-to-create-register-datasets.md) no pacote `Dataset` para obter mais informações.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Você pode usar o `register()` método para registrar o conjunto de dados em seu espaço de trabalho para que eles possam ser compartilhados com outras pessoas, reutilizados em vários experimentos e referenciados por nome em seu script de treinamento.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um destino de computação para a execução do seu trabalho de treinamento. Neste exemplo, crie um cluster de computação Azure Machine Learning habilitado para GPU.

```Python
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Para obter mais informações sobre destinos de computação, consulte o artigo [o que é um destino de computação](concept-compute-target.md) .

### <a name="define-your-environment"></a>Definir seu ambiente

Defina o [ambiente](concept-environments.md) do Azure ml que encapsula as dependências do script de treinamento.

Primeiro, defina suas dependências de Conda em um arquivo YAML; Neste exemplo, o arquivo é nomeado `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

Crie um ambiente do Azure ML a partir desta especificação de ambiente Conda. O ambiente será empacotado em um contêiner do Docker no tempo de execução.

Por padrão, se nenhuma imagem base for especificada, o Azure ML usará uma imagem de CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` como a imagem base. Como este exemplo executa o treinamento em um cluster de GPU, você precisará especificar uma imagem de base de GPU que tenha os drivers e as dependências de GPU necessários. O Azure ML mantém um conjunto de imagens de base publicadas no MCR (registro de contêiner da Microsoft) que você pode usar, consulte o repositório GitHub [Azure/AzureML-containers](https://github.com/Azure/AzureML-Containers) para obter mais informações.

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Para obter mais informações sobre como criar e usar ambientes, consulte [criar e usar ambientes de software em Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurar e enviar sua execução de treinamento

### <a name="create-a-scriptrunconfig"></a>Criar um ScriptRunConfig
Primeiro, obtenha os dados do armazenamento do espaço de trabalho usando a `Dataset` classe.

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Crie um objeto ScriptRunConfig para especificar os detalhes da configuração de seu trabalho de treinamento, incluindo o script de treinamento, o ambiente a ser usado e o destino de computação para a execução.

Todos os argumentos para seu script de treinamento serão passados por meio da linha de comando, se especificado no `arguments` parâmetro. O DatasetConsumptionConfig para nosso filedataset é passado como um argumento para o script de treinamento, para o `--data-folder` argumento. O Azure ML resolverá esse DatasetConsumptionConfig para o ponto de montagem do repositório de armazenamento de backup, que pode ser acessado do script de treinamento.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

Para obter mais informações sobre como configurar trabalhos com o ScriptRunConfig, consulte [configurar e enviar execuções de treinamento](how-to-set-up-training-targets.md).

> [!WARNING]
> Se você estava usando o estimador do TensorFlow anteriormente para configurar seus trabalhos de treinamento do Keras, observe que os estimadores foram preteridos a partir da versão do SDK do 1.19.0. Com o SDK do Azure ML >= 1.15.0, o ScriptRunConfig é a maneira recomendada para configurar trabalhos de treinamento, incluindo aqueles que usam estruturas de aprendizado profundo. Para perguntas comuns de migração, consulte o [estimador para o guia de migração do ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-your-run"></a>Envie sua execução

O [objeto Run](/python/api/azureml-core/azureml.core.run%28class%29) fornece a interface para o histórico de execução enquanto o trabalho está em execução e após sua conclusão.

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>O que acontece durante execução de execução
À medida que a execução é executada, ela passa pelos seguintes estágios:

- **Preparando**: uma imagem do Docker é criada de acordo com o ambiente definido. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenada em cache para execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso. Se um ambiente organizado for especificado, a imagem armazenada em cache que faz o backup desse ambiente organizado será usada.

- **Dimensionamento**: o cluster tentará escalar verticalmente se o cluster de ia do lote exigir mais nós para executar a execução do que está disponível no momento.

- **Em execução**: todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o `script` é executado. As saídas de stdout e a pasta **./logs** são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: a pasta **./Outputs** da execução é copiada para o histórico de execuções.

## <a name="register-the-model"></a>Registre o modelo

Depois de treinar o modelo, você poderá registrá-lo em seu espaço de trabalho. O registro de modelo permite que você armazene e versione os modelos no workspace para simplificar o [gerenciamento e a implantação de modelos](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> A implantação "como" contém uma seção sobre como registrar modelos, mas você pode pular diretamente para [criar um destino de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implantação, já que você já tem um modelo registrado.

Você também pode baixar uma cópia local do modelo. Isso pode ser útil para fazer o trabalho de validação de modelo adicional localmente. No script de treinamento, `keras_mnist.py` , um objeto TensorFlow de proteção persiste o modelo para uma pasta local (local para o destino de computação). Você pode usar o objeto Run para baixar uma cópia do histórico de execuções.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinou e registrou um modelo Keras em Azure Machine Learning. Para saber como implantar um modelo, continue em nosso artigo de implantação de modelo.

* [Como e onde implantar modelos](how-to-deploy-and-where.md)
* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
* [Arquitetura de referência para treinamento de aprendizado profundo distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
