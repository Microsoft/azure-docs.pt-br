---
title: Tutorial – Usar revisões para fazer alterações sem interrupções no Gerenciamento de API com segurança
titleSuffix: Azure API Management
description: Execute as etapas deste tutorial para aprender a fazer alterações sem interrupção usando revisões no Gerenciamento de API.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 3804bfb2a269c431b1a00947f5c7613566a78f49
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377472"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Tutorial: Usar revisões para fazer alterações sem interrupções na API com segurança
Quando sua API estiver pronta e começar a ser usada por desenvolvedores, e algum momento será necessário fazer alterações nessa API e, ao mesmo tempo, não interromper os chamadores de sua API. Também é útil permitir que os desenvolvedores saibam sobre as alterações feitas por você. 

No Gerenciamento de API do Azure, use *revisões* para fazer alterações sem interrupção na API a fim de modelar e testar as alterações com segurança. Quando estiver pronto, você pode tornar uma revisão vigente e substituir a API atual. 

Para saber mais, confira [Versões e revisões](https://azure.microsoft.com/blog/versions-revisions/) e [Controle de versão de API com o Gerenciamento de API do Azure](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar uma nova revisão
> * Fazer alterações sem interrupções em sua revisão
> * Torne sua revisão a atual e adicione uma entrada ao log de alterações
> * Procurar no portal do desenvolvedor para ver as alterações e o log de alterações

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="Revisões de API no portal do Azure":::

## <a name="prerequisites"></a>Pré-requisitos

+ Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
+ Conclua o seguinte guia de início rápido: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o seguinte tutorial: [Importar e publicar sua primeira API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Adicionar uma nova revisão

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até a sua instância do Gerenciamento de API.
1. Selecione **APIs**.
2. Selecione **Demonstração da API de Conferência** na lista de APIs (ou outra API à qual você deseja adicionar revisões).
3. Selecione a guia **Revisões**.
4. Selecione **+ Adicionar revisão**.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="Adicionar revisão de API":::

    > [!TIP]
    > Também é possível selecionar **Adicionar revisão** no menu de contexto ( **...** ) da API.

5. Forneça uma descrição para a nova revisão, para ajudar você a se lembrar de sua função.
6. Selecione **Criar**.
7. Sua nova revisão foi criada.

    > [!NOTE]
    > Sua API original permanece na **Revisão 1**. Essa é a revisão que seus usuários continuarão a chamar até que você opte por tornar uma revisão diferente a atual.

## <a name="make-non-breaking-changes-to-your-revision"></a>Fazer alterações sem interrupções em sua revisão

1. Selecione **Demonstração de API de Conferência** na lista de APIs.
1. Selecione a guia **Design** na parte superior da tela.
1. Observe que o **seletor de revisão** (diretamente acima da guia de design) mostra a revisão atual como **Revisão 2**.

    > [!TIP]
    > Use o seletor de revisão para alternar entre as revisões nas quais você deseja trabalhar.
1. Selecione **+ Adicionar Operação**.
1. Defina a nova operação como **POST** e o Nome, Nome de Exibição e URL da operação como **teste**.
1. **Salve** sua nova operação.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="Modificar revisão":::
1. Agora você fez uma alteração em **Revisão 2**. Use o **seletor de revisão** na parte superior da página para alternar novamente para **Revisão 1**.
1. Observe que a nova operação não aparece em **Revisão 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Torne sua revisão a atual e adicione uma entrada ao log de alterações

1. Selecione a guia **Revisões** no menu próximo à parte superior da página.
1. Abra o menu de contexto (**...**) para a **Revisão 2**.
1. Selecione **Tornar atual**.
1. Marque **Postar no log de alterações públicas para esta API** se quiser postar observações sobre essa alteração. Forneça uma descrição para a alteração que os desenvolvedores vejam, por exemplo: **Teste de revisões. Adicionada uma nova operação de "teste".**
1. Agora, a **Revisão 2** é a atual.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Menu de revisão na janela &quot;Revisões&quot;":::


## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Procurar no portal do desenvolvedor para ver as alterações e o log de alterações

Se você tiver tentado o [portal do desenvolvedor](api-management-howto-developer-portal-customize.md), poderá revisar as alterações da API e o log de alterações lá.

1. No portal do Azure, selecione **APIs**.
1. Selecione **Portal do desenvolvedor** no menu superior.
1. No portal do desenvolvedor, selecione **APIs** e, depois, selecione **Demonstração da API de Conferência**.
1. Observe que sua nova operação de **teste** já está disponível.
1. Clique em **Log de alterações** ao lado do nome da API.
1. Observe que a entrada no log de alterações aparece nessa lista.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Adicionar uma nova revisão
> * Fazer alterações sem interrupções em sua revisão
> * Torne sua revisão a atual e adicione uma entrada ao log de alterações
> * Procurar no portal do desenvolvedor para ver as alterações e o log de alterações

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Publicar várias versões da sua API](api-management-get-started-publish-versions.md)
