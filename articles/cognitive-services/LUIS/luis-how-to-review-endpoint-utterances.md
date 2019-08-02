---
title: Examinar usuário declarações-LUIS
titleSuffix: Azure Cognitive Services
description: O aprendizado ativo captura consultas de ponto de extremidade e seleciona o ponto de extremidade do usuário declarações que não tem certeza de. Revise essas declarações para selecionar as entidades de intenção e marca para essas declarações de leitura do mundo. Aceite essas alterações em seu exemplo declarações, em seguida, treine e publique. LUIS, em seguida, identifica declarações com mais precisão.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: c2f49d4bf573cc2dc2e1a3b8fc13413a738df8ba
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560457"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Como examinar o ponto de extremidade declarações no LUIS portal for active Learning

O [aprendizado ativo](luis-concept-review-endpoint-utterances.md) captura consultas de ponto de extremidade e seleciona o ponto de extremidade do usuário declarações que não tem certeza de. Revise essas declarações para selecionar as entidades de intenção e marca para essas declarações de leitura do mundo. Aceite essas alterações em seu exemplo declarações, em seguida, treine e publique. LUIS, em seguida, identifica declarações com mais precisão.


## <a name="enable-active-learning"></a>Habilitar o aprendizado ativo

Para habilitar o aprendizado ativo, faça log de consultas de usuário. Isso é feito definindo a [consulta de ponto](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) de extremidade `log=true` com o parâmetro e o valor de QueryString.

## <a name="disable-active-learning"></a>Desabilitar o aprendizado ativo

Para desabilitar o aprendizado ativo, não faça log de consultas de usuário. Isso é feito definindo a [consulta de ponto](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) de extremidade `log=false` com o parâmetro e o valor de QueryString.

## <a name="filter-utterances"></a>Filtrar declarações

1. Abra o aplicativo (por exemplo, TravelAgent) selecionando o nome na página **Meus Aplicativos** e selecione **Compilar** na barra superior.

1. Em **Melhorar o desempenho do aplicativo**, selecione **Examinar declarações de ponto de extremidade**.

1. Na página **Examinar declarações de ponto de extremidade**, clique na caixa de texto **Filtrar lista por intenção ou entidade**. Essa lista suspensa inclui todas as intenções em **INTENÇÕES** e todas as entidades em **ENTIDADES**.

    ![Filtro de declarações](./media/label-suggested-utterances/filter.png)

1. Selecione uma categoria (intenções ou entidades) na lista suspensa e examine as declarações.

    ![Declarações de intenção](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Rotular entidades
O LUIS substitui tokens de entidade (palavras) por nomes de entidade realçados em azul. Se uma declaração tem entidades sem rótulo, rotule-as na declaração. 

1. Selecione as palavras na declaração. 

1. Selecione uma entidade na lista.

    ![Rotular entidade](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Alinhar declaração única

Cada declaração tem uma intenção sugerida exibida na coluna **Intenção alinhada**. 

1. Se você concordar com essa sugestão, selecione-a na marca de seleção.

    ![Manter intenção alinhada](./media/label-suggested-utterances/align-intent-check.png)

1. Se você não concordar com a sugestão, selecione a intenção correta na lista suspensa de intenções alinhadas, e marque a caixa de seleção à direita da intenção alinhada. 

    ![Alinhar intenção](./media/label-suggested-utterances/align-intent.png)

1. Depois de marcar a caixa de seleção, a declaração é removida da lista. 

## <a name="align-several-utterances"></a>Alinhar várias declarações

Para alinhar várias declarações, marque a caixa à esquerda das declarações e selecione o botão **Adicionar selecionados**. 

![Alinhar vários](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Verificar intenção alinhada

Você pode verificar se a declaração foi alinhado com a intenção correta na página **Intenções**, selecione o nome da intenção e revise as declarações. A declaração **Examinar declarações de ponto de extremidade** está na lista.

## <a name="delete-utterance"></a>Excluir a declaração

Cada declaração pode ser excluída da lista de revisão. Depois de excluída, ela não aparecerá na lista novamente. Isso será verdadeiro mesmo se o usuário inserir a mesma declaração do ponto de extremidade. 

Se você não tiver certeza se deve excluir a declaração, mova-a para a intenção None ou crie uma nova intenção, como "diversos" e mova a declaração para essa intenção. 

## <a name="delete-several-utterances"></a>Excluir várias declarações

Para excluir várias declarações, selecione cada item e selecione a lixeira à direita do botão **Adicionar selecionados**.

![Excluir vários](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Próximas etapas

Para testar como o desempenho melhora depois que você rotula as declarações sugeridas, você poderá acessar o console de teste selecionando **Teste** no painel superior. Para obter instruções sobre como testar seu aplicativo usando o console de teste, consulte [Treinar e testar seu aplicativo](luis-interactive-test.md).
