---
title: Como implantar um modelo usando uma imagem personalizada do Docker
titleSuffix: Azure Machine Learning service
description: Saiba como usar uma imagem personalizada do Docker ao implantar seus modelos de serviço do Azure Machine Learning. Ao implantar um modelo treinado, uma imagem do Docker é criada para a imagem do host, servidor web e outros componentes necessários para executar o serviço. Enquanto o serviço Azure Machine Learning fornece uma imagem padrão para você, você também pode usar sua própria imagem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/11/2019
ms.openlocfilehash: b8591fe750d4bb1441cdc28c488b2c860eb0bccb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840062"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>Implantar um modelo usando uma imagem personalizada do Docker

Saiba como usar uma imagem personalizada do Docker ao implantar os modelos treinados com o serviço de Azure Machine Learning.

Quando você implanta um modelo treinado em um serviço web ou dispositivo do IoT Edge, uma imagem do Docker é criada. Esta imagem contém o modelo, o ambiente de conda e ativos necessários para usar o modelo. Ele também contém um servidor web para lidar com solicitações de entrada quando implantado como um serviço web e os componentes necessários para trabalhar com o IoT Hub do Azure.

O serviço do Azure Machine Learning fornece uma imagem do Docker padrão, para que você não precisa se preocupar sobre como criar um. Você também pode usar uma imagem personalizada que você cria como um _imagem base_. Uma imagem de base é usada como o ponto de partida quando uma imagem é criada para uma implantação. Ele fornece o sistema operacional e os componentes subjacentes. O processo de implantação, em seguida, adiciona componentes adicionais, como seu modelo, o ambiente do conda e outros ativos, para a imagem antes de implantá-lo.

Normalmente, você cria uma imagem personalizada quando desejar controlar as versões do componente ou economizar tempo durante a implantação. Por exemplo, você talvez queira padronizar em uma versão específica do Python, Conda ou outro componente. Você também poderá instalar o software exigido pelo seu modelo, em que o processo de instalação leva muito tempo. A instalação do software ao criar a imagem base significa que você não precisa instalá-lo para cada implantação.

> [!IMPORTANT]
> Ao implantar um modelo, você não pode substituir componentes principais, como o servidor web ou do IoT Edge. Esses componentes fornecem um ambiente de trabalho conhecidos que é testado e suportado pela Microsoft.

> [!WARNING]
> Microsoft não poderá ajudar a solucionar problemas causados por uma imagem personalizada. Se você encontrar problemas, pode ser solicitado para usar a imagem padrão ou uma das imagens da que Microsoft fornece para ver se o problema é específico para sua imagem.

Este documento é dividido em duas seções:

