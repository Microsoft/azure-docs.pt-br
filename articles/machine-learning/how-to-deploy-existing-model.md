---
title: Usar e implantar modelos existentes
titleSuffix: Azure Machine Learning
description: Saiba como trazer modelos de ML treinados fora do Azure para a nuvem do Azure com Azure Machine Learning. Em seguida, implante o modelo como um serviço Web ou módulo IoT.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 46b8f153e65f436fa1062a0606e0fb0136d972a5
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97824608"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Implantar seu modelo existente com Azure Machine Learning


Neste artigo, você aprende a registrar e implantar um modelo de aprendizado de máquina que você treinou fora Azure Machine Learning. Você pode implantar o como um serviço Web ou um dispositivo IoT Edge.  Depois de implantado, você pode monitorar seu modelo e detectar descompasso de dados em Azure Machine Learning. 

Para obter mais informações sobre os conceitos e termos deste artigo, consulte [gerenciar, implantar e monitorar modelos de aprendizado de máquina](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md)
  + Os exemplos do Python pressupõem que a `ws` variável esteja definida como seu espaço de trabalho Azure Machine Learning. Para obter mais informações sobre como se conectar ao espaço de trabalho, consulte a [documentação Azure Machine Learning SDK para Python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=trueworkspace).
  
  + Os exemplos da CLI usam espaços reservados de `myworkspace` e `myresourcegroup` , que você deve substituir pelo nome do seu espaço de trabalho e o grupo de recursos que o contém.

* O [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).  

* A extensão da [CLI](reference-azure-machine-learning-cli.md)de [CLI do Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) e Machine Learning.

* Um modelo treinado. O modelo deve ser persistido em um ou mais arquivos em seu ambiente de desenvolvimento. <br><br>Para demonstrar o registro de um modelo treinado, o código de exemplo neste artigo usa os modelos do [projeto de análise de opiniões do Twitter do Paolo Ripamonti](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Registrar os modelos

O registro de um modelo permite que você armazene, versione e rastreie metadados sobre modelos em seu espaço de trabalho. Nos exemplos de Python e CLI a seguir, o `models` diretório contém `model.h5` os `model.w2v` arquivos,, `encoder.pkl` e `tokenizer.pkl` . Este exemplo carrega os arquivos contidos no `models` diretório como um novo registro de modelo chamado `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Para obter mais informações, consulte a referência [Model. Register ()](/python/api/azureml-core/azureml.core.model%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) .

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Você também pode definir objetos de adição `tags` e `properties` dicionário para o modelo registrado. Esses valores podem ser usados posteriormente para ajudar a identificar um modelo específico. Por exemplo, a estrutura usada, parâmetros de treinamento, etc.

Para obter mais informações, consulte a referência de [registro do modelo AZ ml](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) .


Para obter mais informações sobre o registro de modelo em geral, consulte [gerenciar, implantar e monitorar modelos de aprendizado de máquina](concept-model-management-and-deployment.md).

## <a name="define-inference-configuration"></a>Definir configuração de inferência

A configuração de inferência define o ambiente usado para executar o modelo implantado. A configuração de inferência referencia as seguintes entidades, que são usadas para executar o modelo quando ela é implantada:

* Um script de entrada, chamado `score.py` , carrega o modelo quando o serviço implantado é iniciado. Esse script também é responsável por receber dados, passá-los para o modelo e, em seguida, retornar uma resposta.
* Um [ambiente](how-to-use-environments.md)de Azure Machine Learning. Um ambiente define as dependências de software necessárias para executar o modelo e o script de entrada.

O exemplo a seguir mostra como usar o SDK para criar um ambiente e, em seguida, usá-lo com uma configuração de inferência:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Para obter mais informações, consulte os seguintes artigos:

+ [Como usar ambientes](how-to-use-environments.md).
+ Referência de [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py) .


A CLI carrega a configuração de inferência de um arquivo YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Com a CLI, o ambiente Conda é definido no `myenv.yml` arquivo referenciado pela configuração de inferência. O seguinte YAML é o conteúdo deste arquivo:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Para obter mais informações sobre a configuração de inferência, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script-scorepy"></a>Script de entrada (score.py)

O script de entrada tem apenas duas funções necessárias `init()` e `run(data)` . Essas funções são usadas para inicializar o serviço na inicialização e executar o modelo usando dados de solicitação passados por um cliente. O restante do script lida com o carregamento e execução dos modelos.

> [!IMPORTANT]
> Não há um script de entrada genérico que funcione para todos os modelos. Ele é sempre específico para o modelo que é usado. Ele deve entender como carregar o modelo, o formato de dados que o modelo espera e como pontuar dados usando o modelo.

O seguinte código Python é um script de entrada de exemplo ( `score.py` ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Para obter mais informações sobre scripts de entrada, confira [Implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definir implantação

O pacote [WebService](/python/api/azureml-core/azureml.core.webservice?preserve-view=true&view=azure-ml-py) contém as classes usadas para implantação. A classe usada determina onde o modelo é implantado. Por exemplo, para implantar como um serviço Web no serviço kubernetes do Azure, use [AksWebService.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) para criar a configuração de implantação.

O código Python a seguir define uma configuração de implantação para uma implantação local. Essa configuração implanta o modelo como um serviço Web em seu computador local.

> [!IMPORTANT]
> Uma implantação local requer uma instalação funcional do [Docker](https://www.docker.com/) no computador local:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Para obter mais informações, consulte a referência de [LocalWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.localwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-port-none-) .

A CLI carrega a configuração de implantação de um arquivo YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

A implantação em um destino de computação diferente, como o serviço kubernetes do Azure na nuvem do Azure, é tão fácil quanto alterar a configuração da implantação. Para obter mais informações, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Implantar o modelo

O exemplo a seguir carrega informações sobre o modelo registrado chamado `sentiment` e, em seguida, implanta-o como um serviço chamado `sentiment` . Durante a implantação, a configuração de inferência e a configuração de implantação são usadas para criar e configurar o ambiente de serviço:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Para obter mais informações, consulte a referência [Model. Deploy ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) .

Para implantar o modelo da CLI, use o comando a seguir. Este comando implanta a versão 1 do modelo registrado ( `sentiment:1` ) usando a inferência e a configuração de implantação armazenadas `inferenceConfig.json` nos `deploymentConfig.json` arquivos e:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Para obter mais informações, consulte a referência de [implantação do modelo AZ ml](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

Para obter mais informações sobre a implantação, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Consumo de solicitação-resposta

Após a implantação, o URI de pontuação é exibido. Esse URI pode ser usado por clientes para enviar solicitações ao serviço. O exemplo a seguir é um cliente Python simples que envia dados para o serviço e exibe a resposta:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Para obter mais informações sobre como consumir o serviço implantado, consulte [criar um cliente](how-to-consume-web-service.md).

## <a name="next-steps"></a>Próximas etapas

* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [Como criar um cliente para um modelo implantado](how-to-consume-web-service.md)