---
title: Exemplos de insights do Bing – Pesquisa Visual do Bing
titleSuffix: Azure Cognitive Services
description: Este artigo contém exemplos de como Pesquisa Visual do Bing pode usar e exibir informações de imagem no Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: e0a3bdc7e68301c067b7e1ca80a3e708522e0df1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493348"
---
# <a name="examples-of-bing-insights-usage"></a>Exemplos de uso de insights do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Este artigo contém exemplos de como o Bing pode usar e exibir informações de imagens no Bing.com.

## <a name="pagesincluding-insight-example"></a>Exemplo de insights do PagesIncluding

O seguinte exibe um link para a primeira página da Web e permite que o usuário expanda e recolha a lista de outras páginas da Web que incluem a imagem:

![Páginas expandidas, incluindo](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Exemplo de insights de ShoppingSources

O código a seguir mostra como o Bing pode exibir fontes de compras para produtos vistos na imagem:

![Fontes de compra](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Exemplo de insight de VisualSearch

Veja a seguir como o Bing pode exibir imagens visualmente semelhantes (consulte as **imagens relacionadas** no exemplo):

![Imagens visualmente semelhantes](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Exemplo de insight de receitas

Veja a seguir como o Bing pode exibir receitas para o alimento mostrado na imagem. O exemplo permite que o usuário saiba que há receitas disponíveis:

![Receitas e páginas incluindo](./media/recipes-pages-including.PNG)

 E fornece o link para as receitas quando o usuário expande a lista:

![Páginas de receita expandidas, incluindo](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>Exemplo de insight RelatedSearches

O exemplo a seguir mostra como o Bing pode exibir pesquisas relacionadas de imagens feitas por outros usuários. Se o usuário clicar na imagem, será levado à página de resultados da pesquisa de Bing.com/images para essa consulta relacionada.

![Pesquisas relacionadas para imagens](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Exemplo de insight de entidade

O exemplo a seguir mostra como o Bing pode exibir informações sobre a entidade (pessoa, lugar ou coisa) mostrada na imagem. Se o usuário clicar no link de entidade, o usuário será levado para a página de resultados da pesquisa do Bing.com para a entidade:

![Entidade mostrada na imagem](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Exibir outros insights que o usuário pode explorar

O exemplo a seguir mostra como o Bing pode exibir outras informações sobre a imagem que o usuário pode explorar.

![Explore outros insights sobre a imagem](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Caixas delimitadoras e pontos de acesso

Marcas não padrão incluem a caixa delimitadora que identifica a área de interesse na imagem a qual a marca se aplica. Se a caixa delimitadora não identificar a imagem inteira, use a caixa delimitadora para criar um ponto de acesso na imagem. O usuário pode clicar no ponto de acesso para obter informações relacionadas ao conteúdo encontrado no ponto de acesso (ou retângulo). Por exemplo, se a imagem for uma imagem de alto nível, os resultados poderão conter marcas (e caixas delimitadoras) para acessórios mostrados na imagem, como bolsa, joia, Scarfs e assim por diante. O exemplo a seguir mostra um retângulo de ponto de acesso para o óculos mostrado na imagem:

![Caixas delimitadoras e ponto de acesso](./media/click-to-search.PNG)

## <a name="next-steps"></a>Próximas etapas

Para começar a usar sua primeira solicitação, consulte os guias de início rápido:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)