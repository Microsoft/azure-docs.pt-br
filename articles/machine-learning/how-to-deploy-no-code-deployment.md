---
title: Nenhuma implantação de código (versão prévia)
titleSuffix: Azure Machine Learning
description: Nenhuma implantação de código permite implantar um modelo como um serviço Web sem a necessidade de criar manualmente um script de entrada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: deploy
ms.reviewer: larryfr
ms.openlocfilehash: a17126695aa5138d1df7fd17cfaa2f5f75ad1004
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93324920"
---
# <a name="preview-no-code-model-deployment"></a>Apresentação Implantação de modelo sem código

No momento, a implantação de modelo sem código está em versão prévia e dá suporte às seguintes estruturas de Machine Learning:

## <a name="tensorflow-savedmodel-format"></a>TensorFlow SavedModel Format
Os modelos TensorFlow precisam ser registrados no **formato SavedModel** para trabalhar com a implantação de modelo sem código.

Consulte [este link](https://www.tensorflow.org/guide/saved_model) para obter informações sobre como criar um SavedModel.

Damos suporte a qualquer versão do TensorFlow listada em "marcas" no [TensorFlow que está servindo DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags).

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>Modelos ONNX

O registro e a implantação do modelo de ONNX têm suporte para qualquer grafo de inferência ONNX. Atualmente, não há suporte para as etapas de pré-processamento e postprocess.

Aqui está um exemplo de como registrar e implantar um modelo MNIST ONNX:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Para pontuar um modelo, consulte [consumir um modelo de Azure Machine Learning implantado como um serviço Web](./how-to-consume-web-service.md). Muitos projetos de ONNX usam arquivos protobuf para armazenar compactar dados de treinamento e validação, o que pode dificultar o conhecimento do formato de dados esperado pelo serviço. Como desenvolvedor de modelo, você deve documentar seus desenvolvedores:

* Formato de entrada (JSON ou binário)
* Tipo e forma de dados de entrada (por exemplo, uma matriz de floats de Shape [100,100, 3])
* Informações de domínio (por exemplo, para uma imagem, o espaço de cores, a ordem dos componentes e se os valores são normalizados)

Se você estiver usando o Pytorch, a [exportação de modelos de Pytorch para ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) terá os detalhes de conversão e limitações. 

## <a name="scikit-learn-models"></a>Modelos do Scikit-learn

Não há suporte para implantação de modelo de código para todos os tipos de modelo scikit-Learn internos.

Aqui está um exemplo de como registrar e implantar um modelo sklearn sem código extra:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> Os modelos que dão suporte ao predict_proba usarão esse método por padrão. Para substituir isso para usar Predict, você pode modificar o corpo da POSTAgem como abaixo:

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> Essas dependências estão incluídas no contêiner de inferência scikit-Learn pré-criado:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de uma implantação com falha](how-to-troubleshoot-deployment.md)
* [Implantar no Serviço de Kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicativos cliente para consumir serviços Web](how-to-consume-web-service.md)
* [Atualizar serviço Web](how-to-deploy-update-web-service.md)
* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas de eventos e gatilhos para implantações de modelo](how-to-use-event-grid.md)