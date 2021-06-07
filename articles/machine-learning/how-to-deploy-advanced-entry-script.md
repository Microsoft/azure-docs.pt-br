---
title: Script de entrada de autor para cenários avançados
titleSuffix: Azure Machine Learning entry script authoring
description: Saiba como gravar scripts de entrada de Azure Machine Learning para pré e pós-processamento durante a implantação.
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: gopalv
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: f88b6bd79e1004c108ef868f910a4e5a974cc08a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508530"
---
# <a name="advanced-entry-script-authoring"></a>Criação de scripts de entrada avançados

Este artigo mostra como gravar scripts de entrada para casos de uso especializados.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha um modelo de aprendizado de máquina treinado que pretende implantar com Azure Machine Learning. Para saber mais sobre a implantação de modelo, consulte [este tutorial](how-to-deploy-and-where.md).

## <a name="automatically-generate-a-swagger-schema"></a>Gerar automaticamente um esquema Swagger

Para gerar automaticamente um esquema para o serviço Web, forneça um exemplo de entrada e/ou saída no construtor para um dos objetos de tipo definidos. O tipo e o exemplo são usados para criar automaticamente o esquema. Azure Machine Learning, em seguida, cria uma especificação de [openapi](https://swagger.io/docs/specification/about/) (Swagger) para o serviço Web durante a implantação. 

> [!WARNING]
> Você não deve usar dados confidenciais ou privados para entrada ou saída de exemplo. A página do Swagger para inferência hospedada em AML expõe os dados de exemplo. 

Atualmente, esses tipos têm suporte:

* `pandas`
* `numpy`
* `pyspark`
* Objeto Python padrão

Para usar a geração de esquema, inclua a versão 1.1.0 ou superior do pacote de código-fonte aberto `inference-schema` no arquivo de dependências. Para obter mais informações sobre este pacote, consulte [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . Para gerar a conformidade do Swagger para o consumo de serviço Web automatizado, a função de script Run () de Pontuação deve ter uma forma de API de:
* Um primeiro parâmetro do tipo "StandardPythonParameterType", chamado **entradas** e aninhado.
* Um segundo parâmetro opcional do tipo "StandardPythonParameterType", nomeado **globalparameters**.
* Retornar um dicionário do tipo "StandardPythonParameterType" chamado **Results** e nested.

Defina os formatos de exemplo de entrada e saída `input_sample` nas `output_sample` variáveis e, que representam os formatos de solicitação e resposta para o serviço Web. Use esses exemplos nos decoradores da função de entrada e saída na `run()` função. O exemplo a seguir scikit-Learn usa a geração de esquema.



## <a name="power-bi-compatible-endpoint"></a>Power BI ponto de extremidade compatível 

O exemplo a seguir demonstra como definir a forma de API de acordo com a instrução acima. Esse método tem suporte para o consumo do serviço Web implantado do Power BI. ([Saiba mais sobre como consumir o serviço Web do Power bi](/power-bi/service-machine-learning-integration).)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


# providing 3 sample inputs for schema generation
numpy_sample_input = NumpyParameterType(np.array([[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]],dtype='float64'))
pandas_sample_input = PandasParameterType(pd.DataFrame({'name': ['Sarah', 'John'], 'age': [25, 26]}))
standard_sample_input = StandardPythonParameterType(0.0)

# This is a nested input sample, any item wrapped by `ParameterType` will be described by schema
sample_input = StandardPythonParameterType({'input1': numpy_sample_input, 
                                        'input2': pandas_sample_input, 
                                        'input3': standard_sample_input})

sample_global_parameters = StandardPythonParameterType(1.0) # this is optional
sample_output = StandardPythonParameterType([1.0, 1.0])
outputs = StandardPythonParameterType({'Results':sample_output}) # 'Results' is case sensitive

@input_schema('Inputs', sample_input) 
# 'Inputs' is case sensitive

@input_schema('GlobalParameters', sample_global_parameters) 
# this is optional, 'GlobalParameters' is case sensitive

@output_schema(outputs)

def run(Inputs, GlobalParameters): 
    # the parameters here have to match those in decorator, both 'Inputs' and 
    # 'GlobalParameters' here are case sensitive
    try:
        data = Inputs['input1']
        # data will be convert to target format
        assert isinstance(data, np.ndarray)
        result = model.predict(data)
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> Dados binários (ou seja, imagem)

Se o modelo aceitar dados binários, como uma imagem, você deverá modificar o `score.py` arquivo usado para sua implantação para aceitar solicitações HTTP brutas. Para aceitar dados brutos, use a `AMLRequest` classe em seu script de entrada e adicione o `@rawhttp` decorador à `run()` função.

Aqui está um exemplo de um `score.py` que aceita dados binários:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse
from PIL import Image
import json


def init():
    print("This is init()")
    

@rawhttp
def run(request):
    print("This is run()")
    
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        file_bytes = request.files["image"]
        image = Image.open(file_bytes).convert('RGB')
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the size of the image as the response..
        return AMLResponse(json.dumps(image.size), 200)
    else:
        return AMLResponse("bad request", 500)
```


> [!IMPORTANT]
> A classe `AMLRequest` está no namespace `azureml.contrib`. As entidades neste namespace são alteradas com frequência enquanto trabalhamos para melhorar o serviço. Qualquer coisa neste namespace deve ser considerada uma versão prévia que não tem suporte total da Microsoft.
>
> Se precisar testar isso em seu ambiente de desenvolvimento local, você poderá instalar os componentes usando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```

A `AMLRequest` classe só permite que você acesse os dados brutos postados no score.py, não há nenhum componente do lado do cliente. Em um cliente, você posta dados normalmente. Por exemplo, o código Python a seguir lê um arquivo de imagem e posta os dados:

```python
import requests

uri = service.scoring_uri
image_path = 'test.jpg'
files = {'image': open(image_path, 'rb').read()}
response = requests.post(url, files=files)

print(response.json)
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>CORS (Compartilhamento de Recursos entre Origens)

O compartilhamento de recursos entre origens é uma maneira de permitir que recursos em uma página da Web sejam solicitados de outro domínio. O CORS funciona por meio de cabeçalhos HTTP enviados com a solicitação do cliente e retornado com a resposta do serviço. Para obter mais informações sobre CORS e cabeçalhos válidos, consulte [compartilhamento de recursos entre origens](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) na Wikipédia.

Para configurar sua implantação de modelo para dar suporte a CORS, use a `AMLResponse` classe em seu script de entrada. Essa classe permite que você defina os cabeçalhos no objeto de resposta.

O exemplo a seguir define o `Access-Control-Allow-Origin` cabeçalho para a resposta do script de entrada:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> A classe `AMLResponse` está no namespace `azureml.contrib`. As entidades neste namespace são alteradas com frequência enquanto trabalhamos para melhorar o serviço. Qualquer coisa neste namespace deve ser considerada uma versão prévia que não tem suporte total da Microsoft.
>
> Se precisar testar isso em seu ambiente de desenvolvimento local, você poderá instalar os componentes usando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning roteará apenas as solicitações POST e GET para os contêineres que executam o serviço de pontuação. Isso pode causar erros devido a navegadores que usam solicitações de opções para solicitações CORS de simulação.
> 


## <a name="load-registered-models"></a>Carregar modelos registrados

Há duas maneiras de localizar modelos em seu script de entrada:
* `AZUREML_MODEL_DIR`: Uma variável de ambiente que contém o caminho para o local do modelo.
* `Model.get_model_path`: Uma API que retorna o caminho para o arquivo de modelo usando o nome do modelo registrado.

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR é uma variável de ambiente criada durante a implantação do serviço. Você pode usar essa variável de ambiente para localizar o local dos modelos implantados.

A tabela a seguir descreve o valor de AZUREML_MODEL_DIR dependendo do número de modelos implantados:

| Implantação | Valor da variável de ambiente |
| ----- | ----- |
| Modelo único | O caminho para a pasta que contém o modelo. |
| Vários modelos | O caminho para a pasta que contém todos os modelos. Os modelos estão localizados por nome e versão nesta pasta ( `$MODEL_NAME/$VERSION` ) |

Durante o registro e a implantação do modelo, os modelos são colocados no caminho de AZUREML_MODEL_DIR e seus nomes de filehorários originais são preservados.

Para obter o caminho para um arquivo de modelo em seu script de entrada, combine a variável de ambiente com o caminho de arquivo que você está procurando.

**Exemplo de modelo único**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Exemplo de vários modelos**

Nesse cenário, dois modelos são registrados com o espaço de trabalho:

* `my_first_model`: Contém um arquivo ( `my_first_model.pkl` ) e há apenas uma versão ( `1` ).
* `my_second_model`: Contém um arquivo ( `my_second_model.pkl` ) e há duas versões; `1` e `2` .

Quando o serviço foi implantado, ambos os modelos são fornecidos na operação de implantação:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

Na imagem do Docker que hospeda o serviço, a `AZUREML_MODEL_DIR` variável de ambiente contém o diretório em que os modelos estão localizados.
Nesse diretório, cada um dos modelos está localizado em um caminho de diretório de `MODEL_NAME/VERSION` . Em que `MODEL_NAME` é o nome do modelo registrado e `VERSION` é a versão do modelo. Os arquivos que compõem o modelo registrado são armazenados nesses diretórios.

Neste exemplo, os caminhos seriam `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` e `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

Ao registrar um modelo, você fornece um nome de modelo que é usado para gerenciar o modelo no registro. Use esse nome com o método [Model.get_model_path ()](/python/api/azureml-core/azureml.core.model.model#get-model-path-model-name--version-none---workspace-none-) para recuperar o caminho do arquivo de modelo ou arquivos no sistema de arquivos local. Se você registrar uma pasta ou uma coleção de arquivos, essa API retornará o caminho do diretório que contém esses arquivos.

Ao registrar um modelo, você lhe dá um nome. O nome corresponde ao local em que o modelo é colocado, seja localmente ou durante a implantação do serviço.

## <a name="framework-specific-examples"></a>Exemplos específicos do Framework

Mais exemplos de script de entrada para casos de uso específicos do Machine Learning podem ser encontrados abaixo:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

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
