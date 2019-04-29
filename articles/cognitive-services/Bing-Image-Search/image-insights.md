---
title: Obtenha insights de imagem - API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API de Pesquisa de Imagem do Bing para obter mais informações sobre uma imagem.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: 33dfbe70d75335eca79d32807407b97e32c2dbbf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918134"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Obter insights de imagem com a API de Pesquisa de Imagem do Bing

> [!IMPORTANT]
> Em vez de usar o ponto de extremidade /images/details para obter insights de imagem, você deverá usar a [Pesquisa Visual](../bing-visual-search/overview.md), pois ela fornece insights mais abrangentes.


Cada imagem inclui um token de insights que você pode usar para obter informações sobre a imagem. Por exemplo, você pode obter uma coleção de imagens relacionadas, páginas da Web que incluem a imagem ou uma lista de comerciantes nos quais você pode comprar o produto mostrado na imagem.  

Para obter insights sobre uma imagem, capture o token [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken) da imagem na resposta.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

Em seguida, chame o ponto de extremidade Detalhes da Imagem e defina o parâmetro de consulta [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) com o token em `imageInsightsToken`.  

Para especificar os insights que você deseja obter, defina o parâmetro de consulta `modules`. Para obter todos os insights, defina `modules` como `All`. Para obter somente os insights da legenda e da coleção, defina `modules` como `Caption%2CCollection`. Para obter uma lista completa de possíveis insights, confira [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested). Nem todos os insights estão disponíveis para todas as imagens. A resposta inclui todos os insights que você solicitou se disponíveis.

O exemplo a seguir solicita todos os insights disponíveis para a imagem anterior.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Obtendo insights de uma imagem conhecida

Se você tiver a URL para uma imagem da qual deseja obter insights, use o parâmetro de consulta [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) em vez do parâmetro [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) para especificar a imagem. Ou se tem o arquivo de imagem, envie o binário da imagem no corpo de uma solicitação POST. Se você usar uma solicitação POST, o cabeçalho `Content-Type` precisará ser definido como `multipart/data-form`. Com uma dessas opções, o tamanho da imagem não pode exceder 1 MB.  

Se você tem uma URL para a imagem, o exemplo a seguir mostra como solicitar insights de uma imagem.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Obtendo todos os insights de uma imagem  

Para solicitar todos os insights de uma imagem, defina o parâmetro de consulta [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) como `All`. Para obter as pesquisas relacionadas, a solicitação precisa incluir a cadeia de caracteres de consulta do usuário. Este exemplo mostra o uso do [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) para especificar a imagem.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

