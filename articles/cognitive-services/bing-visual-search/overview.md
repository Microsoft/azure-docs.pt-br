---
title: O que é a API da Pesquisa Visual do Bing?
titleSuffix: Azure Cognitive Services
description: A Pesquisa Visual do Bing fornece detalhes ou insights sobre uma imagem, como imagens semelhantes ou fontes de compra.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 03/27/2019
ms.author: scottwhi
ms.openlocfilehash: 15abbfd8892464f3a2c21a85c07bc5b8e94e47ad
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594695"
---
# <a name="what-is-the-bing-visual-search-api"></a>O que é a API da Pesquisa Visual do Bing?

A API da Pesquisa Visual do Bing retorna informações para uma imagem. Você pode carregar uma imagem ou fornecer uma URL para uma. Informações são imagens visualmente semelhantes, fontes de compras, páginas da Web que incluem a imagem e muito mais. As informações retornadas pela API da Pesquisa Visual do Bing são similares às mostradas em Bing.com/images.

Se você usar a [API de Pesquisa de Imagem do Bing](../bing-image-search/overview.md), poderá usar tokens de informações dos resultados da pesquisa da API da sua Pesquisa Visual do Bing, em vez de carregar uma imagem.

> [!IMPORTANT]
> Se você receber insights de imagem usando a API de Pesquisa de Imagem do Bing, considere mudar para a API da Pesquisa Visual do Bing, que oferece insights mais abrangentes.

## <a name="insights"></a>Insights

Você pode descobrir as seguintes informações usando a Pesquisa Visual do Bing:

| Insight                              | DESCRIÇÃO |
|--------------------------------------|-------------|
| Imagens visualmente semelhantes              | Uma lista de imagens que são visualmente semelhantes à imagem de entrada. |
| Produtos visualmente semelhantes            | Produtos visualmente similares ao produto mostrado.            |
| Fontes de compra                     | Uma lista dos locais em que você pode comprar o item mostrado na imagem de entrada.            |
| Pesquisas relacionadas                     | Pesquisas relacionadas feitas por outras pessoas ou que se baseiam no conteúdo da imagem.            |
| Páginas da Web que incluem a imagem     | Páginas da Web que incluem a imagem de entrada.            |
| Receitas                              | Páginas da Web que incluem receitas para preparação do prato mostrado na imagem de entrada.            |

Além de informações, a Pesquisa Visual do Bing retorna uma variedade de termos (ou seja, marcas) derivados da imagem de entrada. As marcas permitem aos usuários explorar os conceitos encontrados na imagem. Por exemplo, se a imagem de entrada for a de um atleta famoso, uma das marcas poderá ser o nome do atleta e a outra marca poderá ser Esportes. Se a imagem de entrada for de uma torta de maçã, as marcas poderão ser Torta de Maçã, Tortas e Sobremesas.

Os resultados da Pesquisa Visual do Bing também incluem caixas delimitadoras para as regiões de interesse na imagem. Por exemplo, se a imagem contiver várias celebridades, os resultados poderão incluir caixas delimitadoras para cada uma das celebridades reconhecidas. Se o Bing reconhecer um produto ou um item de vestuário na imagem, o resultado poderá incluir uma caixa delimitadora para o item reconhecido.

## <a name="workflow"></a>Fluxo de trabalho

A API da Pesquisa Visual do Bing é um serviço Web RESTful, facilitando a chamada de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON. É possível usar a API REST ou o SDK para o serviço.

1. Crie uma [conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para acessar as APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, [crie uma conta gratuitamente](https://azure.microsoft.com/free/). Você pode obter sua chave de assinatura do [portal do Azure](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) depois de criar sua conta ou do [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis) depois de ativar uma avaliação gratuita.
2. Envie uma solicitação para a API com uma consulta de pesquisa válida.
3. Processe a resposta da API analisando a mensagem JSON retornada.

## <a name="next-steps"></a>Próximas etapas

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/) da API da Pesquisa Visual do Bing.
A demonstração mostra como você pode, de modo rápido, personalizar uma consulta de pesquisa e vasculhar a Web em busca de imagens.

Para uma introdução rápida à primeira solicitação, consulte os guias de início rápido: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Consulte também

* A documentação de referência de [Imagens – Pesquisa Visual](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) descreve definições e informações sobre os pontos de extremidade, os cabeçalhos de solicitação, as respostas e os parâmetros de consulta que podem ser usados para solicitar os resultados da pesquisa com base em imagem.

* Os [Requisitos de exibição e uso da API de Pesquisa do Bing](../bing-web-search/use-display-requirements.md) especificam os usos aceitáveis do conteúdo e as informações obtidas por meio das APIs de pesquisa do Bing.
