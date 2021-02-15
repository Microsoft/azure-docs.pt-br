---
title: Testar seu aplicativo LUIS
titleSuffix: Azure Cognitive Services
description: O teste é o processo de fornecer exemplo de declarações ao LUIS e obter uma resposta de intenções e entidades reconhecidas pelo LUIS.
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: b33f765e936d7c0db301a5b2fcf38ccaf137f771
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787546"
---
# <a name="testing-example-utterances-in-luis"></a>Testando os enunciados de exemplo no LUIS

O teste é o processo de fornecer exemplo de declarações ao LUIS e obter uma resposta de intenções e entidades reconhecidas pelo LUIS. 

Você pode testar LUIS interativamente, um expressão de cada vez ou fornecer um conjunto de declarações. Durante o teste, você pode comparar a resposta de previsão do modelo ativo atual com a resposta de previsão do modelo publicado. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>O que é uma pontuação no teste?
Confira os conceitos [Pontuação de previsão](luis-concept-prediction-score.md) para saber mais sobre as pontuações de previsão.

## <a name="interactive-testing"></a>Teste interativo
O teste interativo é feito no painel de **teste** do portal do Luis. É possível inserir uma declaração para ver como as intenções e entidades são identificadas e pontuadas. Se LUIS não estiver prevendo as intenções e entidades esperadas em um expressão no painel de teste, copie-as para a página de **intenção** como um novo expressão. Em seguida, rotule as partes do expressão para entidades e treine o LUIS. 

## <a name="batch-testing"></a>Teste em lote
Confira o [teste de lote](./luis-how-to-batch-test.md) se estiver testando mais de uma declaração por vez.

## <a name="endpoint-testing"></a>Teste do ponto de extremidade
É possível testar usando o [ponto de extremidade](luis-glossary.md#endpoint) com no máximo duas versões do seu aplicativo. Com sua versão principal ou em tempo real do seu aplicativo definida como o ponto de extremidade de **produção**, adicione uma segunda versão ao ponto de extremidade de **preparo**. Essa abordagem oferece três versões de uma declaração: o modelo atual no painel de teste do site do [LUIS](luis-reference-regions.md) e as duas versões nos dois pontos de extremidade diferentes. 

Todo o teste de ponto de extremidade conta para sua cota de uso. 

## <a name="do-not-log-tests"></a>Não registrar testes
Se você fizer testes com relação a um ponto de extremidade e não quiser que a declaração fique registrada, lembre-se de usar a configuração da cadeia de caracteres de consulta `logging=false`.

## <a name="where-to-find-utterances"></a>Onde encontrar declarações
O LUIS armazena todos os declarações registrados no log de consultas, disponível para download no portal do LUIS na página de lista de **aplicativos** , bem como as [APIs de criação](https://go.microsoft.com/fwlink/?linkid=2092087)do Luis. 

Quaisquer declarações das quais o LUIS não tem certeza são listadas na página **[Examinar declarações de ponto de extremidade](luis-how-to-review-endpoint-utterances.md)** do site do [LUIS](luis-reference-regions.md). 

## <a name="remember-to-train"></a>Lembrar-se de treinar
Lembre-se de [treinar](luis-how-to-train.md) o LUIS após fazer alterações no modelo. As alterações no aplicativo LUIS não são vistas no teste até que o aplicativo seja treinado. 

## <a name="best-practices"></a>Práticas recomendadas
Conheça as [práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [testar](luis-interactive-test.md) suas declarações.