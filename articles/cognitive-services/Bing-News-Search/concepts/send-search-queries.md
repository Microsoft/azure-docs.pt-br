---
title: Enviando consultas para a API de Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre como enviar solicitações de pesquisa para a API de Pesquisa de Notícias do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: cfa9cd23e19788d847fd9177d70ee2af89c69272
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423837"
---
# <a name="sending-queries-to-the-bing-news-search-api"></a>Enviando consultas para a API de Pesquisa de Notícias do Bing

A API de Pesquisa de Notícias do Bing permite que você pesquise na Web itens de notícias relevantes. Use este artigo para saber mais sobre como enviar consultas de pesquisa para a API.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="sending-a-request"></a>Enviando uma solicitação

Para obter resultados da pesquisa somente de notícias, envie uma solicitação GET para o seguinte ponto de extremidade:

```http
https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

A solicitação precisa usar o protocolo HTTPS.

Recomendamos que todas as solicitações sejam originadas de um servidor. A distribuição da chave como parte de um aplicativo cliente fornece mais oportunidades para um terceiro mal-intencionado acessá-lo. Além disso, fazer chamadas em um servidor fornece um ponto único de atualização para versões futuras da API.

A solicitação precisa especificar o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query), que contém o termo de pesquisa do usuário. Embora seja opcional, a solicitação também deve especificar o parâmetro de consulta [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#mkt), que identifica o mercado de onde você deseja obter os resultados. Para obter uma lista de parâmetros de consulta opcionais, como `freshness` e `textDecorations`, confira [Parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters). Todos os valores de parâmetro de consulta precisam ser codificados em URL.

A solicitação precisa especificar o cabeçalho [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#subscriptionkey). Embora isso seja opcional, você é incentivado a especificar também os seguintes cabeçalhos:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#location)

Os cabeçalhos de IP e local do cliente são importantes para retornar o conteúdo com reconhecimento de local.

Para obter uma lista de todos os cabeçalhos de solicitação e resposta, confira [Cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#headers).

O exemplo a seguir mostra uma solicitação de notícias que inclui todos os cabeçalhos e parâmetros de consulta sugeridos. Se for a primeira vez que você chama qualquer uma das APIs do Bing, não inclua o cabeçalho da ID de cliente. Só inclua a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID de cliente para a combinação de usuário e dispositivo.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="bing-news-search-api-response"></a>Resposta da API de Pesquisa de Notícias do Bing

O exemplo a seguir mostra a resposta à solicitação anterior. O exemplo também mostra os cabeçalhos de resposta.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 994974CC8D994C95A5C31387296A510A
X-MSEdge-ClientID: 3358F499A06F6A562B88F8F4A1236BC0
BingAPIs-Market: en-US

{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=sailing%2bdinghies",
   "queryContext": {
      "originalQuery": "sailing+dinghies",
      "adultIntent": false
   },
   "totalEstimatedMatches": 60000,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=sailing%2bdinghies"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=sailing%2bdinghies&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Global single-handed sailing dinghy market research illuminated by new report",
         "url": "https:\/\/www.whatech.com\/market-research\/consumer\/506504-global-single-handed-sailing-dinghy-market-research-illuminated-by-new-report",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.784C1E1F53BA96E21EAD9382C8D47855&pid=News",
               "width": 490,
               "height": 280
            }
         },
         "description": "With this Single-Handed Sailing Dinghy Market report, one is sure to keep up with information on the dogged competition for market share and control, between elite manufacturers. It also features, price, production, and revenue. Global Single-Handed ...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/ba5cfb68-96fd-4529-bdd3-060f6fca43f1",
               "name": "Single-handed sailing"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "WhaTech",
               "image": {
                  "thumbnail": {
                     "contentUrl": "https:\/\/www.bing.com\/th?id=AR_7f43f11eb94501973a55b64ec0721b55&pid=news"
                  }
               }
            }
         ],
         "datePublished": "2018-09-12T05:30:00.0000000Z",
         "category": "Business"
      },
      {
         "name": "Boat collides with luxury craft at sail launch",
         "url": "https:\/\/www.sunshinecoastdaily.com.au\/news\/boat-capsizes-in-display-at-the-launch-of-the-sout\/3523096\/",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.C2D335BDDAB1844EAF7775B121433119&pid=News",
               "width": 460,
               "height": 258
            }
         },
         "description": "The annual spectacle is never a dull affair with a flotilla of club vessels - from small sailing dinghy's to powerboats - completing a tight turn in the marina right in front of the club house. A strong northerly made conditions extra tricky for the 70 ...", "provider": [{"_type": "Organization", "name": "Sunshine Coast Daily", "image": {"thumbnail": {"contentUrl": "https:\/\/www.bing.com\/th?id=AR_73ce1412dfbebb5c4f539ac7c5e31429&pid=news"}}}], "datePublished": "2018-09-16T22:03:00.0000000Z", "category": "Sports"}, {"name": "Boat capsizes in display at the launch of the Southport Yacht Club sailing season", "url": "https:\/\/www.goldcoastbulletin.com.au\/news\/gold-coast\/boat-capsizes-in-display-at-the-launch-of-the-southport-yacht-club-sailing-season\/news-story\/6c0fb5366ed6a1abe93e85825598aee8", "image": {"thumbnail": {"contentUrl": "https:\/\/www.bing.com\/th?id=ON.247A9B91E2888976DA8F4776DC9B2372&pid=News", "width": 700, "height": 393}}, "description": "The annual spectacle is never a dull affair with a flotilla of club vessels - from small sailing dinghy’s to powerboats - completing a tight turn in the marina right in front of the club house. A strong northerly made conditions extra tricky for the 70 ...", "about": [{"readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf2ea25d-2faf-1486-6db8-0e4c16cabed7", "name": "Southport"}], "provider": [{"_type": "Organization", "name": "Gold Coast Sun"}], "datePublished": "2018-09-16T14:05:00.0000000Z", "category": "Sports"}, {"name": "Caribbean Dinghy Championship 2018", "url": "https:\/\/www.sailingscuttlebutt.com\/2018\/09\/06\/caribbean-dinghy-championship-2018\/", "image": {"thumbnail": {"contentUrl": "https:\/\/www.bing.com\/th?id=ON.C9D13963192B123805E8BD0707370BFF&pid=News", "width": 620, "height": 350}}, "description": "After storms forced the cancellation of the 2017 Caribbean Dinghy Championship, the event returns in 2018 ... Launched in 1997, Scuttlebutt provides sailing news with a North American focus. Look for the latest information to be posted on the website ...", "about": [{"readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/7ee7f148-7ed7-b73e-7461-900ee716ef61", "name": "Caribbean"}], "provider": [{"_type": "Organization", "name": "Scuttlebutt Sailing News", "image": {"thumbnail": {"contentUrl": "https:\/\/www.bing.com\/th?id=AR_bce37315275c769315a43e2792dab150&pid=news"}}}], "datePublished": "2018-09-06T18:29:00.0000000Z"}, {"name": "Severna Park sailor places fifth at Optimist World Championship", "url": "http:\/\/www.capitalgazette.com\/sports\/ac-cs-tommy-sitzmann-feature-20180915-story.html", "description": "When the sailing conditions are right ... he could be a top contender on an international level by placing fifth at the United States Optimist Dinghy Association Team Trials, held April 26-29 out of Key Biscayne Yacht Club. That in itself was a massive ...", "about": [{"readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/1883f6c3-f9bf-357c-01b1-96120862af67", "name": "Severna Park"}, {"readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/5254c06e-0891-5637-4e51-45fa4eec4ca2", "name": "Optimist dinghy"}], "provider": [{"_type": "Organization", "name": "Capital Gazette", "image": {"thumbnail": {"contentUrl": "https:\/\/www.bing.com\/th?id=AR_ef9a3ded8bb90aef15d34f327e53e2ec&pid=news"}}}], "datePublished": "2018-09-15T22:04:00.0000000Z", "category": "Sports"}, {"name": "A Dorset dinghy celebrates 60 years on the water", "url": "https:\/\/www.bbc.co.uk\/programmes\/p06kk2zv", "description": "If you learned to sail as a child, or if you have children or grandchildren who sail then you will know about the AB sailing dinghy. The simple little boat was first designed and built in Poole in 1958 and this year it's celebrating its 60th anniversary.",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/248ebd80-8904-8a43-be23-3cd065a30350",
               "name": "Dorset"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/84fb5887-86ed-ecdb-55b7-718ba1cdefb8",
               "name": "BBC Radio Solent"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/1cf31e75-0340-8af2-1efc-e2cd0d0fa253",
               "name": "Water"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "BBC",
               "image": {
                  "thumbnail": {
                     "contentUrl": "https:\/\/www.bing.com\/th?id=AR_b639c1691c4fa767d85fd87b7042f9e6&pid=news"
                  }
               }
            }
         ],
         "datePublished": "2018-09-07T10:29:00.0000000Z"
      },
      {
         "name": "Angst as Sailing misses Paris 2024 Paralympic inclusion",
         "url": "https:\/\/www.sail-world.com\/news\/210021\/Four-more-years--Para-Sailing-misses-Paris-cut",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0C996DAB550F7202E8C53BBCFF51B9F3&pid=News",
               "width": 700,
               "height": 466
            }
         },
         "description": "World Sailing has also put the second oldest Olympic class, the two-handed 470 on the skids, with moves to drop the men's and women's two-person dinghy events and replace them with a single mixed gender doublehander, with the class yet to be selected.",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/819fda72-a524-6f49-5e39-5d559e2a5969",
               "name": "2024 Summer Olympics"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/24256509-f062-baaa-3ed3-3ec1a7a2d38c",
               "name": "Sailing"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Sail World News"
            }
         ],
         "datePublished": "2018-09-15T06:24:00.0000000Z"
      },
      {
         "name": "West Sacramento sailing club uses warm weather for wind surfing",
         "url": "http:\/\/www.dailydemocrat.com\/article\/NI\/20180912\/NEWS\/180919960",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.6BC8EBF8DEFBA55247EFEEAF545DDE15&pid=News",
               "width": 384,
               "height": 400
            }
         },
         "description": "Once comfortable, newcomers can join in on the many hosted races, like the annual Delta Ditch Dinghy Race that begins in Rio Vista. “It truly is the best kept secret in Sacramento,” Glovin said. “It’s the best sailing around. You get the consistent ...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/aaf3c53a-9932-f043-c1ca-9cae2d19f9cf",
               "name": "West Sacramento"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Woodland Daily Democrat",
               "image": {
                  "thumbnail": {
                     "contentUrl": "https:\/\/www.bing.com\/th?id=AR_0b1348a0984c3f1177b2fff3b8f27549&pid=news"
                  }
               }
            }
         ],
         "datePublished": "2018-09-12T22:55:00.0000000Z"
      },
      {
         "name": "SAILING: Sailing teams begin busy 2018–19",
         "url": "https:\/\/yaledailynews.com\/blog\/2018\/09\/11\/sailing-teams-begin-busy-2018-19\/",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.ACEBE0C4D4E85B3E3DE149E8D31BC026&pid=News",
               "width": 700,
               "height": 524
            }
         },
         "description": "On the first day, Arcot and Ware sailed six of seven races in the Firefly dinghy, a boat with which they had no previous experience. Next weekend, both coed and women’s sailing head to Boston College. The coed team also scatters to MIT and Bowdoin while ...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/dac17b74-6cf9-27ba-27ff-8dc6f57b7a31",
               "name": "Sailing"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/dacda48e-35d6-bcad-0e81-96951267dfda",
               "name": "Sailing"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Yale Daily News",
               "image": {
                  "thumbnail": {
                     "contentUrl": "https:\/\/www.bing.com\/th?id=AR_6b2f0dfab97da868e0d3df07012f44a4&pid=news"
                  }
               }
            }
         ],
         "datePublished": "2018-09-11T04:31:00.0000000Z",
         "category": "Sports"
      },
      {
         "name": "Tasar UK National Championship at Hayling Island Sailing Club",
         "url": "https:\/\/www.sail-world.com\/news\/209889",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.6A28956676E79790BED64ACD9498F7AF&pid=News",
               "width": 700,
               "height": 497
            }
         },
         "description": "Under the beautiful backdrop of the Spinnaker Tower, the Tasar fleet, who had the pleasure of sharing the sailing space with 43 Contenders, had fun milling around the Committee Boat waiting to start. The sight of so many dinghies is always gorgeous ...",
         "provider": [
            {
               "_type": "Organization",
               "name": "Sail World News"
            }
         ],
         "datePublished": "2018-09-11T15:30:00.0000000Z",
         "category": "Sports"
      }
   ]
}

```

## <a name="next-steps"></a>Próximas etapas

* [O que é a Pesquisa de Notícias do Bing?](../search-the-web.md). 
* [Obter as principais notícias de hoje](search-for-news.md#get-todays-top-news)
* [Obter notícias por categoria](search-for-news.md#get-news-by-category)
* [Obter as notícias populares](search-for-news.md#get-trending-news)
