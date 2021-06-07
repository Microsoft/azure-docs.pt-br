---
title: incluir arquivo
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "83653171"
---
Aprenda os [conceitos](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) da normalização e como usar as APIs de [versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) para atualizar essas configurações ou use a seção **Gerenciar** do portal do LUIS na página **Configurações**.


|Configuração da IU|Configuração da API|Informações|
|--|--|--|
|Usar treinamento não determinístico|`UseAllTrainingData`|O treinamento usa um pequeno percentual de amostragem negativa. Se você quiser usar todos os dados, em vez da pequena amostragem negativa, defina para `true`. |
|Normalizar diacríticos|`NormalizeDiacritics`|Normalizar diacríticos substitui os caracteres com sinais diacríticos nos enunciados por caracteres normais. Essa configuração só está disponível em [idiomas](../luis-reference-application-settings.md#diacritics-normalization) que dão suporte a diacríticos.|
|Normalizar pontuação|`NormalizePunctuation`|A normalização da pontuação significa que, antes de seus modelos serem treinados e suas consultas do ponto de extremidade serem previstas, a pontuação será removida dos enunciados.|
|Normalizar formação das palavras|`NormalizeWordForm`|Ignore a formação das palavras além da raiz.|
