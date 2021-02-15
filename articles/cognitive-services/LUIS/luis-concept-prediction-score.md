---
title: Pontuações de previsão-LUIS
description: Uma pontuação de previsão indica o grau de confiança que o serviço de API LUIS tem para os resultados de previsão, com base em um usuário expressão.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d836273e61752ff208133466016ce7c6ff9c28fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316453"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Pontuações de previsão indicam a precisão da previsão de intenções e entidades

Uma pontuação de previsão indica o grau de confiança que o LUIS tem para os resultados de previsão de um usuário expressão.

Uma pontuação de previsão é entre zero (0) e um (1). Um exemplo de uma pontuação com alta confiança do LUIS é 0,99. Um exemplo de uma pontuação com baixa confiança é 0,01.

|Valor da pontuação|Confiança|
|--|--|
|1|correspondência definida|
|0,99|alta confiança|
|0,01|baixa confiança|
|0|falha definida na correspondência|

## <a name="top-scoring-intent"></a>Intenção com maior pontuação

Toda previsão de declaração retorna uma intenção com maior pontuação. Essa previsão é uma comparação numérica de pontuações de previsão.

## <a name="proximity-of-scores-to-each-other"></a>Proximidade de pontuações entre si

As duas pontuações principais podem ter uma diferença muito pequena entre elas. LUIS não indica essa proximidade além de retornar a pontuação superior.

## <a name="return-prediction-score-for-all-intents"></a>Retornar pontuação de previsão para todas as intenções

Um resultado do ponto de extremidade ou do teste pode incluir todas as intenções. Essa configuração é definida no ponto de extremidade usando o par nome/valor da QueryString correto.

|API de previsão|Nome da QueryString|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Examinar intenções com pontuações semelhantes

Examinar a pontuação de todas as intenções é uma boa maneira de verificar se não só a intenção correta foi identificada, mas a pontuação da próxima intenção identificada é significativamente e consistentemente menor para declarações.

Se várias intenções tiverem pontuações de previsão próximas, com base no contexto de uma declaração, o LUIS poderá alternar entre as intenções. Para corrigir essa situação, continue a adicionar o declarações a cada tentativa com uma variedade maior de diferenças contextuais ou você pode ter o aplicativo cliente, como um bot de bate-papo, fazer escolhas programáticas sobre como lidar com as duas principais intenções.

As 2 intenções, com pontuações muito parecidas, podem ser invertidas devido ao **treinamento não determinístico**. A pontuação superior poderia se tornar a parte superior da segunda e a segunda pontuação superior poderia se tornar a primeira pontuação superior. Para evitar essa situação, adicione um exemplo de declarações a cada uma das duas primeiras intenções para esse expressão com a escolha do Word e o contexto que diferencia as duas intenções. As duas intenções devem ter aproximadamente o mesmo número de declarações de exemplo. Um princípio de separação para evitar a inversão devido ao treinamento, é uma diferença de 15% em pontuações.

Você pode desativar o **treinamento não determinístico** ao [treinar todos os dados](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Diferenças com previsões entre diferentes sessões de treinamento

Quando você treina o mesmo modelo em um aplicativo diferente e as pontuações não são as mesmas, essa diferença ocorre porque há um **treinamento não determinístico** (um elemento de aleatoriedade). Em segundo lugar, qualquer sobreposição de uma expressão para mais de uma intenção significa que a principal intenção da mesma declaração pode mudar com base em treinamento.

Se o seu bot de chat exigir uma pontuação LUIS específica para indicar confiança em uma intenção, você deverá usar a diferença de pontuação entre as duas primeiras intenções. Essa situação fornece flexibilidade para variações no treinamento.

Você pode desativar o **treinamento não determinístico** ao [treinar todos os dados](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>Notação E (exponencial)

As pontuações de previsão podem usar a notação exponencial, _aparecendo_ acima do intervalo 0-1 range, como `9.910309E-07`. Essa pontuação é uma indicação de um número muito **pequeno**.

|Pontuação da notação E |Pontuação real|
|--|--|
|9,910309E-07|.0000009910309|

<a name="punctuation"></a>

## <a name="application-settings"></a>Configurações do aplicativo

Use [as configurações do aplicativo](luis-reference-application-settings.md) para controlar como os sinais diacríticos e a pontuação afetam as pontuações de previsão.

## <a name="next-steps"></a>Próximas etapas

Confira [Adicionar entidades](luis-how-to-add-entities.md) para saber como adicionar entidades ao seu aplicativo LUIS.
