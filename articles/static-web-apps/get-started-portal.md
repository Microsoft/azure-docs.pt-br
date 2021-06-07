---
title: 'Início Rápido: Como criar seu primeiro aplicativo Web estático com os Aplicativos Web Estáticos do Azure usando o portal do Azure'
description: Aprenda a implantar um site estático nos Aplicativos Web Estáticos do Azure com o portal do Azure.
services: static-web-apps
author: craigshoemaker
ms.author: cshoe
ms.date: 09/03/2020
ms.topic: quickstart
ms.service: static-web-apps
ms.custom:
- mode-portal
ms.openlocfilehash: 78af2290a2e71d349303d3913f8a40510eb9c6a0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531587"
---
# <a name="quickstart-building-your-first-static-site-in-the-azure-portal"></a>Início Rápido: Como criar seu primeiro site estático no portal do Azure

Os Aplicativos Web Estáticos do Azure publicam um site em um ambiente de produção por meio da criação de aplicativos por meio de um repositório GitHub. Neste guia de início rápido, você implantará um aplicativo Web nos Aplicativos Web Estáticos do Azure usando o portal do Azure.

Se você não tiver uma assinatura do Azure, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- [GitHub](https://github.com)
- Conta do [Azure](https://portal.azure.com)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Criar um aplicativo Web estático

Agora que o repositório foi criado, você pode criar um aplicativo Web estático no portal do Azure.

1. Navegue até o [portal do Azure](https://portal.azure.com)
1. Selecione **Criar um Recurso**
1. Pesquise por **Aplicativos Web Estáticos**
1. Selecione **Aplicativos Web Estáticos (Versão Prévia)**
1. Escolha **Criar**

Na seção _Informações Básicas_, comece configurando seu novo aplicativo e vinculando-o a um repositório GitHub.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Seção Básico":::

1. Selecione sua _Assinatura do Azure_.
1. Selecione ou crie um novo _Grupo de Recursos_
1. Nomeie o aplicativo **my-first-static-web-app**.
      1. Os caracteres válidos são `a-z` (não diferencia maiúsculas de minúsculas), `0-9` e `-`.
1. Selecione a _Região_ mais próxima de você.
1. Selecione o **SKU** _gratuito_
1. Escolha o botão **Entrar com o GitHub** e autentique-se no GitHub

Depois de entrar no GitHub, insira as informações do repositório.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Detalhes do repositório":::

1. Selecione sua _Organização_ de preferência.
1. Selecione **my-first-web-static-app** na lista suspensa _Repositório_.
1. Selecione **principal** na lista suspensa _Ramificação_

> [!NOTE]
> Caso você não veja nenhum repositório, talvez seja necessário autorizar Aplicativos Web Estáticos do Azure no GitHub. Navegue até o repositório do GitHub e vá para **Configurações > Aplicativos > Aplicativos OAuth Autorizados**, selecione **Aplicativos Web Estáticos do Azure** e **Conceder**. Em repositórios corporativos, você precisa ser um proprietário da organização para conceder as permissões.

1. Na seção _Detalhes do Build_, adicione detalhes de configuração específicos à sua estrutura de front-end preferida.

    # <a name="no-framework"></a>[Nenhuma estrutura](#tab/vanilla-javascript)

    1. Selecione **Personalizado** na lista suspensa _Predefinições de Build_
    1. Mantenha o valor padrão na caixa _Localização do aplicativo_
    1. Limpe o valor padrão na caixa _Local da API_.
    1. Deixe a caixa _Local do artefato do aplicativo_ vazia

    # <a name="angular"></a>[Angular](#tab/angular)

    1. Selecione **Angular** na lista suspensa _Predefinições de Build_
    1. Mantenha o valor padrão na caixa _Localização do aplicativo_
    1. Limpe o valor padrão na caixa _Local da API_.
    1. Digite **dist/angular-basic** na caixa _Localização do artefato do aplicativo_

    # <a name="react"></a>[React](#tab/react)

    1. Selecione **Reagir** da lista suspensa _Predefinições de Build_
    1. Mantenha o valor padrão na caixa _Localização do aplicativo_
    1. Limpe o valor padrão na caixa _Local da API_.
    1. Digite **build** na caixa _Localização do artefato do aplicativo_

    # <a name="vue"></a>[Vue](#tab/vue)

    1. Selecione **Vue.js** na lista suspensa _Predefinições de Build_
    1. Mantenha o valor padrão na caixa _Localização do aplicativo_
    1. Limpe o valor padrão na caixa _Local da API_.
    1. Mantenha o valor padrão na caixa _Local do artefato do aplicativo_

    ---

1. Selecione **Examinar + criar**.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="Botão Revisar Criar":::.

    > [!NOTE]
    > Edite o [arquivo de fluxo de trabalho](github-actions-workflow.md) para alterar esses valores depois de criar o aplicativo.

1. Selecione **Criar**.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Botão Criar":::

1. Selecione **Ir para o recurso**.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Botão Ir para o recurso":::.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser continuar usando esse aplicativo, poderá excluir a instância do Aplicativo Web Estático do Azure com as seguintes etapas:

1. Abra o [portal do Azure](https://portal.azure.com)
1. Pesquise **my-first-web-static-app** na barra de pesquisa superior.
1. Selecione o nome do aplicativo
1. Selecione o botão **Excluir**
1. Selecione **Sim** para confirmar a ação de exclusão (essa ação pode levar alguns minutos para ser concluída)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)
