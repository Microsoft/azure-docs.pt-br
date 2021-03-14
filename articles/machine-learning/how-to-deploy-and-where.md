---
title: Como implantar modelos de aprendizado de máquina
titleSuffix: Azure Machine Learning
description: Saiba como e onde implantar modelos de aprendizado de máquina. Implante em instâncias de contêiner do Azure, serviço kubernetes do Azure, Azure IoT Edge e FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 01/13/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
adobe-target: true
ms.openlocfilehash: fa68db4bd166ebe1acd1ae85fca2d7e51236a4c4
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522046"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Implantar modelos de aprendizado de máquina no Azure

Saiba como implantar o aprendizado de máquina ou o modelo de aprendizado profundo como um serviço Web na nuvem do Azure. Você também pode implantar em dispositivos Azure IoT Edge.

O fluxo de trabalho é semelhante, independentemente do local de implantação do seu modelo:

1. Registre o modelo (opcional, veja abaixo).
1. Prepare uma configuração de inferência (a menos que não use a [implantação sem código](./how-to-deploy-no-code-deployment.md)).
1. Prepare um script de entrada (a menos que use a [implantação sem código](./how-to-deploy-no-code-deployment.md)).
1. Escolha um destino de computação.
1. Implante o modelo no destino de computação.
1. Teste o serviço Web resultante.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação do Machine Learning, consulte [gerenciar, implantar e monitorar modelos com Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).
- Um modelo. Se você não tiver um modelo treinado, poderá usar os arquivos de modelo e de dependência fornecidos neste [tutorial](https://aka.ms/azml-deploy-cloud).
- A [extensão da CLI (interface de linha de comando) do Azure para o serviço de Machine Learning](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).
- Um modelo. Se você não tiver um modelo treinado, poderá usar os arquivos de modelo e de dependência fornecidos neste [tutorial](https://aka.ms/azml-deploy-cloud).
- O [Azure Machine Learning Software Development Kit (SDK) para Python](/python/api/overview/azure/ml/intro).

---

## <a name="connect-to-your-workspace"></a>Conectar-se ao workspace

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Siga as instruções na documentação do CLI do Azure para [definir seu contexto de assinatura](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Em seguida, faça o:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

para ver os espaços de trabalho aos quais você tem acesso.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Para obter mais informações sobre como usar o SDK para se conectar a um espaço de trabalho, consulte a documentação [Azure Machine Learning SDK para Python](/python/api/overview/azure/ml/intro#workspace) .


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> Registrar seu modelo (opcional)

Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo no espaço de trabalho. Depois de registrar os arquivos, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que você registrou.

> [!TIP] 
> O registro de um modelo para acompanhamento de versão é recomendado, mas não obrigatório. Se preferir sem registrar um modelo, você precisará especificar um diretório de origem em seu [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) ou [inferenceconfig.js](./reference-azure-machine-learning-cli.md#inference-configuration-schema) e garantir que seu modelo resida nesse diretório de origem.

> [!TIP]
> Ao registrar um modelo, você fornece o caminho de um local de nuvem (de uma execução de treinamento) ou um diretório local. Esse caminho é apenas para localizar os arquivos para upload como parte do processo de registro. Ele não precisa corresponder ao caminho usado no script de entrada. Para obter mais informações, consulte [Localizar arquivos de modelo em seu script de entrada](./how-to-deploy-advanced-entry-script.md#load-registered-models).

> [!IMPORTANT]
> Ao usar filtrar por `Tags` opção na página modelos do Azure Machine Learning Studio, em vez de usar `TagName : TagValue` os clientes devem usar `TagName=TagValue` (sem espaço)

Os exemplos a seguir demonstram como registrar um modelo.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrar um modelo de uma execução de treinamento do Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

O `--asset-path` parâmetro refere-se ao local da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários arquivos no registro do modelo, defina `--asset-path` para o caminho de uma pasta que contém os arquivos.

### <a name="register-a-model-from-a-local-file"></a>Registrar um modelo de um arquivo local

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Para incluir vários arquivos no registro do modelo, defina `-p` para o caminho de uma pasta que contém os arquivos.

Para obter mais informações sobre `az ml model register` o, consulte a [documentação de referência](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrar um modelo de uma execução de treinamento do Azure ML

  Ao usar o SDK para treinar um modelo, você pode receber um objeto de [execução](/python/api/azureml-core/azureml.core.run.run) ou um objeto [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) , dependendo de como você treinou o modelo. Cada objeto pode ser usado para registrar um modelo criado por uma execução de experimento.

  + Registrar um modelo de um `azureml.core.Run` objeto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    O `model_path` parâmetro refere-se ao local da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários arquivos no registro do modelo, defina `model_path` para o caminho de uma pasta que contém os arquivos. Para obter mais informações, consulte a documentação do [Run.register_model](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Registrar um modelo de um `azureml.train.automl.run.AutoMLRun` objeto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    Neste exemplo, os `metric` parâmetros e `iteration` não são especificados, portanto, a iteração com a melhor métrica primária será registrada. O `model_id` valor retornado da execução é usado em vez de um nome de modelo.

    Para obter mais informações, consulte a documentação do [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .

### <a name="register-a-model-from-a-local-file"></a>Registrar um modelo de um arquivo local

Você pode registrar um modelo fornecendo o caminho local do modelo. Você pode fornecer o caminho de uma pasta ou de um único arquivo. Você pode usar esse método para registrar modelos treinados com Azure Machine Learning e, em seguida, baixados. Você também pode usar esse método para registrar modelos treinados fora do Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Usando o SDK e o ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Para incluir vários arquivos no registro do modelo, defina `model_path` para o caminho de uma pasta que contém os arquivos.

Para obter mais informações, consulte a documentação da [classe Model](/python/api/azureml-core/azureml.core.model.model).

Para obter mais informações sobre como trabalhar com modelos treinados fora do Azure Machine Learning, consulte [como implantar um modelo existente](how-to-deploy-existing-model.md).

---

## <a name="define-an-entry-script"></a>Definir um script de entrada

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definir uma configuração de inferência


Uma configuração de inferência descreve como configurar o serviço Web que contém seu modelo. Ele é usado posteriormente, quando você implanta o modelo.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Uma configuração de inferência mínima pode ser escrita como:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir"
}
```

Isso especifica que a implantação do Machine Learning usará o arquivo `score.py` no `./working_dir` diretório para processar as solicitações de entrada.

[Consulte este artigo](./reference-azure-machine-learning-cli.md#inference-configuration-schema) para obter uma discussão mais completa sobre as configurações de inferência. 

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra:

1. carregando um [ambiente organizado](resource-curated-environments.md) do seu espaço de trabalho
1. Clonando o ambiente
1. Especificando `scikit-learn` como uma dependência.
1. Usando o ambiente para criar um InferenceConfig

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

Para obter mais informações sobre ambientes, consulte [criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md).

Para obter mais informações sobre a configuração de inferência, consulte a documentação da classe [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) .

---

> [!TIP] 
> Para obter informações sobre como usar uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Escolher um destino de computação

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definir uma configuração de implantação

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

As opções disponíveis para uma configuração de implantação diferem de acordo com o destino de computação escolhido.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Para obter mais informações, consulte [esta referência](./reference-azure-machine-learning-cli.md#deployment-configuration-schema).

# <a name="python"></a>[Python](#tab/python)

Antes de implantar seu modelo, você deve definir a configuração de implantação. *A configuração de implantação é específica para o destino de computação que hospedará o serviço Web.* Por exemplo, ao implantar um modelo localmente, você deve especificar a porta onde o serviço aceita solicitações. A configuração de implantação não faz parte do script de entrada. Ele é usado para definir as características do destino de computação que hospedará o modelo e o script de entrada.

Talvez você também precise criar o recurso de computação, se, por exemplo, você ainda não tiver uma instância do AKS (serviço kubernetes do Azure) associada ao seu espaço de trabalho.

A tabela a seguir fornece um exemplo de criação de uma configuração de implantação para cada destino de computação:

| Destino de computação | Exemplo de configuração de implantação |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Instâncias de Contêiner do Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Serviço de Kubernetes do Azure | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As classes para local, instâncias de contêiner do Azure e serviços Web AKS podem ser importadas de `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-machine-learning-model"></a>Implantar o modelo de aprendizado de máquina

Agora você está pronto para implantar seu modelo. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

### <a name="using-a-registered-model"></a>Usando um modelo registrado

Se você registrou seu modelo em seu espaço de trabalho Azure Machine Learning, substitua "MyModel: 1" pelo nome do seu modelo e seu número de versão.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Usando um modelo local

Se preferir não registrar seu modelo, você pode passar o parâmetro "sourceDirectory" no seu inferenceconfig.jspara especificar um diretório local a partir do qual servir seu modelo.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra uma implantação local. A sintaxe irá variar dependendo do destino de computação escolhido na etapa anterior.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações, consulte a documentação de [LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice), [Model. Deployment ()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [WebService](/python/api/azureml-core/azureml.core.webservice.webservice).

---

### <a name="understanding-service-state"></a>Compreendendo o estado do serviço

Durante a implantação do modelo, você pode ver a alteração do estado do serviço durante a implantação completa.

A tabela a seguir descreve os diferentes Estados de serviço:

| Estado WebService | Descrição | Estado final?
| ----- | ----- | ----- |
| Transição | O serviço está em processo de implantação. | Não |
| Unhealthy | O serviço foi implantado, mas está inacessível no momento.  | Não |
| Não agendável | O serviço não pode ser implantado no momento devido à falta de recursos. | Não |
| Com falha | O serviço falhou ao ser implantado devido a um erro ou falha. | Yes |
| Íntegros | O serviço está íntegro e o ponto de extremidade está disponível. | Yes |

> [!TIP]
> Durante a implantação, as imagens do Docker para destinos de computação são criadas e carregadas do ACR (registro de contêiner do Azure). Por padrão, Azure Machine Learning cria um ACR que usa a camada de serviço *básica* . Alterar o ACR para o seu espaço de trabalho para a camada Standard ou Premium pode reduzir o tempo necessário para criar e implantar imagens em seus destinos de computação. Para obter mais informações, confira [Níveis de serviço do Registro de Contêiner do Azure](../container-registry/container-registry-skus.md).

> [!NOTE]
> Se você estiver implantando um modelo no AKS (serviço kubernetes do Azure), aconselharemos a habilitar [Azure monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) para esse cluster. Isso ajudará você a entender a integridade geral do cluster e o uso de recursos. Você também pode encontrar os seguintes recursos úteis:
>
> * [Verificar Resource Health eventos que afetam o cluster AKS](../aks/aks-resource-health.md)
> * [Diagnóstico do serviço kubernetes do Azure](../aks/concepts-diagnostics.md)
>
> Se você estiver tentando implantar um modelo em um cluster não íntegro ou sobrecarregado, espera-se que eles tenham problemas. Se precisar de ajuda para solucionar problemas de cluster AKS, entre em contato com o suporte do AKS.

### <a name="batch-inference"></a><a id="azuremlcompute"></a> Inferência de lote
Azure Machine Learning destinos de computação são criados e gerenciados pelo Azure Machine Learning. Eles podem ser usados para previsão de lote de pipelines de Azure Machine Learning.

Para obter uma explicação sobre a inferência de lote com Azure Machine Learning computação, consulte [como executar previsões de lote](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> Inferência de IoT Edge
O suporte para a implantação no Edge está em versão prévia. Para obter mais informações, consulte [implantar Azure Machine Learning como um módulo IOT Edge](../iot-edge/tutorial-deploy-machine-learning.md).

## <a name="delete-resources"></a>Excluir recursos

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Para excluir um WebService implantado, use `az ml service <name of webservice>` .

Para excluir um modelo registrado do seu espaço de trabalho, use `az ml model delete <model id>`

Leia mais sobre como [excluir um WebService](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) e [excluir um modelo](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete).

# <a name="python"></a>[Python](#tab/python)

Para excluir um serviço Web implantado, use `service.delete()`.
Para excluir um modelo registrado, use `model.delete()`.

Para obter mais informações, consulte a documentação de [WebService. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) e [Model. Delete ()](/python/api/azureml-core/azureml.core.model.model#delete--).

---

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