---
title: 'Início Rápido: Visualização de URL do Projeto, Python'
titlesuffix: Azure Cognitive Services
description: Exemplo de script para começar rapidamente a usar a visualização de URL do Projeto com Python.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 19a226fb580f3d4215b7c3f04f17c3f92505987e
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697973"
---
# <a name="quickstart-url-preview-with-python"></a>Início Rápido: Visualização de URL com Python

O exemplo de Python a seguir cria uma visualização de Url para o site SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Pré-requisitos

Obtenha uma chave de acesso para a avaliação gratuita de [Laboratórios dos Serviços Cognitivos](https://labs.cognitive.microsoft.com/en-us/project-answer-search)

Este exemplo usa Python 3.6.

## <a name="code-scenario"></a>Cenário do código 

O código a seguir cria uma visualização de URL.
Ele é implementado nas etapas a seguir:
1. Declare variáveis para especificar o ponto de extremidade por host e caminho.
2. Especifique a URL de consulta para visualizar e adicione o parâmetro de consulta.  
3. Defina o parâmetro de consulta.
4. Defina a função Search que cria a solicitação e adicione o cabeçalho *Ocp-Apim-Subscription-Key*.
5. Adicione o cabeçalho *Ocp-Apim-Subscription-Key*. 
6. Faça a conexão e envie a solicitação.
7. Imprima os resultados JSON.

O código completo para esta demonstração é:

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
```
## <a name="next-steps"></a>Próximas etapas
- [Início Rápido do C#](csharp.md)
- [Início Rápido do Java](java-quickstart.md)
- [Início rápido em JavaScript](javascript.md)
- [Início rápido de URL em Node](node-quickstart.md)
