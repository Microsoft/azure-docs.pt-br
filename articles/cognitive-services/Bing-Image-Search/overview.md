---
title: O que é a API de Pesquisa de Imagem do Bing?
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa de Imagem do Bing permite o uso de recursos de pesquisa de imagens cognitivas do Bing em seu aplicativo. Ao enviar consultas de pesquisa de usuário com a API, você pode obter e exibir imagens relevantes e de alta qualidade semelhantes às Imagens do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 044cdde8e7c2f79835f4197ae70f7f0dc8d9ccec
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302681"
---
# <a name="what-is-the-bing-image-search-api"></a>O que é a API de Pesquisa de Imagem do Bing?

A API de Pesquisa de Imagem do Bing permite o uso de recursos de pesquisa de imagens do Bing em seu aplicativo. Ao enviar consultas de pesquisa para a API, você pode obter imagens de alta qualidade como em [bing.com/images](https://www.bing.com/images).

Embora a API de Pesquisa de Imagem do Bing forneça resultados de pesquisa apenas com imagens, você pode combinar ou usar outras [APIs de Pesquisa do Bing](../bing-web-search/bing-api-comparison.md) disponíveis para encontrar vários tipos de conteúdo na Web.

## <a name="bing-image-search-features"></a>Recursos da Pesquisa de Imagem do Bing

| Recurso                                                                                                                                                                                 | DESCRIÇÃO                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir os termos de pesquisa em tempo real](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | Aprimore sua experiência com aplicativos usando a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para exibir os termos de pesquisa sugeridos à medida que eles são digitados. |
| [Filtrar e restringir resultados de imagem](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | Filtrar as imagens que o Bing retorna ao editar parâmetros de consulta.                                                                                                       |
| [Cortar, redimensionar e exibir miniaturas](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/resize-and-crop-thumbnails)                                                | Editar e exibir as visualizações de miniaturas para imagens retornadas pela Pesquisa de Imagem do Bing.                                                                                      |
| [Dinamizar e expandir as consultas de pesquisa do usuário](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | Expanda seus recursos de pesquisa por meio da inclusão e exibição dos termos de pesquisa sugeridos pelo Bing para consultas.                                                                    |
| [Obter imagens populares](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | Personalize uma pesquisa para as imagens mais populares no mundo inteiro.                                                                                                          |

## <a name="workflow"></a>Fluxo de trabalho

A API de Pesquisa de Imagem do Bing é um serviço Web RESTful, facilitando a chamada a partir de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON. Você pode usar o serviço usando a [API REST](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?), ou o [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Crie uma [Conta da API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuitamente.
2. Envie uma solicitação para a API, com uma [consulta de pesquisa](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) válida.
3. Processe a resposta da API analisando a mensagem JSON retornada.

## <a name="next-steps"></a>Próximas etapas

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) da API de Pesquisa de Imagem do Bing.
Essa demonstração mostra como você pode, de modo rápido, personalizar uma consulta de pesquisa e vasculhar a Web em busca de imagens.

Quando você estiver pronto para chamar a API, crie uma [Conta da API de serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuitamente.

Para iniciar sua primeira solicitação de API rapidamente, você pode aprender a:

* [Enviar consultas de pesquisa ao Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) usando a API REST ou
* [Solicitar e filtrar](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) as imagens que o Bing retorna usando o SDK.

## <a name="see-also"></a>Consulte também

* [Detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) para as APIs de Pesquisa do Bing. 

* A seção de referência da [API de Pesquisa de Imagem do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) contém informações sobre os pontos de extremidade da API, os cabeçalhos, as respostas da API e os parâmetros de consulta.

* Os [Requisitos de exibição e uso do Bing](./useanddisplayrequirements.md) especificam os usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.

* O artigo [Obtendo imagens da Web com a API de Pesquisa de Imagem do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) descreve como pesquisar e obter imagens da Web.

* O artigo [Enviando e trabalhando com consultas de pesquisa](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) descreve como criar, personalizar e dinamizar consultas de pesquisa.
