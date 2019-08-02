---
title: Como usar um modelo existente
titleSuffix: Azure Machine Learning service
description: Saiba como você pode usar o serviço Azure Machine Learning com modelos que foram treinados fora de serviço. Você pode registrar os modelos criados fora do serviço de Azure Machine Learning e, em seguida, implantá-los como um serviço web ou o módulo do Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: 332129c9847c317369d5631c3af584da9430e9dd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453678"
---
# <a name="how-to-use-an-existing-model-with-azure-machine-learning-service"></a>Como usar um modelo existente com o serviço Azure Machine Learning

Saiba como usar uma modelo com o serviço de Azure Machine Learning de aprendizado de máquina existente.

Se você tiver um modelo de machine learning que foi treinado fora do serviço de Azure Machine Learning, você ainda pode usar o serviço para implantar o modelo como um serviço web ou para um dispositivo IoT Edge. 

> [!TIP]
> Este artigo fornece informações básicas sobre como registrar e implantar um modelo existente. Uma vez implantado, o serviço Azure Machine Learning fornece monitoramento para seu modelo. Ele também permite que você armazene dados de entrada enviados para a implantação, o que pode ser usada para dados descompasso treinamento ou análise de novas versões do modelo.
>
> Para obter mais informações sobre os conceitos e termos usados aqui, consulte [gerenciar, implantar e monitorar modelos de aprendizado de máquina](concept-model-management-and-deployment.md).
>
> Para obter informações gerais sobre o processo de implantação, consulte [implantar modelos com o serviço Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um workspace de serviço do Azure Machine Learning. Para obter mais informações, consulte [criar um espaço de trabalho](setup-create-workspace.md).

    > [!TIP]
    > Os exemplos de Python neste artigo pressupõem que o `ws` variável é definida como seu espaço de trabalho do serviço de Azure Machine Learning.
    >
    > Os exemplos CLI usam um espaço reservado de `myworkspace` e `myresourcegroup`. Substitua-os com o nome do seu espaço de trabalho e o grupo de recursos que o contém.

* O Machine Learning do Azure SDK. Para obter mais informações, consulte a seção SDK do Python [criar um espaço de trabalho](setup-create-workspace.md#sdk).

* O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e [extensão da CLI de aprendizado de máquina](reference-azure-machine-learning-cli.md).

* Um modelo treinado. O modelo deve ser mantido em um ou mais arquivos em seu ambiente de desenvolvimento.

    > [!NOTE]
    > Para demonstrar o registro de um modelo treinado fora do serviço de Azure Machine Learning, os trechos de código de exemplo neste artigo usam os modelos criados pelo projeto de análise de sentimento de Paolo Ripamonti Twitter: [ https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis ](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Registrar os modelos

Registro de um modelo permite que você armazene, versão e controlar metadados sobre os modelos no seu espaço de trabalho. Nos exemplos a seguir Python e a CLI, o `models` diretório contém o `model.h5`, `model.w2v`, `encoder.pkl`, e `tokenizer.pkl` arquivos. Este exemplo carrega os arquivos contidos na `models` diretório como um novo registro de modelo chamado `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

Para obter mais informações, consulte o [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) referência.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Para obter mais informações, consulte o [registro de modelo do az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) referência.


Para obter mais informações sobre o registro de modelo em geral, consulte [gerenciar, implantar e monitorar modelos de aprendizado de máquina](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Definir a configuração de inferência de tipos

A configuração de inferência define o ambiente usado para executar o modelo implantado. A configuração de inferência referencia os seguintes arquivos que são usados para executar o modelo quando ele é implantado:

* O tempo de execução. O único valor válido para tempo de execução no momento é o Python.
* Um script de entrada. Esse arquivo (denominado `score.py`) carrega o modelo quando o serviço implantado é iniciado. Também é responsável por receber dados, passando-o para o modelo e, em seguida, retornar uma resposta.
* Um arquivo de ambiente do conda. Esse arquivo define os pacotes Python necessários para executar o script de modelo e de entrada. 

O exemplo a seguir mostra uma configuração de inferência de tipos básicos usando o SDK do Python:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

Para obter mais informações, consulte o [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) referência.

A CLI carrega a configuração de inferência de tipos de um arquivo YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Para obter mais informações sobre a configuração de inferência de tipos, consulte [implantar modelos com o serviço Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Script de entrada

O script de entrada tem apenas duas funções necessárias, `init()` e `run(data)`. Essas funções são usadas para inicializar o serviço na inicialização e executar o modelo usando dados de solicitação passados por um cliente. O restante do script manipula Carregando e executando os modelos.

> [!IMPORTANT]
> Não há um script de entrada genérico que funciona para todos os modelos. Sempre é específico para o modelo que é usado. Ela deve entender como carregar o modelo, o formato de dados que o modelo espera e como pontuar os dados usando o modelo.

O código Python a seguir está um exemplo de script de entrada (`score.py`):

```python
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec
from azureml.core.model import Model

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

    # Get the path where the model(s) registered as the name 'sentiment' can be found.
    model_path = Model.get_model_path('sentiment')
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

Para obter mais informações sobre scripts de entrada, consulte [implantar modelos com o serviço Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="conda-environment"></a>Ambiente do Conda

O YAML a seguir descreve o ambiente de conda necessário para executar o script de modelo e de entrada:

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
```

Para obter mais informações, consulte [implantar modelos com o serviço Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definir a implantação

O [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) pacote contém as classes usadas para implantação. A classe que você usa determina onde o modelo é implantado. Por exemplo, para implantar como um serviço web no serviço Kubernetes do Azure, use [AksWebService.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-) para criar a configuração de implantação.

O código Python a seguir define uma configuração de implantação para uma implantação local. Essa configuração implanta o modelo como um serviço web em seu computador local.

> [!IMPORTANT]
> Uma implantação local requer uma instalação funcional do [Docker](https://www.docker.com/) no computador local:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Para obter mais informações, consulte o [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) referência.

A CLI carrega a configuração de implantação de um arquivo YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Implantando em um destino de computação diferentes, como o serviço de Kubernetes do Azure na nuvem do Azure, é tão fácil quanto alterar a configuração de implantação. Para obter mais informações, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Implantar o modelo

O exemplo a seguir carrega informações sobre o modelo registrado chamado `sentiment`e, em seguida, implantado como um serviço chamado `sentiment`. Durante a implantação, a configuração de inferência de tipos e a configuração de implantação são usados para criar e configurar o ambiente do serviço:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Para obter mais informações, consulte o [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) referência.

Para implantar o modelo da CLI, use o comando a seguir. Esse comando implanta o modelo registrado a versão 1 (`sentiment:1`) usando a configuração de implantação e Inferência de tipos armazenada na `inferenceConfig.json` e `deploymentConfig.json` arquivos:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Para obter mais informações, consulte o [implantar o modelo de ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) referência.

Para obter mais informações sobre implantação, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Consumo de solicitação-resposta

Após a implantação, o URI de pontuação é exibido. Esse URI pode ser usado pelos clientes para enviar solicitações para o serviço. O exemplo a seguir é um cliente de Python básico que envia dados para o serviço e exibe a resposta:

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

* [Monitorar seus modelos do Azure Machine Learning com o Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [Como e onde implantar modelos](how-to-deploy-and-where.md)
* [Como criar um cliente para um modelo implantado](how-to-consume-web-service.md)