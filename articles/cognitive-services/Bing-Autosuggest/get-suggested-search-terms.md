---
title: O que é a Sugestão Automática do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API de Sugestão Automática do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 13606fa5a58106575648a39e5074507f5d3bf471
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405376"
---
# <a name="what-is-bing-autosuggest"></a>O que é a Sugestão Automática do Bing?

Se o aplicativo enviar consultas para uma das APIs de Pesquisa do Bing, você poderá usar a API de Sugestão Automática do Bing para aprimorar a experiência de pesquisa dos usuários. A API de Sugestão Automática do Bing retorna uma lista de consultas sugeridas com base na cadeia de consulta parcial da caixa de pesquisa. Como os caracteres são inseridos na caixa de pesquisa, você pode exibir as sugestões em uma lista suspensa.

## <a name="bing-autosuggest-api-features"></a>Recursos da API de Sugestão Automática do Bing

| Recurso                                                                                                                                                                                 | DESCRIÇÃO                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir os termos de pesquisa em tempo real](concepts/get-suggestions.md) | Aprimore a experiência de seu aplicativo usando a API de Sugestão Automática para exibir termos de pesquisa sugeridos à medida que eles são digitados. |

## <a name="workflow"></a>Fluxo de trabalho

A API de Sugestão Automática do Bing é um serviço Web RESTful, fácil de ser chamado em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar JSON. 

1. Crie uma [Conta da API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuitamente.
2. Envie uma solicitação a essa API sempre que um usuário digitar um novo caractere na caixa de pesquisa de seu aplicativo.
3. Processe a resposta da API analisando a mensagem JSON retornada.

Normalmente, você chamará essa API sempre que o usuário digitar um novo caractere na caixa de pesquisa do aplicativo. Conforme mais caracteres forem inseridos, a API retornará consultas de pesquisa sugeridas mais relevantes. Por exemplo, as sugestões que a API poderá retornar para um único `s` provavelmente serão menos relevantes do que aquelas para `sail`.

O exemplo a seguir mostra uma caixa de pesquisa suspensa com os termos de consulta sugeridos pela API de Sugestão Automática do Bing.

![Lista suspensa da caixa de pesquisa com sugestão automática](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Quando um usuário seleciona uma sugestão na lista suspensa, você pode usá-la para iniciar a pesquisa com uma das APIs de Pesquisa do Bing ou acessar diretamente a página de resultados da pesquisa do Bing.

## <a name="next-steps"></a>Próximas etapas

Para se familiarizar rapidamente com sua primeira solicitação, confira [Fazendo sua primeira consulta](quickstarts/csharp.md).

Familiarize-se com a referência [API de Sugestão Automática do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference). A referência contém a lista de pontos de extremidade, cabeçalhos e parâmetros de consulta que você usaria para solicitar os termos de consulta sugeridos, além das definições dos objetos de resposta.

Saiba como pesquisar na Web usando a [API de Pesquisa na Web do Bing](../bing-web-search/search-the-web.md).

Leia os [Requisitos de exibição e uso do Bing](./useanddisplayrequirements.md) para não violar nenhuma das regras sobre como usar os resultados da pesquisa.
