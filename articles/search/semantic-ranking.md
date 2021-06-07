---
title: Classificação semântica
titleSuffix: Azure Cognitive Search
description: Saiba como o algoritmo de classificação semântica funciona no Azure Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: bf311eb2b2d0ff7a9c17380d2e384bc05c6f05f3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562028"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Classificação semântica no Azure Pesquisa Cognitiva

> [!IMPORTANT]
> Os recursos de pesquisa semântica estão em visualização pública, disponíveis por meio da API REST de visualização e do Portal. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso suplementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não têm a garantia de ter a mesma implementação em disponibilidade geral. Esses recursos são faturáveis. Para obter mais informações, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing).

A classificação semântica é uma extensão do pipeline de execução de consulta que melhora a precisão, reclassificando as correspondências principais de um conjunto de resultados inicial. A classificação semântica é apoiada por grandes redes baseadas em transformações, treinadas para capturar o significado semântico de termos de consulta, em oposição à correspondência lingüística em palavras-chave. Em contraste com o [algoritmo de classificação de similaridade padrão](index-ranking-similarity.md), o classificador semântico usa o contexto e o significado de palavras para determinar a relevância.

A classificação semântica é muito demorada para o recurso e o tempo. Para concluir o processamento dentro da latência esperada de uma operação de consulta, as entradas para o classificador semântico são consolidadas e reduzidas para que as etapas de resumo e reclassificação subjacentes possam ser concluídas o mais rápido possível.

## <a name="pre-processing"></a>Pré-processando

Antes da Pontuação de relevância, o conteúdo deve ser reduzido para um número gerenciável de entradas que podem ser tratadas com eficiência pelo classificador semântico.

1. Primeiro, a redução de conteúdo começa com o conjunto de resultados inicial retornado pelo [algoritmo de classificação de similaridade](index-ranking-similarity.md) padrão usado para pesquisa de palavra-chave. Para qualquer consulta específica, os resultados podem ser alguns documentos, até o limite máximo de 1.000. Como o processamento de um grande número de correspondências levaria muito tempo, apenas os 50 principais progressos para a classificação semântica.

   Seja qual for a contagem de documentos, seja uma ou 50, o conjunto de resultados inicial estabelecerá a primeira iteração do documento corpus para classificação semântica.

1. Em seguida, em Corpus, o conteúdo de cada campo em "searchFields" é extraído e combinado em uma cadeia de caracteres longa.

1. Após a consolidação de cadeias de caracteres, todas as cadeias que são excessivamente longas são cortadas para garantir que o comprimento geral atenda aos requisitos de entrada da etapa de resumo.

   Esse exercício de corte é o motivo pelo qual é importante posicionar os campos concisos primeiro em "searchFields", para garantir que eles sejam incluídos na cadeia de caracteres. Se você tiver documentos muito grandes com campos com texto pesado, qualquer coisa após o limite máximo será ignorada.

Cada documento agora é representado por uma única cadeia de caracteres longa.

> [!NOTE]
> A cadeia de caracteres é composta de tokens, não caracteres ou palavras. A geração de tokens é determinada em parte pela atribuição do analisador em campos pesquisáveis. Se você estiver usando um analisador especializado, como nGram ou EdgeNGram, talvez queira excluir esse campo de searchFields. Para obter informações sobre como as cadeias de caracteres são indexadas, você pode examinar a saída do token de um analisador usando a [API REST do Test Analyzer](/rest/api/searchservice/test-analyzer).

## <a name="extraction"></a>Extração

Após a redução da cadeia de caracteres, agora é possível passar as entradas reduzidas por meio da compreensão da leitura do computador e dos modelos de representação de linguagem para determinar quais frases e frases melhor resumem o documento, em relação à consulta. Essa fase extrai o conteúdo da cadeia de caracteres que avançará para o classificador semântico.

As entradas de resumo são as cadeias de caracteres longas obtidas para cada documento na fase de preparação. De cada cadeia de caracteres, o modelo de resumo encontra uma passagem que é a mais representativa. Essa passagem também constitui uma [legenda semântica](semantic-how-to-query-request.md) para o documento. Cada legenda está disponível em uma versão de texto sem formatação e em uma versão de realce, com frequência menor que 200 palavras por documento.

Uma [resposta semântica](semantic-answers.md) também será retornada se você tiver especificado o parâmetro "respostas", se a consulta foi apresentada como uma pergunta, e se uma passagem pode ser encontrada na cadeia de caracteres longa que provavelmente fornece uma resposta para a pergunta.

## <a name="semantic-ranking"></a>Classificação semântica

1. As legendas são avaliadas para relevância conceitual e semântica, em relação à consulta fornecida.

   O diagrama a seguir fornece uma ilustração do que significa "relevância semântica". Considere o termo "capital", que poderia ser usado no contexto de finanças, legislação, geografia ou gramática. Se uma consulta incluir termos do mesmo espaço de vetor (por exemplo, "capital" e "investimento"), um documento que também inclui tokens no mesmo cluster resultará em uma pontuação maior do que não.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Representação de vetor para o contexto" border="true":::

1. O @search.rerankerScore é atribuído a cada documento com base na relevância semântica da legenda.

1. Depois que todos os documentos são pontuados, eles são listados em ordem decrescente por Pontuação e incluídos na carga de resposta da consulta. A carga inclui respostas, texto sem formatação e legendas realçadas e todos os campos marcados como recuperáveis ou especificados em uma cláusula SELECT.

## <a name="next-steps"></a>Próximas etapas

A classificação semântica é oferecida em camadas padrão, em regiões específicas. Para obter mais informações sobre a disponibilidade e a inscrição, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing). Um novo tipo de consulta habilita as estruturas de classificação e resposta da pesquisa semântica. Para começar, [crie uma consulta semântica](semantic-how-to-query-request.md).

Como alternativa, examine os artigos a seguir sobre a classificação padrão. A classificação semântica depende do classificador de similaridade para retornar os resultados iniciais. Conhecer a execução e a classificação da consulta fornecerá uma ampla compreensão de como o processo inteiro funciona.

+ [Pesquisa de texto completo no Azure Pesquisa Cognitiva](search-lucene-query-architecture.md)
+ [Similaridade e pontuação no Azure Cognitive Search](index-similarity-and-scoring.md)
+ [Analisadores para processamento de texto no Azure Pesquisa Cognitiva](search-analyzers.md)