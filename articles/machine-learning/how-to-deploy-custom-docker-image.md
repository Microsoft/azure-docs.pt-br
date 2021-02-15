---
title: Implantar modelos com imagem personalizada do Docker
titleSuffix: Azure Machine Learning
description: Saiba como usar uma imagem de base do Docker personalizada para implantar seus modelos de Azure Machine Learning. Embora Azure Machine Learning forneça uma imagem base padrão para você, você também pode usar sua própria imagem base.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 1ff4d7693a7e493ccb736ab9363fd26c93017c79
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695343"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Implantar um modelo usando uma imagem de base do Docker personalizada

Saiba como usar uma imagem de base do Docker personalizada ao implantar modelos treinados com Azure Machine Learning.

Azure Machine Learning usará uma imagem base do Docker padrão, se nenhuma for especificada. Você pode encontrar a imagem específica do Docker usada com o `azureml.core.runconfig.DEFAULT_CPU_IMAGE` . Você também pode usar __ambientes__ de Azure Machine Learning para selecionar uma imagem de base específica ou usar um personalizado que você fornecer.

Uma imagem base é usada como ponto de partida quando uma imagem é criada para uma implantação. Ele fornece o sistema operacional e os componentes subjacentes. Em seguida, o processo de implantação adiciona componentes adicionais, como seu modelo, ambiente Conda e outros ativos, à imagem.

Normalmente, você cria uma imagem de base personalizada quando deseja usar o Docker para gerenciar suas dependências, manter um controle mais rígido sobre as versões de componentes ou economizar tempo durante a implantação. Talvez você também queira instalar o software exigido por seu modelo, onde o processo de instalação leva muito tempo. Instalar o software ao criar a imagem base significa que você não precisa instalá-lo para cada implantação.

> [!IMPORTANT]
> Ao implantar um modelo, não é possível substituir os componentes principais, como o servidor Web ou os componentes do IoT Edge. Esses componentes fornecem um ambiente de trabalho conhecido que é testado e suportado pela Microsoft.

> [!WARNING]
> A Microsoft pode não ser capaz de ajudar a solucionar problemas causados por uma imagem personalizada. Se você encontrar problemas, você pode ser solicitado a usar a imagem padrão ou uma das imagens que a Microsoft fornece para ver se o problema é específico da imagem.

Este documento está dividido em duas seções:

