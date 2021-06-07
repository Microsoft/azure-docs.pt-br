---
title: Examinar usuário declarações-LUIS
titleSuffix: Azure Cognitive Services
description: Examine o declarações capturado pelo aprendizado ativo para selecionar entidades de intenção e marca para declarações do mundo de leitura; aceitar alterações, treinar e publicar.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/08/2020
ms.openlocfilehash: ea2b44d05d25756a16b6b84f0734966b1f579848
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97007595"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Como melhorar o aplicativo LUIS examinando o ponto de extremidade declarações

O processo de revisão do ponto de extremidade declarações para previsões corretas é chamado de [aprendizado ativo](luis-concept-review-endpoint-utterances.md). O aprendizado ativo captura consultas de ponto de extremidade e seleciona o ponto de extremidade do usuário declarações que não tem certeza de. Revise essas declarações para selecionar as entidades de intenção e marca para essas declarações reais. Aceite essas alterações no seu exemplo de enunciado e, em seguida, treine e publique. LUIS, em seguida, identifica declarações com mais precisão.

## <a name="log-user-queries-to-enable-active-learning"></a>Registrar consultas de usuário para habilitar o aprendizado ativo

Para habilitar o aprendizado ativo, você deve registrar as consultas de usuário. Isso é feito chamando a [consulta de ponto de extremidade](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) com o `log=true` parâmetro e o valor de QueryString.

Use o portal do LUIS para construir a consulta de ponto de extremidade correta.

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **Meus Aplicativos**.
1. Vá para a seção **gerenciar** e, em seguida, selecione **recursos do Azure**.
1. Para o recurso de previsão atribuído, selecione **Alterar parâmetros de consulta**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostra o link alterar parâmetros de consulta.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Alterne **salvar logs** e salve selecionando **concluído**.

    > [!div class="mx-imgBorder"]
    > ![Use o portal do LUIS para salvar logs, o que é necessário para o aprendizado ativo.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Essa ação altera a URL de exemplo adicionando o parâmetro querystring `log=true`. Copie e use a URL de consulta de exemplo alterada ao fazer consultas de previsão para o ponto de extremidade do runtime.

## <a name="correct-intent-predictions-to-align-utterances"></a>Corrigir previsões de intenção para alinhar declarações

Cada declaração tem uma intenção sugerida exibida na coluna **Intenção alinhada**.

> [!div class="mx-imgBorder"]
> [![Examinar o ponto de extremidade declarações que o LUIS não tem certeza de](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Se você concordar com essa intenção, selecione a marca de seleção. Se você não concordar com a sugestão, selecione a intenção correta na lista suspensa de intenções alinhadas, e marque a caixa de seleção à direita da intenção alinhada. Depois de selecionar na marca de seleção, o expressão é movido para a intenção e removido da lista **examinar ponto de extremidade declarações** .

> [!TIP]
> É importante ir até a página de detalhes da intenção para revisar e corrigir as previsões de entidade de todos os exemplos declarações da lista **examinar ponto de extremidade declarações** .

## <a name="delete-utterance"></a>Excluir a declaração

Cada declaração pode ser excluída da lista de revisão. Depois de excluída, ela não aparecerá na lista novamente. Isso será verdadeiro mesmo se o usuário inserir a mesma declaração do ponto de extremidade.

Se você não tiver certeza se deve excluir o expressão, mova-o para a intenção nenhum ou crie uma nova intenção, como `miscellaneous` e mova o expressão para essa intenção.

## <a name="disable-active-learning"></a>Desabilitar o aprendizado ativo

Para desabilitar o aprendizado ativo, não faça log de consultas de usuário. Isso é feito definindo a [consulta de ponto de extremidade](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) com o `log=false` parâmetro e o valor de QueryString ou não usando o valor de QueryString porque o valor padrão é false.

## <a name="next-steps"></a>Próximas etapas

Para testar como o desempenho melhora depois que você rotula as declarações sugeridas, você poderá acessar o console de teste selecionando **Teste** no painel superior. Para obter instruções sobre como testar seu aplicativo usando o console de teste, consulte [Treinar e testar seu aplicativo](luis-interactive-test.md).
