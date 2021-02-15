---
title: Examinar usuário expressão-LUIS
description: Com o aprendizado ativo, seus enunciados de ponto de extremidade são analisados para verificar se a intenção e a entidade estão corretas. O LUIS escolhe os enunciados de ponto de extremidade sobre os quais ele não tem certeza.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 82f228d5e6f801539c549e16faea371782ad4b59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316436"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Conceitos para habilitar o aprendizado ativo revisando declarações de ponto de extremidade
O aprendizado ativo é uma das três estratégias para melhorar a precisão da previsão e a mais fácil de implementar. Com o aprendizado ativo, seus enunciados de ponto de extremidade são analisados para verificar se a intenção e a entidade estão corretas. O LUIS escolhe os enunciados de ponto de extremidade sobre os quais ele não tem certeza.

## <a name="what-is-active-learning"></a>O que é o aprendizado ativo
Aprendizado ativo é um processo de duas etapas. Primeiro, o LUIS seleciona declarações que recebe no ponto de extremidade do aplicativo que precisa de validação. A segunda etapa é executada pelo proprietário do aplicativo ou colaborador para validar as declarações selecionadas para [revisão](luis-how-to-review-endpoint-utterances.md), incluindo a intenção correta e todas as entidades dentro da intenção. Após revisar, treine e publique as declarações no aplicativo novamente.

## <a name="which-utterances-are-on-the-review-list"></a>Quais declarações estão na lista de revisão
O LUIS adiciona declarações à lista de revisão quando a principal intenção de disparo tiver uma pontuação baixa, ou quando as duas pontuações principais de intenção estiverem muito próximas.

## <a name="single-pool-for-utterances-per-app"></a>Pool único para enunciados por aplicativo
A lista **Examinar enunciados de ponto de extremidade** não altera com base na versão. Há um único conjunto de enunciados para revisar, independentemente de qual versão a expressão está ativamente editando ou qual versão do aplicativo foi publicado no ponto de extremidade.

Na [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58b6f32139e2bb139ce823c9), o nome da versão é necessário e deve existir no aplicativo, mas não é usado além dessa validação. O declarações de revisão se aplica a um aplicativo inteiro. Se você remover o declarações de uma _versão_, todas as versões serão afetadas.

## <a name="where-are-the-utterances-from"></a>De onde são as declarações
Os declarações do ponto de extremidade são obtidos de consultas do usuário final no ponto de extremidade HTTP do aplicativo. Se o seu aplicativo não estiver publicado ou ainda não tiver acessos, você não terá declarações para examinar. Se nenhuma ocorrência do ponto de extremidade for recebida para uma intenção ou entidade específica, você não terá declarações para revisar que as contenha.

## <a name="schedule-review-periodically"></a>Agendar revisões periódicas
A revisão de declarações sugeridas não precisa ser feita diariamente, mas deve fazer parte de sua manutenção regular do LUIS.

## <a name="delete-review-items-programmatically"></a>Excluir itens de revisão programaticamente
Use a API **[Excluir não rotulada do declarações](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** . Faça backup dessas declarações antes da exclusão **[exportando os arquivos de log](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="enable-active-learning"></a>Habilitar o aprendizado ativo

Para habilitar o aprendizado ativo, você deve registrar as consultas de usuário. Isso é feito chamando a [consulta de ponto de extremidade](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) com o `log=true` parâmetro e o valor de QueryString.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [revisar](luis-how-to-review-endpoint-utterances.md) declarações do ponto de extremidade