* Criar uma imagem de base personalizada: fornece informações para administradores e DevOps sobre como criar uma imagem personalizada e configurar a autenticação para um registro de contêiner do Azure usando o CLI do Azure e Machine Learning a CLI.
* Implantar um modelo usando uma imagem de base personalizada: fornece informações para cientistas de dados e engenheiros de DevOps/ML usando imagens personalizadas ao implantar um modelo treinado do SDK do Python ou da CLI do ML.

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte o artigo [criar um espaço de trabalho](how-to-manage-workspace.md) .
* O [SDK do Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py). 
* O [CLI do Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* A [Extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Um [registro de contêiner do Azure](../container-registry/index.yml) ou outro registro do Docker que está acessível na Internet.
* As etapas neste documento pressupõem que você esteja familiarizado com a criação e o uso de um objeto de __configuração de inferência__ como parte da implantação do modelo. Para obter mais informações, consulte [onde implantar e como](how-to-deploy-and-where.md).

## <a name="create-a-custom-base-image"></a>Criar uma imagem de base personalizada

As informações nesta seção pressupõem que você está usando um registro de contêiner do Azure para armazenar imagens do Docker. Use a seguinte lista de verificação ao planejar criar imagens personalizadas para Azure Machine Learning:

* Você usará o registro de contêiner do Azure criado para o espaço de trabalho Azure Machine Learning ou um registro de contêiner do Azure autônomo?

    Ao usar imagens armazenadas no __registro de contêiner para o espaço de trabalho__, você não precisa se autenticar no registro. A autenticação é tratada pelo espaço de trabalho.

    > [!WARNING]
    > O registro de contêiner do Azure para seu espaço de trabalho é __criado na primeira vez que você treina ou implanta um modelo__ usando o espaço de trabalho. Se você tiver criado um novo espaço de trabalho, mas não for treinado ou criado um modelo, nenhum registro de contêiner do Azure existirá para o espaço de trabalho.

    Ao usar as imagens armazenadas em um __registro de contêiner autônomo__, será necessário configurar uma entidade de serviço que tenha pelo menos acesso de leitura. Em seguida, forneça a ID da entidade de serviço (nome de usuário) e a senha para qualquer pessoa que usa imagens do registro. A exceção é se você tornar o registro de contêiner acessível publicamente.

    Para obter informações sobre como criar um registro de contêiner do Azure privado, consulte [criar um registro de contêiner privado](../container-registry/container-registry-get-started-azure-cli.md).

    Para obter informações sobre como usar entidades de serviço com o registro de contêiner do Azure, consulte [autenticação do registro de contêiner do Azure com entidades de serviço](../container-registry/container-registry-auth-service-principal.md).

* Informações de registro e imagem de contêiner do Azure: forneça o nome da imagem para qualquer pessoa que precise usá-la. Por exemplo, uma imagem chamada `myimage` , armazenada em um registro chamado `myregistry` , é referenciada como `myregistry.azurecr.io/myimage` ao usar a imagem para implantação de modelo

### <a name="image-requirements"></a>Requisitos de imagem

Azure Machine Learning oferece suporte apenas a imagens do Docker que fornecem o seguinte software:
* Ubuntu 16, 4 ou superior.
* Conda 4.5. # ou superior.
* Python 3.5 +.

Para usar conjuntos de valores, instale o pacote libfuse-dev. Além disso, certifique-se de instalar qualquer pacote de espaço de usuário que você possa precisar.

O Azure ML mantém um conjunto de imagens de CPU e de GPU com base no registro de contêiner da Microsoft que você pode, opcionalmente, aproveitar (ou referenciar) em vez de criar sua própria imagem personalizada. Para ver o Dockerfiles para essas imagens, consulte o repositório GitHub [Azure/AzureML-containers](https://github.com/Azure/AzureML-Containers) .

Para imagens de GPU, o Azure ML atualmente oferece imagens de base cuda9 e cuda10. As principais dependências instaladas nessas imagens básicas são:

| Dependências | IntelMPI CPU | CPU de OpenMP | GPU IntelMPI | GPU de OpenMP |
| --- | --- | --- | --- | --- |
| miniconda | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 |
| MPI | intelmpi = = 2018.3.222 |openmpi = = 3.1.2 |intelmpi = = 2018.3.222| openmpi = = 3.1.2 |
| CUDA | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2.4 | 2.4 |
| Git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

As imagens de CPU são criadas a partir do Ubuntu 16.04. As imagens de GPU para cuda9 são criadas a partir do NVIDIA/CUDA: 9.0-cudnn7-desenvolvedor-Ubuntu 16.04. As imagens de GPU para cuda10 são criadas a partir do NVIDIA/CUDA: 10.0-cudnn7-desenvolvedor-Ubuntu 16.04.
<a id="getname"></a>

> [!IMPORTANT]
> Ao usar imagens personalizadas do Docker, é recomendável que você fixe as versões do pacote para garantir melhor a reprodução.

### <a name="get-container-registry-information"></a>Obter informações do registro de contêiner

Nesta seção, saiba como obter o nome do registro de contêiner do Azure para seu espaço de trabalho Azure Machine Learning.

> [!WARNING]
> O registro de contêiner do Azure para seu espaço de trabalho é __criado na primeira vez que você treina ou implanta um modelo__ usando o espaço de trabalho. Se você tiver criado um novo espaço de trabalho, mas não for treinado ou criado um modelo, nenhum registro de contêiner do Azure existirá para o espaço de trabalho.

Se você já tiver treinado ou implantado modelos usando Azure Machine Learning, um registro de contêiner foi criado para seu espaço de trabalho. Para localizar o nome desse registro de contêiner, use as seguintes etapas:

1. Abra um novo shell ou prompt de comando e use o seguinte comando para se autenticar em sua assinatura do Azure:

    ```azurecli-interactive
    az login
    ```

    Siga os prompts para autenticar a assinatura.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Use o comando a seguir para listar o registro de contêiner para o espaço de trabalho. Substitua `<myworkspace>` pelo nome do espaço de trabalho do Azure Machine Learning. Substitua `<resourcegroup>` pelo grupo de recursos do Azure que contém seu espaço de trabalho:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    As informações retornadas são semelhantes ao seguinte texto:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    O `<registry_name>` valor é o nome do registro de contêiner do Azure para seu espaço de trabalho.

### <a name="build-a-custom-base-image"></a>Criar uma imagem de base personalizada

As etapas nesta seção orientam a criação de uma imagem personalizada do Docker no registro de contêiner do Azure. Para dockerfiles de exemplo, consulte o repositório GitHub [Azure/AzureML-containers](https://github.com/Azure/AzureML-Containers) ).

1. Crie um novo arquivo de texto chamado `Dockerfile` e use o seguinte texto como o conteúdo:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Em um shell ou prompt de comando, use o seguinte para se autenticar no registro de contêiner do Azure. Substitua pelo `<registry_name>` nome do registro de contêiner no qual você deseja armazenar a imagem:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Para carregar o Dockerfile e compilá-lo, use o comando a seguir. Substitua `<registry_name>` pelo nome do registro de contêiner no qual você deseja armazenar a imagem:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > Neste exemplo, uma marca de `:v1` é aplicada à imagem. Se nenhuma marca for fornecida, uma marca de `:latest` será aplicada.

    Durante o processo de compilação, as informações são transmitidas de volta para a linha de comando. Se a compilação for bem-sucedida, você receberá uma mensagem semelhante ao seguinte texto:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Para obter mais informações sobre como criar imagens com um registro de contêiner do Azure, consulte [Compilar e executar uma imagem de contêiner usando tarefas do registro de contêiner do Azure](../container-registry/container-registry-quickstart-task-cli.md)

Para obter mais informações sobre como carregar imagens existentes para um registro de contêiner do Azure, consulte [enviar por push sua primeira imagem para um registro de contêiner privado do Docker](../container-registry/container-registry-get-started-docker-cli.md).

## <a name="use-a-custom-base-image"></a>Usar uma imagem de base personalizada

Para usar uma imagem personalizada, você precisará das seguintes informações:

* O __nome da imagem__. Por exemplo, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` é o caminho para uma imagem simples do Docker fornecida pela Microsoft.

    > [!IMPORTANT]
    > Para imagens personalizadas que você criou, certifique-se de incluir todas as marcas que foram usadas com a imagem. Por exemplo, se a imagem foi criada com uma marca específica, como `:v1` . Se você não usou uma marca específica ao criar a imagem, uma marca de `:latest` foi aplicada.

* Se a imagem estiver em um __repositório privado__, você precisará das seguintes informações:

    * O __endereço__ do registro. Por exemplo, `myregistry.azureecr.io`.
    * Um nome de __usuário__ e __senha__ da entidade de serviço que tem acesso de leitura ao registro.

    Se você não tiver essas informações, fale com o administrador do registro de contêiner do Azure que contém a imagem.

### <a name="publicly-available-base-images"></a>Imagens base disponíveis publicamente

A Microsoft fornece várias imagens do Docker em um repositório publicamente acessível, que pode ser usado com as etapas nesta seção:

| Imagem | Descrição |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Imagem principal para Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Contém tempo de execução ONNX para CPU inferência |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Contém o tempo de execução ONNX e CUDA para GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Contém ONNX Runtime e TensorRT para GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Contém o ONNX Runtime e o OpenVINO para o design do Intel <sup></sup> Vision Accelerator com base em Movidius<sup>TM</sup> MyriadX VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Contém ONNX Runtime e OpenVINO para Intel <sup></sup> Movidius<sup>TM</sup> USB pentes |

Para obter mais informações sobre as imagens base de tempo de execução ONNX, consulte a [seção dockerfile de tempo de execução ONNX](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) no repositório github.

> [!TIP]
> Como essas imagens estão disponíveis publicamente, você não precisa fornecer um endereço, nome de usuário ou senha ao usá-las.

Para obter mais informações, consulte Azure Machine Learning repositório de [contêineres](https://github.com/Azure/AzureML-Containers) no github.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Usar uma imagem com o SDK do Azure Machine Learning

Para usar uma imagem armazenada no **registro de contêiner do Azure para seu espaço de trabalho** ou um **registro de contêiner que esteja publicamente acessível**, defina os seguintes atributos de [ambiente](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) :

+ `docker.enabled=True`
+ `docker.base_image`: Defina como o registro e o caminho para a imagem.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Para usar uma imagem de um __registro de contêiner privado__ que não está em seu espaço de trabalho, você deve usar `docker.base_image_registry` para especificar o endereço do repositório e um nome de usuário e senha:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

Você deve adicionar o azureml-padrões com a versão >= 1.0.45 como uma dependência Pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web. Você também deve definir inferencing_stack_version Propriedade no ambiente como "mais recente", isso instalará os pacotes apt específicos necessários para o serviço Web. 

Depois de definir o ambiente, use-o com um objeto [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py) para definir o ambiente de inferência no qual o modelo e o serviço Web serão executados.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Neste ponto, você pode continuar a implantação. Por exemplo, o trecho de código a seguir implantaria um serviço Web localmente usando a configuração de inferência e a imagem personalizada:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações sobre a implantação, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

Para obter mais informações sobre como personalizar o ambiente do Python, confira [Criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md). 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Usar uma imagem com a CLI do Machine Learning

> [!IMPORTANT]
> Atualmente, a CLI do Machine Learning pode usar imagens do registro de contêiner do Azure para seu espaço de trabalho ou repositórios publicamente acessíveis. Ele não pode usar imagens de registros privados autônomos.

Antes de implantar um modelo usando a CLI do Machine Learning, crie um [ambiente](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) que usa a imagem personalizada. Em seguida, crie um arquivo de configuração de inferência que faça referência ao ambiente. Você também pode definir o ambiente diretamente no arquivo de configuração de inferência. O documento JSON a seguir demonstra como fazer referência a uma imagem em um registro de contêiner público. Neste exemplo, o ambiente é definido em linha:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Esse arquivo é usado com o `az ml model deploy` comando. O `--ic` parâmetro é usado para especificar o arquivo de configuração de inferência.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Para obter mais informações sobre como implantar um modelo usando a CLI do ML, consulte a seção "registro do modelo, criação de perfil e implantação" da [extensão da CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) artigo.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [onde implantar e como](how-to-deploy-and-where.md).
* Saiba como [treinar e implantar modelos de aprendizado de máquina usando o Azure pipelines](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops&preserve-view=true).