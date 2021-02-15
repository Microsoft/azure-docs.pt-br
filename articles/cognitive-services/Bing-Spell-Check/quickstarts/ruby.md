---
title: 'Início Rápido: Verificar a ortografia com a API REST e o Ruby – Verificação Ortográfica do Bing'
titleSuffix: Azure Cognitive Services
description: Comece a usar Ruby e a API REST de Verificação Ortográfica do Bing para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: 46b62b5721eca622fcf2795a395653071bb88c95
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352636"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>Início Rápido: Verificar a ortografia com a API REST de Verificação Ortográfica do Bing e o Ruby

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Use este Início Rápido para fazer sua primeira chamada à API REST de Verificação Ortográfica do Bing usando o Ruby. Este aplicativo simples envia uma solicitação à API e retorna uma lista de correções sugeridas. 

Embora esse aplicativo seja escrito no Ruby, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código-fonte desse aplicativo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb)

## <a name="prerequisites"></a>Pré-requisitos

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) ou posterior.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um arquivo Ruby em seu editor ou IDE favorito e adicione os seguintes requisitos: 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. Crie variáveis para a chave de assinatura, o URI do ponto de extremidade e o caminho. É possível usar o ponto de extremidade global no código a seguir ou o ponto de extremidade do [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso. Crie os parâmetros de solicitação:

   1. Atribua seu código de mercado ao parâmetro `mkt` com o operador `=`. O código de mercado é o código do país/da região em que você faz a solicitação. 

   1. Adicione o parâmetro `mode` com o operador `&` e, em seguida, atribua o modo de verificação ortográfica. O modo pode ser `proof` (captura a maioria dos erros de ortografia/gramática) ou `spell` (captura a maioria dos erros de ortografia, mas não tantos erros de gramática). 

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>Enviar uma solicitação de verificação ortográfica

1. Crie um URI com base no URI do host, no caminho e na cadeia de caracteres de parâmetros. Defina a consulta para que ela contenha o texto do qual deseja verificar a ortografia.

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. Crie uma solicitação usando o URI construído anteriormente. Adicione a chave ao cabeçalho `Ocp-Apim-Subscription-Key`.

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. Enviar a solicitação.

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. Obtenha a resposta JSON e imprima-a no console. 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>Executar o aplicativo

Compile e execute seu projeto. Se estiver usando a linha de comando, use os seguintes comandos para executar o aplicativo:

   ```bash
   ruby <FILE_NAME>.rb
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