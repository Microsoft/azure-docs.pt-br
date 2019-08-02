---
title: Habilitar o registro em log no Serviço do Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Saiba como habilitar o registro em log no Serviço do Azure Machine Learning usando tanto o pacote de log do Python padrão quanto a funcionalidade específica do SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 07/12/2019
ms.openlocfilehash: 384cd2df9c629a73095cc55f4f6d65aa6a727225
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360970"
---
# <a name="enable-logging-in-azure-machine-learning-service"></a>Habilitar o registro em log no Serviço do Azure Machine Learning

O SDK do Python do Azure Machine Learning permite que você habilite o registro em log usando o pacote de log do Python padrão e a funcionalidade específica do SDK, tanto para registro em log local quanto para log no workspace no portal. Os logs fornecem aos desenvolvedores informações em tempo real sobre o estado do aplicativo e podem auxiliar no diagnóstico de erros ou avisos. Neste artigo, você aprenderá maneiras diferentes de habilitar registro em log nas seguintes áreas:

> [!div class="checklist"]
> * Modelos de treinamento e destinos de computação
> * Criação de imagem
> * Modelos implantados
> * Configurações do Python `logging`

[Criar um workspace do Serviço do Azure Machine Learning](setup-create-workspace.md). Use o [guia](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para obter mais informações sobre o SDK.

## <a name="training-models-and-compute-target-logging"></a>Modelos de treinamento e registro em log de destino de computação

Há várias maneiras de habilitar o registro em log durante o processo de treinamento do modelo e os exemplos mostrados ilustrarão os padrões de design comuns. É possível registrar os dados relacionados à execução facilmente no workspace na nuvem, usando a função `start_logging` na classe `Experiment`.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Consulte a documentação de referência para a classe [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) para funções de log adicionais.

Para habilitar o registro em log local do estado do aplicativo durante o andamento do treinamento, use o parâmetro `show_output`. Ao habilitar o registro em log detalhado, será possível visualizar os detalhes do processo de treinamento e as informações sobre quaisquer recursos remotos ou destinos de computação. Use o código a seguir para habilitar registro em log no envio do experimento.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

É possível usar o mesmo parâmetro na função `wait_for_completion` na execução resultante.

```python
run.wait_for_completion(show_output=True)
```

Além disso, o SDK dá suporte para usar o pacote de log do Python padrão em determinados cenários para treinamento. O exemplo a seguir habilita um nível de log de `INFO` em um objeto `AutoMLConfig`.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task='regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Você também pode usar o parâmetro `show_output` ao criar um destino de computação persistente. Especifique o parâmetro na função `wait_for_completion` para habilitar o registro durante a criação do destino de computação.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Registro em log durante a criação da imagem

Habilitar o registro em log durante a criação da imagem permitirá que você visualize erros durante o processo de criação. Defina o parâmetro `show_output` na função `wait_for_deployment()`.

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                       image=image,
                                       name="example-image",
                                       workspace=ws
                                       )

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Registro em log de modelos implantados

Para recuperar logs de um serviço Web implantado anteriormente, carregue o serviço e use a função `get_logs()`. Os logs podem conter informações detalhadas sobre quaisquer erros ocorridos durante a implantação.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Do mesmo modo, é possível registrar rastreamentos de pilha personalizados para o serviço Web, habilitando o Application Insights que permite monitorar tempos de resposta/solicitação, taxas de falhas e exceções. Chame a função `update()` em um serviço Web existente para habilitar o Application Insights.

```python
service.update(enable_app_insights=True)
```

Consulte as [instruções](how-to-enable-app-insights.md) para obter mais informações sobre como trabalhar com o Application Insights no portal do Azure.

## <a name="python-native-logging-settings"></a>Configurações de registro em log nativo do Python

Determinados logs no SDK poderão conter um erro que irá instrui-lo a definir o nível de log como DEBUG. Para definir o nível de log, adicione o seguinte código ao script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```
