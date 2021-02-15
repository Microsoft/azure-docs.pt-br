---
title: O que é a Sugestão Automática do Bing?
titleSuffix: Azure Cognitive Services
description: A API de Sugestão Automática do Bing retorna uma lista de consultas sugeridas com base na cadeia de consulta parcial da caixa de pesquisa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 014705cf628aa2d2df43d0964ff843fae09595ac
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352760"
---
# <a name="what-is-bing-autosuggest"></a>O que é a Sugestão Automática do Bing?

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisaram ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Se o aplicativo enviar consultas para uma das APIs de Pesquisa do Bing, você poderá usar a API de Sugestão Automática do Bing para aprimorar a experiência de pesquisa dos usuários. A API de Sugestão Automática do Bing retorna uma lista de consultas sugeridas com base na cadeia de consulta parcial da caixa de pesquisa. Como os caracteres são inseridos na caixa de pesquisa, você pode exibir as sugestões em uma lista suspensa.

## <a name="bing-autosuggest-api-features"></a>Recursos da API de Sugestão Automática do Bing

| Recurso                                                                                                                                                                                 | Descrição                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir os termos de pesquisa em tempo real](concepts/get-suggestions.md) | Aprimore a experiência de seu aplicativo usando a API de Sugestão Automática para exibir termos de pesquisa sugeridos à medida que eles são digitados. |

## <a name="workflow"></a>Fluxo de trabalho

A API de Sugestão Automática do Bing é um serviço Web RESTful, fácil de ser chamado em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar JSON.

1. Crie uma [Conta da API dos Serviços Cognitivos](../cognitive-services-apis-create-account.md) com acesso às APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuitamente.
2. Envie uma solicitação a essa API sempre que um usuário digitar um novo caractere na caixa de pesquisa de seu aplicativo.
3. Processe a resposta da API analisando a mensagem JSON retornada.

Normalmente, você chamará essa API sempre que o usuário digitar um novo caractere na caixa de pesquisa do aplicativo. Conforme mais caracteres forem inseridos, a API retornará consultas de pesquisa sugeridas mais relevantes. Por exemplo, as sugestões que a API poderá retornar para um único `s` provavelmente serão menos relevantes do que aquelas para `sail`.

O exemplo a seguir mostra uma caixa de pesquisa suspensa com os termos de consulta sugeridos pela API de Sugestão Automática do Bing.

![Lista suspensa da caixa de pesquisa com sugestão automática](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Quando um usuário seleciona uma sugestão na lista suspensa, você pode usá-la para iniciar a pesquisa com uma das APIs de Pesquisa do Bing ou acessar diretamente a página de resultados da pesquisa do Bing.

## <a name="next-steps"></a>Próximas etapas

Para se familiarizar rapidamente com sua primeira solicitação, confira [Fazendo sua primeira consulta](quickstarts/csharp.md).

Familiarize-se com a referência [API de Sugestão Automática do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference). A referência contém a lista de pontos de extremidade, cabeçalhos e parâmetros de consulta que você usaria para solicitar os termos de consulta sugeridos, além das definições dos objetos de resposta.

Visite a [página do hub da API de Pesquisa do Bing](../bing-web-search/overview.md) para explorar as outras APIs disponíveis.


Saiba como pesquisar na Web usando a [API de Pesquisa na Web do Bing](../bing-web-search/overview.md) e explore as outras [APIs de Pesquisa do Bing](../bing-web-search/index.yml).

Leia os [Requisitos de exibição e uso do Bing](../bing-web-search/use-display-requirements.md) para não violar nenhuma das regras sobre como usar os resultados da pesquisa.