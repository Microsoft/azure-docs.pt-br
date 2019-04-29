---
title: Pontuações de previsão
titleSuffix: Language Understanding - Azure Cognitive Services
description: Uma pontuação de previsão indica o grau de confiança que o serviço de API do LUIS tem para resultados de previsão, com base em uma declaração de usuário.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 383ce4c4248f7e21f745f503c74a29cb613983e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814000"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Pontuações de previsão indicam a precisão da previsão de intenções e entidades

Uma pontuação de previsão indica o grau de confiança LUIS tem para resultados de previsão, com base em uma declaração de usuário.

Uma pontuação de previsão é entre zero (0) e um (1). Um exemplo de uma pontuação com alta confiança do LUIS é 0,99. Um exemplo de uma pontuação com baixa confiança é 0,01. 

|Valor da pontuação|Confiança|
|--|--|
|1|correspondência definida|
|0.99|alta confiança|
|0,01|baixa confiança|
|0|falha definida na correspondência|

Quando um enunciado resulta em uma classificação de baixa confiança, o LUIS destaca isso na página de [Intenção](luis-reference-regions.md) do site do **LUIS**, com a **intenção-rotulada** identificada destacada em vermelho.

![Discrepância da pontuação](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Intenção com maior pontuação

Toda previsão de declaração retorna uma intenção com maior pontuação. Essa previsão é uma comparação numérica de pontuações de previsão. As maiores 2 pontuações podem ter uma pequena diferença entre eles. LUIS não indica essa proximidade diferente de retornar a pontuação superior.  

## <a name="return-prediction-score-for-all-intents"></a>Retornar pontuação de previsão para todas as intenções

Um resultado do ponto de extremidade ou do teste pode incluir todas as intenções. Essa configuração é definida no [ponto de extremidade](https://aka.ms/v1-endpoint-api-docs) com o par nome/valor da cadeia de caracteres de consulta `verbose=true`.

## <a name="review-intents-with-similar-scores"></a>Examinar intenções com pontuações semelhantes

Examinar a pontuação de todas as intenções é uma boa maneira de verificar que não só a intenção correta foi identificada, mas que a intenção da próxima intenção identificada é significativamente menor para declarações de maneira consistente.

Se várias intenções tiverem pontuações de previsão próximas, com base no contexto de uma declaração, o LUIS poderá alternar entre as intenções. Para corrigir essa situação, continuar a adicionar declarações para cada tentativa com uma variedade maior de diferenças contextuais ou você pode ter o aplicativo cliente, como um chat bot, fazer escolhas programáticas sobre como lidar com as tentativas superior a 2.

As tentativas de 2, que são muito-perto pontuadas podem inverter devido ao treinamento não determinística. A pontuação superior poderia se tornar a parte superior da segunda e a segunda pontuação superior poderia se tornar a primeira pontuação superior. Para evitar essa situação, adicione declarações de exemplo para cada uma das duas principais intenções para essa expressão com a opção do word e o contexto que diferencia as tentativas de 2. As duas intenções devem ter aproximadamente o mesmo número de declarações de exemplo. Um princípio de separação para evitar a inversão devido ao treinamento, é uma diferença de 15% em pontuações.

Você pode desativar o treinamento não-determinística pela [com todos os dados de treinamento](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Diferenças com previsões entre as sessões de treinamento diferentes

Quando você treina o mesmo modelo em um aplicativo diferente, e as pontuações não forem iguais, essa diferença é porque não há treinamento não-determinística (um elemento de aleatoriedade). Em segundo lugar, qualquer sobreposição de uma expressão para mais de uma intenção significa que a principal intenção da mesma declaração pode mudar com base em treinamento.

Se seu bot de bate-papo exigir uma pontuação de LUIS específica para indicar confiança em uma intenção, você deve usar a diferença de pontuação entre as tentativas de duas principais. Essa situação proporciona flexibilidade para variações de treinamento.

## <a name="e-exponent-notation"></a>Notação E (exponencial)

As pontuações de previsão podem usar a notação exponencial, *aparecendo* acima do intervalo 0-1 range, como `9.910309E-07`. Essa pontuação é uma indicação de um número muito **pequeno**.

|Pontuação da notação E |Pontuação real|
|--|--|
|9,910309E-07|.0000009910309|

## <a name="punctuation"></a>Pontuação

Pontuação é um token separado no LUIS. Uma expressão que contém um ponto no final em comparação com uma expressão que não contém um ponto no final são duas declarações separadas e pode receber duas diferentes de previsões. Certifique-se de que o modelo manipule pontuação nos [exemplos de expressões](luis-concept-utterance.md) (com e sem pontuação) ou nos [padrões](luis-concept-patterns.md), onde é mais fácil ignorar a pontuação com a sintaxe especial: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Próximos passos

Confira [Adicionar entidades](luis-how-to-add-entities.md) para saber como adicionar entidades ao seu aplicativo LUIS.
