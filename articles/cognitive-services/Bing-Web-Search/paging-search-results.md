---
title: Como percorrer os resultados da pesquisa-APIs de Pesquisa do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como paginar os resultados da pesquisa do APIs de Pesquisa do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: 670460759a9495de735da35ae9f3d8388e59e0e5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350612"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Como paginar os resultados da APIs de Pesquisa do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Quando você envia uma chamada para as APIs Web, personalizada, imagem, notícias ou Pesquisa de Vídeo do Bing, o Bing retorna um subconjunto do número total de resultados que podem ser relevantes para a consulta. Para obter o número total estimado de resultados disponíveis, acesse o campo do objeto de resposta `totalEstimatedMatches` . 

Por exemplo: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Paginação por meio de resultados da pesquisa

Para paginar os resultados disponíveis, use `count` os `offset` parâmetros de consulta e ao enviar sua solicitação.  

> [!NOTE]
>
> * A paginação com as APIs de vídeo, imagem e notícias do Bing aplica-se somente às pesquisas gerais de vídeo ( `/video/search` ), de notícias () `/news/search` e de imagem ( `/image/search` ). Não há suporte para paginação por meio de tópicos e categorias de tendências.  
> * O `TotalEstimatedMatches` campo é uma estimativa do número total de resultados da pesquisa para a consulta atual. Quando você define os `count` `offset` parâmetros e, essa estimativa pode ser alterada.

| Parâmetro | Descrição                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Especifica o número de resultados a serem retornados na resposta. Observe que o valor padrão de `count` e o número máximo de resultados que você pode solicitar variam de acordo com a API. Você pode encontrar esses valores na documentação de referência em [próximas etapas](#next-steps). |
| `offset`  | Especifica o número de resultados para ignorar. O `offset` é baseado em zero e deve ser menor que (`totalEstimatedMatches` - `count`).                                           |

Por exemplo, se você quiser exibir 15 resultados por página, defina `count` como 15 e `offset` como 0 para obter a primeira página de resultados. Para cada chamada de API subsequente, você incrementaria `offset` 15. O exemplo a seguir solicita 15 páginas da Web começando no deslocamento 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Se você usar o `count` valor padrão, só precisará especificar o `offset` parâmetro de consulta em suas chamadas à API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Ao usar as APIs de imagem e vídeo do Bing, você pode usar o `nextOffset` valor para evitar resultados de pesquisa duplicados. Obtenha o valor dos `Images` objetos de `Videos` resposta ou e use-o em suas solicitações com o `offset` parâmetro.  

> [!NOTE]
> O API de Pesquisa na Web do Bing retorna resultados da pesquisa que podem incluir páginas da Web, imagens, vídeos e notícias. Ao percorrer os resultados da pesquisa da API de Pesquisa na Web do Bing, você está paginando apenas as [páginas da Web](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)e não outros tipos de resposta, como imagens ou notícias. Os resultados da pesquisa em `WebPage` objetos podem incluir resultados que também apareçam em outros tipos de resposta.
>
> Se você usar o `responseFilter` parâmetro de consulta sem especificar nenhum valor de filtro, não use os `count` `offset` parâmetros e. 

## <a name="next-steps"></a>Próximas etapas

* [Quais são as APIs de Pesquisa na Web do Bing?](bing-api-comparison.md)
* [Referência da API de Pesquisa na Web do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Referência do API de Pesquisa Personalizada do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Referência do API de Pesquisa de Notícias do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Referência do API de Pesquisa de Vídeo do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Referência do API de Pesquisa de Imagem do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)