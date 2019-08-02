---
title: 'Início Rápido: executar uma pesquisa com Python – API de Pesquisa na Web do Bing'
titleSuffix: Azure Cognitive Services
description: Use este Início Rápido para enviar solicitações para a API REST de Pesquisa na Web do Bing usando o Python e receba uma resposta JSON
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 4f7f1c28423e67ff9ff09385a5e0c7675e4a6049
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338834"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Início Rápido: usar Python para chamar a API de Pesquisa na Web do Bing  

Use este Início Rápido para fazer sua primeira chamada à API de Pesquisa na Web do Bing e receber a resposta JSON. Este aplicativo Python envia uma solicitação de pesquisa à API e exibe a resposta. Embora esse aplicativo seja escrito em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Este exemplo é executado como um Jupyter Notebook em [MyBinder](https://mybinder.org). Selecione a notificação do associador de inicialização:

[![Associador](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 2.x ou 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Definir variáveis

Substitua o valor `subscription_key` por uma chave de assinatura válida da sua conta do Azure.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Declare o ponto de extremidade da API de Pesquisa na Web do Bing. Caso encontre erros de autorização, verifique novamente esse valor em relação ao ponto de extremidade de pesquisa do Bing no painel do Azure.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Fique à vontade para personalizar a consulta de pesquisa substituindo o valor para `search_term`.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Fazer uma solicitação

Este bloco usa a biblioteca `requests` para chamar a API de Pesquisa na Web do Bing e retornar os resultados como um objeto JSON. A chave de API é passada no dicionário `headers` e os parâmetros de consulta e termo de pesquisa são passados no dicionário `params`. Veja a documentação [API de Pesquisa na Web do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) para obter uma lista completa de opções e parâmetros.

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Formatar e exibir a resposta

O objeto `search_results` inclui os resultados da pesquisa e metadados como consultas e as páginas relacionadas. Este código usa a biblioteca `IPython.display` para formatar e exibir a resposta no seu navegador.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Exemplo de código no GitHub

Se você quiser executar esse código localmente, o [exemplo completo está disponível no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única de pesquisa na Web do Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
