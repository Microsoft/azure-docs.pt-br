---
title: Modelos predefinidos para Reconhecimento vocal
titleSuffix: Azure Cognitive Services
description: O LUIS inclui um conjunto de modelos predefinidos para adicionar rapidamente cenários de usuário de conversação comuns.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 2080139732d71aa5df9a9d4ad13fcc4545128dff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541910"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Adicionar modelos predefinidos para cenários de uso comuns

O LUIS inclui um conjunto de modelos predefinidos para adicionar rapidamente cenários de usuário de conversação comuns. Essa é uma maneira rápida e fácil de adicionar habilidades ao seu aplicativo cliente de conversa sem ter que projetar os modelos para essas habilidades.

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio predefinido

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .

1. Selecione **domínios predefinidos** na barra de ferramentas à esquerda.

1. Localize o domínio que você deseja adicionar ao aplicativo e selecione botão **Adicionar domínio** .

    > [!div class="mx-imgBorder"]
    > ![Adicionar domínio predefinido do Calendário](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Adicionar uma intenção predefinida

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .

1. Na página **tentativas** , selecione **Adicionar intenção de domínio predefinido** na barra de ferramentas acima da lista de intenções.

1. Selecione uma intenção na caixa de diálogo pop-up.

    > [!div class="mx-imgBorder"]
    > ![Adicionar intenção predefinida](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selecione o botão **Concluído**.

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade predefinida
1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Selecione **entidades** no lado esquerdo.

1. Na página **entidades** , selecione **Adicionar entidade predefinida**.

1. Na caixa de diálogo **adicionar entidades predefinidas** , selecione a entidade predefinida.

    > [!div class="mx-imgBorder"]
    > ![Adicionar caixa de diálogo de entidade predefinida](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selecione **Concluído**. Depois que a entidade é adicionada, você precisa treinar o aplicativo.

## <a name="add-a-prebuilt-domain-entity"></a>Adicionar uma entidade de domínio predefinida
1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Selecione **entidades** no lado esquerdo.

1. Na página **entidades** , selecione **Adicionar entidade de domínio predefinida**.

1. Na caixa de diálogo **adicionar modelos de domínio predefinidos** , selecione a entidade de domínio predefinida.

1. Selecione **Concluído**. Depois que a entidade é adicionada, você precisa treinar o aplicativo.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicar para exibir o modelo predefinido do ponto de extremidade de previsão

A maneira mais fácil de exibir o valor de um modelo predefinido é consultar o ponto de extremidade publicado.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entidades que contêm um token de entidade predefinida

Se você tiver uma entidade de aprendizado de máquina que precisa de um recurso necessário de uma entidade predefinida, adicione uma subentidade à entidade de aprendizado de máquina e adicione um recurso _necessário_ de uma entidade predefinida.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Compilar modelo a partir de. csv com APIs REST](./luis-tutorial-node-import-utterances-csv.md)
