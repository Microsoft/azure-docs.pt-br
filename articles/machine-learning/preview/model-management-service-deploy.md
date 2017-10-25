---
title: "Implantação do serviço Web de Gerenciamento de Modelos do Azure Machine Learning | Microsoft Docs"
description: "Este documento descreve as etapas envolvidas na implantação de um modelo de aprendizado de máquina usando o Gerenciamento de Modelos do Azure Machine Learning."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 0ebf8964bc565e5d4f95fb1ad3edbc16f841851c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>Implantação de um modelo de Machine Learning como um serviço Web

O Gerenciamento de Modelos do Azure Machine Learning fornece interfaces para implantar modelos como em serviços Web em contêineres com base em Docker. Você pode implantar os modelos que você cria usando estruturas como Spark, CNTK (Kit de Ferramentas de Serviços Cognitivos da Microsoft), Keras, Tensorflow e Python. 

Este documento aborda as etapas para implantar seus modelos como serviços Web usando a CLI (interface de linha de comando) do Gerenciamento de Modelos do Azure Machine Learning.

## <a name="deploying-web-services"></a>Implantação de serviços Web
Usando as CLIs, você pode implantar os serviços Web para execução no computador local ou em um cluster.

É recomendável começar com uma implantação local. Primeiramente, valide que seu modelo e código funcionam e, em seguida, implante um serviço Web a um cluster para uso em escala de produção. Para obter mais informações sobre como configurar seu ambiente para implantação de cluster, consulte [Configuração de Gerenciamento de Modelos](model-management-configuration.md). 

A seguir estão as etapas de implantação:
1. Use seu modelo de Machine Learning treinado e salvo
2. Crie um esquema para dados de saída e entrada do serviço Web
3. Crie uma imagem de contêiner com base em Docker
4. Crie e implante o serviço Web

### <a name="1-save-your-model"></a>1. Salve seu modelo
Inicie com o arquivo de modelo treinado e salvo, por exemplo, mymodel.pkl. A extensão de arquivo varia de acordo com a plataforma que você usa para treinar e salvar o modelo. 

Por exemplo, você pode usar a biblioteca de Pickle do Python para salvar um modelo treinado em um arquivo. Veja um [exemplo](http://scikit-learn.org/stable/modules/model_persistence.html) de uso do conjunto de dados Iris:

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2. Crie um arquivo schema.json
Esta etapa é opcional. 

Crie um esquema para validar automaticamente a entrada e a saída do serviço Web. As CLIs também usam o esquema para gerar um documento de Swagger para o serviço Web.

Para criar o esquema, importe as bibliotecas a seguir:

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
Em seguida, defina as variáveis de entrada como um dataframe do Spark, dataframe do Pandas ou matriz NumPy. O exemplo a seguir usa uma matriz Numpy:

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```
O exemplo a seguir usa um dataframe do Spark:

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```

O exemplo a seguir usa um dataframe do PANDAS:

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3. Crie um arquivo score.py
Você fornece um arquivo score.py, que carrega o modelo e retorna os resultados de previsão usando o modelo.

O arquivo deve incluir duas funções: inicialização e execução.

Adicione o seguinte código na parte superior do arquivo score.py para habilitar a funcionalidade de coleta de dados que ajuda a coletar dados de entrada e previsão de modelo

    ```
    from azureml.datacollector import ModelDataCollector
    ```

Verifique a seção [Coleta de dados de modelo](how-to-use-model-data-collection.md) para obter mais detalhes sobre como usar esse recurso.

#### <a name="init-function"></a>Função de inicialização
Use a função de inicialização para carregar o modelo salvo.

Exemplo de uma função de inicialização simples ao carregar o modelo:

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Função de execução
A função de execução usa o modelo e os dados de entrada para retornar uma previsão.

Exemplo de uma simples função de execução processando a entrada e retornando o resultado de previsão:

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4. Registrar um modelo
O comando a seguir é usado para registrar um modelo criado na etapa 1 acima,

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5. Criar manifesto
O comando a seguir ajuda a criar um manifesto para o modelo,

```
az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]
```
Você pode adicionar um modelo registrado anteriormente ao manifesto usando o argumento `--model-id` ou `-i` no comando acima. Vários modelos podem ser especificados com argumentos -i adicionais.

### <a name="6-create-an-image"></a>6. Criar uma imagem 
Você pode criar uma imagem com a opção de ter criado seu manifesto antes. 

```
az ml image create -n [image name] -manifest-id [the manifest ID]
```

Ou você pode criar o manifesto e a imagem com um único comando. 

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>Para obter mais detalhes sobre os parâmetros de comando, digite -h no final do comando, por exemplo, az ml image create -h.


### <a name="7-create-and-deploy-the-web-service"></a>7. Crie e implante o serviço Web
Implante o serviço usando o seguinte comando:

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>Você também pode usar um único comando para executar as quatro etapas anteriores. Use -h com o comando de criação de serviço para obter mais detalhes.

### <a name="8-test-the-service"></a>8. Teste o serviço
Use o comando a seguir para obter informações sobre como chamar o serviço:

```
az ml service usage realtime -i <service id>
```

Chame o serviço usando a função de execução do prompt de comando:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

O exemplo a seguir chama um serviço Web Iris de exemplo:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>Próximas etapas
Agora que você testou seu serviço Web para ser executado localmente, você pode implantá-lo em um cluster para uso em larga escala. Para obter detalhes sobre como configurar um cluster para implantação de serviço Web, consulte [Configuração de Gerenciamento de Modelos](model-management-configuration.md). 
