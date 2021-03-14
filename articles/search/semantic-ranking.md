---
title: Classificação semântica
titleSuffix: Azure Cognitive Search
description: Descreve o algoritmo de classificação semântica no Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: e3078c8f71f8862cacad552bb3176c08530e79bb
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418837"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Classificação semântica no Azure Pesquisa Cognitiva

> [!IMPORTANT]
> Os recursos de pesquisa semântica estão em visualização pública, disponíveis somente por meio da API REST de visualização. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso suplementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não têm a garantia de ter a mesma implementação em disponibilidade geral. Para obter mais informações, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing).

A classificação semântica é uma extensão do pipeline de execução de consulta que melhora a precisão e a RECALL, reclassificando as principais correspondências de um conjunto de resultados inicial. A classificação semântica é apoiada por uma máquina aprofundada que lê modelos de compreensão, treinados para consultas expressas em linguagem natural, em oposição à correspondência lingüística em palavras-chave. Em contraste com o [algoritmo de classificação de similaridade padrão](index-ranking-similarity.md), o classificador semântico usa o contexto e o significado de palavras para determinar a relevância.

## <a name="how-semantic-ranking-works"></a>Como funciona a classificação semântica

A classificação semântica é muito demorada para o recurso e o tempo. Para concluir o processamento dentro da latência esperada de uma operação de consulta, o modelo usa como entrada apenas os principais documentos 50 retornados do algoritmo de [classificação de similaridade](index-ranking-similarity.md)padrão. Os resultados da classificação inicial podem incluir mais de 50 correspondências, mas apenas a primeira 50 será reclassificada semanticamente. 

Para classificação semântica, o modelo usa a compreensão de leitura do computador e o aprendizado de transferência para pontuar novamente os documentos com base em quão bem cada um corresponde à intenção da consulta.

1. Para cada documento, o classificador semântico avalia os campos no parâmetro searchFields em ordem, consolidando o conteúdo em uma grande cadeia de caracteres.

1. Em seguida, a cadeia de caracteres é cortada para garantir que o comprimento geral não tenha mais de 8.000 tokens. Se você tiver documentos muito grandes, com um campo de conteúdo ou campo de merged_content que tenha muitas páginas de conteúdo, qualquer coisa após o limite de token será ignorada.

1. Cada um dos documentos 50 agora é representado por uma única cadeia de caracteres longa. Essa cadeia de caracteres é enviada para o modelo de resumo. O modelo de resumo produz legendas (e respostas), usando a compreensão da leitura do computador para identificar passagens que aparecem para resumir o conteúdo ou responder à pergunta. A saída do modelo de resumo é uma cadeia de caracteres mais reduzida, que tem no máximo 128 tokens.

1. A cadeia de caracteres menor se torna a legenda do documento e representa as passagens mais relevantes encontradas na cadeia de caracteres maior. O conjunto de 50 (ou menos) legendas é classificado em ordem de relevância. 

A relevância conceitual e semântica é estabelecida por meio de agrupamentos de termos e de representação de vetor. Enquanto um algoritmo de similaridade de palavra-chave pode dar um peso igual a qualquer termo na consulta, o modelo semântico foi treinado para reconhecer a interdependência e as relações entre as palavras que, de outra forma, não estão relacionadas na superfície. Como resultado, se uma cadeia de caracteres de consulta incluir termos do mesmo cluster, um documento contendo ambos será classificado como mais de um que não.

:::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Representação de vetor para o contexto" border="true":::

## <a name="next-steps"></a>Próximas etapas

A classificação semântica é oferecida em camadas padrão, em regiões específicas. Para obter mais informações e se inscrever, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing).

Um novo tipo de consulta permite a classificação de relevância e as estruturas de resposta da pesquisa semântica. [Crie uma consulta semântica](semantic-how-to-query-request.md) para começar.

Como alternativa, examine qualquer um dos artigos a seguir para obter informações relacionadas.

+ [Adicionar verificação ortográfica aos termos da consulta](speller-how-to-add.md)
+ [Retornar uma resposta semântica](semantic-answers.md)