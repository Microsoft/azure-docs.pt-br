---
title: Como executar e implantar localmente
titleSuffix: Azure Machine Learning
description: Este artigo descreve como usar seu computador local como um destino para treinamento, depuração ou implantação de modelos criados no Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: 71f393897dff266f1b0922a19eefd70cffea133d
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600346"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>Implantar modelos treinados com o Azure Machine Learning em seus computadores locais 

Este artigo descreve como usar seu computador local como um destino para treinamento ou implantação de modelos criados no Azure Machine Learning. Azure Machine Learning é flexível o suficiente para funcionar com a maioria das estruturas de aprendizado de máquina do Python. As soluções de aprendizado de máquina geralmente têm dependências complexas que podem ser difíceis de duplicar. Este artigo mostrará como balancear o controle total com facilidade de uso.

Os cenários para a implantação local incluem:

* Iterar rapidamente dados, scripts e modelos no início de um projeto.
* Depuração e solução de problemas em estágios posteriores.
* Implantação final em hardware gerenciado pelo usuário.

## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).
- Um modelo e um ambiente. Se você não tiver um modelo treinado, poderá usar os arquivos de modelo e de dependência fornecidos neste [tutorial](tutorial-train-models-with-aml.md).
- O [SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).
- Um Gerenciador de Conda, como Anaconda ou Miniconda, se você quiser espelhar Azure Machine Learning dependências de pacote.
- Docker, se você quiser usar uma versão em contêiner do ambiente de Azure Machine Learning.

## <a name="prepare-your-local-machine"></a>Preparar seu computador local

A maneira mais confiável de executar localmente um modelo de Azure Machine Learning é com uma imagem do Docker. Uma imagem do Docker fornece uma experiência isolada e em contêineres que duplica, exceto para problemas de hardware, o ambiente de execução do Azure. Para obter mais informações sobre como instalar e configurar o Docker para cenários de desenvolvimento, consulte [visão geral do desenvolvimento remoto do Docker no Windows](/windows/dev-environment/docker/overview).

