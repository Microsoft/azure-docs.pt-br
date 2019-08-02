---
title: Design de aplicativo iterativo-LUIS
titleSuffix: Azure Cognitive Services
description: O LUIS aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 2a540606a6f9cfa790a2244628e7f0b7bef35986
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639269"
---
# <a name="authoring-cycle-for-your-luis-app"></a>Ciclo de criação de seu aplicativo LUIS
O LUIS aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade. 

![Ciclo de criação](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Criando um modelo LUIS
A finalidade do modelo é descobrir qual usuário está solicitando (a intenção) e quais partes da pergunta fornecem detalhes (entidades) que ajudam a determinar a resposta. 

O modelo precisa ser específico para o domínio do aplicativo a fim de determinar palavras e frases relevantes, assim como a ordenação normal de palavras. 

O modelo requer tentativas e _deve ter_ entidades. 

## <a name="add-training-examples"></a>Adicionar exemplos de treinamento
O LUIS precisa de declarações de exemplo nas intenções. Os exemplos precisam de variação de escolha e de ordem de palavras suficientes para poder determinar a qual intenção a declaração se destina. Cada exemplo de declaração precisa ter dados necessários rotulados como entidades. 

Instrua o LUIS para ignorar declarações que não são relevantes para o domínio do seu aplicativo atribuindo a declaração à intenção **Nenhum**. Palavras ou frases que você não precisa extrair de uma declaração não precisam ser rotuladas. Não há nenhum rótulo palavras ou frases ignorarem. 

## <a name="train-and-publish-the-app"></a>Treinar e publicar o aplicativo
Depois de ter 15 a 30 declarações diferentes em cada tentativa, com as entidades necessárias rotuladas, você precisa [treinar](luis-how-to-train.md) e [publicar](luis-how-to-publish-app.md). Na notificação de publicação bem-sucedida, use o link para obter seus pontos de extremidade. Certifique-se de criar e publicar seu aplicativo para que ele esteja disponível nas [regiões de ponto de extremidade](luis-reference-regions.md) de que você precisa. 

## <a name="https-endpoint-testing"></a>Teste de ponto de extremidade HTTPS
É possível testar seu aplicativo LUIS no ponto de extremidade HTTPS. O teste do ponto de extremidade permite que o LUIS escolha qualquer declarações com baixa confiança para [revisão](luis-how-to-review-endpoint-utterances.md).  

## <a name="recycle"></a>Reciclar

Quando você conclui um ciclo de criação, é possível começar novamente. Comece com a [revisão do ponto de extremidade declarações](luis-how-to-review-endpoint-utterances.md) Luis marcado com baixa confiança. Verifique as intenções e a entidade dessas declarações. Depois de revisar as declarações, a lista de revisão deve estar vazia.  

Considere [clonar](luis-concept-version.md#clone-a-version) a versão atual em uma nova versão e, em seguida, comece a criar alterações na nova versão. 

## <a name="batch-testing"></a>Teste em lote

O [teste em lotes](luis-concept-batch-test.md) é uma maneira de ver quantos exemplos de declarações são pontuados por Luis. Os exemplos devem ser novos para o LUIS e devem estar rotulados corretamente com a intenção e as entidades que você deseja que o LUIS localize. Os resultados de teste indicam o quão bem seria o desempenho do LUIS nesse conjunto de declarações. 

## <a name="next-steps"></a>Próximas etapas

Conheça conceitos sobre [colaboração](luis-concept-collaborator.md).
