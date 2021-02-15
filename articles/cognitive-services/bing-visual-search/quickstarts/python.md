---
title: 'Início Rápido: Obtenha insights de imagem usando a API REST e o Python – Pesquisa Visual do Bing'
titleSuffix: Azure Cognitive Services
description: Saiba como carregar uma imagem usando a API da Pesquisa Visual do Bing e o Python e obtenha insights sobre ela.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-python
ms.openlocfilehash: 5c43df5880c1d54fa8e4f86acaa0b3456d778374
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499037"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Início Rápido: Obtenha insights de imagem usando a API REST da Pesquisa Visual do Bing e o Python

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Use este início rápido para fazer sua primeira chamada à API da Pesquisa Visual do Bing. Este aplicativo Python carrega uma imagem na API e exibe as informações retornadas por ela. Embora esse aplicativo seja escrito em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar o aplicativo

1. Crie um arquivo do Python em seu IDE ou editor favorito e adicione a seguinte instrução `import`:

    ```python
    import requests, json
    ```

2. Crie variáveis para a chave de assinatura, o ponto de extremidade e o caminho para a imagem que você está carregando. Para o valor de `BASE_URI`, você pode usar o ponto de extremidade global no código a seguir ou usar o ponto de extremidade do [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
3. Quando você carrega uma imagem local, os dados do formulário precisam incluir o cabeçalho `Content-Disposition`. Defina o parâmetro `name` como "imagem" e o parâmetro `filename` como o nome de arquivo da sua imagem. O conteúdo do formulário inclui os dados binários da imagem. O tamanho máximo da imagem que pode ser carregada é 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

4. Crie um objeto de dicionário para armazenar as informações de cabeçalho da solicitação. Associe sua chave de assinatura à cadeia de caracteres `Ocp-Apim-Subscription-Key`.

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

5. Crie outro dicionário para conter a imagem, que será aberta e carregada quando você enviar a solicitação.

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analisar a resposta JSON

Crie um método chamado `print_json()` para aceitar a resposta da API e imprima o JSON.

```python
def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
```

## <a name="send-the-request"></a>Enviar a solicitação

Use `requests.post()` para enviar uma solicitação para a API da Pesquisa Visual do Bing. Inclua a cadeia de caracteres para o ponto de extremidade, o cabeçalho e as informações do arquivo. Imprima `response.json()` com `print_json()`.

```python
try:
    response = requests.post(BASE_URI, headers=HEADERS, files=file)
    response.raise_for_status()
    print_json(response.json())
    
except Exception as ex:
    raise ex
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única da Pesquisa Visual](../tutorial-bing-visual-search-single-page-app.md)