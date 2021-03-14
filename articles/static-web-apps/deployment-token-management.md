---
title: Redefinir tokens de implantação em aplicativos Web estáticos do Azure (visualização)
description: Redefinir tokens em um site de aplicativos Web estáticos do Azure
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744167"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a>Redefinir tokens de implantação em aplicativos Web estáticos do Azure (visualização)

Quando você cria um novo site de aplicativos Web estáticos do Azure, o Azure gera um token usado para identificar o aplicativo durante a implantação. Durante o provisionamento, esse token é armazenado como um segredo no repositório do GitHub. Este artigo explica como usar e gerenciar esse token.

Normalmente, você não precisa se preocupar com o token de implantação, mas estes são alguns motivos pelos quais você pode precisar recuperar ou redefinir o token.

* **Comprometimento de token**: redefina o token se ele estiver exposto a uma parte externa.
* **Implantando de um repositório GitHub separado**: se você estiver implantando manualmente de um repositório GitHub separado, será necessário definir o token de implantação no novo repositório.

## <a name="prerequisites"></a>Pré-requisitos

- Um repositório GitHub existente configurado com Aplicativos Web Estáticos do Azure.
- Consulte [Como criar seu primeiro aplicativo estático](getting-started.md) se você não tiver um.

## <a name="reset-a-deployment-token"></a>Redefinir um token de implantação

1. Clique no link **gerenciar token de implantação** na página _visão geral_ do site de aplicativos Web estáticos do Azure.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="Gerenciando o token de implantação":::

1. Clique no botão **Redefinir token** .

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="Redefinindo o token de implantação":::

1. Depois de exibir um novo token no campo _token de implantação_ , copie o token clicando em **copiar para** o ícone da área de transferência.


## <a name="update-a-secret-in-the-github-repository"></a>Atualizar um segredo no repositório do GitHub

Para manter a implantação automatizada em execução, depois de redefinir um token, você precisa definir o novo valor no repositório GitHub correspondente.

1. Navegue até o repositório do seu projeto no GitHub e clique na guia **configurações** .
1. Clique no item de menu **segredos** . Você encontrará um segredo gerado durante o provisionamento de aplicativo Web estático chamado _AZURE_STATIC_WEB_APPS_API_TOKEN_... na seção _segredos do repositório_ .

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="Listando segredos do repositório":::

    > [!NOTE]
    > Se você criou o site de aplicativos Web estáticos do Azure em várias ramificações deste repositório, verá várias _AZURE_STATIC_WEB_APPS_API_TOKEN_... segredos nesta lista. Selecione o correto correspondendo ao nome do arquivo listado no campo _Editar fluxo de trabalho_ na guia _visão geral_ do site de aplicativos Web estáticos.

1. Clique no botão **Atualizar** para abrir o editor.
1. **Cole o valor** do token de implantação no campo _valor_ .
1. Clique em **Atualizar segredo**.

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="Atualizando segredo do repositório":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Publicar de um gerador de site estático](publish-gatsby.md)