* Crie uma imagem personalizada: Fornece informações para administradores e DevOps sobre como criar uma imagem personalizada e configurando a autenticação para um registro de contêiner do Azure usando a CLI do Azure e a CLI de aprendizado de máquina.
* Use uma imagem personalizada: Fornece informações para os cientistas de dados e DevOps/MLOps sobre como usar imagens personalizadas ao implantar um modelo treinado do SDK do Python ou da CLI do ML.

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de trabalho de serviço do Azure Machine Learning. Para obter mais informações, consulte o [criar um espaço de trabalho](setup-create-workspace.md) artigo.
* O Machine Learning do Azure SDK. Para obter mais informações, consulte a seção SDK do Python para o [criar um espaço de trabalho](setup-create-workspace.md#sdk) artigo.
* O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* O [extensão CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Uma [registro de contêiner do Azure](/azure/container-registry) ou outros registros do Docker que está acessível na internet.
* As etapas neste documento pressupõem que você esteja familiarizado com a criação e uso de um __configuração de inferência__ objeto como parte da implantação de modelo. Para obter mais informações, consulte a seção "preparar para implantar" [onde implantar e como](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-image"></a>Criar uma imagem personalizada

As informações nesta seção pressupõem que você está usando um registro de contêiner do Azure para armazenar imagens do Docker. Use a lista de verificação a seguir ao planejar para criar imagens personalizadas para o serviço de Azure Machine Learning:

* Você usará o registro de contêiner do Azure criado para o espaço de trabalho do serviço de Azure Machine Learning ou um registro de contêiner do Azure autônomo?

    Quando usar imagens armazenados na __registro de contêiner para o espaço de trabalho__, você não precisa se autenticar no registro. Autenticação é manipulada pelo espaço de trabalho.

    > [!WARNING]
    > É o Rzegistry de contêiner do Azure para seu espaço de trabalho __criado na primeira vez, treinar ou implantar um modelo__ usando o espaço de trabalho. Se você tiver criado um novo espaço de trabalho, mas não treinado ou criou um modelo, nenhum registro de contêiner do Azure continuará a existir no espaço de trabalho.

    Para obter informações sobre como recuperar o nome do registro de contêiner do Azure para seu espaço de trabalho, consulte o [nome do registro de contêiner Get](#getname) seção deste artigo.

    Quando usar imagens armazenados em uma __registro de contêiner autônoma__, você precisará configurar uma entidade de serviço que tem pelo menos acesso de leitura. Você, em seguida, forneça o ID da entidade de serviço (nome de usuário) e a senha para qualquer pessoa que usa imagens do registro. A exceção é se você tornar o registro de contêiner publicamente acessível.

    Para obter informações sobre como criar um registro de contêiner privado do Azure, consulte [criar um registro de contêiner privado](/azure/container-registry/container-registry-get-started-azure-cli).

    Para obter informações sobre como usar as entidades de serviço com o registro de contêiner do Azure, consulte [autenticação de registro de contêiner do Azure com entidades de serviço](/azure/container-registry/container-registry-auth-service-principal).

* Informações de registro de contêiner e imagem do Azure: Forneça o nome da imagem para qualquer pessoa que precise usá-lo. Por exemplo, uma imagem chamada `myimage`, armazenada em um registro denominado `myregistry`, é referenciado como `myregistry.azurecr.io/myimage` ao usar a imagem para implantação de modelo

* Requisitos de imagem: O serviço do Azure Machine Learning só dá suporte a imagens do Docker que fornecem o seguinte software:

    * Ubuntu 16.04 ou posterior.
    * 4\.5 Conda. # ou maior.
    * Python 3.5. # ou 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Obter informações de registro de contêiner

Nesta seção, saiba como obter o nome do registro de contêiner do Azure para seu espaço de trabalho do serviço de Azure Machine Learning.

> [!WARNING]
> O registro de contêiner do Azure para seu espaço de trabalho é __criado na primeira vez, treinar ou implantar um modelo__ usando o espaço de trabalho. Se você tiver criado um novo espaço de trabalho, mas não treinado ou criou um modelo, nenhum registro de contêiner do Azure continuará a existir no espaço de trabalho.

Se você já tiver treinado ou implantado modelos usando o serviço de Azure Machine Learning, um registro de contêiner foi criado para seu espaço de trabalho. Para localizar o nome deste registro de contêiner, use as seguintes etapas:

1. Abra um novo shell ou prompt de comando e use o comando a seguir para autenticar sua assinatura do Azure:

    ```azurecli-interactive
    az login
    ```

    Siga os prompts para autenticar a assinatura.

2. Use o comando a seguir para listar o registro de contêiner para o espaço de trabalho. Substitua `<myworkspace>` com o nome do espaço de trabalho de serviço do Azure Machine Learning. Substitua `<resourcegroup>` com o grupo de recursos do Azure que contém seu espaço de trabalho:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    As informações retornadas são semelhantes ao seguinte texto:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    O `<registry_name>` valor é o nome do registro de contêiner do Azure para seu espaço de trabalho.

### <a name="build-a-custom-image"></a>Criar uma imagem personalizada

As etapas neste passo a passo de seção criar uma imagem personalizada do Docker no registro de contêiner do Azure.

1. Criar um novo arquivo de texto chamado `Dockerfile`e use o seguinte texto como conteúdo:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Em um prompt de comando ou shell, use o seguinte para se autenticar no registro de contêiner do Azure. Substitua o `<registry_name>` com o nome do registro de contêiner que você deseja armazenar a imagem no:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Para carregar o Dockerfile e compilá-lo, use o comando a seguir. Substitua `<registry_name>` com o nome do registro de contêiner que você deseja armazenar a imagem no:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    Durante o processo de compilação, as informações são transmitidas para o de volta para a linha de comando. Se a compilação for bem-sucedida, você receberá uma mensagem semelhante ao seguinte texto:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Para obter mais informações sobre a criação de imagens com um registro de contêiner do Azure, consulte [compilar e executar uma imagem de contêiner usando as tarefas de registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Para obter mais informações sobre como carregar imagens existentes para um registro de contêiner do Azure, consulte [envie sua primeira imagem para um registro de contêiner do Docker privado](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-image"></a>Usar uma imagem personalizada

Para usar uma imagem personalizada, você precisa ter as seguintes informações:

* O __nome da imagem__. Por exemplo, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` é o caminho para uma imagem do Docker básico fornecido pela Microsoft.
* Se a imagem está em um __repositório privado__, você precisa das seguintes informações:

    * O registro __endereço__. Por exemplo, `myregistry.azureecr.io`.
    * Uma entidade de serviço __nome de usuário__ e __senha__ que tenha acesso de leitura ao registro.

    Se você não tiver essas informações, converse com o administrador para o registro de contêiner do Azure que contém a imagem.

### <a name="publicly-available-images"></a>Imagens disponíveis publicamente

A Microsoft fornece várias imagens do docker em um repositório acessível publicamente, o que pode ser usado com as etapas nesta seção:

| Image | DESCRIÇÃO |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Imagem básica para o serviço de Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | Contém o tempo de execução ONNX. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | Contém o tempo de execução ONNX e componentes CUDA. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | Contém o tempo de execução ONNX e o TensorRT. |

> [!TIP]
> Como essas imagens estão disponíveis publicamente, você não precisa fornecer um endereço, nome de usuário ou senha quando usá-los.

> [!IMPORTANT]
> Imagens da Microsoft que usam o CUDA ou o TensorRT devem ser usadas apenas nos serviços do Microsoft Azure.

> [!TIP]
>__Se seu modelo é treinado em computação do Azure Machine Learning__, usando __versão 1.0.22 ou maior__ do SDK do Azure Machine Learning, uma imagem é criada durante o treinamento. Para descobrir o nome dessa imagem, use `run.properties["AzureML.DerivedImageName"]`. O exemplo a seguir demonstra como usar esta imagem:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Usar uma imagem com o SDK do Azure Machine Learning

Para usar uma imagem personalizada, defina as `base_image` propriedade do [objeto de configuração de inferência](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) para o endereço da imagem:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

Esse formato funciona para ambas as imagens armazenadas no registro de contêiner do Azure para seus registros de contêiner e de espaço de trabalho que são acessíveis publicamente. Por exemplo, o código a seguir usa uma imagem padrão fornecida pela Microsoft:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Para usar uma imagem de um __registro de contêiner privado__ que não está em seu espaço de trabalho, você deve especificar o endereço do repositório e um nome de usuário e senha:

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>Usar uma imagem com a CLI de aprendizado de máquina

> [!IMPORTANT]
> Atualmente, a CLI do Machine Learning pode usar imagens do registro de contêiner do Azure para seu espaço de trabalho ou repositórios publicamente acessíveis. Ele não pode usar imagens de registros privados de autônomo.

Ao implantar um modelo usando a CLI de aprendizado de máquina, você pode fornecer um arquivo de configuração de inferência de tipos que referencia a imagem personalizada. O documento JSON a seguir demonstra como fazer referência a uma imagem em um registro de contêiner público:

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

Esse arquivo é usado com o `az ml model deploy` comando. O `--ic` parâmetro é usado para especificar o arquivo de configuração de inferência de tipos.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Para obter mais informações sobre como implantar um modelo usando a CLI do ML, consulte a seção "registro do modelo de criação de perfil e implantação" a [extensão CLI para o serviço de Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) artigo.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [onde implantar e como](how-to-deploy-and-where.md).
* Saiba como [treinar e implantar modelos de aprendizado de máquina usando o Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
