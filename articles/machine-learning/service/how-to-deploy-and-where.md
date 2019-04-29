---
title: Como e onde implantar modelos
titleSuffix: Azure Machine Learning service
description: 'Saiba como e onde implantar os modelos do serviço do Azure Machine Learning incluindo: Instâncias de Contêiner do Azure, Serviço de Kubernetes do Azure, Azure IoT Edge e matriz de porta programável no campo.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: a6ef53d56fa293791658b37b16cbaff94aee6ef3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819810"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Implantar modelos com o serviço do Azure Machine Learning

Neste documento, saiba como implantar seu modelo como um serviço Web na nuvem do Azure ou em dispositivos do IoT Edge. 

## <a name="compute-targets-for-deployment"></a>Destinos de computação para implantação

Use o SDK do Azure Machine Learning para implantar seu modelo treinado para os seguintes locais:

| Destino de computação | Tipo de implantação | DESCRIÇÃO |
| ----- | ----- | ----- |
| [AKS (Serviço de Kubernetes do Azure)](#aks) | Inferência de tipos em tempo real | Ideal para implantações de produção em grande escala. Fornece o dimensionamento automático e tempo de resposta rápido. |
| [Azure computação do Machine Learning (amlcompute)](#azuremlcompute) | Inferência de tipos de lote | Execute a previsão em lotes em computação sem servidor. Dá suporte a VMs normais e de baixa prioridade. |
| [ACI (Instâncias de Contêiner do Azure)](#aci) | Testando | Bom para teste ou desenvolvimento. **Não é adequado para cargas de trabalho de produção.** |
| [Azure IoT Edge](#iotedge) | (Visualização) Módulo do IoT | Implante modelos em dispositivos do IoT. A inferência acontece no dispositivo. |
| [FPGA (matriz de porta programável no campo)](#fpga) | (Visualização) Serviço Web | Latência extremamente baixa para inferência em tempo real. |

## <a name="deployment-workflow"></a>Fluxo de trabalho de implantação

O processo de implantação de um modelo é semelhante para todos os destinos de computação:

1. Treine e registre um modelo.
1. Configure e registre uma imagem que usa o modelo.
1. Implante a imagem em um destino de computação.
1. Teste a implantação

O vídeo a seguir demonstra como implantar instâncias de contêiner do Azure:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, confira [Gerenciar, implantar e monitorar modelos com o Serviço do Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites-for-deployment"></a>Pré-requisitos para implantação

[!INCLUDE [aml-prereq](../../../includes/aml-prereq.md)]

- Um modelo treinado. Se você não tiver um modelo treinado, use as etapas no tutorial [Treinar modelos](tutorial-train-models-with-aml.md) para treinar e registrar um com o serviço do Azure Machine Learning.

    > [!NOTE]
    > Enquanto o serviço de Azure Machine Learning pode funcionar com qualquer modelo genérico que pode ser carregado em Python 3, os exemplos deste documento demonstram usando um modelo armazenado no formato de pickle do Python.
    >
    > Para obter mais informações sobre o uso de modelos ONNX, consulte o documento [ONNX e Azure Machine Learning](how-to-build-deploy-onnx.md).

## <a id="registermodel"></a> Registrar um modelo treinado

O registro de modelo é uma maneira de armazenar e organizar modelos treinados na nuvem do Azure. Os modelos são registrados em seu workspace do Serviço do Azure Machine Learning. O modelo pode ser treinado usando o Azure Machine Learning ou outro serviço. O código a seguir demonstra como registrar um modelo de arquivo, defina um nome, marcas e uma descrição:

```python
from azureml.core.model import Model

model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Tempo estimado**: Cerca de 10 segundos.

Para obter um exemplo de registro de um modelo, consulte [treinar um classificador de imagem](tutorial-train-models-with-aml.md).

Para obter mais informações, consulte a documentação de referência da [classe do modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Criar e registrar uma imagem

Os modelos implantados são empacotados como uma imagem. A imagem contém as dependências necessárias para executar o modelo.

Para implantações de **Instância de Contêiner do Azure**, **Serviço de Kubernetes do Azure** e **Azure IoT Edge**, a classe [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) é usada para criar uma configuração de imagem. Em seguida, a configuração da imagem é usada para criar uma nova imagem do Docker.

Ao criar a configuração de imagem, você pode usar um __imagem padrão__ fornecidos pelo serviço do Azure Machine Learning ou um __imagem personalizada__ fornecida por você.

O código a seguir demonstra como criar uma nova configuração de imagem:

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml"}
                                                 )
```

**Tempo estimado**: Cerca de 10 segundos.

Os parâmetros importantes usados neste exemplo são descritos na tabela a seguir:

| Parâmetro | DESCRIÇÃO |
| ----- | ----- |
| `execution_script` | Especifica um script Python que é usado para receber solicitações enviadas para o serviço. Neste exemplo, o script está contido no arquivo `score.py`. Para obter mais informações, confira a seção [Script de execução](#script). |
| `runtime` | Indica que a imagem usa o Python. A outra opção é `spark-py`, que usa o Python com o Apache Spark. |
| `conda_file` | Usado para fornecer um arquivo de ambiente do Conda. Esse arquivo define o ambiente do Conda para o modelo implantado. Para obter mais informações sobre como criar esse arquivo, consulte [Criar um arquivo de ambiente (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

Para obter um exemplo de criação de uma configuração de imagem, consulte [implantar um classificador de imagem](tutorial-deploy-models-with-aml.md).

Para obter mais informações, consulte a documentação de referência da [classe ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)

### <a id="customimage"></a> Usar uma imagem personalizada

Ao usar uma imagem personalizada, a imagem deve atender aos seguintes requisitos:

* Ubuntu 16.04 ou posterior.
* 4.5 Conda. # ou maior.
* Python 3.5. # ou 3.6. #.

Para usar uma imagem personalizada, defina o `base_image` propriedade de configuração da imagem para o endereço da imagem. O exemplo a seguir demonstra como usar uma imagem de um registro de contêiner do Azure públicas e privadas:

```python
# use an image available in public Container Registry without authentication
image_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
image_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
image_config.base_image_registry.address = "myregistry.azurecr.io"
image_config.base_image_registry.username = "username"
image_config.base_image_registry.password = "password"
```

Para obter mais informações sobre como carregar imagens para um registro de contêiner do Azure, consulte [envie sua primeira imagem para um registro de contêiner do Docker privado](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Se seu modelo é treinado em computação do Azure Machine Learning, usando __versão 1.0.22 ou maior__ do SDK do Azure Machine Learning, uma imagem é criada durante o treinamento. O exemplo a seguir demonstra como usar esta imagem:

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

### <a id="script"></a> Script de execução

O script de execução recebe os dados enviados para uma imagem implantada e passa-o para o modelo. Ele então envia de volta ao cliente a resposta retornada pelo modelo. **O script é específico para seu modelo**; ele deve compreender os dados que o modelo de espera e retorna. Para um script de exemplo que funciona com um modelo de classificação de imagem, consulte [implantar um classificador de imagem](tutorial-deploy-models-with-aml.md).

O script contém duas funções que carregam e executam o modelo:

* `init()`: Normalmente, essa função carrega o modelo em um objeto global. Essa função é executada apenas uma vez quando o contêiner do Docker é iniciado.

* `run(input_data)`: Essa função usa o modelo para prever um valor com base nos dados de entrada. Entradas e saídas para a execução normalmente usam JSON para serialização e desserialização. Você também pode trabalhar com os dados binários brutos. Você pode transformar os dados antes de enviá-los para o modelo ou antes de retorná-los ao cliente.

#### <a name="working-with-json-data"></a>Trabalhando com dados JSON

O script de exemplo a seguir aceita e retorna dados JSON. A função `run` transforma os dados JSON em um formato que o modelo espera e, em seguida, transforma a resposta em JSON antes de retorná-la:

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>Trabalhando com os dados binários

Se o modelo aceita __dados binários__, use `AMLRequest`, `AMLResponse` e `rawhttp`. O exemplo de script a seguir aceita dados binários e retorna os bytes invertidos para solicitações POST. Para solicitações GET, ele retorna a URL completa no corpo da resposta:

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> O namespace `azureml.contrib` muda com frequência enquanto trabalhamos para melhorar o serviço. Assim, tudo nesse namespace deve ser considerado como uma versão prévia e sem o suporte total da Microsoft.
>
> Se você precisar testar isso em seu ambiente de desenvolvimento local, instale os componentes no namespace `contrib` usando o seguinte comando:
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> Registrar a imagem

Após criar a configuração da imagem, você poderá usá-la para registrar uma imagem. Essa imagem é armazenada no registro de contêiner para o workspace. Uma vez criada, será possível implantar a mesma imagem em vários serviços.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage",
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Tempo estimado**: Aproximadamente 3 minutos.

Quando você registra várias imagens com o mesmo nome, o controle de versão das imagens é automático. Por exemplo, para a primeira imagem registrada como `myimage` é atribuída uma ID de `myimage:1`. Na próxima vez que você registrar uma imagem como `myimage`, a ID da nova imagem será `myimage:2`.

Para obter mais informações, consulte a documentação de referência da [classe ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="deploy"></a> Implantar como um serviço web

Ao obter a implantação, o processo será ligeiramente diferente dependendo do destino de computação da implantação. Use as informações nas seções a seguir para saber como implantar para:

| Destino de computação | Tipo de implantação | DESCRIÇÃO |
| ----- | ----- | ----- |
| [AKS (Serviço de Kubernetes do Azure)](#aks) | Serviço Web (inferência de tipos em tempo real)| Ideal para implantações de produção em grande escala. Fornece o dimensionamento automático e tempo de resposta rápido. |
| [Computação do ML do Azure](#azuremlcompute) | Serviço Web (inferência de tipos de lote)| Execute a previsão em lotes em computação sem servidor. Dá suporte a VMs normais e de baixa prioridade. |
| [ACI (Instâncias de Contêiner do Azure)](#aci) | Serviço Web (desenvolvimento/teste)| Bom para teste ou desenvolvimento. **Não é adequado para cargas de trabalho de produção.** |
| [Azure IoT Edge](#iotedge) | (Visualização) Módulo do IoT | Implante modelos em dispositivos do IoT. A inferência acontece no dispositivo. |
| [FPGA (matriz de porta programável no campo)](#fpga) | (Visualização) Serviço Web | Latência extremamente baixa para inferência em tempo real. |

> [!IMPORTANT]
> O CORS (compartilhamento de recurso entre origens) atualmente não é compatível com a ação de implantar um modelo como um serviço Web.

Os exemplos desta seção usam [deploy_from_image](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), que requer que você registre o modelo e a imagem antes de fazer uma implantação. Para obter mais informações sobre outros métodos de implantação, consulte [implante](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) e [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).

### <a id="aci"></a> Implantar instâncias de contêiner do Azure (desenvolvimento/teste)

Use as Instâncias de Contêiner do Azure para implantar os modelos como um serviço Web, se uma ou mais das seguintes condições forem verdadeiras:

- Você precisa implantar rapidamente e validar o modelo. A implantação da ACI termina em menos de cinco minutos.
- Você está testando um modelo ainda em desenvolvimento. Para ver a disponibilidade de região e cotas para ACI, consulte o documento [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

Para implantar nas Instâncias de Contêiner do Azure, use as etapas a seguir:

1. Defina a configuração de implantação. Essa configuração depende dos requisitos do seu modelo. O exemplo a seguir define uma configuração que usa um núcleo da CPU e 1 GB de memória:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. Para implantar a imagem criada na seção [Criar a imagem](#createimage) deste documento, use o código a seguir:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Tempo estimado**: Aproximadamente 5 minutos.

Para obter mais informações, consulte a documentação de referência para as classes [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a> Implantar o serviço de Kubernetes do Azure (produção)

Para implantar o modelo como um serviço Web de produção em larga escala, use o AKS (Serviço de Kubernetes do Azure). Você pode usar um cluster AKS existente ou criar outro usando o SDK do Azure Machine Learning, a CLI ou o portal do Azure.

A criação de um cluster AKS é um processo único em seu espaço de trabalho. Você pode reutilizar esse cluster para várias implantações.

> [!IMPORTANT]
> Se você excluir o cluster, será necessário criar outro cluster na próxima vez que precisar implantar.

O Serviço de Kubernetes do Azure fornece os seguintes recursos:

* Dimensionamento automático
* Registro em log
* Coleta de dados de modelo
* Tempo de resposta rápido para seus serviços Web
* Encerramento de TLS
* Authentication

#### <a name="autoscaling"></a>Dimensionamento automático

Dimensionamento automático pode ser controlado definindo `autoscale_target_utilization`, `autoscale_min_replicas`, e `autoscale_max_replicas` para o AKS, serviço da web. O exemplo a seguir demonstra como habilitar o dimensionamento automático:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Decisões para escalar verticalmente baseia-se a utilização das réplicas de contêiner atual. O número de réplicas que estão ocupados (uma solicitação de processamento) dividido pelo total de número de réplicas atuais é a utilização atual. Se esse número exceder a utilização de destino, mais réplicas são criadas. Se ele for menor, as réplicas são reduzidas. Por padrão, a utilização de destino é 70%.

Decisões de adicionar réplicas são feitas e implementadas rapidamente (cerca de 1 segundo). Decisões para remover réplicas levar mais tempo (aproximadamente 1 minuto). Esse comportamento mantém ociosas réplicas em torno de um minuto no caso de novas solicitações forem recebidas que podem manipular.

Você pode calcular as réplicas necessárias usando o código a seguir:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

Para obter mais informações sobre a configuração `autoscale_target_utilization`, `autoscale_max_replicas`, e `autoscale_min_replicas`, consulte o [AksWebservice.deploy_configuration](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) referência.

#### <a name="create-a-new-cluster"></a>Crie um novo cluster

Para criar um novo cluster do Serviço de Kubernetes do Azure, use o seguinte código:

> [!IMPORTANT]
> Criar o cluster AKS é um processo único para o seu workspace. Uma vez criado, você pode reutilizar este cluster para várias implantações. Se você excluir o cluster ou o grupo de recursos que o contém, será necessário criar outro cluster na próxima vez que precisar implantar.
> Para [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se você escolher valores personalizados para agent_count e vm_size, será necessário certificar-se de que agent_count multiplicado por vm_size será maior ou igual a 12 CPUs virtuais. Por exemplo, se você usar um vm_size de "Standard_D3_v2", que tenha 4 CPUs virtuais, será necessário escolher um agent_count de 3 ou maior.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**Tempo estimado**: Aproximadamente 20 minutos.

#### <a name="use-an-existing-cluster"></a>Usar um cluster existente

Se você já tiver o cluster do AKS em sua assinatura do Azure, e é a versão 1.12. # # e tiver pelo menos 12 CPUs virtuais, você pode usá-lo para implantar sua imagem. O código a seguir demonstra como anexar um 1.12 existente do AKS. # # cluster ao espaço de trabalho:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**Tempo estimado**: Aproximadamente 3 minutos.

Para obter mais informações sobre como criar um cluster do AKS fora do SDK do Azure Machine Learning, consulte os seguintes artigos:

* [Criar um cluster AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fen-us%2Fazure%2Faks%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Criar um cluster do AKS (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

#### <a name="deploy-the-image"></a>Implantar a imagem

Para implantar a imagem criada na seção [Criar a imagem](#createimage) deste documento no cluster do Serviço de Kubernetes do Azure, use o código a seguir:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws,
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Tempo estimado**: Aproximadamente 3 minutos.

Para obter mais informações, consulte a documentação de referência para as classes [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py).

### <a id="azuremlcompute"></a> Inferência de computação do ML do Azure

Destinos de computação do ML do Azure são criados e gerenciados pelo serviço de Azure Machine Learning. Eles podem ser usados para previsão de lote de Pipelines do ML do Azure.

Para obter uma explicação de inferência de lote com a computação do ML do Azure, leia as [como executar previsões de lote](how-to-run-batch-predictions.md) documento.


### <a id="fpga"></a> Implantar para FPGA (matriz de porta programável no campo)

O projeto Brainwave torna possível obter latência extremamente baixa para solicitações de inferência em tempo real. O Project Brainwave acelera as DNN (redes neurais profundas) implantadas em matrizes de portas programáveis em campo na nuvem do Azure. As DNNs mais usadas estão disponíveis como recursos para aprendizados de transferência ou personalizável com pesos treinados de seus próprios dados.

Para obter um passo a passo de implantação de um modelo usando o Projeto Brainwave, consulte o documento [Implantar para FPGA](how-to-deploy-fpga-web-service.md).

## <a name="define-schema"></a>Definir o esquema

Os decoradores personalizados podem ser usados para [OpenAPI](https://swagger.io/docs/specification/about/) geração de especificação e entrada do tipo de manipulação ao implantar o serviço web. No `score.py` arquivo, você fornece um exemplo de entrada e/ou à saída no construtor para um dos objetos de tipo definido, e o tipo e o exemplo são usados para criar automaticamente o esquema. Atualmente, há suporte para os seguintes tipos:

* `pandas`
* `numpy`
* `pyspark`
* standard Python

Primeiro verifique se as dependências necessárias para o `inference-schema` pacote estão incluídos no seu `env.yml` arquivo do ambiente conda. Este exemplo usa o `numpy` tipo de parâmetro para o esquema, portanto, o pip extra `[numpy-support]` também é instalado.

```python
%%writefile myenv.yml
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

Em seguida, modifique a `score.py` arquivo para importar o `inference-schema` pacotes. Definir a entrada e formatos de exemplo na saída de `input_sample` e `output_sample` variáveis que representam os formatos de solicitação e resposta para o serviço web. Usar esses exemplos na entrada e saída decoradores de função no `run()` função.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)


input_sample = np.array([[1.8]])
output_sample = np.array([43638.88])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

Depois de seguir a imagem normal da web e registro do serviço processo de implantação com atualizada `score.py` de arquivos, recuperar o uri do Swagger do serviço. Solicitar esse uri retornará o `swagger.json` arquivo.

```python
service.wait_for_deployment(show_output=True)
print(service.swagger_uri)
```



Quando você criar uma nova imagem, deve atualizar manualmente cada serviço que a usará. Para atualizar o serviço Web, use o método `update`. O código a seguir demonstra como atualizar o serviço Web para usar uma nova imagem:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Para obter mais informações, confira a documentação de referência da classe [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a name="test-web-service-deployments"></a>Implantações de serviço web de teste

Para testar uma implantação do serviço Web, você pode usar o método `run` do objeto Webservice. No exemplo a seguir, um documento JSON é configurado para um serviço Web e o resultado é exibido. Os dados enviados devem corresponder ao que o modelo espera. Neste exemplo, o formato de dados corresponde à entrada esperada pelo modelo de diabetes.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

O serviço Web é uma API REST, portanto, você pode criar aplicativos de cliente em uma variedade de linguagens de programação. Para obter mais informações, confira [Criar aplicativos cliente para consumir serviços Web](how-to-consume-web-service.md).

## <a id="update"></a> Atualizar o serviço Web

Quando você criar uma nova imagem, deve atualizar manualmente cada serviço que a usará. Para atualizar o serviço Web, use o método `update`. O código a seguir demonstra como atualizar o serviço Web para usar uma nova imagem:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Para obter mais informações, confira a documentação de referência da classe [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a id="iotedge"></a> Implantar para Azure IoT Edge

Um dispositivo Azure IoT Edge é um dispositivo baseado em Linux ou Windows que executa o tempo de execução do Azure IoT Edge. Usando o IoT Hub do Azure, você pode implantar modelos de aprendizado de máquina para esses dispositivos como módulos do IoT Edge. A implantação de um modelo em um dispositivo IoT Edge permite que o dispositivo use o modelo diretamente, em vez de precisar enviar dados para a nuvem para processamento. Você obtém tempos de resposta mais rápidos e menos transferência de dados.

Os módulos do Azure IoT Edge são implantados no dispositivo a partir de um registro de contêiner. Ao criar uma imagem a partir do modelo, a imagem é armazenada no registro de contêiner do workspace.

> [!IMPORTANT]
> As informações nesta seção pressupõem que você já esteja familiarizado com módulos de IoT Hub do Azure e o Azure IoT Edge. Embora algumas das informações desta seção é específico ao serviço de Azure Machine Learning, a maior parte do processo de implantar em um dispositivo de borda acontece no serviço do IoT do Azure.
>
> Se você estiver familiarizado com o Azure IoT, consulte [conceitos básicos do Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/) e [do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) para obter informações básicas. Em seguida, use os outros links nesta seção para saber mais sobre operações específicas.

### <a name="set-up-your-environment"></a>Configure seu ambiente

* Um ambiente de desenvolvimento. Para obter mais informações, consulte o [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md) documento.

* Uma [IoT Hub do Azure](../../iot-hub/iot-hub-create-through-portal.md) na sua assinatura do Azure.

* Um modelo treinado. Para obter um exemplo de como treinar um modelo, consulte o documento [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md). Um modelo pré-treinado está disponível no [AI Toolkit para o repositório GitHub do Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

### <a id="getcontainer"></a> Obter credenciais de registro de contêiner

Para implantar um módulo do IoT Edge no dispositivo, o Azure IoT precisa das credenciais de registro de contêiner, nas quais o serviço do Azure Machine Learning armazena imagens do docker.

Você pode obter as credenciais de duas maneiras:

+ **No portal do Azure**:

  1. Entre no [Portal do Azure](https://portal.azure.com/signin/index).

  1. Vá para o espaço de trabalho do serviço de Aprendizagem no Computador do Azure e selecione __Visão Geral__. Para ir para as configurações do registro de contêiner, selecione o link __registro__.

     ![Uma imagem da entrada do registro de contêiner](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. Uma vez no registro do contêiner, selecione **Access Keys** e, em seguida, ative o usuário admin.

     ![Uma imagem da tela de chaves de acesso](./media/how-to-deploy-and-where/findaccesskey.png)

  1. Salve os valores para **servidor de logon**, **nome de usuário** e **senha**.

+ **Com um script do Python**:

  1. Use o seguinte script do Python após o código executado acima para criar um contêiner:

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. Salve os valores de ContainerURL, nome do servidor, nome de usuário e senha.

     Essas credenciais são necessárias para fornecer o acesso do dispositivo do IoT Edge às imagens no registro de contêiner particular.

### <a name="prepare-the-iot-device"></a>Preparar o dispositivo IoT

Registrar seu dispositivo no IoT Hub do Azure e, em seguida, instale o tempo de execução do IoT Edge no dispositivo. Se você não estiver familiarizado com esse processo, consulte [guia de início rápido: Implantar seu primeiro módulo IoT Edge em um dispositivo de Linux x64](../../iot-edge/quickstart-linux.md).

Outros métodos de registro de um dispositivo são:

* [Portal do Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)
* [CLI do Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-cli)
* [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-vscode)

### <a name="deploy-the-model-to-the-device"></a>Implantar o modelo no dispositivo

Para implantar o modelo para o dispositivo, use as informações de registro coletadas a [obter credenciais de registro de contêiner](#getcontainer) etapas de seção com a implantação de módulo para módulos do IoT Edge. Por exemplo, quando [módulos de implantação do Azure IoT Edge do portal do Azure](../../iot-edge/how-to-deploy-modules-portal.md), você deve configurar o __configurações do registro__ para o dispositivo. Use o __servidor de logon__, __nome de usuário__, e __senha__ para seu registro de contêiner do espaço de trabalho.

Você também pode implantar usando [CLI do Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) e [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-vscode).

## <a name="clean-up"></a>Limpar

Para excluir um serviço Web implantado, use `service.delete()`.

Para excluir uma imagem, use `image.delete()`.

Para excluir um modelo registrado, use `model.delete()`.

Para obter mais informações, confira a documentação de referência para [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--) e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Proteger serviços Web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Como executar previsões em lotes](how-to-run-batch-predictions.md)
* [Monitorar seus modelos do Azure Machine Learning com o Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [SDK do Serviço do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Usar o Serviço do Azure Machine Learning com redes virtuais do Azure](how-to-enable-virtual-network.md)
* [Melhores práticas para criar sistemas de recomendação](https://github.com/Microsoft/Recommenders)
* [Compilar uma API de recomendação em tempo real no Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
