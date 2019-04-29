---
title: Frases e tokens ‒ API de Análise Linguística
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre a separação de frases e geração de tokens na API de Análise Linguística.
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: davl
ROBOTS: NOINDEX
ms.openlocfilehash: 435513023cf74bbc259cb922220d5f9940452d79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635459"
---
# <a name="sentence-separation-and-tokenization"></a>Separação de frases e geração de tokens

> [!IMPORTANT]
> A versão prévia da Análise Linguística foi encerrada em 9 de agosto de 2018. Recomendamos usar os [módulos de Análise de Texto do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) para análise e processamento de texto.

## <a name="background-and-motivation"></a>Motivação e segundo plano

Fornecendo-se um corpo do texto, a primeira etapa na análise linguística é fragmentá-lo em frases e tokens.

### <a name="sentence-separation"></a>Separação de frases

Na primeira vista, parece que quebrar o texto em sentenças é simples: basta localizar os marcadores de término da oração e quebrar as sentenças aí.
No entanto, esses marcadores são geralmente ambíguos e complicados.

Considere o texto de exemplo a seguir:

> What did you say?!? I didn't hear about the director's "new proposal." It's important to Mr. and Mrs. Smith.

This text contains three sentences:

- What did you say?!?
- I didn't hear about the director's "new proposal."
- It's important to Mr. and Mrs. Smith.

Observe como o final das frases é marcado de maneiras muito diferentes.
A primeira termina em uma combinação de pontos de interrogação e pontos de exclamação (às vezes chamados de um interrobang).
A segunda termina com um ponto final, mas a aspa seguinte deve ser extraída para a sentença anterior.
Na terceira frase, você pode ver como esse mesmo caractere de ponto final pode ser usado para marcar as abreviações também.
Olhar apenas a pontuação fornece um conjunto de bons candidatos, mas o trabalho adicional é necessário para identificar os limites de sentença true.

### <a name="tokenization"></a>Geração de tokens

A próxima tarefa é dividir essas frases em tokens.
Na maior parte do tempo, os tokens em inglês são delimitados por espaço em branco.
(Localizar palavras ou tokens é muito mais fácil em inglês que em chinês, em que os espaços não são usados principalmente entre palavras.
A primeira sentença pode ser escrita como "Whatdidyousay?")

Há alguns casos difíceis.
Primeiro, a pontuação normalmente (mas nem sempre) deve ser dividida longe do contexto.
Segundo, o inglês tem *contrações*, como "didn’t" ou "it’s", onde as palavras foram compactadas e abreviadas em partes menores.
O objetivo do tokenizer é quebrar a sequência de caracteres em palavras.

Vamos retornar para as frases de exemplo acima.
Agora, colocamos um "ponto central" (&middot;) entre cada token distinto.

- What &middot; did &middot; you &middot; say &middot; ?!?
- I &middot; did &middot; n't &middot; hear &middot; about &middot; the &middot; director &middot; 's &middot; " &middot; new &middot; proposal &middot; . &middot; "
- It &middot; 's &middot; important &middot; to &middot; Mr. &middot; and &middot; Mrs. &middot; Smith &middot; .

Observe como a maioria dos tokens são palavras que você encontraria no dicionário (por exemplo, *importante*, *diretor*).
Outros podem consistir apenas de pontuação.
Por fim, há mais tokens incomuns para representar contrações como *n'* para *not*, pronomes possessivos como *‘s* etc.
Essa geração de tokens nos permite lidar com a palavra *didn't* e a frase *did not* de forma mais consistente.

## <a name="specification"></a>Especificação

É importante tomar decisões consistentes sobre o que consiste uma oração e um token.
Contamos com as especificações do [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42) (alguns detalhes adicionais estão disponíveis em ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html).
