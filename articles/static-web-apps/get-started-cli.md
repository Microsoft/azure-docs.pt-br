---
title: 'Início Rápido: Como criar seu primeiro site estático com os Aplicativos Web Estáticos do Azure usando a CLI'
description: Aprenda a implantar um site estático nos Aplicativos Web Estáticos do Azure com a CLI do Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: a95e1658c3633f4ae8d09b71e9d3b0c82446754a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727579"
---
# <a name="quickstart-building-your-first-static-site-using-the-azure-cli"></a>Início Rápido: Como criar seu primeiro site estático usando a CLI do Azure

Os Aplicativos Web Estáticos do Azure publicam um site em um ambiente de produção por meio da criação de aplicativos por meio de um repositório GitHub. Neste guia de início rápido, você implantará um aplicativo Web nos Aplicativos Web Estáticos do Azure usando a CLI do Azure.

Se você não tiver uma assinatura do Azure, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Pré-requisitos

- [GitHub](https://github.com)
- [O token de acesso pessoal do GitHub](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- Conta do [Azure](https://portal.azure.com)
- [CLI do Azure](/cli/azure/install-azure-cli) instalada (versão 2.8.0 e superior)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Em seguida, alterne para a nova pasta usando o comando a seguir.

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>Criar um aplicativo Web estático

Agora que o repositório foi criado, você pode criar um aplicativo Web estático usando a CLI do Azure.

> [!IMPORTANT]
> Verifique se você está na pasta _my-first-static-web-app_ no terminal.

1. Entre na CLI do Azure usando o comando a seguir.

    ```azurecli
    az login
    ```

1. Crie um aplicativo Web estático por meio do seu repositório.

    # <a name="no-framework"></a>[Nenhuma estrutura](#tab/vanilla-javascript)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---
    
    > [!IMPORTANT]
    > A URL passada para o parâmetro `s` não deve incluir o sufixo `.git`.

    - `<RESOURCE_GROUP_NAME>`: substitua esse valor pelo [nome de um grupo de recursos do Azure](../azure-resource-manager/management/manage-resources-cli.md) existente.

      - Confira a documentação de [az group](/cli/azure/group#az_group_list) para obter detalhes sobre como listar grupos de recursos.

    - `<YOUR_GITHUB_ACCOUNT_NAME>`: substitua esse valor pelo seu nome de usuário do GitHub.

    - `<LOCATION>`: Substitua esse valor pela localização mais próxima. As opções incluem: _CentralUS_, _EastAsia_, _EastUS2_, _WestEurope_ e _WestUS2_.

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: substitua esse valor pelo [token de acesso pessoal do GitHub](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token) gerado anteriormente.

    Agora você poderá ver o aplicativo criado no Azure.

1. Abra o [portal do Azure](https://portal.azure.com).

1. Pesquise **my-first-web-static-app** na barra de pesquisa superior.

1. Escolha **my-first-web-static-app**.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não pretende continuar usando esse aplicativo, exclua a instância dos Aplicativos Web Estáticos do Azure executando o seguinte comando:

```azurecli
az staticwebapp delete \
    --name my-first-static-web-app \
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)