É possível anexar um depurador a um processo em execução no Docker. (Consulte [anexar a um contêiner em execução](https://code.visualstudio.com/docs/remote/attach-container).) Mas talvez você prefira depurar e iterar seu código Python sem envolver o Docker. Nesse cenário, é importante que o computador local use as mesmas bibliotecas que são usadas quando você executa o experimento no Azure Machine Learning. Para gerenciar dependências do Python, o Azure usa [Conda](https://docs.conda.io/). Você pode recriar o ambiente usando outros gerenciadores de pacotes, mas instalar e configurar o Conda em seu computador local é a maneira mais fácil de sincronizar. 

## <a name="prepare-your-entry-script"></a>Preparar o script de entrada

Mesmo que você use o Docker para gerenciar o modelo e as dependências, o script de Pontuação do Python deve ser local. O script deve ter dois métodos:

- Um `init()` método que não usa nenhum argumento e não retorna nada 
- Um `run()` método que usa uma cadeia de caracteres formatada em JSON e retorna um objeto serializável por JSON

O argumento para o `run()` método estará neste formulário: 

```json
{
    "data": <model-specific-data-structure>
}
```

O objeto retornado do `run()` método deve implementar `toJSON() -> string` .

O exemplo a seguir demonstra como carregar um modelo scikit-Learn registrado e pontua-lo usando os dados do NumPy. Este exemplo se baseia no modelo e nas dependências deste [tutorial](tutorial-train-models-with-aml.md).

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It's the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION).
    # For multiple models, it points to the folder containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # Make prediction.
    y_hat = model.predict(data)
    # You can return any data type as long as it's JSON-serializable.
    return y_hat.tolist()
```

Para obter exemplos mais avançados, incluindo a geração automática de esquema do Swagger e dados binários de Pontuação (por exemplo, imagens), consulte [criação avançada de script de entrada](how-to-deploy-advanced-entry-script.md). 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Implantar como um serviço Web local usando o Docker

A maneira mais fácil de replicar o ambiente usado pelo Azure Machine Learning é implantar um serviço Web usando o Docker. Com o Docker em execução no computador local, você irá:

1. Conecte-se ao espaço de trabalho Azure Machine Learning no qual seu modelo está registrado.
1. Crie um `Model` objeto que representa o modelo.
1. Crie um `Environment` objeto que contenha as dependências e defina o ambiente de software no qual seu código será executado.
1. Crie um `InferenceConfig` objeto que associa o script de entrada ao `Environment` .
1. Crie um `DeploymentConfiguration` objeto da subclasse `LocalWebserviceDeploymentConfiguration` .
1. Use `Model.deploy()` para criar um `Webservice` objeto. Esse método baixa a imagem do Docker e a associa com o `Model` , o `InferenceConfig` e o `DeploymentConfiguration` .
1. Ative o `Webservice` usando o `Webservice.wait_for_deployment()` .

O código a seguir mostra estas etapas:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

A chamada para `Model.deploy()` pode levar alguns minutos. Depois de ter implantado inicialmente o serviço Web, é mais eficiente usar o `update()` método em vez de começar do zero. Consulte [atualizar um serviço Web implantado](how-to-deploy-update-web-service.md).

### <a name="test-your-local-deployment"></a>Testar sua implantação local

Quando você executar o script de implantação anterior, ele produzirá o URI para o qual você pode postar dados para Pontuação (por exemplo, `http://localhost:6789/score` ). O exemplo a seguir mostra um script que classifica os dados de exemplo usando o `"sklearn-mnist-local"` modelo implantado localmente. O modelo, se treinado corretamente, infere que `normalized_pixel_values` deve ser interpretado como um "2". 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>Baixe e execute seu modelo diretamente

Usar o Docker para implantar seu modelo como um serviço Web é a opção mais comum. Mas talvez você queira executar seu código diretamente usando scripts Python locais. Você precisará de dois componentes importantes: 

- O modelo propriamente dito
- As dependências sobre as quais o modelo depende 

Você pode baixar o modelo:  

- No portal, selecionando a guia **modelos** , selecionando o modelo desejado e, na página **detalhes** , selecionando **baixar**.
- Na linha de comando, usando `az ml model download` . (Consulte [download do modelo.](/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext_azure_cli_ml_az_ml_model_download&preserve-view=false))
- Usando o método SDK do Python `Model.download()` . (Consulte [classe de modelo.](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#download-target-dir------exist-ok-false--exists-ok-none-&preserve-view=false))

Um modelo do Azure é um ou mais objetos Python serializados, empacotados como um arquivo Python pickle (extensão. PKL). O conteúdo do arquivo pickle depende da biblioteca de aprendizado de máquina ou da técnica usada para treinar o modelo. Por exemplo, se você estiver usando o modelo do tutorial, você pode carregar o modelo com:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

As dependências são sempre complicadas de serem corretas, especialmente com o aprendizado de máquina, em que muitas vezes pode haver uma espantosa Web de requisitos de versão específicos. Você pode recriar um ambiente de Azure Machine Learning em seu computador local como um ambiente Conda completo ou como uma imagem do Docker usando o `build_local()` método da `Environment` classe: 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

Se você definir o `build_local()` `useDocker` argumento como `True` , a função criará uma imagem do Docker em vez de um ambiente Conda. Se você quiser mais controle, poderá usar o `save_to_directory()` método de `Environment` , que grava conda_dependencies. yml e azureml_environment.jsem arquivos de definição que você pode ajustar e usar como base para a extensão. 

A `Environment` classe tem vários outros métodos para sincronizar ambientes em seu hardware de computação, seu espaço de trabalho do Azure e imagens do Docker. Para obter mais informações, consulte [classe de ambiente](/python/api/azureml-core/azureml.core.environment(class)).

Depois de baixar o modelo e resolver suas dependências, não há restrições definidas pelo Azure sobre como executar a pontuação, ajustar o modelo, usar o aprendizado de transferência e assim por diante. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Carregar um modelo retreinado para Azure Machine Learning

Se você tiver um modelo treinado ou retreinado localmente, poderá registrá-lo com o Azure. Depois de registrado, você pode continuar a ajustá-lo usando a computação do Azure ou implantá-lo usando instalações do Azure, como o [serviço kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md) ou o [servidor de inferência Triton (versão prévia)](how-to-deploy-with-triton.md).

Para ser usado com o SDK do Azure Machine Learning Python, um modelo deve ser armazenado como um objeto Python serializado no formato pickle (um arquivo. PKL). Ele também deve implementar um `predict(data)` método que retorne um objeto serializável em JSON. Por exemplo, você pode armazenar um modelo treinado scikit-Learn diabetes localmente com: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Para disponibilizar o modelo no Azure, você pode usar o `register()` método da `Model` classe:

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

Em seguida, você pode encontrar seu modelo recentemente registrado na guia **modelo** de Azure Machine Learning:

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Captura de tela da guia modelo de Azure Machine Learning, mostrando um modelo carregado.":::

Para obter mais informações sobre como carregar e atualizar modelos e ambientes, consulte [registrar modelo e implantar localmente com usos avançados](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb).

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre como gerenciar ambientes, consulte [criar & usar ambientes de software no Azure Machine Learning](how-to-use-environments.md).
- Para saber mais sobre como acessar os dados do seu armazenamento, consulte [conectar-se aos serviços de armazenamento no Azure](how-to-access-data.md).
