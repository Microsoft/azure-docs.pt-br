---
title: Testar o aplicativo no portal do LUIS
description: Use o LUIS (Serviço Inteligente de Reconhecimento Vocal) para trabalhar continuamente em seu aplicativo para refiná-lo e melhorar seu reconhecimento vocal.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 31885eba16d59e2e48a08f84c56271b84e6c565f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790911"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testar seu aplicativo LUIS no portal do LUIS

[O teste](luis-concept-test.md) de um aplicativo é um processo iterativo. Após treinar seu aplicativo LUIS, teste-o com declarações de exemplo para ver se as intenções e as entidades são reconhecidas corretamente. Caso não sejam, faça atualizações ao aplicativo de LUIS, treine e teste novamente.

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Treinar antes do teste

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Para testar a versão mais recente do aplicativo ativo, selecione **treinar** no menu superior, antes de testar.

## <a name="test-an-utterance"></a>Testar uma declaração

O expressão de teste não deve ser exatamente o mesmo que qualquer declarações de exemplo no aplicativo. O expressão de teste deve incluir a opção de palavra, o comprimento da frase e o uso da entidade que você espera para um usuário.

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .

1. Para acessar o painel de deslizamento de **teste** , selecione **teste** no painel superior do seu aplicativo.

    > [!div class="mx-imgBorder"]
    > ![Página treinar & testar aplicativo](./media/luis-how-to-interactive-test/test.png)

1. Insira uma declaração na caixa de texto e selecione Enter. É possível digitar quantas declarações de teste você desejar no **Teste**, mas apenas uma declaração por vez.

1. A declaração, sua principal intenção e a pontuação são adicionadas à lista de declarações embaixo da caixa de texto.

    > [!div class="mx-imgBorder"]
    > ![O teste interativo identifica a intenção incorreta](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>Inspecionar a previsão

Você inspeciona detalhes do resultado do teste no painel **inspecionar** .

1. Com o painel extensível **Testar** aberto, selecione **Inspecionar** para uma declaração que você deseja comparar.

    > [!div class="mx-imgBorder"]
    > ![Selecione o botão Inspecionar para ver mais detalhes sobre os resultados do teste](./media/luis-how-to-interactive-test/inspect.png)

1. O painel de **inspeção** é exibido. O painel inclui a principal intenção de pontuação e as entidades identificadas. O painel mostra a previsão do expressão selecionado.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela parcial do painel inspecionar teste](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>Adicionar ao exemplo declarações

No painel de inspeção, você pode adicionar o expressão de teste a uma intenção selecionando **Adicionar ao exemplo declarações**.

## <a name="disable-required-features"></a>Desabilitar recursos necessários

Essa alternância ajuda a determinar se o aplicativo treinado está prevendo corretamente suas entidades com base nos recursos necessários. A configuração padrão é aplicar o recurso conforme necessário durante a previsão. Selecione essa alternância para ver qual será a previsão se o recurso da subentidade não fosse necessário.

### <a name="when-to-disable-required-features"></a>Quando desabilitar os recursos necessários

O aplicativo treinado pode prever incorretamente uma entidade aprendida por máquina com base em uma das seguintes opções:
* Rotulação incorreta do exemplo declarações.
* O recurso necessário não corresponde ao texto.

Um exemplo é uma entidade aprendida por máquina com uma subentidade do nome de uma pessoa.

:::image type="content" source="media/luis-how-to-interactive-test/disable-required-feature.png" alt-text="Captura de tela da máquina do portal do LUIS-conhece o esquema de entidade com o recurso necessário":::

Um exemplo de expressão para esta entidade aprendida por computador é: `Assign Bob Jones to work on the new security feature` .

A extração deve ser `security feature` como a descrição do tíquete e `Bob Jones` como o engenheiro, duas subentidades de `Assign ticket` entidade.

Para ajudar a subentidade a prever com êxito, adicione o recurso predefinido AA de entidade de [usuário](luis-reference-prebuilt-person.md) para a `engineer` subentidade. Se você tornar o recurso necessário, isso significará que a subentidade só será extraída se a entidade predefinida PersonName for prevista para o texto. Isso significa que qualquer nome no texto que não prevê a subentidade PersonName não será retornado como uma subentidade rotulada, `engineer` .

Quando você usa o painel de teste interativo e vê uma subentidade, com um recurso necessário, não está prevendo, alterne essa configuração para ver se a subentidade deve ser prevista sem que o recurso seja necessário. A subentidade pode ser capaz de ser prevista corretamente sem o recurso, conforme necessário, devido à rotulagem correta do exemplo declarações.

## <a name="view-sentiment-results"></a>Exibir resultados de sentimento

Se a **Análise de sentimento** estiver configurada na página **[Publicar](luis-how-to-publish-app.md#enable-sentiment-analysis)**, os resultados de teste incluirão o sentimento encontrado na declaração.

## <a name="correct-matched-patterns-intent"></a>Corrigir intenção do padrão correspondente

Se estiver usando [Padrões](luis-concept-patterns.md) e a declaração tiver correspondido a um padrão, mas a intenção incorreta estava prevista, selecione o link **Editar** pelo padrão e selecione a intenção correta.

## <a name="compare-with-published-version"></a>Comparar com a versão publicada

É possível testar a versão ativa do seu aplicativo com a versão do [ponto de extremidade](luis-glossary.md#endpoint) publicada. No painel **Inspecionar**, selecione **Comparar com publicado**. Qualquer teste com relação ao modelo publicado é deduzido do saldo da sua cota de assinatura do Azure.

> [!div class="mx-imgBorder"]
> ![Comparar com o publicado](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Exibir JSON de ponto de extremidade no painel de teste
É possível exibir o JSON do ponto de extremidade retornado para a comparação selecionando **Mostrar exibição JSON**.

> [!div class="mx-imgBorder"]
> ![Resposta JSON publicada](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Configurações adicionais no painel de teste

### <a name="luis-endpoint"></a>Ponto de extremidade LUIS

Se tiver vários pontos de extremidade LUIS, use o link **Configurações Adicionais** no painel Publicado do Teste para alterar o ponto de extremidade usado para teste. Se não tiver certeza sobre qual ponto de extremidade usar, selecione o **Starter_Key** padrão.

> [!div class="mx-imgBorder"]
> ![Testar painel com o link Configurações Adicionais realçado](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Teste em lote
Confira os [conceitos](./luis-how-to-batch-test.md) de teste do lote e saiba [como](luis-how-to-batch-test.md) testar um lote de declarações.

## <a name="next-steps"></a>Próximas etapas

Se o teste indica que seu aplicativo LUIS não reconhece as intenções e entidades corretas, é possível trabalhar para melhorar a precisão do seu aplicativo LUIS rotulando mais declarações ou adicionando recursos.

* [Rotular enunciados sugeridos com o LUIS](luis-how-to-review-endpoint-utterances.md)
* [Usar recursos para melhorar o desempenho do aplicativo de LUIS](luis-how-to-add-features.md)