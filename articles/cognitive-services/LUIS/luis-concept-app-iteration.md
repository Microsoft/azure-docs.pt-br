---
title: Design de aplicativo iterativo
titleSuffix: Language Understanding - Azure Cognitive Services
description: O LUIS aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 67bcb33727bc808f5e5bea701daffc77dde736ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813774"
---
# <a name="authoring-cycle-for-your-luis-app"></a>Ciclo de criação de seu aplicativo LUIS
O LUIS aprende melhor em um ciclo iterativo de alterações de modelo, exemplos de expressão, publicação e coleta de dados de consultas de ponto de extremidade. 

![Ciclo de criação](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Criando um modelo LUIS
A finalidade do modelo é descobrir qual usuário está solicitando (a intenção) e quais partes da pergunta fornecem detalhes (entidades) que ajudam a determinar a resposta. 

O modelo precisa ser específico para o domínio do aplicativo a fim de determinar palavras e frases relevantes, assim como a ordenação normal de palavras. 

O modelo inclui a intenção e entidades. 

## <a name="add-training-examples"></a>Adicionar exemplos de treinamento
O LUIS precisa de declarações de exemplo nas intenções. Os exemplos precisam de variação de escolha e de ordem de palavras suficientes para poder determinar a qual intenção a declaração se destina. Cada exemplo de declaração precisa ter dados necessários rotulados como entidades. 

Instrua o LUIS para ignorar declarações que não são relevantes para o domínio do seu aplicativo atribuindo a declaração à intenção **Nenhum**. Palavras ou frases que você não precisa extrair de uma declaração não precisam ser rotuladas. Não há nenhum rótulo palavras ou frases ignorarem. 

## <a name="train-and-publish-the-app"></a>Treinar e publicar o aplicativo
Quando você tiver de 10 a 15 enunciados diferentes em cada intenção, com as entidades necessárias rotuladas, treine e publique. Na notificação de publicação bem-sucedida, use o link para obter seus pontos de extremidade. Certifique-se de criar seu aplicativo e publicá-lo para ele estar disponível nas [regiões de ponto de extremidade](luis-reference-regions.md) necessárias. 

## <a name="https-endpoint-testing"></a>Teste de ponto de extremidade HTTPS
É possível testar seu aplicativo LUIS no ponto de extremidade HTTPS. O teste do ponto de extremidade permite que o LUIS escolha quaisquer declarações com baixa confiança para revisão.  

## <a name="recycle"></a>Reciclar
Quando você conclui um ciclo de criação, é possível começar novamente. Comece a revisar as declarações de ponto de extremidade que o LUIS marcou como de baixa confiança. Verifique as intenções e a entidade dessas declarações. Depois de revisar as declarações, a lista de revisão deve estar vazia.  

## <a name="batch-testing"></a>Teste de lote
O teste de lote é uma maneira de ver quantas declarações de exemplo são classificadas pelo LUIS. Os exemplos devem ser novos para o LUIS e devem estar rotulados corretamente com a intenção e as entidades que você deseja que o LUIS localize. Os resultados de teste indicam o quão bem seria o desempenho do LUIS nesse conjunto de declarações. 

## <a name="next-steps"></a>Próximos passos

Conheça conceitos sobre [colaboração](luis-concept-collaborator.md).
