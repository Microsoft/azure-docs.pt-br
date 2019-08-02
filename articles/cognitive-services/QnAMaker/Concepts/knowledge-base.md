---
title: Base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma base de dados de conhecimento de QnA Maker consiste em um conjunto de pares de pergunta/resposta (QnA) e metadados opcionais associados a cada par de QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e40af9b2362ee52a1d00f29cdc112d3c2b9a842
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565843"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>O que é uma base de dados de conhecimento do QnA Maker?

Uma base de dados de conhecimento de QnA Maker consiste em um conjunto de pares de pergunta/resposta (QnA) e metadados opcionais associados a cada par de QnA.

## <a name="key-knowledge-base-concepts"></a>Conceitos principais da base de dados de conhecimento

* **Perguntas** – uma pergunta contém texto que melhor representa uma consulta de usuário. 
* **Respostas** – uma resposta é a resposta que é retornada quando uma consulta de usuário é comparada com a pergunta associada.  
* **Metadados** – os metadados são marcas associadas a um par de QnA e são representados como pares chave-valor. As marcas de metadados são usadas para filtrar pares QnA e limitar o conjunto sobre o qual a correspondência da consulta será executada.

Um único QnA, representado por uma ID numérica do QnA, tem diversas variantes de uma pergunta (perguntas alternativas) que são mapeadas para uma única resposta. Além disso, cada par de tal pode ter vários campos de metadados associados a ele: uma chave e um valor.

![Bases de conhecimento do QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formato do conteúdo da base de dados de conhecimento

Ao receber o conteúdo avançado em uma base de dados de conhecimento, o QnA Maker tenta converter o conteúdo em markdown. Leia [este](https://aka.ms/qnamaker-docs-markdown-support) blog para entender os formatos de markdown reconhecidos pela maioria dos clientes de chat.

Os campos de metadados consistem em pares chave-valor separados por dois-pontos **(Produto: Shredder)** . A chave e o valor devem ser somente texto. A chave de metadados não deve conter espaços. Metadados dá suporte a apenas um valor por chave.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Como o QnA Maker processa uma consulta de usuário para selecionar a melhor resposta

O treinado e [publicados](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) da Base de dados de conhecimento do QnA Maker recebe uma consulta de usuário, de um bot ou outro aplicativo de cliente, cada a [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). O diagrama a seguir ilustra o processo quando a consulta de usuário é recebida.

![O processo de classificação para uma consulta de usuário](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

O processo é explicado na tabela a seguir:

|Etapa|Finalidade|
|--|--|
|1|O aplicativo cliente envia a consulta de usuário para o [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|O QnA Maker pré-processamento de consulta do usuário com a detecção de idioma, verificadores ortográficos e separadores de palavras.|
|3|Esse processo de pré-processamento é obtido para alterar a consulta de usuário para obter melhores resultados de pesquisa.|
|4|Essa consulta alterada é enviada para o índice de Azure Search, recebendo o `top` número de resultados. Se a resposta correta não esses resultados, aumente o valor de `top` um pouco. Geralmente, um valor de 10 para `top` funciona em 90% das consultas.|
|5|O QnA Maker se aplica a personalização avançada para determinar a exatidão dos resultados da pesquisa de Azure buscadas para consulta de usuário. |
|6|O modelo de classificador treinado usa a pontuação do recurso da etapa 5, para classificar os resultados da pesquisa do Azure.|
|7|Os novos resultados são retornados ao aplicativo cliente em ordem de classificação.|
|||

Os recursos usados incluem, mas não estão limitados a semântica de nível de palavra, o termo de nível de importância em um corpo e profundos modelos semânticos aprendidos para determinar similaridade e relevância entre duas cadeias de caracteres de texto.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ciclo de vida de desenvolvimento de uma base de dados de conhecimento](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Consulte também

[Visão geral do QnA Maker](../Overview/overview.md)
