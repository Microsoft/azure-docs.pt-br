---
title: Implantar modelos em FPGAs
titleSuffix: Azure Machine Learning service
description: Saiba como implantar um serviço Web com um modelo em execução em um FPGA com o serviço do Azure Machine Learning para inferência de latência ultrabaixa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 1/29/2019
ms.custom: seodec18
ms.openlocfilehash: 7aa0e11ed47219829830369d17b300270d3fbffb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819434"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Implantar um modelo como um serviço Web em uma FPGA com o serviço do Azure Machine Learning

Você pode implantar um modelo como um serviço web em [campo matrizes de portões programáveis (FPGAs)](concept-accelerate-with-fpgas.md).  O uso de FPGAs fornece inferência de latência extremamente baixa, mesmo com um tamanho de lote único.  Estes modelos estão disponíveis no momento:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16   

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Teste hoje mesmo a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree).

- Um espaço de trabalho do serviço do Machine Learning do Azure e o SDK do Azure Machine Learning para Python instalados. Aprenda como obter esses pré-requisitos usando o documento [Como configurar um ambiente de desenvolvimento](how-to-configure-environment.md).
 
  - Seu workspace precisa estar na região *East US 2*.

  - Instale os extras contrib:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```

  - Atualmente, há suporte apenas para o TensorFlow versão<=1.10, assim, instale-o depois de concluir todas as outras instalações:

    ```shell
    pip install "tensorflow==1.10"
    ```

## <a name="create-and-deploy-your-model"></a>Criar e implantar seu modelo
Criar um pipeline para a imagem de entrada de pré-processamento, torná-lo um recurso usando ResNet 50 em um FPGA e, em seguida, executar os recursos por meio de um classificador treinado no conjunto de dados ImageNet.

Siga as instruções para:

* Define o modelo pipeline
* Implantar o modelo
* Consumir o modelo implantado
* Excluir serviços implantados

> [!IMPORTANT]
> Para otimizar a latência e a taxa de transferência, seu cliente deve estar na mesma região do Azure que o ponto de extremidade.  Atualmente, as APIs são criadas na região do Azure Leste dos EUA.



### <a name="preprocess-image"></a>Imagem de pré-processamento
O primeiro estágio do pipeline é pré-processar as imagens.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>Adicionar recurso
Inicialize o modelo e baixe um ponto de verificação TensorFlow da versão quantizada do ResNet50 para ser usado como um featurizer.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Adicionar classificação
Este classificador foi treinado no conjunto de dados do ImageNet.

```python
classifier_output = model.get_default_classifier(feature_tensor)
```

### <a name="create-service-definition"></a>Criar definição de serviço
Agora que você definiu o pré-processamento, o recurso e o classificador de imagens que são executados no serviço, você pode criar uma definição de serviço. A definição de serviço é um conjunto de arquivos gerados a partir do modelo que é implantado no serviço FPGA. A definição de serviço consiste em um pipeline. O pipeline é uma série de estágios que são executados em ordem.  Estágios TensorFlow, estágios Keras e estágios BrainWave são suportados.  Os estágios são executados em ordem no serviço, com a saída de cada entrada de estágio tornando-se o estágio subsequente.

Para criar um estágio do TensorFlow, especifique uma sessão contendo o gráfico (neste caso, o gráfico padrão é usado) e os tensores de entrada e saída para este estágio.  Essas informações são usadas para salvar o gráfico para que ele possa ser executado no serviço.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'model_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, feature_tensor, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Implantar modelo
Crie um serviço da definição de serviço.  Seu workspace precisa estar no local Leste dos EUA 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(True)
```

### <a name="test-the-service"></a>Teste o serviço
Para enviar uma imagem para a API e testar a resposta, inclua um mapeamento do ID da classe de saída no nome da classe ImageNet.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Ligue para o seu serviço e substitua o nome do arquivo "sua-imagem.jpg" abaixo por uma imagem da sua máquina. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Limpeza de serviço
Exclua o serviço.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Proteger serviços web FPGA

Para obter informações sobre como proteger serviços web FPGA, confira o documento [Proteger serviços web](how-to-secure-web-service.md).


## <a name="next-steps"></a>Próximas etapas

Saiba como [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md).
