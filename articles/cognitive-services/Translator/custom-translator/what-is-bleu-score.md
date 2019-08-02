---
title: O que é uma pontuação no BLEU? - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: O BLEU é uma medida das diferenças entre uma tradução automática e uma ou mais traduções de referência criadas pelo homem da mesma sentença fonte. O algoritmo BLEU compara frases consecutivas da tradução automática com as frases consecutivas encontradas na tradução de referência e conta o número de correspondências, de maneira ponderada.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 1993819ad227d7e9aa5ef899045e00447a6740b8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595405"
---
# <a name="what-is-a-bleu-score"></a>O que é uma pontuação no BLEU?

[BLEU (Understudy de Avaliação Bilíngue)](https://en.wikipedia.org/wiki/BLEU) é uma medida das diferenças entre uma tradução automática e uma ou mais traduções de referência criadas por humanos da mesma sentença fonte.

## <a name="scoring-process"></a>Processo de pontuação

O algoritmo BLEU compara frases consecutivas da tradução automática com as frases consecutivas encontradas na tradução de referência e conta o número de correspondências, de maneira ponderada. Essas correspondências são independentes de posição. Um maior grau de correspondência indica um maior grau de similaridade com a tradução de referência e maior pontuação. Inteligibilidade e correção gramatical não são levadas em conta.

## <a name="how-bleu-works"></a>Como funciona o BLEU?

A força do BLEU é que ele se correlaciona bem com o julgamento humano, calculando a média dos erros individuais de julgamento de sentença sobre um corpus de teste, em vez de tentar elaborar o julgamento humano exato para cada sentença.

É uma discussão mais ampla de pontuações BLEU [aqui](https://youtu.be/-UqDljMymMg).

Os resultados do BLEU dependem fortemente da amplitude de seu domínio, da consistência dos dados de teste com os dados de treinamento e ajuste e da quantidade de dados que você tem disponível para treinar. Se seus modelos foram treinados em um domínio restrito e seus dados de treinamento são consistentes com seus dados de teste, você pode esperar uma alta pontuação no BLEU.

>[!NOTE]
>Uma comparação entre as pontuações BLEU só é justificável quando BLEU resultados são comparados com o mesmo conjunto de teste, o mesmo par de idioma e o mesmo mecanismo MT. Uma pontuação de BLEU de um conjunto de testes diferente é obrigada a ser diferente.
