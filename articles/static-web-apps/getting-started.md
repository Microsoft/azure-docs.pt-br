---
title: 'Início Rápido: Como criar seu primeiro site estático com os Aplicativos Web Estáticos do Azure'
description: Saiba como implantar um site estático nos Aplicativos Web Estáticos do Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: eb2356451c349f894c9ca74b1359f6a02d0e002a
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562507"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Início Rápido: Como criar seu primeiro site estático com os Aplicativos Web Estáticos do Azure

Os Aplicativos Web Estáticos do Azure publicam um site em um ambiente de produção por meio da criação de aplicativos por meio de um repositório GitHub. Neste guia de início rápido, você implantará um aplicativo Web nos Aplicativos Web Estáticos do Azure usando a extensão do Visual Studio Code.

Se você não tiver uma assinatura do Azure, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- [GitHub](https://github.com)
- Conta do [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Extensão dos Aplicativos Web Estáticos do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Em seguida, abra o Visual Studio Code e acesse **Arquivo > Abrir Pasta** para abrir no editor o repositório recém-clonado no computador.

## <a name="create-a-static-web-app"></a>Criar um aplicativo Web estático

1. No Visual Studio Code, selecione o logotipo do Azure na barra de atividade para abrir a janela de extensões do Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logotipo do Azure":::

    > [!NOTE]
    > A entrada no Azure e no GitHub é necessária. Se você ainda não entrou no Azure e no GitHub por meio do Visual Studio Code, a extensão solicitará a você que entre em ambos durante o processo de criação.

1. Posicione o mouse sobre o rótulo _Aplicativos Web Estáticos_ e escolha o **sinal de adição**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nome do aplicativo":::

1. A paleta de comandos será aberta na parte superior do editor e solicitará a você que nomeie o aplicativo.

    Digite **my-first-static-web-app** e clique em **ENTER**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Criar Aplicativo Web Estático":::

1. Selecione o branch **main** e pressione **Enter**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Nome do branch":::

1. Selecione **/** como a localização do código do aplicativo e clique em **ENTER**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Localização do código do aplicativo":::

1. A extensão procura a localização da API no aplicativo. Este artigo não implementa uma API.

    Selecione **Ignorar por enquanto** e clique em **ENTER**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Localização da API":::

1. Selecione a localização em que os arquivos são criados para produção no aplicativo.

    # <a name="no-framework"></a>[Nenhuma estrutura](#tab/vanilla-javascript)

    Desmarque a caixa e clique em **ENTER**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Caminho dos arquivos do aplicativo":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Digite **dist/angular-basic** e clique em **ENTER**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Caminho dos arquivos do aplicativo Angular":::

    # <a name="react"></a>[React](#tab/react)

    Digite **build** e clique em **ENTER**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Caminho dos arquivos do aplicativo React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Digite **dist** e clique em **ENTER**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Caminho dos arquivos do aplicativo Vue":::

    ---

1. Selecione uma localização mais próxima e clique em **ENTER**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Localização do recurso":::

1. Depois que o aplicativo é criado, uma notificação de confirmação é mostrada no Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Confirmação criada":::

1. Na janela do Explorador do Visual Studio Code, navegue até o nó que tem o nome da sua assinatura e expanda-o. Pode levar vários minutos para que a implantação seja concluída. Em seguida, retorne à seção Aplicativos Web Estáticos e selecione o nome do aplicativo, depois clique com o botão direito do mouse em my-first-static-web-app e selecione Abrir no Portal para ver o aplicativo no portal do Azure.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Abrir portal":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não pretende continuar usando esse aplicativo, exclua a instância dos Aplicativos Web Estáticos do Azure por meio da extensão.

Na janela do Visual Studio Code Explorer, volte à seção _Aplicativos Web Estáticos_, clique com o botão direito do mouse em **my-first-static-web-app** e selecione **Excluir**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Excluir aplicativo":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)
