---
title: 'Início Rápido: Verificar a ortografia com a API REST e o Python – Verificação Ortográfica do Bing'
titleSuffix: Azure Cognitive Services
description: Comece a usar Python e a API REST de Verificação Ortográfica do Bing para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 27e5a336b0a7e6fa0e47e20ad0d3c6204cef7757
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352653"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Início Rápido: Verificar a ortografia com a API REST de Verificação Ortográfica do Bing e o Python

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Use este Início Rápido para fazer sua primeira chamada à API REST de Verificação Ortográfica do Bing. Este aplicativo Python simples envia uma solicitação à API e retorna uma lista de correções sugeridas. 

Embora esse aplicativo seja escrito em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código-fonte desse aplicativo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Pré-requisitos

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar o aplicativo

1. Crie um arquivo Python em seu IDE ou o editor favorito e adicione as seguintes instruções de importação:

   ```python
   import requests
   import json
   ```

2. Crie variáveis para o texto do qual deseja fazer a verificação ortográfica, a chave de assinatura e o ponto de extremidade da Verificação Ortográfica do Bing. É possível usar o ponto de extremidade global no código a seguir ou o ponto de extremidade do [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Criar os parâmetros para a solicitação

1. Crie um dicionário com `text` como a chave e o texto como o valor.

    ```python
    data = {'text': example_text}
    ```

2. Adicione os parâmetros à solicitação: 

   1. Atribua seu código de mercado ao parâmetro `mkt` com o operador `=`. O código de mercado é o código do país/da região em que você faz a solicitação. 

   1. Adicione o parâmetro `mode` com o operador `&` e, em seguida, atribua o modo de verificação ortográfica. O modo pode ser `proof` (captura a maioria dos erros de ortografia/gramática) ou `spell` (captura a maioria dos erros de ortografia, mas não tantos erros de gramática). 
 
    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Adicione um cabeçalho `Content-Type` e a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Enviar a solicitação e ler a resposta

1. Envie a solicitação POST usando a biblioteca de solicitações.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Obtenha a resposta JSON e imprima-a.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>Executar o aplicativo

Se estiver usando a linha de comando, use os seguintes comandos para executar o aplicativo:

```bash
python <FILE_NAME>.py
```

## <a name="example-json-response"></a>Resposta JSON de exemplo

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorials/spellcheck.md)

- [O que é API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência de API de Verificação Ortográfica do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)