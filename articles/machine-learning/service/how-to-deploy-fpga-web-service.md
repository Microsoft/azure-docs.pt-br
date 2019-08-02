---
title: Implantar modelos em FPGAs
titleSuffix: Azure Machine Learning service
description: Saiba como implantar um serviço Web com um modelo em execução em um FPGA com serviço de Azure Machine Learning para inferência de latência extremamente baixa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 07/25/2019
ms.custom: seodec18
ms.openlocfilehash: cec1a74938690a4f781ea7850fdd6d649550b3eb
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494924"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Implantar um modelo como um serviço Web em uma FPGA com o serviço do Azure Machine Learning

Você pode implantar um modelo como um serviço Web no [campo FPGAs (matrizes de porta programável)](concept-accelerate-with-fpgas.md) com Azure Machine Learning modelos de aceleração de hardware. O uso de FPGAs fornece inferência de latência ultra baixa, mesmo com um único tamanho de lote. A inferência, ou a Pontuação do modelo, é a fase em que o modelo implantado é usado para previsão, mais comumente em dados de produção.

Estes modelos estão disponíveis no momento:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGAs estão disponíveis nessas regiões do Azure:
  - East US
  - Sudeste da Ásia
  - Europa Ocidental
  - Oeste dos EUA 2

> [!IMPORTANT]
> Para otimizar a latência e a taxa de transferência, o cliente que está enviando dados para o modelo FPGA deve estar em uma das regiões acima (aquela em que você implantou o modelo).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.  Se você não tiver uma, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

