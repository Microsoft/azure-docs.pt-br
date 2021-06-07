---
title: Rotular o enunciado de exemplo da entidade
description: Saiba como rotular uma entidade de aprendizado de máquina com subentidades em um exemplo expressão em uma página de detalhes da intenção do portal do LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ffbaa2e40d5924ba61e548398e63295cf7dba2b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019728"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>Rotular entidade Machine-Learning em um exemplo de expressão

A rotulagem de uma entidade em um enunciado de exemplo dá ao LUIS um exemplo do que é a entidade e de em que parte do enunciado a entidade poderá ser exibida.

Você pode rotular entidades e subentidades aprendidas por computador.

Como não é possível rotular expressão regular, lista ou entidades predefinidas, crie uma entidade ou subentidade e, em seguida, adicione essas entidades como recursos, quando aplicável, à entidade ou subentidade.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>Exemplo de rótulo declarações da página de detalhes da intenção

Para rotular exemplos de entidades dentro do expressão, selecione a intenção do expressão.

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **Meus Aplicativos**.
1. Selecione a intenção que tem o exemplo declarações que você deseja rotular para extração com uma entidade.
1. Selecione o texto que você deseja rotular e, em seguida, selecione a entidade.

## <a name="two-techniques-to-label-entities"></a>Duas técnicas para rotular entidades

Há suporte para duas técnicas de rotulação na página de detalhes da intenção.
* Selecione entidade ou subentidade na [paleta de entidades](#label-with-the-entity-palette-visible) e, em seguida, selecione o texto de exemplo expressão. Essa é a técnica recomendada porque você pode verificar visualmente se está trabalhando com a entidade ou subentidade correta, de acordo com seu esquema.
* Selecione no primeiro texto expressão de exemplo. Quando você faz isso, um menu pop-up de [Opções de rotulagem](#how-to-label-entity-from-in-place-menu) é apresentado.

## <a name="label-with-the-entity-palette-visible"></a>Rótulo com a paleta de entidades visível

Depois de [planejar seu esquema com entidades](luis-how-plan-your-app.md), mantenha a **paleta de entidades** visível ao rotular. A **paleta de entidades** é um lembrete de quais entidades você planejou extrair.

Para acessar a **paleta de entidades**, selecione o **@** símbolo na barra de ferramentas contextual acima da lista expressão de exemplo.

> [!div class="mx-imgBorder"]
> ![Captura de tela da paleta de entidades na página de detalhes da intenção.](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>Como rotular entidade da paleta de entidades

A paleta de entidades oferece uma alternativa à experiência de rotulagem anterior. Ele permite que você focalize o texto para rotulá-lo instantaneamente com uma entidade.

1. Abra a paleta de entidades selecionando o **@** símbolo no canto superior direito da tabela expressão.

2. Selecione a entidade da paleta que você deseja rotular. Essa ação é visualmente indicada com um novo cursor. O cursor segue o mouse à medida que você move no portal do LUIS.

3. No enunciado de exemplo, _pinte_ a entidade com o cursor.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostra a entidade pintada com o cursor.](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>Adicionando a entidade como um recurso da paleta de entidades

A seção inferior da paleta de entidades permite que você adicione recursos à entidade atualmente selecionada. Você pode selecionar entre todas as entidades e listas de frases existentes ou criar uma nova lista de frases.

> [!div class="mx-imgBorder"]
> ![Captura de tela da paleta de entidades com a entidade como um recurso](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>Rotular funções de entidade

As funções de entidade são rotuladas usando a **paleta de entidades**.

1. Na página Detalhes da intenção, selecione a **paleta de entidades** na barra de ferramentas de contexto.
1. Depois que a Paleta de entidades for aberta, selecione a entidade na lista de entidades.
1. Abaixo da lista de entidades, selecione uma função existente.
1. No texto do enunciado de exemplo, rotule o texto com a função de entidade.

## <a name="how-to-label-entity-from-in-place-menu"></a>Como rotular a entidade no menu no local

Rotular no local permite que você selecione rapidamente o texto dentro do expressão e etiquete-o. Você também pode criar uma entidade de Machine Learning ou uma entidade de lista a partir do texto rotulado.

Considere o exemplo expressão, `hi, please I want a cheese pizza in 20 minutes` .

Selecione o texto mais à esquerda e, em seguida, selecione o texto mais à direita da entidade e, em seguida, no menu no local, escolha a entidade com a qual você deseja rotular.

> [!div class="mx-imgBorder"]
> ![Rotular entidade de aprendizado de máquina completa](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>Examinar texto rotulado

Após a rotulagem, examine o enunciado de exemplo e verifique se o intervalo de texto selecionado foi sublinhado com a entidade escolhida. A linha sólida indica que o texto foi rotulado.

> [!div class="mx-imgBorder"]
> ![Rotulada como entidade de aprendizado de máquina completa](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Confirmar a entidade prevista

Se houver uma caixa com linhas pontilhadas ao longo do intervalo de texto, indica que o texto é previsto, mas _não rotulado ainda_. Para transformar a previsão em um rótulo, selecione a linha expressão e, em seguida, selecione **confirmar entidades** na barra de ferramentas contextual.

## <a name="relabeling-over-existing-entities"></a>Rerotulando as entidades existentes

Se você rerotular o texto que já está rotulado, LUIS poderá dividir ou mesclar rótulos existentes.

## <a name="labeling-for-punctuation"></a>Rotulagem para Pontuação

Não é necessário rotular para pontuação. Use [as configurações do aplicativo](luis-reference-application-settings.md) para controlar como a pontuação afeta as previsões de expressão.

## <a name="unlabel-entities"></a>Remover o rótulo de entidades

> [!NOTE]
> Somente entidades aprendidas do computador podem ser sem rótulo. Não é possível rotular ou desrotular entidades de expressão regular, entidades de lista ou entidades predefinidas.

Para desrotular uma entidade, selecione a entidade e selecione **desrotular** no menu no local.

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando a entidade de desrotulação](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>Rotulamento automático para entidades pai e filho

Se você estiver rotulando uma entidade pai, qualquer subentidade que possa ser prevista com base na versão atualmente treinada será rotulada.

Se você estiver rotulando uma subentidade, o pai será rotulado automaticamente.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>Rotulamento automático para entidades aprendidas que não sejam da máquina

Entidades aprendidas não Machine incluem entidades predefinidas, entidades de expressão regular, entidades de lista e padrão. quaisquer entidades. Elas são rotuladas automaticamente pelo LUIS, então não precisam ser rotuladas manualmente pelos usuários.

## <a name="intent-prediction-errors"></a>Erros de previsão de intenção

Um erro de previsão de intenção indica que o expressão de exemplo, dado o aplicativo treinado atual, não seria previsto para a intenção.

Saiba como [exibir esses erros](luis-how-to-add-intents.md#intent-prediction-errors) na página de detalhes da intenção.

## <a name="entity-prediction-errors"></a>Erros de previsão da entidade

Erros de previsão de entidade indicam que a entidade prevista não corresponde à entidade rotulada. Isso é visualizado com auxílio de um indicador de cuidado ao lado do enunciado.

> [!div class="mx-imgBorder"]
> ![Paleta de entidades para entidade de aprendizado de máquina](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Próximas etapas

Use o [painel](luis-how-to-use-dashboard.md) e os [enunciados de revisão de pontos de extremidade](luis-how-to-review-endpoint-utterances.md) para melhorar a qualidade de previsão de seu aplicativo.
