---
title: Base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma base de dados de conhecimento de QnA Maker consiste em um conjunto de pares de pergunta/resposta (QnA) e metadados opcionais associados a cada par de QnA.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 02111ac90fe97ddaddbd41ad42410e7e76f1c405
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61379204"
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

Os campos de metadados consistem em pares chave-valor separados por dois-pontos **(Produto: Shredder)**. A chave e o valor devem ser somente texto. A chave de metadados não deve conter espaços. Metadados dá suporte a apenas um valor por chave.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ciclo de vida de desenvolvimento de uma base de dados de conhecimento](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Consulte também

[Visão geral do QnA Maker](../Overview/overview.md)
