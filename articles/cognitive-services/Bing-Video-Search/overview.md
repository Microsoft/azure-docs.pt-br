---
title: O que é a API da Pesquisa de Vídeo do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba como pesquisar vídeos na Web usando a API de Pesquisa de Vídeo do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 06/26/2019
ms.author: scottwhi
ms.openlocfilehash: 4f3a52590a9a0c50bdc87f87792027333fc5269f
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500163"
---
# <a name="what-is-the-bing-video-search-api"></a>O que é a API da Pesquisa de Vídeo do Bing?

A API de Pesquisa de Vídeo do Bing torna mais fácil adicionar recursos de pesquisa de vídeos a seus serviços e aplicativos. Ao enviar consultas de pesquisa de usuário com a API, você pode obter e exibir vídeos relevantes e de alta qualidade semelhantes ao [Vídeo do Bing](https://www.bing.com/video). Use essa API para resultados da pesquisa que contenham apenas vídeos. A [API de Pesquisa na Web do Bing](../bing-web-search/search-the-web.md) pode retornar outros tipos de conteúdo da Web, incluindo páginas da Web, vídeos, notícias e imagens.

## <a name="bing-video-search-api-features"></a>Recursos da API de Pesquisa de Vídeo do Bing

| Recurso                                                                                                                                                                                 | DESCRIÇÃO                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir os termos de pesquisa em tempo real](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Aprimore sua experiência com aplicativos usando a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para exibir os termos de pesquisa sugeridos à medida que eles são digitados. |
| [Filtrar e restringir os resultados de vídeos](concepts/get-videos.md#filtering-videos)                      | Filtre os vídeos retornados editando parâmetros de consulta.                                                                                                       |
| [Cortar, redimensionar e exibir miniaturas](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Edite e exiba as visualizações de miniatura para os vídeos retornados pela API de Pesquisa de Vídeo do Bing.                                                                                      |
| [Obter vídeos populares](trending-videos.md) | Pesquisar os vídeos mais populares no mundo.                                                                                                          |
| [Obter insights para vídeos](video-insights.md) | Personalize uma pesquisa de vídeos populares em todo o mundo.                                                                                                          |

## <a name="workflow"></a>Fluxo de trabalho

A API da Pesquisa de Vídeo do Bing é um serviço Web RESTful, facilitando a chamada de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON. Você pode usar o serviço usando a [API REST](csharp.md), ou o [SDK](video-search-sdk-quickstart.md).

1. Crie uma [Conta da API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuitamente.
2. Envie uma solicitação para a API, com uma consulta de pesquisa válida.
3. Processe a resposta da API analisando a mensagem JSON retornada.


## <a name="next-steps"></a>Próximas etapas

A [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) da API de Pesquisa de Vídeo do Bing mostra como você pode personalizar uma consulta de pesquisa e pesquisar vídeos na Web.

Quando você estiver pronto para chamar a API, crie uma [Conta de API de serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuitamente.

Use o [início rápido](csharp.md) para se familiarizar rapidamente com sua primeira solicitação de API.

## <a name="see-also"></a>Consulte também

* A página de referência da [API de Pesquisa de Vídeo do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) contém a lista de pontos de extremidade, cabeçalhos e parâmetros de consulta usados para solicitar os resultados da pesquisa.

* Os [Requisitos de exibição e uso do Bing](./useanddisplayrequirements.md) especificam os usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.