O objeto de nível superior é um objeto [ImageInsightsResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse), em vez de um objeto [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Reconhecendo entidades em uma imagem  

O recurso de reconhecimento de entidade identifica as entidades em uma imagem, atualmente, somente pessoas. Para identificar as entidades em uma imagem, defina o parâmetro de consulta [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) como `RecognizedEntities`.  

> [!NOTE]
> Não é possível especificar esse módulo com qualquer outro módulo. Se você especificar esse módulo com outros módulos, a resposta não incluirá entidades reconhecidas.  

O exemplo a seguir mostra como especificar a imagem usando o parâmetro [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl). Lembre-se de codificar os parâmetros de consulta em URL.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

O exemplo a seguir mostra a resposta à solicitação anterior. Como a imagem contém duas pessoas, a resposta identifica uma região para cada pessoa. Nesse caso, as pessoas foram reconhecidas nos grupos CelebrityAnnotations e CelebRecognitionAnnotations. O Bing lista as pessoas em cada grupo com base na probabilidade de que elas correspondam à pessoa da imagem original. A lista está em ordem decrescente de confiança. O grupo CelebRecognitionAnnotations fornece o nível mais alto de confiança de que a correspondência está correta.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

O campo `region` identifica a área da imagem na qual o Bing reconheceu a entidade. Para pessoas, a região representa a face da pessoa.  

Os valores do retângulo são relativos à largura e à altura da imagem original e estão no intervalo de 0,0 a 1,0. Por exemplo, se a imagem tiver 300x200 e a parte superior esquerda da região estiver no ponto (10, 20) e a parte inferior direita estiver no ponto (290, 150), o retângulo normalizado será:  

-   Esquerda: 10 / 300 = 0,03333...  
-   Superior:  20 / 200 = 0,1  
-   Direita: 290 / 300 = 0,9667...  
-   Inferior: 150 / 200 = 0,75  

Use a região que o Bing retorna nas chamadas seguintes de insights. Por exemplo, para obter imagens visualmente semelhantes da entidade reconhecida. Para obter mais informações, confira Cortar imagens para usá-las com imagens visualmente semelhantes e módulos de reconhecimento de entidade. O exemplo a seguir mostra o mapeamento entre os campos de região e os parâmetros de consulta que você usará para cortar as imagens.  

-   A esquerda é mapeada para [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
-   A parte superior é mapeada para [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
-   A direita é mapeada para [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
-   A parte inferior é mapeada para [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Encontrando imagens visualmente semelhantes  

Para encontrar imagens visualmente semelhantes à imagem original, defina o parâmetro de consulta [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) como SimilarImages.  

A solicitação a seguir mostra como obter imagens visualmente semelhantes. A solicitação usa o parâmetro de consulta [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) para identificar a imagem original. Para melhorar a relevância, você deve incluir a cadeia de caracteres de consulta do usuário.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


O exemplo a seguir mostra a resposta à solicitação anterior.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Cortando imagens para usá-las com imagens visualmente semelhantes e módulos de reconhecimento de entidade  

Para especificar a região da imagem usada pelo Bing para determinar se as imagens são visualmente semelhantes ou para executar o reconhecimento de entidade, use os parâmetros de consulta [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal), [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat), [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab) e [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car). Por padrão, o Bing usa a imagem inteira.  

Os parâmetros especificam a parte superior esquerda e a parte inferior direita da região que o Bing usa para comparação. Especifique os valores como frações da largura e da altura da imagem original. Os valores fracionários começam com (0,0, 0,0) na parte superior esquerda e terminam com (1,0, 1,0) na parte inferior direita. Por exemplo, para especificar que a parte superior esquerda começa em um quarto para baixo da parte superior e um quarto do lado esquerdo, defina `cal` como 0,25 e `cat` como 0,25.  

A sequência de chamadas a seguir mostra o efeito da especificação da região de corte. A primeira chamada não inclui o corte e o Bing reconhece duas pessoas em pé lado a lado no meio da imagem.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

A resposta mostra duas entidades reconhecidas.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

A segunda chamada corta a imagem verticalmente para baixo no meio e o Bing reconheceu uma única pessoa no lado direito da imagem.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

A resposta mostra uma entidade reconhecida.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Encontrando produtos visualmente semelhantes  

Para localizar imagens que contêm produtos visualmente similares aos produtos encontrados na imagem original, defina as [módulos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) SimilarProducts parâmetro de consulta.  

A solicitação a seguir mostra como obter imagens de produtos visualmente semelhantes. A solicitação usa o parâmetro de consulta [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) para identificar a imagem original retornada em uma solicitação anterior. Para melhorar a relevância, você deve incluir a cadeia de caracteres de consulta do usuário.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

O exemplo a seguir mostra a resposta à solicitação anterior. A resposta contém uma imagem de um produto semelhante e indica quantos comerciantes oferecem o produto online, se há classificações do produto e o preço mais baixo encontrado (confira o campo `aggregateOffer`).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

Para obter uma lista dos comerciantes que oferecem o produto online (confira o campo [offerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)), chame a API novamente e defina `modules` como ShoppingSources. Em seguida, defina o parâmetro de consulta `insightsToken` com o token encontrado na imagem de resumo de produto.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

O exemplo a seguir é a resposta para a solicitação anterior.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```
