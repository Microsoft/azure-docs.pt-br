---
title: Implantar um serviço inferência criptografado (visualização)
titleSuffix: Azure Machine Learning
description: Saiba como usar o Microsoft SEAL para implantar um serviço de previsão criptografado para classificação de imagens
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, responsible-ml
ms.openlocfilehash: a2c8b765f109408b8cb08f78a4b3ad2283f35eb9
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223577"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service-preview"></a>Como implantar um serviço Web inferência criptografado (versão prévia)

Saiba como implantar um modelo de classificação de imagem como um serviço Web de inferência criptografado nas [Instâncias de Contêiner do Azure](../container-instances/index.yml) (ACI). O serviço Web é uma imagem de contêiner do Docker que contém o modelo e a lógica de pontuação.

Nesta parte do guia, você usará o Serviço do Azure Machine Learning para:

> [!div class="checklist"]
> * Configurar seus ambientes
> * Implantar o serviço Web de inferência criptografado
> * Preparar os dados de teste
> * Fazer previsões criptografadas
> * Limpar os recursos

ACIs são uma ótima solução para testar e compreender o fluxo de trabalho de implantação de modelo. Para implantações de produção escalonável, considere usar o Serviço de Kubernetes do Azure. Para obter mais informações, confira [como e em que local implantar](./how-to-deploy-and-where.md).

O método de criptografia usado neste exemplo é a [criptografia homomórfica](https://github.com/Microsoft/SEAL#homomorphic-encryption). A criptografia homomórfica permite que as computações sejam feitas em dados criptografados sem necessidade de acesso a uma chave secreta (descriptografia). Os resultados dos cálculos são criptografados e podem ser revelados somente pelo proprietário da chave secreta. 

## <a name="prerequisites"></a>Pré-requisitos

Este guia pressupõe que você tenha um modelo de classificação de imagem registrado no Azure Machine Learning. Se não tiver, use um [modelo previamente treinado ](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl) para registrar o modelo ou crie o seu próprio modelo concluindo o [treinamento de modelo de classificação de imagem com o tutorial do Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="configure-local-environment"></a>Configurar ambiente local

Em um notebook do Jupyter

1. Importe os pacotes do Python necessários para esta amostra.

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. Instale a biblioteca de criptografia homomórfica para garantir a inferência.

    > [!NOTE]
    > O pacote `encrypted-inference` está em versão preliminar.

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) é uma biblioteca que contém associações para inferências criptografadas com base no [Microsoft SEAL](https://github.com/Microsoft/SEAL).

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>Configurar o ambiente de inferência

Crie um ambiente para inferência e adicione o pacote `encrypted-inference` como uma dependência Conda.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>Implantar o serviço Web de inferência criptografado

Implante o modelo como um serviço Web hospedado no ACI.

Para compilar o ambiente correto para ACI, forneça o seguinte:

* Um script de pontuação para mostrar como usar o modelo
* Um arquivo de configuração para construir a ACI
* Um modelo treinado

### <a name="create-scoring-script"></a>Criar script de pontuação

Crie o script de pontuação `score.py` usado pelo serviço Web para inferência.

É necessário incluir duas funções exigidas no script de pontuação:

* A função `init()`, que normalmente carrega o modelo em um objeto global. Essa função é executada apenas uma vez quando o contêiner do Docker é iniciado.
* A função `run(input_data)` usa o modelo para prever um valor com base nos dados de entrada. Entradas e saídas para a execução normalmente usam JSON para serialização e desserialização, mas outros formatos têm suporte. A função busca criptografia homomórfica com base em chaves públicas carregadas pelo chamador do serviço.

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>Criar arquivo de configuração

Crie um arquivo de configuração de implantação e especifique o número de CPUs e gigabytes de RAM necessários para o contêiner de ACI. Embora dependa do modelo, o padrão de 1 núcleo e 1 gigabyte de RAM geralmente é suficiente para muitos modelos. Se você achar que precisará de mais posteriormente, será necessário recriar a imagem e reimplantar o serviço.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>Implantar para Instâncias de Contêiner do Azure

Tempo estimado para a conclusão: **aproximadamente de 2 a 5 minutos**

Configure a imagem e implante. O código a seguir passa por estas etapas:

1. Crie um objeto de ambiente contendo as dependências necessárias para o modelo que usa o arquivo de ambiente (`myenv.yml`)
1. Crie a configuração de inferência necessária para implantar o modelo como um serviço Web usando:
   * O arquivo de pontuação (`score.py`)
   * Objeto de ambiente criado na etapa anterior
1. Implante o modelo no contêiner do ACI.
1. Obtenha o terminal HTTP do serviço Web.

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Obtenha o ponto de extremidade HTTP do serviço Web de pontuação, que aceita chamadas do cliente REST. Esse ponto de extremidade pode ser compartilhado com qualquer pessoa que queira testar o serviço Web ou integrá-lo a um aplicativo.

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>Preparar os dados de teste

1. Baixe os dados de teste. Nesse caso, eles são salvos em um diretório chamado *data*.

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. Baixe os dados de teste do diretório *data*.

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>Fazer previsões criptografadas

Use o conjunto de dados de teste com o modelo para receber previsões.

Para fazer previsões criptografadas:

1. Crie um novo `EILinearRegressionClient`, um cliente baseado em criptografia homomórfica, e chaves públicas.

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. Carregue as chaves públicas geradas pela criptografia homomórfica no armazenamento do blob padrão do workspace. Isso permitirá compartilhar as chaves com o servidor de inferência.

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. Criptografar os dados de teste

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. Use a API `run` do SDK para invocar o serviço e fornecer o conjunto de dados de teste ao modelo para receber previsões. Precisaremos enviar a cadeia de conexão ao armazenamento de blob onde as chaves públicas foram carregadas.

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. Use o cliente para descriptografar os resultados.

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua o serviço Web criado neste exemplo:

```python
service.delete()
```

Exclua os recursos do Azure criados que não planeja mais usar. Isso impede a cobrança por recursos não utilizados que ainda estão em execução. Consulte este guia sobre como [limpar recursos](how-to-manage-workspace.md#clean-up-resources) para saber mais.