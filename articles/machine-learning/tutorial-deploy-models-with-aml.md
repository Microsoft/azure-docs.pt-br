---
title: 'Tutorial de classificação de imagens: Implantar modelos'
titleSuffix: Azure Machine Learning
description: Este tutorial mostra como usar o Azure Machine Learning para implantar um modelo de classificação de imagem com o Scikit-learn em um Python Jupyter Notebook.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: 0981d325b3c5982793ada480a87afc48bf58acf7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066571"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Tutorial: Implantar um modelo de classificação de imagem em Instâncias de Contêiner do Azure


Este tutorial é **parte dois de uma série de tutoriais de duas partes**. No [tutorial anterior](tutorial-train-models-with-aml.md), você treinou modelos de machine learning e, depois, registrou um modelo no workspace na nuvem.  Agora, você está pronto para implantar o modelo como um serviço Web. Um serviço Web é uma imagem, neste caso, uma imagem do Docker. Ele encapsula a lógica de pontuação e o próprio modelo. 

Nesta parte do tutorial, você usará o Azure Machine Learning para as seguintes tarefas:

> [!div class="checklist"]
> * Configurar seu ambiente de teste.
> * Recuperar o modelo de seu workspace.
> * Implantar o modelo nas Instâncias de Contêiner.
> * Testar o modelo implantado.

Instâncias de Contêiner são uma ótima solução para testar e compreender o fluxo de trabalho. Para implantações de produção escalonável, considere usar o Serviço de Kubernetes do Azure. Para obter mais informações, confira [como e em que local implantar](how-to-deploy-and-where.md).

>[!NOTE]
> O código deste artigo foi testado com a versão 1.0.83 do SDK do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Para executar o notebook, primeiro conclua o treinamento de modelo no [Tutorial (parte 1): treinar um modelo de classificação de imagem](tutorial-train-models-with-aml.md).   Em seguida, abra o notebook *img-classification-part2-deploy.ipynb* na pasta *tutorials/image-classification-mnist-data* clonada.

Este tutorial também estará disponível no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) caso você deseje usá-lo em seu próprio [ambiente local](how-to-configure-environment.md#local).  Verifique se você instalou o `matplotlib` e o `scikit-learn` em seu ambiente. 

> [!Important]
> O restante deste artigo contém o mesmo conteúdo que você vê no notebook.  
>
> Alterne para o Jupyter Notebook agora se quiser ler enquanto executa o código.
> Para executar uma única célula de código em um notebook, clique na célula de código e pressione **Shift + Enter**. Ou execute o notebook inteiro escolhendo a **Executar tudo** na barra de ferramentas superior.

## <a name="set-up-the-environment"></a><a name="start"></a>Configurar o ambiente

Inicie configurando um ambiente de teste.

### <a name="import-packages"></a>Importar pacotes

Importe os pacotes do Python necessários para este tutorial.


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
 
import azureml.core

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

## <a name="deploy-as-web-service"></a>Implantar como serviço Web

Implante o modelo como um serviço Web hospedado no ACI. 

Para compilar o ambiente correto para ACI, forneça o seguinte:
* Um script de pontuação para mostrar como usar o modelo
* Um arquivo de configuração para construir a ACI
* O modelo que você treinou antes

### <a name="create-scoring-script"></a>Criar script de pontuação

Crie o script de pontuação, chamado score.py, usado pela chamada de serviço Web para mostrar como usar o modelo.

É necessário incluir duas funções exigidas no script de pontuação:
* A função `init()`, que normalmente carrega o modelo em um objeto global. Essa função é executada apenas uma vez quando o contêiner do Docker é iniciado. 

* A função `run(input_data)` usa o modelo para prever um valor com base nos dados de entrada. Entradas e saídas para a execução normalmente usam JSON para serialização e desserialização, mas outros formatos têm suporte.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

### <a name="create-configuration-file"></a>Criar arquivo de configuração

Crie um arquivo de configuração de implantação e especifique o número de CPUs e gigabytes de RAM necessários para o contêiner de ACI. Embora dependa do modelo, o padrão de 1 núcleo e 1 gigabyte de RAM geralmente é suficiente para muitos modelos. Se você achar que precisará de mais posteriormente, será necessário recriar a imagem e reimplantar o serviço.


```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>Implantar em ACI
Tempo estimado para a conclusão: **aproximadamente de 2 a 5 minutos**

Configure a imagem e implante. O código a seguir passa por estas etapas:

1. Crie um objeto de ambiente contendo as dependências necessárias para o modelo que usa o ambiente (`tutorial-env`) salvo durante o treinamento.
1. Crie a configuração de inferência necessária para implantar o modelo como um serviço Web usando:
   * O arquivo de pontuação (`score.py`)
   * objeto de ambiente criado na etapa anterior
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


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc3', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Obtenha o ponto de extremidade HTTP do serviço Web de pontuação, que aceita chamadas do cliente REST. Esse ponto de extremidade pode ser compartilhado com qualquer pessoa que queira testar o serviço Web ou integrá-lo a um aplicativo.


```python
print(service.scoring_uri)
```

## <a name="test-the-model"></a>Testar o modelo


### <a name="download-test-data"></a>Baixar dados de teste
Baixe os dados de teste para o diretório **./data/**


```python
import os
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)
```

### <a name="load-test-data"></a>Carregar dados de teste

Carregue os dados de teste do diretório **./data/** criado durante o tutorial de treinamento.


```python
from utils import load_data
import os
import glob

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
```

### <a name="predict-test-data"></a>Prever dados de teste

Forneça o conjunto de dados de teste ao modelo para obter previsões.


O código a seguir passa por estas etapas:
1. Envie os dados como uma matriz JSON para o serviço Web hospedado na ACI. 

1. Use a API `run` do SDK para invocar o serviço. Também é possível fazer chamadas brutas usando qualquer ferramenta HTTP, como curl.


```python
import json
test = json.dumps({"data": X_test.tolist()})
test = bytes(test, encoding='utf8')
y_hat = service.run(input_data=test)
```

###  <a name="examine-the-confusion-matrix"></a>Examine a matriz de confusão

Gere uma matriz de confusão para ver quantas amostras do conjunto de testes são classificadas corretamente. Observe o valor classificado incorretamente para as previsões incorretas.


```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

