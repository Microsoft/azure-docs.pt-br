---
title: Treinar e implantar um modelo TensorFlow
titleSuffix: Azure Machine Learning
description: Saiba como Azure Machine Learning permite escalar horizontalmente um trabalho de treinamento do TensorFlow usando recursos de computação em nuvem elástico.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 41231e19960edfe1a4f0521b8738fa62a463c927
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796458"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Treine os modelos TensorFlow em escala com Azure Machine Learning

Neste artigo, saiba como executar seus scripts de treinamento do [TensorFlow](https://www.tensorflow.org/overview) em escala usando Azure Machine Learning.

Este exemplo treina e registra um modelo TensorFlow para classificar dígitos manuscritos usando uma DNN (rede neural profunda).

Se você estiver desenvolvendo um modelo de TensorFlow do zero ou estiver trazendo um [modelo existente](how-to-deploy-existing-model.md) para a nuvem, poderá usar Azure Machine Learning para expandir os trabalhos de treinamento de software livre para criar, implantar, obter uma versão e monitorar modelos de nível de produção.

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:

 - Instância de computação do Azure Machine Learning - nenhum download ou instalação é necessária

     - Conclua o [Tutorial: Configure o ambiente e o espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o exemplo de repositório.
    - Na pasta exemplos de aprendizado profundo no servidor do notebook, encontre um bloco de anotações concluído e expandido navegando até este diretório: **como usar-azureml > ml-frameworks > tensorflow > Train-hiperparameter-Prepare-deploy-com-tensorflow** pasta. 
 
 - Seu próprio servidor Jupyter Notebook

    - [Instale o SDK do Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Criar um arquivo de configuração de workspace](how-to-configure-environment.md#workspace).
    - [Baixar os arquivos](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` de script de exemplo e `utils.py`
     
    Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) deste guia na página de exemplos do github. O notebook inclui seções expandidas que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurar o experimento

Esta seção configura o teste de treinamento carregando os pacotes do python necessários, inicializando um espaço de trabalho, criando o destino de computação e definindo o ambiente de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas Python necessárias.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializar um workspace

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar. No SDK do Python, você pode acessar os artefatos do espaço de trabalho criando um [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) objeto.

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

Use o `register()` método para registrar o conjunto de dados em seu espaço de trabalho para que eles possam ser compartilhados com outras pessoas, reutilizados em vários experimentos e referenciados por nome em seu script de treinamento.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um destino de computação para a execução do seu trabalho do TensorFlow. Neste exemplo, crie um cluster de computação Azure Machine Learning habilitado para GPU.

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

Para definir o [ambiente](concept-environments.md) do am do Azure que encapsula as dependências do script de treinamento, você pode definir um ambiente personalizado ou usar um ambiente organizado do Azure ml.

#### <a name="use-a-curated-environment"></a>Usar um ambiente organizado
O Azure ML fornece ambientes predefinidos e estruturados se você não quiser definir seu próprio ambiente. O Azure ML tem vários ambientes de CPU e GPU organizados para TensorFlow correspondentes a diferentes versões do TensorFlow. Para obter mais informações, consulte [aqui](resource-curated-environments.md).

Se você quiser usar um ambiente organizado, poderá executar o seguinte comando em vez disso:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Para ver os pacotes incluídos no ambiente organizado, você pode gravar as dependências de Conda em disco:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Verifique se o ambiente organizado inclui todas as dependências exigidas pelo seu script de treinamento. Caso contrário, você precisará modificar o ambiente para incluir as dependências ausentes. Observe que, se o ambiente for modificado, você precisará dar a ele um novo nome, pois o prefixo ' AzureML ' é reservado para ambientes organizados. Se você tiver modificado o arquivo YAML de dependências Conda, poderá criar um novo ambiente com um novo nome, por exemplo:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Se, em vez disso, você tiver modificado o objeto de ambiente organizado diretamente, poderá clonar esse ambiente com um novo nome:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>Criar um ambiente personalizado

Você também pode criar seu próprio ambiente do Azure ML que encapsula as dependências do script de treinamento.

Primeiro, defina suas dependências de Conda em um arquivo YAML; Neste exemplo, o arquivo é nomeado `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Crie um ambiente do Azure ML a partir desta especificação de ambiente Conda. O ambiente será empacotado em um contêiner do Docker no tempo de execução.

Por padrão, se nenhuma imagem base for especificada, o Azure ML usará uma imagem de CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` como a imagem base. Como este exemplo executa o treinamento em um cluster de GPU, você precisará especificar uma imagem de base de GPU que tenha os drivers e as dependências de GPU necessários. O Azure ML mantém um conjunto de imagens de base publicadas no MCR (registro de contêiner da Microsoft) que você pode usar, consulte o repositório GitHub [Azure/AzureML-containers](https://github.com/Azure/AzureML-Containers) para obter mais informações.

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Opcionalmente, você pode apenas capturar todas as suas dependências diretamente em uma imagem personalizada do Docker ou Dockerfile e criar seu ambiente a partir dela. Para obter mais informações, consulte [treinar com imagem personalizada](how-to-train-with-custom-image.md).

Para obter mais informações sobre como criar e usar ambientes, consulte [criar e usar ambientes de software em Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurar e enviar sua execução de treinamento

### <a name="create-a-scriptrunconfig"></a>Criar um ScriptRunConfig

Crie um objeto [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) para especificar os detalhes da configuração de seu trabalho de treinamento, incluindo o script de treinamento, o ambiente a ser usado e o destino de computação para a execução. Todos os argumentos para seu script de treinamento serão passados por meio da linha de comando, se especificado no `arguments` parâmetro.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning executa scripts de treinamento copiando o diretório de origem inteiro. Se você tiver dados confidenciais que não deseja carregar, use um [arquivo. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem. Em vez disso, acesse os seus dados usando um [DataSet](how-to-train-with-datasets.md)ml do Azure.

Para obter mais informações sobre como configurar trabalhos com o ScriptRunConfig, consulte [configurar e enviar execuções de treinamento](how-to-set-up-training-targets.md).

> [!WARNING]
> Se você estava usando o estimador do TensorFlow anteriormente para configurar seus trabalhos de treinamento do TensorFlow, observe que os estimadores foram preteridos a partir da versão do SDK do 1.19.0. Com o SDK do Azure ML >= 1.15.0, o ScriptRunConfig é a maneira recomendada para configurar trabalhos de treinamento, incluindo aqueles que usam estruturas de aprendizado profundo. Para perguntas comuns de migração, consulte o [estimador para o guia de migração do ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-a-run"></a>Enviar uma execução

O [objeto Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e após sua conclusão.

```Python
run = Experiment(workspace=ws, name='Tutorial-TF-Mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>O que acontece durante execução de execução
À medida que a execução é executada, ela passa pelos seguintes estágios:

- **Preparando**: uma imagem do Docker é criada de acordo com o ambiente definido. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenada em cache para execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso. Se um ambiente organizado for especificado, a imagem armazenada em cache que faz o backup desse ambiente organizado será usada.

- **Dimensionamento**: o cluster tentará escalar verticalmente se o cluster de ia do lote exigir mais nós para executar a execução do que está disponível no momento.

- **Em execução**: todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o `script` é executado. As saídas de stdout e a pasta **./logs** são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: a pasta **./Outputs** da execução é copiada para o histórico de execuções.

## <a name="register-or-download-a-model"></a>Registrar ou baixar um modelo

Depois de treinar o modelo, você poderá registrá-lo em seu espaço de trabalho. O registro de modelo permite que você armazene e versione os modelos no workspace para simplificar o [gerenciamento e a implantação de modelos](concept-model-management-and-deployment.md). Opcional: especificando os parâmetros `model_framework` , `model_framework_version` e, a `resource_configuration` implantação do modelo sem código se torna disponível. Isso permite que você implante diretamente seu modelo como um serviço Web do modelo registrado, e o `ResourceConfiguration` objeto define o recurso de computação para o serviço Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Você também pode baixar uma cópia local do modelo usando o objeto Run. No script de treinamento `tf_mnist.py` , um objeto TensorFlow de proteção persiste o modelo para uma pasta local (local para o destino de computação). Você pode usar o objeto Run para baixar uma cópia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Treinamento distribuído

O Azure Machine Learning também dá suporte a trabalhos TensorFlow distribuídos de vários nós para que você possa dimensionar suas cargas de trabalho de treinamento. Você pode executar facilmente trabalhos TensorFlow distribuídos e o Azure ML gerenciará a orquestração para você.

O Azure ML dá suporte à execução de trabalhos de TensorFlow distribuídos com a API de treinamento distribuída interna Horovod e TensorFlow.

### <a name="horovod"></a>Horovod
O [Horovod](https://github.com/uber/horovod) é uma estrutura de software livre, tudo que reduz o treinamento distribuído desenvolvido pela Uber. Ele oferece um caminho fácil para escrever código TensorFlow distribuído para treinamento.

Seu código de treinamento terá de ser instrumentado com Horovod para treinamento distribuído. Para obter mais informações sobre como usar o Horovod com TensorFlow, consulte a documentação do Horovod:

Para obter mais informações sobre como usar o Horovod com TensorFlow, consulte a documentação do Horovod:

* [Horovod com TensorFlow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod com a API Keras do TensorFlow](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Além disso, verifique se seu ambiente de treinamento inclui o pacote **horovod** . Se você estiver usando um ambiente TensorFlow organizado, o horovod já estará incluído como uma das dependências. Se você estiver usando seu próprio ambiente, verifique se a dependência horovod está incluída, por exemplo:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

Para executar um trabalho distribuído usando MPI/Horovod no Azure ML, você deve especificar um [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) para o `distributed_job_config` parâmetro do Construtor ScriptRunConfig. O código abaixo configurará um trabalho distribuído de 2 nós que executa um processo por nó. Se você também quiser executar vários processos por nó (ou seja, se o SKU do cluster tiver várias GPUs), especifique também o `process_count_per_node` parâmetro em MpiConfiguration (o padrão é `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Para obter um tutorial completo sobre como executar TensorFlow distribuídas com Horovod no Azure ML, consulte [TensorFlow distribuída com Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod).

### <a name="tfdistribute"></a>TF. distribute

Se você estiver usando [TensorFlow distribuídas nativas](https://www.tensorflow.org/guide/distributed_training) no seu código de treinamento, por exemplo, a API do TensorFlow 2. x `tf.distribute.Strategy` , você também poderá iniciar o trabalho distribuído por meio do Azure ml. 

Para fazer isso, especifique um [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py) para o `distributed_job_config` parâmetro do Construtor ScriptRunConfig. Se você estiver usando o `tf.distribute.experimental.MultiWorkerMirroredStrategy` , especifique o `worker_count` no TensorflowConfiguration correspondente ao número de nós para seu trabalho de treinamento.

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

No TensorFlow, a `TF_CONFIG` variável de ambiente é necessária para treinamento em vários computadores. O Azure ML configurará e definirá a `TF_CONFIG` variável adequadamente para cada trabalho antes de executar o script de treinamento. Você pode acessar `TF_CONFIG` de seu script de treinamento se precisar via `os.environ['TF_CONFIG']` .

Exemplo de estrutura de `TF_CONFIG` conjunto em um nó de trabalho principal:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Se o seu script de treinamento usar a estratégia de servidor de parâmetros para treinamento distribuído, ou seja, para TensorFlow 1. x herdado, também será necessário especificar o número de servidores de parâmetro a serem usados no trabalho, por exemplo, `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` .

## <a name="deploy-a-tensorflow-model"></a>Implantar um modelo TensorFlow

A implantação "como" contém uma seção sobre como registrar modelos, mas você pode pular diretamente para [criar um destino de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implantação, já que você já tem um modelo registrado.

### <a name="preview-no-code-model-deployment"></a>Apresentação Implantação de modelo sem código

Em vez da rota de implantação tradicional, você também pode usar o recurso de implantação sem código (versão prévia) para TensorFlow. Ao registrar seu modelo, conforme mostrado acima com os `model_framework` `model_framework_version` parâmetros, e `resource_configuration` , você pode simplesmente usar a `deploy()` função estática para implantar seu modelo.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

A implantação [completa abrange](how-to-deploy-and-where.md) em Azure Machine Learning mais detalhadamente.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinou e registrou um modelo TensorFlow e aprendeu sobre as opções de implantação. Consulte estes outros artigos para saber mais sobre Azure Machine Learning.

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Arquitetura de referência para treinamento de aprendizado profundo distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
