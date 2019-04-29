---
title: Testar seu aplicativo LUIS
titleSuffix: Language Understanding - Azure Cognitive Services
description: O teste é o processo de fornecer exemplo de declarações ao LUIS e obter uma resposta de intenções e entidades reconhecidas pelo LUIS.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: c9f1cf80cd3a781e878daca2048f7c5dc9095a7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710602"
---
# <a name="testing-example-utterances-in-luis"></a>Testando os enunciados de exemplo no LUIS

O teste é o processo de fornecer exemplo de declarações ao LUIS e obter uma resposta de intenções e entidades reconhecidas pelo LUIS. 

É possível [testar](luis-interactive-test.md) o LUIS interativamente, uma declaração por vez ou fornecer um [lote](luis-concept-batch-test.md) de declarações. Com o teste, você compara o modelo [ativo](luis-concept-version.md#active-version) atual com o modelo publicado. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>O que é uma pontuação no teste?
Confira os conceitos [Pontuação de previsão](luis-concept-prediction-score.md) para saber mais sobre as pontuações de previsão.

## <a name="interactive-testing"></a>Teste interativo
O teste interativo é feito no painel **Testar** do site. É possível inserir uma declaração para ver como as intenções e entidades são identificadas e pontuadas. Se o LUIS não estiver prevendo as intenções e as entidades conforme o esperado em uma declaração no painel de teste, copie-a para a página **Intenção** como uma nova declaração. Em seguida, rotule as partes dessa declaração e treine o LUIS. 

## <a name="batch-testing"></a>Teste de lote
Confira o [teste de lote](luis-concept-batch-test.md) se estiver testando mais de uma declaração por vez.

## <a name="endpoint-testing"></a>Teste do ponto de extremidade
É possível testar usando o [ponto de extremidade](luis-glossary.md#endpoint) com no máximo duas versões do seu aplicativo. Com sua versão principal ou em tempo real do seu aplicativo definida como o ponto de extremidade de **produção**, adicione uma segunda versão ao ponto de extremidade de **preparo**. Essa abordagem oferece três versões de um enunciado: o modelo atual no painel de Teste do site do [LUIS](luis-reference-regions.md) e as duas versões nos dois pontos de extremidade diferentes. 

Todo o teste de ponto de extremidade conta para sua cota de uso. 

## <a name="do-not-log-tests"></a>Não registrar testes
Se você fizer testes com relação a um ponto de extremidade e não quiser que a declaração fique registrada, lembre-se de usar a configuração da cadeia de caracteres de consulta `logging=false`.

## <a name="where-to-find-utterances"></a>Onde encontrar declarações
O LUIS armazena todos os enunciados registrados em log de consulta, disponíveis para download no site do [LUIS](luis-reference-regions.md), página **Aplicativos**, assim como as [APIs de criação do LUIS](https://aka.ms/luis-authoring-apis). 

Os enunciados que o LUIS não tiver certeza estarão listados na página **[Examinar enunciados de ponto de extremidade](luis-how-to-review-endpoint-utterances.md)** no site do [LUIS](luis-reference-regions.md). 

![Examinar declarações de ponto de extremidade](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>Lembrar-se de treinar
Lembre-se de [treinar](luis-how-to-train.md) o LUIS após fazer alterações no modelo. As alterações no aplicativo LUIS não são vistas no teste até que o aplicativo seja treinado. 

## <a name="best-practices"></a>Práticas recomendadas
Conheça as [práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre como [testar](luis-interactive-test.md) suas declarações.
