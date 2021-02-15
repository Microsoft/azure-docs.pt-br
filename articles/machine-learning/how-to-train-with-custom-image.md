---
title: Treinar um modelo usando uma imagem personalizada do Docker
titleSuffix: Azure Machine Learning
description: Saiba como usar suas próprias imagens do Docker ou os listados da Microsoft para treinar modelos no Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: ad86ebdbef8bf8c8ec00e54e15b751fd2285ad68
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601340"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Treinar um modelo usando uma imagem personalizada do Docker

Neste artigo, saiba como usar uma imagem personalizada do Docker quando você estiver treinando modelos com Azure Machine Learning. Você usará os scripts de exemplo neste artigo para classificar imagens de animais de estimação criando uma rede neural de convolução. 

Azure Machine Learning fornece uma imagem base padrão do Docker. Você também pode usar ambientes de Azure Machine Learning para especificar uma imagem de base diferente, como uma das [imagens de base Azure Machine Learning](https://github.com/Azure/AzureML-Containers) mantidas ou sua própria [imagem personalizada](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). As imagens de base personalizadas permitem que você gerencie de perto suas dependências e mantenha um controle mais rígido sobre as versões de componentes ao executar trabalhos de treinamento.

## <a name="prerequisites"></a>Pré-requisitos

Execute o código em qualquer um desses ambientes:

* Azure Machine Learning instância de computação (sem downloads ou instalação necessária):
  * Conclua o tutorial de [configuração de ambiente e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
  * No repositório de [exemplos](https://github.com/Azure/azureml-examples)de Azure Machine Learning, encontre um notebook concluído acessando o diretório **notebooks**  >  **fastai**  >  **Train-pets-resnet34. ipynb** . 
* Seu próprio servidor de Jupyter Notebook:
  * Crie um [arquivo de configuração de workspace](how-to-configure-environment.md#workspace).
  * Instale o [SDK do Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py). 
  * Crie um [registro de contêiner do Azure](../container-registry/index.yml) ou outro registro do Docker que esteja disponível na Internet.

## <a name="set-up-a-training-experiment"></a>Configurar um teste de treinamento

Nesta seção, você configura o teste de treinamento inicializando um espaço de trabalho, definindo seu ambiente e configurando um destino de computação.

### <a name="initialize-a-workspace"></a>Inicializar um workspace

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele oferece um local centralizado para trabalhar com todos os artefatos que você criar. No SDK do Python, você pode acessar os artefatos do espaço de trabalho criando um [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) objeto.

Crie um `Workspace` objeto do config.jsno arquivo que você criou como um [pré-requisito](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Definir seu ambiente

Crie um `Environment` objeto e habilite o Docker.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

A imagem base especificada no código a seguir dá suporte à biblioteca fast.ai, que permite recursos de aprendizado profundo distribuído. Para obter mais informações, consulte o [repositório do Hub do docker Fast.ai](https://hub.docker.com/u/fastdotai). 

Quando você estiver usando sua imagem personalizada do Docker, talvez você já tenha seu ambiente do Python configurado corretamente. Nesse caso, defina o `user_managed_dependencies` sinalizador como `True` para usar o ambiente interno do Python de sua imagem personalizada. Por padrão, o Azure Machine Learning cria um ambiente Conda com dependências que você especificou. O serviço executa o script nesse ambiente em vez de usar as bibliotecas do Python que você instalou na imagem de base.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Usar um registro de contêiner privado (opcional)

Para usar uma imagem de um registro de contêiner privado que não está em seu espaço de trabalho, use `docker.base_image_registry` para especificar o endereço do repositório e um nome de usuário e senha:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Usar um Dockerfile personalizado (opcional)

Também é possível usar um Dockerfile personalizado. Use essa abordagem se você precisar instalar pacotes não Python como dependências. Lembre-se de definir a imagem base como `None` .

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

>[!IMPORTANT]
> Azure Machine Learning oferece suporte apenas a imagens do Docker que fornecem o seguinte software:
> * Ubuntu 16, 4 ou superior.
> * Conda 4.5. # ou superior.
> * Python 3.5 +.

Para obter mais informações sobre como criar e gerenciar ambientes de Azure Machine Learning, consulte [criar e usar ambientes de software](how-to-use-environments.md). 

### <a name="create-or-attach-a-compute-target"></a>Criar ou anexar um destino de computação

Você precisa criar um [destino de computação](concept-azure-machine-learning-architecture.md#compute-targets) para treinar seu modelo. Neste tutorial, você criará `AmlCompute` como seu recurso de computação de treinamento.

A criação de `AmlCompute` demora alguns minutos. Se o `AmlCompute` recurso já estiver em seu espaço de trabalho, esse código ignorará o processo de criação.

Assim como ocorre com outros serviços do Azure, há limites em determinados recursos (por exemplo, `AmlCompute` ) associados ao serviço de Azure Machine Learning. Para obter mais informações, consulte [limites padrão e como solicitar uma cota mais alta](how-to-manage-quotas.md).

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>Configurar seu trabalho de treinamento

Para este tutorial, use o script de treinamento *Train.py* no [GitHub](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py). Na prática, você pode usar qualquer script de treinamento personalizado e executá-lo, como está, com Azure Machine Learning.

Crie um `ScriptRunConfig` recurso para configurar seu trabalho para execução no destino de [computação](how-to-set-up-training-targets.md)desejado.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Enviar o trabalho de treinamento

Quando você envia uma execução de treinamento usando um `ScriptRunConfig` objeto, o `submit` método retorna um objeto do tipo `ScriptRun` . O `ScriptRun` objeto retornado fornece acesso programático a informações sobre a execução do treinamento. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning executa scripts de treinamento copiando o diretório de origem inteiro. Se você tiver dados confidenciais que não deseja carregar, use um [arquivo. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem. Em vez disso, acesse seus dados por meio de um [datastore](/python/api/azureml-core/azureml.data?preserve-view=true&view=azure-ml-py).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você treinou um modelo usando uma imagem personalizada do Docker. Consulte estes outros artigos para saber mais sobre Azure Machine Learning:
* [Acompanhe as métricas de execução](how-to-track-experiments.md) durante o treinamento.
* [Implante um modelo](how-to-deploy-custom-docker-image.md) usando uma imagem personalizada do Docker.
