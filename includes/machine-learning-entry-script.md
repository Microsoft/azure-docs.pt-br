---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325273"
---
O script de entrada recebe os dados enviados para um serviço Web implantado e os transmite ao modelo. Em seguida, ele envia de volta ao cliente a resposta retornada pelo modelo. *O script é específico para seu modelo*. Ele deve entender os dados esperados e retornados pelo modelo.

As duas coisas que você precisa realizar em seu script de entrada são:

1. Carregando seu modelo (usando uma função chamada `init()` )
1. Executando o modelo em dados de entrada (usando uma função chamada `run()` )

Vamos percorrer essas etapas detalhadamente.

### <a name="writing-init"></a>Gravando init () 

#### <a name="loading-a-registered-model"></a>Carregando um modelo registrado

Os modelos registrados são armazenados em um caminho apontado por uma variável de ambiente chamada `AZUREML_MODEL_DIR` . Para obter mais informações sobre a estrutura de diretório exata, consulte [Localizar arquivos de modelo em seu script de entrada](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)

#### <a name="loading-a-local-model"></a>Carregando um modelo local

Se você tiver optado por registrar seu modelo e passar seu modelo como parte do diretório de origem, poderá lê-lo como faria localmente, passando o caminho para o modelo relativo ao script de pontuação. Por exemplo, se você tivesse um diretório estruturado como:

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

Você pode carregar seus modelos com o seguinte código Python:

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>Gravação de execução ()

`run()` é executado toda vez que seu modelo recebe uma solicitação de Pontuação e espera que o corpo da solicitação seja um documento JSON com a seguinte estrutura:

```json
{
    "data": <model-specific-data-structure>
}

```

A entrada para `run()` é uma cadeia de caracteres do Python que contém o que segue a chave "dados".

O exemplo a seguir demonstra como carregar um modelo scikit-Learn registrado e pontua-lo com os dados do numpy:

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Para obter exemplos mais avançados, incluindo dados de geração de esquema Swagger automático e binário (ou seja, imagem), leia [o artigo sobre criação de script de entrada avançada](../articles/machine-learning/how-to-deploy-advanced-entry-script.md)