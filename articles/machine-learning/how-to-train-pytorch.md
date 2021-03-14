---
title: Treinar modelos de PyTorch de aprendizado profundo
titleSuffix: Azure Machine Learning
description: Saiba como executar seus scripts de treinamento do PyTorch em escala empresarial usando o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b1cb14e07f6c0e402510abad6f1cb160f5215c63
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518374"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Treine os modelos PyTorch em escala com Azure Machine Learning

Neste artigo, saiba como executar seus scripts de treinamento do [PyTorch](https://pytorch.org/) em escala empresarial usando o Azure Machine Learning.

Os scripts de exemplo neste artigo são usados para classificar as imagens de galinha e Turquia para criar uma rede neural de aprendizado profundo (DNN) com base no [tutorial](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)de aprendizado de transferência do PyTorch. O aprendizado de transferência é uma técnica que aplica o conhecimento obtido da solução de um problema para um problema diferente, mas relacionado. Isso aborda o processo de treinamento exigindo menos dados, tempo e recursos de computação do que o treinamento do zero. Consulte o artigo aprendizado [profundo vs. Machine Learning](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) para saber mais sobre o aprendizado de transferência.

Se você está treinando um modelo de PyTorch de aprendizado profundo do zero ou está trazendo um modelo existente para a nuvem, você pode usar Azure Machine Learning para expandir trabalhos de treinamento de software livre usando recursos de computação em nuvem elásticos. Você pode criar, implantar, obter uma versão e monitorar modelos de nível de produção com Azure Machine Learning. 

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:

- Instância de computação do Azure Machine Learning - nenhum download ou instalação é necessária

    - Conclua o [Tutorial: Configure o ambiente e o espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o exemplo de repositório.
    - Na pasta exemplos de aprendizado profundo no servidor do notebook, encontre um bloco de anotações concluído e expandido navegando até este diretório: **como usar-azureml > ml-frameworks > pytorch > Train-hiperparameter-Prepare-deploy-com-pytorch** pasta. 
 
 - Seu próprio servidor Jupyter Notebook
    - [Instale o SDK do Azure Machine Learning](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Criar um arquivo de configuração de workspace](how-to-configure-environment.md#workspace).
    - [Baixar os arquivos de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) deste guia na página de exemplos do github. O notebook inclui seções expandidas que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurar o experimento

Esta seção configura o teste de treinamento carregando os pacotes do python necessários, inicializando um espaço de trabalho, criando o destino de computação e definindo o ambiente de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas Python necessárias.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializar um workspace

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar. No SDK do Python, você pode acessar os artefatos do espaço de trabalho criando um [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) objeto.

Crie um objeto de espaço de trabalho a partir do `config.json` arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>Obter os dados

O conjunto de informações consiste em cerca de 120 imagens de treinamento para turkeys e Chickens, com imagens de validação de 100 para cada classe. Vamos baixar e extrair o conjunto de um como parte do nosso script de treinamento `pytorch_train.py` . As imagens são um subconjunto do [conjunto de imagens do Open images V5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-script"></a>Preparar script de treinamento

Neste tutorial, o script de treinamento, `pytorch_train.py` , já foi fornecido. Na prática, você pode usar qualquer script de treinamento personalizado, como está, e executá-lo com Azure Machine Learning.

Crie uma pasta para seus scripts de treinamento.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um destino de computação para a execução do seu trabalho do PyTorch. Neste exemplo, crie um cluster de computação Azure Machine Learning habilitado para GPU.

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
O Azure ML fornece ambientes predefinidos e estruturados se você não quiser definir seu próprio ambiente. O Azure ML tem vários ambientes de CPU e GPU organizados para PyTorch correspondentes a diferentes versões do PyTorch. Para obter mais informações, consulte [aqui](resource-curated-environments.md).

Se você quiser usar um ambiente organizado, poderá executar o seguinte comando em vez disso:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Para ver os pacotes incluídos no ambiente organizado, você pode gravar as dependências de Conda em disco:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Verifique se o ambiente organizado inclui todas as dependências exigidas pelo seu script de treinamento. Caso contrário, você precisará modificar o ambiente para incluir as dependências ausentes. Observe que, se o ambiente for modificado, você precisará dar a ele um novo nome, pois o prefixo ' AzureML ' é reservado para ambientes organizados. Se você tiver modificado o arquivo YAML de dependências Conda, poderá criar um novo ambiente com um novo nome, por exemplo:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Se, em vez disso, você tiver modificado o objeto de ambiente organizado diretamente, poderá clonar esse ambiente com um novo nome:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
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
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Crie um ambiente do Azure ML a partir desta especificação de ambiente Conda. O ambiente será empacotado em um contêiner do Docker no tempo de execução.

Por padrão, se nenhuma imagem base for especificada, o Azure ML usará uma imagem de CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` como a imagem base. Como este exemplo executa o treinamento em um cluster de GPU, você precisará especificar uma imagem de base de GPU que tenha os drivers e as dependências de GPU necessários. O Azure ML mantém um conjunto de imagens de base publicadas no MCR (registro de contêiner da Microsoft) que você pode usar, consulte o repositório GitHub [Azure/AzureML-containers](https://github.com/Azure/AzureML-Containers) para obter mais informações.

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Opcionalmente, você pode apenas capturar todas as suas dependências diretamente em uma imagem personalizada do Docker ou Dockerfile e criar seu ambiente a partir dela. Para obter mais informações, consulte [treinar com imagem personalizada](how-to-train-with-custom-image.md).

Para obter mais informações sobre como criar e usar ambientes, consulte [criar e usar ambientes de software em Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurar e enviar sua execução de treinamento

### <a name="create-a-scriptrunconfig"></a>Criar um ScriptRunConfig

Crie um objeto [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) para especificar os detalhes da configuração de seu trabalho de treinamento, incluindo o script de treinamento, o ambiente a ser usado e o destino de computação para a execução. Todos os argumentos para seu script de treinamento serão passados por meio da linha de comando, se especificado no `arguments` parâmetro. 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning executa scripts de treinamento copiando o diretório de origem inteiro. Se você tiver dados confidenciais que não deseja carregar, use um [arquivo. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem. Em vez disso, acesse os seus dados usando um [DataSet](how-to-train-with-datasets.md)ml do Azure.

Para obter mais informações sobre como configurar trabalhos com o ScriptRunConfig, consulte [configurar e enviar execuções de treinamento](how-to-set-up-training-targets.md).

> [!WARNING]
> Se você estava usando o estimador do PyTorch anteriormente para configurar seus trabalhos de treinamento do PyTorch, observe que os estimadores foram preteridos a partir da versão do SDK do 1.19.0. Com o SDK do Azure ML >= 1.15.0, o ScriptRunConfig é a maneira recomendada para configurar trabalhos de treinamento, incluindo aqueles que usam estruturas de aprendizado profundo. Para perguntas comuns de migração, consulte o [estimador para o guia de migração do ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

## <a name="submit-your-run"></a>Envie sua execução

O [objeto Run](/python/api/azureml-core/azureml.core.run%28class%29) fornece a interface para o histórico de execução enquanto o trabalho está em execução e após sua conclusão.

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>O que acontece durante execução de execução
À medida que a execução é executada, ela passa pelos seguintes estágios:

- **Preparando**: uma imagem do Docker é criada de acordo com o ambiente definido. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenada em cache para execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso. Se um ambiente organizado for especificado, a imagem armazenada em cache que faz o backup desse ambiente organizado será usada.

- **Dimensionamento**: o cluster tentará escalar verticalmente se o cluster de ia do lote exigir mais nós para executar a execução do que está disponível no momento.

- **Em execução**: todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o `script` é executado. As saídas de stdout e a pasta **./logs** são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: a pasta **./Outputs** da execução é copiada para o histórico de execuções.

## <a name="register-or-download-a-model"></a>Registrar ou baixar um modelo

Depois de treinar o modelo, você poderá registrá-lo em seu espaço de trabalho. O registro de modelo permite que você armazene e versione os modelos no workspace para simplificar o [gerenciamento e a implantação de modelos](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> A implantação "como" contém uma seção sobre como registrar modelos, mas você pode pular diretamente para [criar um destino de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implantação, já que você já tem um modelo registrado.

Você também pode baixar uma cópia local do modelo usando o objeto Run. No script de treinamento `pytorch_train.py` , um objeto PyTorch Save persiste o modelo para uma pasta local (local para o destino de computação). Você pode usar o objeto Run para baixar uma cópia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Treinamento distribuído

O Azure Machine Learning também dá suporte a trabalhos PyTorch distribuídos de vários nós para que você possa dimensionar suas cargas de trabalho de treinamento. Você pode executar facilmente trabalhos PyTorch distribuídos e o Azure ML gerenciará a orquestração para você.

O Azure ML dá suporte à execução de trabalhos de PyTorch distribuídos com o módulo DistributedDataParallel interno Horovod e PyTorch.

### <a name="horovod"></a>Horovod
O [Horovod](https://github.com/uber/horovod) é uma estrutura de software livre, tudo que reduz o treinamento distribuído desenvolvido pela Uber. Ele oferece um caminho fácil para escrever código PyTorch distribuído para treinamento.

Seu código de treinamento terá de ser instrumentado com Horovod para treinamento distribuído. Para obter mais informações sobre como usar o Horovod com PyTorch, consulte a [documentação do Horovod](https://horovod.readthedocs.io/en/stable/pytorch.html).

Além disso, verifique se seu ambiente de treinamento inclui o pacote **horovod** . Se você estiver usando um ambiente PyTorch organizado, o horovod já estará incluído como uma das dependências. Se você estiver usando seu próprio ambiente, verifique se a dependência horovod está incluída, por exemplo:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - horovod==0.19.5
```

Para executar um trabalho distribuído usando MPI/Horovod no Azure ML, você deve especificar um [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) para o `distributed_job_config` parâmetro do Construtor ScriptRunConfig. O código abaixo configurará um trabalho distribuído de 2 nós que executa um processo por nó. Se você também quiser executar vários processos por nó (ou seja, se o SKU do cluster tiver várias GPUs), especifique também o `process_count_per_node` parâmetro em MpiConfiguration (o padrão é `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Para obter um tutorial completo sobre como executar PyTorch distribuídas com Horovod no Azure ML, consulte [PyTorch distribuída com Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod).

### <a name="distributeddataparallel"></a>DistributedDataParallel
Se você estiver usando o módulo [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) interno do PyTorch criado usando o pacote **Torch. Distributed** no seu código de treinamento, você também poderá iniciar o trabalho distribuído por meio do Azure ml.

Para iniciar um trabalho PyTorch distribuído no Azure ML, você tem duas opções:
1. Inicialização por processo: especifique o número total de processos de trabalho que você deseja executar e o am do Azure tratará do início de cada processo.
2. Inicialização por nó com `torch.distributed.launch` : forneça o `torch.distributed.launch` comando que você deseja executar em cada nó. O utilitário de inicialização Torch tratará do início dos processos de trabalho em cada nó.

Não há diferenças fundamentais entre essas opções de inicialização; é basicamente a preferência do usuário ou as convenções das estruturas/bibliotecas criadas com base na PyTorch de baunilha (como face relâmpago ou hugging).

#### <a name="per-process-launch"></a>Inicialização por processo
Para usar essa opção para executar um trabalho PyTorch distribuído, faça o seguinte:
1. Especificar o script de treinamento e os argumentos
2. Crie um [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) e especifique o `process_count` , bem como `node_count` . O `process_count` corresponde ao número total de processos que você deseja executar para o trabalho. Isso normalmente deve ser igual ao número de GPUs por nó multiplicado pelo número de nós. Se `process_count` não for especificado, o Azure ml iniciará um processo por nó por padrão.

O Azure ML definirá as seguintes variáveis de ambiente:
* `MASTER_ADDR` -Endereço IP do computador que hospedará o processo com a classificação 0.
* `MASTER_PORT` -Uma porta livre no computador que hospedará o processo com a classificação 0.
* `NODE_RANK` -A classificação do nó para treinamento de vários nós. Os valores possíveis são 0 a (n º total de nós-1).
* `WORLD_SIZE` -O número total de processos. Isso deve ser igual ao número total de dispositivos (GPU) usados para treinamento distribuído.
* `RANK` -A classificação (global) do processo atual. Os valores possíveis são 0 a (tamanho mundial-1).
* `LOCAL_RANK` -A classificação local (relativa) do processo dentro do nó. Os valores possíveis são 0 a (n º de processos no nó-1).

Como as variáveis de ambiente necessárias serão definidas para você pelo Azure ML, você pode usar [o método de inicialização de variável de ambiente padrão](https://pytorch.org/docs/stable/distributed.html#environment-variable-initialization) para inicializar o grupo de processos em seu código de treinamento.

O trecho de código a seguir configura um trabalho de PyTorch de dois nós e dois processos por nó:
```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=4, node_count=2)

src = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 25],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

> [!WARNING]
> Para usar essa opção para treinamento de vários processos por nó, você precisará usar o SDK do Python do Azure ML >= 1.22.0, como `process_count` foi introduzido no 1.22.0.

> [!TIP]
> Se o script de treinamento passar informações como classificação local ou classificação como argumentos de script, você poderá fazer referência à (s) variável (ões) de ambiente nos argumentos: `arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]` .

#### <a name="per-node-launch-with-torchdistributedlaunch"></a>Lançamento por nó com `torch.distributed.launch`
O PyTorch fornece um utilitário de inicialização no [Torch. Distributed. Launch](https://pytorch.org/docs/stable/distributed.html#launch-utility) que os usuários podem usar para iniciar vários processos por nó. O `torch.distributed.launch` módulo irá gerar vários processos de treinamento em cada um dos nós.

As etapas a seguir demonstram como configurar um trabalho do PyTorch com um iniciador por nó no Azure ML que atingirá o equivalente à execução do seguinte comando:

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Forneça o `torch.distributed.launch` comando para o `command` parâmetro do `ScriptRunConfig` Construtor. O Azure ML executará esse comando em cada nó do seu cluster de treinamento. `--nproc_per_node` deve ser menor ou igual ao número de GPUs disponíveis em cada nó. `MASTER_ADDR`, `MASTER_PORT` e `NODE_RANK` são todos definidos pelo Azure ml, para que você possa apenas referenciar as variáveis de ambiente no comando. O Azure ML define `MASTER_PORT` como 6105, mas você pode passar um valor diferente para o `--master_port` argumento do `torch.distributed.launch` comando, se desejar. (O utilitário de inicialização redefinirá as variáveis de ambiente.)
2. Crie um `PyTorchConfiguration` e especifique o `node_count` . Você não precisa definir `process_count` como o Azure ml usará o padrão para iniciar um processo por nó, que executará o comando de inicialização especificado.

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 2 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

src = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

Para obter um tutorial completo sobre como executar PyTorch distribuídas no Azure ML, consulte [Distributed PyTorch with DistributedDataParallel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-distributeddataparallel).

### <a name="troubleshooting"></a>Solução de problemas

* O **Horovod foi desligado**: na maioria dos casos, se você encontrar "AbortedError: Horovod foi desligado", houve uma exceção subjacente em um dos processos que fizeram com que o Horovod fosse desligado. Cada classificação no trabalho da MPI obtém seu próprio arquivo de log dedicado no Azure Machine Learning. Esses logs são nomeados `70_driver_logs`. No caso de treinamento distribuído, os nomes de log têm o sufixo `_rank` para facilitar a diferenciação dos logs. Para localizar o erro exato que fez com que o Horovod fosse desligado, percorra todos os arquivos de log e procure `Traceback` no final dos arquivos de driver_log. Um desses arquivos fornecerá a exceção subjacente real. 

## <a name="export-to-onnx"></a>Exportar para ONNX

Para otimizar a inferência com o [tempo de execução ONNX](concept-onnx.md), converta seu modelo PyTorch treinado para o formato ONNX. A inferência, ou a Pontuação do modelo, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção. Consulte o [tutorial](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) para obter um exemplo.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinou e registrou uma rede neural de aprendizado profundo usando PyTorch em Azure Machine Learning. Para saber como implantar um modelo, continue em nosso artigo de implantação de modelo.

* [Como e onde implantar modelos](how-to-deploy-and-where.md)
* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
* [Arquitetura de referência para treinamento de aprendizado profundo distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