- Cota de FPGA. Use o CLI do Azure para verificar se você tem cota:

    ```shell
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Os outros locais possíveis são ``southeastasia``, ``westeurope``e ``westus2``.

    O comando retorna um texto semelhante ao seguinte:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Verifique se você tem pelo menos 6 vCPUs em __CurrentValue__.

    Se você não tiver cota, envie uma solicitação em [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI).

- Um espaço de trabalho do serviço do Machine Learning do Azure e o SDK do Azure Machine Learning para Python instalados. Para obter mais informações, consulte [criar um espaço de trabalho](setup-create-workspace.md).
 
- O SDK do Python para modelos com aceleração de hardware:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Notebooks de exemplo

Para sua conveniência, os notebooks de [exemplo](https://aka.ms/aml-accel-models-notebooks) estão disponíveis para o exemplo abaixo e outros exemplos.

## <a name="create-and-containerize-your-model"></a>Criar e colocar seu modelo em contêiner

Este documento descreverá como criar um grafo TensorFlow para pré-processar a imagem de entrada, torná-la um featurizer usando ResNet 50 em um FPGA e, em seguida, executar os recursos por meio de um classificador treinado no conjunto de dados ImageNet.

Siga as instruções para:

* Definir o modelo TensorFlow
* Converter o modelo
* Implantar o modelo
* Consumir o modelo implantado
* Excluir serviços implantados

### <a name="load-azure-ml-workspace"></a>Carregar espaço de trabalho do Azure ML

Carregue seu espaço de trabalho do Azure ML.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Imagem de pré-processamento

A entrada para o serviço Web é uma imagem JPEG.  A primeira etapa é decodificar a imagem JPEG e pré-processar-la.  As imagens JPEG são tratadas como cadeias de caracteres e o resultado são os tempos que serão a entrada para o modelo ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Carregar featurizer

Inicialize o modelo e baixe um ponto de verificação TensorFlow da versão quantizada do ResNet50 para ser usado como um featurizer.  Você pode substituir "QuantizedResnet50" no trecho de código abaixo pelo importando outras redes neurais profundas:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Adicionar classificador

Este classificador foi treinado no conjunto de dados do ImageNet.  Exemplos de aprendizado de transferência e treinamento de seus pesos personalizados estão disponíveis no conjunto de [blocos de anotações de exemplo](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Salvar o modelo

Agora que o pré-processador, ResNet 50 featurizer e o classificador foram carregados, salve o grafo e as variáveis associadas como um modelo.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                               inputs={'images': in_images},
                               outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Economizar tempos de entrada e de saída
Os tempos de entrada e de saída que foram criados durante o pré-processamento e as etapas do classificador serão necessários para a conversão e a inferência de modelos.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Salve os tempos de entrada e saída, pois você precisará deles para a conversão de modelo e solicitações de inferência.

Os modelos disponíveis e os tempos de saída do classificador padrão correspondentes estão abaixo, que é o que você usaria para a inferência se você usou o classificador padrão.

+ Resnet50, QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Registrar modelo

[Registre](./concept-model-management-and-deployment.md) o modelo que você criou.  Adicionar marcas e outros metadados sobre o modelo ajuda você a controlar seus modelos treinados.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Se você já registrou um modelo e deseja carregá-lo, você pode recuperá-lo.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Converter modelo

Converta o grafo TensorFlow para o formato de troca de rede neural aberto ([ONNX](https://onnx.ai/)).  Você precisará fornecer os nomes dos tempos de entrada e de saída, e esses nomes serão usados pelo cliente quando você consumir o serviço Web.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Criar imagem do Docker

O modelo convertido e todas as dependências são adicionados a uma imagem do Docker.  Essa imagem do Docker pode ser implantada e instanciada.  Os destinos de implantação com suporte incluem AKS na nuvem ou em um dispositivo de borda, como [Azure data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Você também pode adicionar marcas e descrições para sua imagem do Docker registrada.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

Liste as imagens por marca e obtenha os logs detalhados para qualquer depuração.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>Implantação de modelo

### <a name="deploy-to-the-cloud"></a>Implantar na nuvem

Para implantar o modelo como um serviço Web de produção em larga escala, use o AKS (Serviço de Kubernetes do Azure). Você pode criar um novo usando o SDK do Azure Machine Learning, a CLI ou a portal do Azure.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_PB6s",
                                                    agent_count=1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

A implantação do AKS pode levar cerca de 15 minutos.  Verifique se a implantação foi bem-sucedida.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Implante o contêiner no cluster AKS.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>Testar o serviço de nuvem
A imagem do Docker dá suporte a gRPC e TensorFlow que atende à API "Predict".  Use o cliente de exemplo para chamar a imagem do Docker para obter previsões do modelo.  O código de cliente de exemplo está disponível:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Se você quiser usar o TensorFlow servindo, você pode [baixar um cliente de exemplo](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Como esse classificador foi treinado no conjunto de dados [ImageNet](http://www.image-net.org/) , mapeie as classes para rótulos legíveis.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Limpar o serviço
Exclua o serviço Web, a imagem e o modelo (deve ser feito nesta ordem, pois há dependências).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Implantar em um servidor de borda local

Todos [os dispositivos](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) Azure data Box Edge contêm um FPGA para executar o modelo.  Somente um modelo pode ser executado em FPGA ao mesmo tempo.  Para executar um modelo diferente, basta implantar um novo contêiner. As instruções e o código de exemplo podem ser encontrados neste [exemplo do Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Proteger serviços web FPGA

Para obter informações sobre como proteger serviços web FPGA, confira o documento [Proteger serviços web](how-to-secure-web-service.md).

## <a name="pbs-family-vms"></a>VMs da família PBS

A família PBS de VMs do Azure contém o Intel Arria 10 FPGAs.  Ele será mostrado como "Standard PBS Family vCPUs" quando você verificar sua alocação de cota do Azure.  A VM PB6 tem seis vCPUs e uma FPGA, e ela será automaticamente provisionada pelo Azure ML como parte da implantação de um modelo em um FPGA.  Ele só é usado com o Azure ML e não pode executar fragmentado arbitrários.  Por exemplo, você não poderá atualizar o FPGA com fragmentado para fazer criptografia, codificação, etc. 
