---
title: 'Início Rápido: Como criar seu primeiro site estático com os Aplicativos Web Estáticos do Azure'
description: Saiba como implantar um site estático nos Aplicativos Web Estáticos do Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 335f78bba24947b1b6c3d6132bc38f237b3298b9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449067"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Início Rápido: Como criar seu primeiro site estático com os Aplicativos Web Estáticos do Azure

Os Aplicativos Web Estáticos do Azure publicam um site pela criação de aplicativos por meio de um repositório de código. Neste guia de início rápido, você implantará um aplicativo nos Aplicativos Web Estáticos do Azure usando a extensão do Visual Studio Code.

Se você não tiver uma assinatura do Azure, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- [GitHub](https://github.com)
- Conta do [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Extensão dos Aplicativos Web Estáticos do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Instalar o Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Em seguida, abra o Visual Studio Code e acesse **Arquivo > Abrir Pasta** para abrir no editor o repositório clonado no computador.

## <a name="create-a-static-web-app"></a>Criar um aplicativo Web estático

1. No Visual Studio Code, selecione o logotipo do Azure na barra de atividade para abrir a janela de extensões do Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logotipo do Azure":::

    > [!NOTE]
    > A entrada no Azure e no GitHub é necessária. Se você ainda não entrou no Azure e no GitHub por meio do Visual Studio Code, a extensão solicitará a você que entre em ambos durante o processo de criação.

1. No rótulo _Aplicativos Web Estáticos_, escolha o **sinal de adição**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nome do aplicativo":::

1. A paleta de comandos será aberta na parte superior do editor e solicitará a você que nomeie o aplicativo.

    Digite **my-first-static-web-app** e clique em **ENTER**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Criar Aplicativo Web Estático":::

1. Selecione as predefinições que correspondem ao seu tipo de aplicativo.

    # <a name="no-framework"></a>[Nenhuma estrutura](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Predefinições de aplicativo: nenhuma estrutura":::

    Insira **./** como a localização dos arquivos de aplicativo

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Localização dos arquivos de aplicativo":::

    Selecione **Ignorar por enquanto** como a localização para a API do Azure Functions

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Localização da API":::

    Insira **./** como a localização de saída do build

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Localização de saída do build do aplicativo":::

    # <a name="angular"></a>[Angular](#tab/angular)

    :::image type="content" source="media/getting-started/extension-presets-angular.png" alt-text="Predefinições de aplicativo: Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Predefinições de aplicativo: React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Predefinições de aplicativo: Vue":::

    ---

1. Selecione uma localização mais próxima e clique em **ENTER**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Localização do recurso":::

1. Depois que o aplicativo é criado, uma notificação de confirmação é mostrada no Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Confirmação criada":::

    Em seguida, clique no botão **Abrir Ações no GitHub**. Esta página mostra o status do build do aplicativo.

    Depois que a ação do GitHub for concluída, navegue até o site publicado.

1. Para ver o site no navegador, clique com o botão direito do mouse no projeto na extensão de Aplicativos Web Estáticos e selecione **Procurar no Site**.

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Procurar no site":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não pretende continuar usando esse aplicativo, exclua a instância dos Aplicativos Web Estáticos do Azure por meio da extensão.

Na janela do Visual Studio Code Explorer, volte à seção _Aplicativos Web Estáticos_, clique com o botão direito do mouse em **my-first-static-web-app** e selecione **Excluir**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Excluir aplicativo":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)
