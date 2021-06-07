---
title: Adicionar tentativas-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione intenções ao seu aplicativo do LUIS para identificar grupos de perguntas ou comandos que têm as mesmas intenções.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.service: cognitive-services
ms.openlocfilehash: f2401c032f0fc90024e0049fad5f696b8a184544
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018932"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Adicionar intenções para determinar a intenção do usuário de expressões

Adicione [intents](luis-concept-intent.md) ao seu aplicativo LUIS para identificar grupos de perguntas ou comandos que tenham a mesma intenção.

No portal do LUIS, as intenções são gerenciadas na seção de **Build** da barra de navegação superior e, em seguida, das **tentativas** do painel esquerdo.

## <a name="add-an-intent-to-your-app"></a>Adicionar uma intenção ao seu aplicativo

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Na página **tentativas** , selecione **+ criar**.
1. Na caixa de diálogo **criar nova tentativa** , insira o nome da intenção, por exemplo `ModifyOrder` , e selecione **concluído**.

    > [!div class="mx-imgBorder"]
    > ![Adicionar intenção](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    A intenção precisa de um [exemplo de declarações](luis-concept-utterance.md) para prever declarações no ponto de extremidade de previsão publicado.

## <a name="add-an-example-utterance"></a>Adicionar uma expressão de exemplo

Enunciados de exemplo são exemplos de texto de comandos ou perguntas do usuário. Para ensinar Reconhecimento vocal (LUIS) quando prever essa intenção, você precisa adicionar o declarações de exemplo a uma intenção. O LUIS precisa do intervalo de 15 a 30 exemplos de declarações para começar a entender a intenção. Não adicione o exemplo declarações em massa. Cada expressão deve ser escolhido cuidadosamente para como ele é diferente dos exemplos já existentes.

1. Na página detalhes da intenção, insira um expressão relevante que você espera de seus usuários, como `Deliver a large cheese pizza` na caixa de texto abaixo do nome da intenção e pressione Enter.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da página Detalhes de intenções, com o enunciado realçado](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS converte todas as declarações para minúsculas e adiciona espaços em volta de [tokens](luis-language-support.md#tokenization) como hifens.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Erros de previsão de intenção

Um erro de previsão de intenção é determinado quando o expressão não é previsto com o aplicativo treinado para a intenção.

1. Para localizar erros de previsão do expressão e corrigi-los, use as opções de **filtro** de incorretos e não claros.

    > [!div class="mx-imgBorder"]
    > ![Para localizar erros de previsão de expressão e corrigi-los, use a opção de filtro.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

1. Para exibir o valor de pontuação na página de detalhes da intenção, selecione **Mostrar pontuações de intenção de detalhes** no menu de opções de **exibição** .

    Quando os filtros e a exibição são aplicados e há um exemplo de declarações com erros, a lista de expressão de exemplo mostra o declarações e os problemas.

Cada linha mostra a pontuação de previsão do treinamento atual para o exemplo expressão, a Pontuação do rival mais próximo, que é a diferença nessas duas pontuações.

### <a name="fixing-intents"></a>Corrigindo tentativas

Para saber como corrigir erros de previsão de intenção, use o [painel de resumo](luis-how-to-use-dashboard.md). O painel de resumo fornece análise para o último treinamento da versão ativa e oferece as principais sugestões para corrigir seu modelo.

## <a name="using-the-contextual-toolbar"></a>Usando a barra de ferramentas contextual

A barra de ferramentas de contexto fornece outras ações:

* Editar ou excluir exemplo expressão
* Reatribuir expressão de exemplo a uma intenção diferente
* Filtros e exibições: mostrar somente declarações que contêm entidades filtradas ou exibir detalhes opcionais
* Pesquisar por meio de exemplo declarações

## <a name="train-your-app-after-changing-model-with-intents"></a>Treinar seu aplicativo após alterar o modelo com intenções

Depois de adicionar, editar ou remover intenções, [train](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que as alterações sejam aplicadas às consultas de ponto de extremidade. Não treine após cada alteração única. Treine após um grupo de alterações.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como adicionar [declarações de exemplo](./luis-how-to-add-entities.md) com entidades.