---
title: Implantar um modelo para inferência com GPU
titleSuffix: Azure Machine Learning service
description: Este artigo ensina como usar o serviço de Azure Machine Learning para implantar um modelo de aprendizado profundo Tensorflow habilitado para GPU como um serviço Web. solicitações de inferência de serviço e pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 07/24/2019
ms.openlocfilehash: 520e7fe953256e4c489e4c540493d9f74dda3aef
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494347"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Implantar um modelo de aprendizado profundo para inferência com GPU

Este artigo ensina como usar o serviço de Azure Machine Learning para implantar um modelo habilitado para GPU como um serviço Web. As informações neste artigo se baseiam na implantação de um modelo no serviço de kubernetes do Azure (AKS). O cluster AKS fornece um recurso de GPU que é usado pelo modelo para inferência.

A inferência, ou a Pontuação do modelo, é a fase em que o modelo implantado é usado para fazer previsões. O uso de GPUs em vez de CPUs oferece vantagens de desempenho na computação altamente paralelizáveis.

> [!TIP]
> Embora os trechos de código neste artigo usee um modelo TensorFlow, você pode aplicar as informações a qualquer estrutura de Machine Learning que ofereça suporte a GPUs.

## <a name="prerequisites"></a>Pré-requisitos

* Um workspace de serviço do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho de serviço](setup-create-workspace.md).

* Um ambiente de desenvolvimento do Python com o SDK do Azure Machine Learning instalado. Para obter mais informações, consulte a seção [SDK do Python](setup-create-workspace.md#sdk) do artigo criar um espaço de trabalho.

* Um modelo registrado que usa uma GPU.

    * Para saber como registrar modelos, consulte [implantar modelos](../service/how-to-deploy-and-where.md#registermodel).

    * Para criar e registrar o modelo de Tensorflow usado para criar este documento, consulte [como treinar um modelo de Tensorflow](how-to-train-tensorflow.md).

* Uma compreensão geral de [como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Conectar-se ao seu espaço de trabalho

Para se conectar a um espaço de trabalho existente, use o seguinte código:

> [!IMPORTANT]
> Esse trecho de código espera que a configuração do espaço de trabalho seja salva no diretório atual ou em seu pai. Para obter mais informações sobre como criar um espaço de trabalho e salvar a configuração no arquivo, consulte [criar um Azure Machine Learning espaço de trabalho de serviço](setup-create-workspace.md).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Criar um cluster kubernetes com GPUs

O serviço kubernetes do Azure fornece várias opções de GPU diferentes. Você pode usar qualquer um deles para a inferência de modelo. Consulte [a lista de VMs da série N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) para obter uma análise completa dos recursos e dos custos.

O código a seguir demonstra como criar um novo cluster AKS para seu espaço de trabalho:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> O Azure cobrará você desde que o cluster AKS exista. Certifique-se de excluir o cluster AKS quando tiver terminado.

Para obter mais informações sobre como usar o serviço kubernetes do Azure com o serviço Azure Machine Learning, consulte [como implantar e onde](how-to-deploy-and-where.md#deploy-aks).

## <a name="write-the-entry-script"></a>Gravar o script de entrada

O script de entrada recebe dados enviados para o serviço Web, passa-os para o modelo e retorna os resultados da pontuação. O script a seguir carrega o modelo Tensorflow na inicialização e, em seguida, usa o modelo para pontuar dados.

> [!TIP]
> O script de entrada é específico para seu modelo. Por exemplo, o script deve saber a estrutura a ser usada com seu modelo, formatos de dados, etc.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Esse arquivo é nomeado `score.py`. Para obter mais informações sobre scripts de entrada, consulte [como e onde implantar](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Definir o ambiente Conda

O arquivo de ambiente Conda especifica as dependências para o serviço. Ele inclui dependências exigidas pelo modelo e pelo script de entrada. O YAML a seguir define o ambiente para um modelo de Tensorflow. Ele especifica `tensorflow-gpu`, que fará uso da GPU usada nesta implantação:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

Para este exemplo, o arquivo é salvo como `myenv.yml`.

## <a name="define-the-deployment-configuration"></a>Definir a configuração de implantação

A configuração de implantação define o ambiente do serviço kubernetes do Azure usado para executar o serviço Web:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Para obter mais informações, consulte a documentação de referência para [AksService. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-).

## <a name="define-the-inference-configuration"></a>Definir a configuração de inferência

A configuração de inferência aponta para o script de entrada e o arquivo de ambiente Conda. Ele também habilita o suporte a GPU, que instala o CUDA na imagem do Docker criada para o serviço Web:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml",
                                   enable_gpu=True)
```

Para obter mais informações, consulte a documentação de referência para [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

## <a name="deploy-the-model"></a>Implantar o modelo

Implante o modelo em seu cluster AKS e aguarde até que ele crie seu serviço.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

> [!NOTE]
> Se o `InferenceConfig` objeto tiver `enable_gpu=True`, o `deployment_target` parâmetro deverá fazer referência a um cluster que fornece uma GPU. Caso contrário, a implantação falhará.

Para obter mais informações, consulte a documentação de referência para o [modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Emitir uma consulta de exemplo para seu serviço

Envie uma consulta de teste para o modelo implantado. Quando você envia uma imagem JPEG para o modelo, ela classifica a imagem. O exemplo de código a seguir baixa dados de teste e, em seguida, seleciona uma imagem de teste aleatória para enviar ao serviço. 

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Para obter mais informações sobre como criar um aplicativo cliente, consulte [criar cliente para consumir o serviço Web](how-to-consume-web-service.md)implantado.

## <a name="clean-up-the-resources"></a>Limpar os recursos

Se você criou o cluster AKS especificamente para este exemplo, exclua seus recursos depois de terminar.

> [!IMPORTANT]
> O Azure cobra com base em quanto tempo o cluster AKS é implantado. Certifique-se de limpá-lo depois de concluir a tarefa.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Próximas etapas

* [Implantar modelo em FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Implantar modelo com ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Treinar modelos do Tensorflow DNN](../service/how-to-train-tensorflow.md)
