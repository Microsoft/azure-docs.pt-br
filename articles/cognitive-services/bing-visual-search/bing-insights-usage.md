---
title: Exemplos de insights do Bing – Pesquisa Visual do Bing
titleSuffix: Azure Cognitive Services
description: Mostra exemplos de informações de imagem mostrados em Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 94f8d4ea98f42ad919597ef53dc63281825f0e6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610472"
---
# <a name="examples-of-bing-insights-usage"></a>Exemplos de uso de insights do Bing

Este artigo contém exemplos de como o Bing pode usar e exibir informações de imagens no Bing.com.

## <a name="pagesincluding-insight-example"></a>Exemplo de insights do PagesIncluding

Exibe um link para a primeira página da Web e permite que o usuário expandir e recolher a lista de outras páginas da Web que incluem a imagem a seguir:

![Páginas expandidas, incluindo](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>Exemplo de insights de ShoppingSources

O exemplo a seguir mostra como o Bing pode exibir fontes de compras de produtos mostrados na imagem:

![Fontes de compra](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>Exemplo de insight de VisualSearch

A seguir mostra como o Bing pode exibir imagens visualmente semelhantes (consulte **imagens relacionadas** no exemplo):

![Imagens visualmente semelhantes](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Exemplo de insight de receitas

Veja a seguir como o Bing pode exibir receitas para o alimento mostrado na imagem. O exemplo informa ao usuário receitas estão disponíveis:

![Receitas e páginas incluindo](./media/recipes-pages-including.PNG)

 E o link para as receitas quando o usuário expande a lista:

![Páginas de receita expandidas, incluindo](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>Exemplo de insight RelatedSearches

O exemplo a seguir mostra como o Bing pode exibir pesquisas relacionadas de imagens feitas por outros usuários. Se o usuário clicar na imagem, será levado à página de resultados da pesquisa de Bing.com/images para essa consulta relacionada.

![Pesquisas relacionadas para imagens](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Exemplo de insight de entidade

O exemplo a seguir mostra como o Bing pode exibir informações sobre a entidade (pessoa, lugar ou coisa) mostrada na imagem. Se o usuário clica no link de entidade, o usuário é levado à página de resultados de pesquisa de Bing.com para a entidade:

![Entidade mostrada na imagem](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>Exibir outros insights que o usuário pode explorar

O exemplo a seguir mostra como o Bing pode exibir outras informações sobre a imagem que o usuário pode explorar.

![Explore outros insights sobre a imagem](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Caixas delimitadoras e pontos de acesso

Marcas não padrão incluem a caixa delimitadora que identifica a área de interesse na imagem a qual a marca se aplica. Se a caixa delimitadora não identificar a imagem inteira, use a caixa delimitadora para criar um ponto de acesso na imagem. O usuário pode clicar no ponto de acesso para obter informações relacionadas ao conteúdo encontrado no ponto de acesso (ou retângulo). Por exemplo, se a imagem é uma imagem de modo alto, os resultados podem conter marcas (e caixas delimitadoras) para Acessórios mostrado na imagem, como uma bolsa, joias, scarfs e assim por diante. O exemplo a seguir mostra um retângulo de ponto de acesso para os óculos de sol mostrado na imagem:

![Caixas delimitadoras e ponto de acesso](./media/click-to-search.PNG)

## <a name="next-steps"></a>Próximas etapas

Para começar a usar com sua primeira solicitação, consulte os guias de início rápido: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)