A saída mostra a matriz de confusão:

```output
[[ 960    0    1    2    1    5    6    3    1    1]
 [   0 1112    3    1    0    1    5    1   12    0]
 [   9    8  920   20   10    4   10   11   37    3]
 [   4    0   17  921    2   21    4   12   20    9]
 [   1    2    5    3  915    0   10    2    6   38]
 [  10    2    0   41   10  770   17    7   28    7]
 [   9    3    7    2    6   20  907    1    3    0]
 [   2    7   22    5    8    1    1  950    5   27]
 [  10   15    5   21   15   27    7   11  851   12]
 [   7    8    2   13   32   13    0   24   12  898]]
Overall accuracy: 0.9204
```

Use `matplotlib` para exibir a matriz de confusão como um grafo. Neste grafo, o eixo X representa os valores reais e o eixo Y representa os valores previstos. A cor em cada grade representa a taxa de erro. Quanto mais clara a cor, maior é a taxa de erro. Por exemplo, muitos 5's são classificados incorretamente como 3'. Assim, você vê uma grade brilhante em (5,3).

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![Matriz de confusão de exibição de gráfico](./media/tutorial-deploy-models-with-aml/confusion.png)


## <a name="show-predictions"></a>Exibir previsões

Teste o modelo implantado com uma amostra aleatória de 30 imagens dos dados de teste.  


1. Imprima as previsões retornadas e plote-as junto com as imagens de entrada. A fonte vermelha e a imagem inversa (branco sobre preto) são usadas para realçar as amostras classificadas incorretamente. 

 Como a precisão do modelo é alta, talvez seja necessário executar o código a seguir algumas vezes, antes de poder ver uma amostra classificada incorretamente.



```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Também é possível enviar uma solicitação HTTP bruta para testar o serviço Web.


```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para manter o grupo de recursos e o workspace para outros tutoriais e exploração, você pode excluir apenas a implantação das Instâncias de Contêiner usando esta chamada à API:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre todas as [opções de implantação do Azure Machine Learning](how-to-deploy-and-where.md).
+ Saiba como [criar clientes para o serviço Web](how-to-consume-web-service.md).
+  [Fazer previsões sobre grandes quantidades de dados](./tutorial-pipeline-batch-scoring-classification.md) assincronamente.
+ [Monitorar seus modelos do Azure Machine Learning com o Application Insights](how-to-enable-app-insights.md).
+ Experimente o tutorial de [seleção automática de algoritmo](tutorial-auto-train-models.md).