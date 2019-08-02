---
title: Como e onde implantar modelos
titleSuffix: Azure Machine Learning service
description: 'Saiba como e onde implantar os modelos do serviço do Azure Machine Learning incluindo: Instâncias de Contêiner do Azure, Serviço de Kubernetes do Azure, Azure IoT Edge e matriz de porta programável no campo.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: 6b9ebb2f7ef46fd2900d036f178201863ecbc8d4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358828"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Implantar modelos com o serviço do Azure Machine Learning

Saiba como implantar o modelo de aprendizado de máquina como um serviço Web na nuvem do Azure ou para IoT Edge dispositivos. 

O fluxo de trabalho é semelhante [, independentemente de onde você implanta](#target) seu modelo:

1. Registre o modelo.
1. Preparar para implantar (especificar ativos, uso, destino de computação)
1. Implante o modelo no destino de computação.
1. Teste o modelo implantado, também chamado de serviço Web.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, confira [Gerenciar, implantar e monitorar modelos com o Serviço do Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um modelo. Se você não tiver um modelo treinado, poderá usar o modelo & arquivos de dependência fornecidos neste [tutorial](https://aka.ms/azml-deploy-cloud).

- A [extensão CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), o [SDK do Azure Machine Learning Python](https://aka.ms/aml-sdk)ou a [extensão Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

## <a id="registermodel"></a>Registrar seu modelo

Um contêiner lógico de modelo registrado para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo no espaço de trabalho. Após o registro, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que foram registrados.

Os modelos de aprendizado de máquina são registrados em seu espaço de trabalho do Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou pode vir de outro lugar. Os exemplos a seguir demonstram como registrar um modelo do arquivo:

### <a name="register-a-model-from-an-experiment-run"></a>Registrar um modelo de uma execução de experimento

+ **Scikit-Aprenda o exemplo usando o SDK**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > Para incluir vários arquivos no registro do modelo, defina `model_path` para o diretório que contém os arquivos.

+ **Usando a CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```

  > [!TIP]
  > Para incluir vários arquivos no registro do modelo, defina `--asset-path` para o diretório que contém os arquivos.

+ **Usando VS Code**

  Registre modelos usando qualquer arquivo ou pasta de modelo com a extensão [vs Code](how-to-vscode-tools.md#deploy-and-manage-models) .

### <a name="register-an-externally-created-model"></a>Registrar um modelo criado externamente

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

Você pode registrar um modelo criado externamente fornecendo um **caminho local** para o modelo. Você pode fornecer uma pasta ou um único arquivo.

+ **Exemplo de ONNX com o SDK do Python:**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

  > [!TIP]
  > Para incluir vários arquivos no registro do modelo, defina `model_path` para o diretório que contém os arquivos.

+ **Usando a CLI**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > Para incluir vários arquivos no registro do modelo, defina `-p` para o diretório que contém os arquivos.

**Tempo estimado**: Cerca de 10 segundos.

Para obter mais informações, consulte a documentação de referência da [classe do modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Para obter mais informações sobre como trabalhar com modelos treinados fora do Azure Machine Learning Service, consulte [como implantar um modelo existente](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Escolher um destino de computação

Os seguintes destinos de computação, ou recursos de computação, podem ser usados para hospedar a implantação do serviço Web. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Preparar-se para implantar

Para implantar como um serviço Web, você deve criar uma configuração de inferência (`InferenceConfig`) e uma configuração de implantação. A inferência, ou a Pontuação do modelo, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção. Na configuração de inferência, você especifica os scripts e as dependências necessárias para atender ao seu modelo. Na configuração de implantação, você especifica detalhes de como servir o modelo no destino de computação.

> [!IMPORTANT]
> O SDK do Azure Machine Learning não fornece uma maneira de implantações de serviço Web ou de IoT Edge para acessar seu armazenamento de dados ou conjuntos. Se você precisar que o modelo implantado acesse dados armazenados fora da implantação, como em uma conta de armazenamento do Azure, você deve desenvolver uma solução de código personalizado usando o SDK relevante. Por exemplo, o [SDK do armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python).
>
> Outra alternativa que pode funcionar para seu cenário é [previsões de lote](how-to-run-batch-predictions.md), que fornece acesso a repositórios de armazenamento durante a pontuação.

### <a id="script"></a> 1. Definir o script de entrada & dependências

O script de entrada recebe dados enviados para um serviço Web implantado e os transmite para o modelo. Ele então envia de volta ao cliente a resposta retornada pelo modelo. **O script é específico para seu modelo**; Ele deve entender os dados esperados e retornados pelo modelo.

O script contém duas funções que carregam e executam o modelo:

* `init()`: Normalmente, essa função carrega o modelo em um objeto global. Essa função é executada apenas uma vez quando o contêiner do Docker para o serviço Web é iniciado.

* `run(input_data)`: Essa função usa o modelo para prever um valor com base nos dados de entrada. Entradas e saídas para a execução normalmente usam JSON para serialização e desserialização. Você também pode trabalhar com os dados binários brutos. Você pode transformar os dados antes de enviá-los para o modelo ou antes de retorná-los ao cliente.

#### <a name="what-is-getmodelpath"></a>O que é o get_model_path?

Ao registrar um modelo, você fornece um nome de modelo usado para gerenciar o modelo no registro. Use esse nome com o [modelo. Obtenha _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) para recuperar o caminho dos arquivos de modelo no sistema de arquivos local. Se você registrar uma pasta ou uma coleção de arquivos, essa API retornará o caminho para o diretório que contém esses arquivos.

Ao registrar um modelo, você fornece um nome, que corresponde a onde o modelo é colocado, seja localmente ou durante a implantação do serviço.

O exemplo abaixo retornará um caminho para um único arquivo chamado `sklearn_mnist_model.pkl` (que foi registrado com o nome `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

#### <a name="optional-automatic-swagger-schema-generation"></a>Adicional Geração de esquema Swagger automático

Para gerar automaticamente um esquema para o serviço Web, forneça um exemplo de entrada e/ou saída no construtor para um dos objetos de tipo definidos, e o tipo e o exemplo são usados para criar automaticamente o esquema. Azure Machine Learning serviço, em seguida, cria uma especificação de [openapi](https://swagger.io/docs/specification/about/) (Swagger) para o serviço Web durante a implantação.

Atualmente, há suporte para os seguintes tipos:

* `pandas`
* `numpy`
* `pyspark`
* objeto Python padrão

Para usar a geração de esquema, `inference-schema` inclua o pacote em seu arquivo de ambiente Conda. O exemplo a seguir `[numpy-support]` usa uma vez que o script de entrada usa um tipo de parâmetro numpy: 

#### <a name="example-dependencies-file"></a>Arquivo de dependências de exemplo
O YAML a seguir é um exemplo de um arquivo de dependências Conda para inferência.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Se você quiser usar a geração de esquema automática, seu script  de entrada deverá `inference-schema` importar os pacotes. 

Defina os formatos de exemplo de entrada e saída `input_sample` nas `output_sample` variáveis e, que representam os formatos de solicitação e resposta para o serviço Web. Use esses exemplos nos decoradores da função de entrada e saída na `run()` função. O exemplo scikit-Learn abaixo usa a geração de esquema.

> [!TIP]
> Depois de implantar o serviço, use a `swagger_uri` propriedade para recuperar o documento JSON do esquema.

#### <a name="example-entry-script"></a>Exemplo de script de entrada

O exemplo a seguir demonstra como aceitar e retornar dados JSON:

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>Exemplo de script com entrada de dicionário (consumo de suporte de Power BI)

O exemplo a seguir demonstra como definir dados de entrada como < chave: valor > dicionário, usando dataframe. Esse método tem suporte para o consumo do serviço Web implantado do Power BI ([saiba mais sobre como consumir o serviço Web do Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```
Para obter mais scripts de exemplo, consulte os exemplos a seguir:

* Pytorch[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Pontuação em relação a dados binários: [Como consumir um serviço Web](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Definir seu InferenceConfig

A configuração de inferência descreve como configurar o modelo para fazer previsões. O exemplo a seguir demonstra como criar uma configuração de inferência. Essa configuração especifica o tempo de execução, o script de entrada e (opcionalmente) o arquivo de ambiente Conda:

```python
inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Para obter mais informações, consulte a referência de classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Para obter informações sobre como usar uma imagem personalizada do Docker com a configuração de inferência, consulte [como implantar um modelo usando uma imagem personalizada](how-to-deploy-custom-docker-image.md)do Docker.

### <a name="cli-example-of-inferenceconfig"></a>Exemplo de CLI de InferenceConfig

[!INCLUDE [inferenceconfig](../../../includes/machine-learning-service-inference-config.md)]

O comando a seguir demonstra como implantar um modelo usando a CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Neste exemplo, a configuração contém os seguintes itens:

* Que este modelo requer Python
* O [script de entrada](#script), que é usado para manipular solicitações da Web enviadas ao serviço implantado
* O arquivo Conda que descreve os pacotes do python necessários para a inferência

Para obter informações sobre como usar uma imagem personalizada do Docker com a configuração de inferência, consulte [como implantar um modelo usando uma imagem personalizada](how-to-deploy-custom-docker-image.md)do Docker.

### <a name="3-define-your-deployment-configuration"></a>3. Definir sua configuração de implantação

Antes de implantar o, você deve definir a configuração de implantação. A configuração de implantação é específica para o destino de computação que hospedará o serviço Web. Por exemplo, ao implantar localmente, você deve especificar a porta onde o serviço aceita solicitações.

Talvez você também precise criar o recurso de computação. Por exemplo, se você ainda não tiver um serviço kubernetes do Azure associado ao seu espaço de trabalho.

A tabela a seguir fornece um exemplo de criação de uma configuração de implantação para cada destino de computação:

| Destino de computação | Exemplo de configuração de implantação |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Instância do Contêiner do Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Serviço de Kubernetes do Azure | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As seções a seguir demonstram como criar a configuração de implantação e, em seguida, usá-la para implantar o serviço Web.

### <a name="optional-profile-your-model"></a>Opcional: Criar perfil do seu modelo

Antes de implantar seu modelo como um serviço, você pode criar um perfil para determinar os requisitos de CPU e memória ideais usando o SDK ou a CLI.  Os resultados de criação de perfil de modelo são `Run` emitidos como um objeto. Os detalhes completos do [esquema de perfil de modelo podem ser encontrados na documentação da API](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)

Saiba mais sobre [como criar o perfil de seu modelo usando o SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-).

Para criar o perfil de seu modelo usando a CLI, use o [perfil de modelo AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

## <a name="deploy-to-target"></a>Implantar no destino

### <a id="local"></a>Implantação local

Para implantar localmente, você precisa ter o **Docker instalado** no computador local.

+ **Usando o SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Usando a CLI**

    Para implantar usando a CLI, use o comando a seguir. Substituir `mymodel:1` pelo nome e versão do modelo registrado:

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    [!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-local-deploy-config.md)]

### <a id="aci"></a>DEVTEST (instâncias de contêiner do Azure)

Use as Instâncias de Contêiner do Azure para implantar os modelos como um serviço Web, se uma ou mais das seguintes condições forem verdadeiras:
- Você precisa implantar rapidamente e validar o modelo.
- Você está testando um modelo ainda em desenvolvimento. 

Para ver a disponibilidade de cota e região para o ACI, consulte o artigo [cotas e disponibilidade de região para instâncias de contêiner do Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) .

+ **Usando o SDK**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Usando a CLI**

    Para implantar usando a CLI, use o comando a seguir. Substituir `mymodel:1` pelo nome e versão do modelo registrado. Substitua `myservice` pelo nome para fornecer este serviço:

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    [!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

+ **Usando VS Code**

  Para [implantar seus modelos com vs Code](how-to-vscode-tools.md#deploy-and-manage-models) você não precisa criar um contêiner ACI para testar com antecedência, porque os contêineres ACI são criados de forma dinâmica.

Para obter mais informações, consulte a documentação de referência para as classes [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a>Serviço kubernetes do Azure (DEVTEST & produção)

Você pode usar um cluster AKS existente ou criar outro usando o SDK do Azure Machine Learning, a CLI ou o portal do Azure.

<a id="deploy-aks"></a>

Se você já tiver um cluster AKS anexado, poderá implantá-lo. Se você não criou ou anexou um cluster AKS, siga o processo para <a href="#create-attach-aks">criar um novo cluster AKs</a>.

+ **Usando o SDK**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **Usando a CLI**

    Para implantar usando a CLI, use o comando a seguir. Substitua `myaks` pelo nome do destino de computação AKs. Substituir `mymodel:1` pelo nome e versão do modelo registrado. Substitua `myservice` pelo nome para fornecer este serviço:

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    [!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

+ **Usando VS Code**

  Você também pode [implantar no AKs por meio da extensão vs Code](how-to-vscode-tools.md#deploy-and-manage-models), mas precisará configurar os clusters do AKS com antecedência.

Saiba mais sobre a implantação do AKS e o dimensionamento automático na referência [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) .

#### Criar um novo cluster AKS<a id="create-attach-aks"></a>
**Tempo estimado**: Aproximadamente 20 minutos.

Criar ou anexar um cluster AKS é um processo de uma vez para seu espaço de trabalho. Você pode reutilizar esse cluster para várias implantações. Se você excluir o cluster ou o grupo de recursos que o contém, deverá criar um novo cluster na próxima vez que precisar implantá-lo. Você pode ter vários clusters AKS anexados ao seu espaço de trabalho.

Se você quiser criar um cluster AKs para desenvolvimento, validação e teste, defina `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` ao usar [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)o. Um cluster criado com essa configuração terá apenas um nó.

> [!IMPORTANT]
> A `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` configuração cria um cluster AKs que não é adequado para lidar com o tráfego de produção. Os tempos de inferência podem ser maiores do que em um cluster criado para produção. A tolerância a falhas também não é garantida para clusters de desenvolvimento/teste.
>
> Recomendamos que os clusters criados para desenvolvimento/teste usem pelo menos duas CPUs virtuais.

O exemplo a seguir demonstra como criar um novo cluster do serviço kubernetes do Azure:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output=True)
```

Para obter mais informações sobre como criar um cluster AKS fora do SDK do Azure Machine Learning, consulte os seguintes artigos:
* [Criar um cluster AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Criar um cluster AKS (Portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Para obter mais informações sobre `cluster_purpose` o parâmetro, consulte a referência de [AksCompute. ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) .

> [!IMPORTANT]
> Para [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se você escolher valores personalizados para agent_count e vm_size, será necessário certificar-se de que agent_count multiplicado por vm_size será maior ou igual a 12 CPUs virtuais. Por exemplo, se você usar um vm_size de "Standard_D3_v2", que tenha 4 CPUs virtuais, será necessário escolher um agent_count de 3 ou maior.
>
> O SDK do Azure Machine Learning não fornece suporte para dimensionar um cluster AKS. Para dimensionar os nós no cluster, use a interface do usuário do cluster AKS no portal do Azure. Você só pode alterar a contagem de nós, não o tamanho da VM do cluster.

#### <a name="attach-an-existing-aks-cluster"></a>Anexar um cluster AKS existente
**Tempo estimado:** Aproximadamente 5 minutos.

Se você já tiver o cluster AKS em sua assinatura do Azure e for a versão 1.12. # #, você poderá usá-lo para implantar a imagem.

> [!WARNING]
> Ao anexar um cluster AKs a um espaço de trabalho, você pode definir como usará o cluster definindo o `cluster_purpose` parâmetro.
>
> Se você não definir o `cluster_purpose` parâmetro ou definido `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, o cluster deverá ter pelo menos 12 CPUs virtuais disponíveis.
>
> Se você definir `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, o cluster não precisará ter 12 CPUs virtuais. No entanto, um cluster configurado para desenvolvimento/teste não será adequado para o tráfego de nível de produção e poderá aumentar os tempos de inferência.

O código a seguir demonstra como anexar um cluster AKS 1.12. # # existente ao seu espaço de trabalho:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group=resource_group,
                                                cluster_name=cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

Para obter mais informações `attack_configuration()`sobre o, consulte a referência [AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-) .

Para obter mais informações sobre `cluster_purpose` o parâmetro, consulte a referência de [AksCompute. ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) .

## <a name="consume-web-services"></a>Consumir serviços Web

Cada serviço Web implantado fornece uma API REST, para que você possa criar aplicativos cliente em uma variedade de linguagens de programação. Se você tiver habilitado a autenticação para seu serviço, será necessário fornecer uma chave de serviço como um token no cabeçalho da solicitação.

### <a name="request-response-consumption"></a>Consumo de solicitação-resposta

Aqui está um exemplo de como invocar seu serviço no Python:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Para obter mais informações, confira [Criar aplicativos cliente para consumir serviços Web](how-to-consume-web-service.md).


### <a id="azuremlcompute"></a>Inferência de lote
Azure Machine Learning destinos de computação são criados e gerenciados pelo serviço de Azure Machine Learning. Eles podem ser usados para previsão de lote de pipelines de Azure Machine Learning.

Para obter uma explicação sobre a inferência de lote com Azure Machine Learning computação, leia o artigo [como executar previsões de lote](how-to-run-batch-predictions.md) .

### <a id="iotedge"></a>Inferência de IoT Edge
O suporte para a implantação no Edge está em versão prévia. Para obter mais informações, consulte o artigo [implantar Azure Machine Learning como um módulo IOT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) .


## <a id="update"></a>Atualizar serviços Web

Ao criar um novo modelo, você deve atualizar manualmente cada serviço do qual deseja usar o novo modelo. Para atualizar o serviço Web, use o método `update`. O código a seguir demonstra como atualizar o serviço Web para usar um novo modelo:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name=service_name, workspace=ws)

# Update to new model(s)
service.update(models=[new_model])
print(service.state)
print(service.get_logs())
```

## <a name="continuous-model-deployment"></a>Implantação de modelo contínua 

Você pode implantar modelos continuamente usando a extensão Machine Learning para o [Azure DevOps](https://azure.microsoft.com/services/devops/). Usando a extensão de Machine Learning para DevOps do Azure, você pode disparar um pipeline de implantação quando um novo modelo de aprendizado de máquina é registrado no espaço de trabalho Azure Machine Learning serviço. 

1. Inscreva-se para [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), o que torna a integração contínua e a entrega de seu aplicativo a qualquer plataforma/qualquer nuvem possível. Azure Pipelines [difere dos pipelines ml](concept-ml-pipelines.md#compare). 

1. [Crie um projeto DevOps do Azure.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Instalar a [extensão de Machine Learning para Azure pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. Use __conexões de serviço__ para configurar uma conexão de entidade de serviço com o espaço de trabalho do Azure Machine Learning Service para acessar todos os seus artefatos. Vá para configurações do projeto, clique em conexões de serviço e selecione Azure Resource Manager.

    ![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png) 

1. Defina AzureMLWorkspace como o __nível de escopo__ e preencha os parâmetros subsequentes.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Em seguida, para implantar continuamente o modelo de aprendizado de máquina usando o Azure Pipelines, em pipelines, selecione __liberar__. Adicione um novo artefato, selecione artefato do modelo do AzureML e a conexão de serviço que foi criada na etapa anterior. Selecione o modelo e a versão para disparar uma implantação. 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. Habilite o gatilho de modelo em seu artefato de modelo. Ativando o gatilho, toda vez que a versão especificada (ou seja, a versão mais recente) desse modelo é registrar em seu espaço de trabalho, um pipeline de versão do Azure DevOps é disparado. 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

Para projetos de exemplo e exemplos, confira [o repositório MLOps](https://github.com/Microsoft/MLOps)

## <a name="clean-up-resources"></a>Limpar recursos
Para excluir um serviço Web implantado, use `service.delete()`.
Para excluir um modelo registrado, use `model.delete()`.

Para obter mais informações, consulte a documentação de referência para [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)e [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Próximas etapas
* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Proteger serviços Web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitorar seus modelos do Azure Machine Learning com o Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)

