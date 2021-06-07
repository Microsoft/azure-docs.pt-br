---
title: 'Tutorial: Publicar um site do Hugo em Aplicativos Web Estáticos do Azure'
description: Saiba como implantar um aplicativo Hugo em Aplicativos Web Estáticos do Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 4539c32a367bb0974212d989176a96b530da21a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652319"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Tutorial: Publicar um site do Hugo na Versão Prévia dos Aplicativos Web Estáticos do Azure

Este artigo demonstra como criar e implantar um aplicativo Web [Hugo](https://gohugo.io/) para [Aplicativos Web Estáticos do Azure](overview.md). O resultado final são novos Aplicativos Web Estáticos do Azure com o GitHub Actions associado que dão controle a você sobre como o aplicativo é criado e publicado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar um aplicativo Hugo
> - Configurar Aplicativos Web Estáticos do Azure
> - Implantar um aplicativo Hugo no Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. Se você não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/).
- Uma conta do GitHub. Se você não tiver uma, [crie uma conta gratuita](https://github.com/join).

## <a name="create-a-hugo-app"></a>Criar um aplicativo Hugo

Crie um aplicativo Hugo usando a CLI (Interface de Linha de Comando) do Hugo:

1. Siga o [guia de instalação](https://gohugo.io/getting-started/installing/) do Hugo em seu SO.

1. Abra um terminal

1. Execute a CLI do Hugo para criar um novo aplicativo.

   ```bash
   hugo new site static-app
   ```

1. Navegue até o aplicativo recém-criado.

   ```bash
   cd static-app
   ```

1. Inicialize um repositório Git.

   ```bash
    git init
   ```

1. Em seguida, adicione um tema ao site instalando um tema como um submódulo git e especificando-o no arquivo de configuração do Hugo.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Confirme as alterações.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Envie por push seu aplicativo para o GitHub.

Você precisa de um repositório no GitHub para conectar os Aplicativos Web Estáticos do Azure. As etapas a seguir mostram como criar um repositório para seu site.

1. Crie um repositório GitHub em branco (não crie um README) a partir do [https://github.com/new](https://github.com/new)hugo-static-app **nomeado**.

1. Adicione o repositório do GitHub como sendo remoto ao seu repositório local. Adicione o nome de usuário do GitHub no lugar do espaço reservado `<YOUR_USER_NAME>` no comando a seguir.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Envie por push seu repositório local para o GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Implantar o aplicativo Web

As etapas a seguir mostram como criar um novo aplicativo de site estático e implantá-lo em um ambiente de produção.

### <a name="create-the-application"></a>Criar o aplicativo

1. Navegue até o [portal do Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**
1. Pesquise por **Aplicativos Web Estáticos**
1. Clique em **Aplicativos Web Estáticos (Versão Prévia)**
1. Clique em **Criar**

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Criar um recurso de Aplicativos Web Estáticos do Azure no portal":::

1. Em **Assinatura**, aceite a assinatura que está listada ou selecione uma nova na lista suspensa.

1. Em _Grupo de recursos_, selecione **Novo**. Em _Novo nome do grupo de recursos_, insira **hugo-static-app** e selecione **OK**.

1. Em seguida, um nome para o seu aplicativo na caixa **Nome**. Os caracteres válidos incluem `a-z`, `A-Z`, `0-9` e `-`.

1. Para _Região_, selecione uma região disponível próxima de você.

1. Para _SKU_, selecione **Gratuito**.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Detalhes preenchidos":::

1. Clique no botão **Entrar com o GitHub**.

1. Selecione a **Organização** na qual você criou o repositório.

1. Selecione **hugo-static-app** como o _Repositório_.

1. Para _Branch_, selecione **main**.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Informações do GitHub concluídas":::

### <a name="build"></a>Build

Em seguida, você adiciona as definições de configuração que o processo de compilação usa para criar seu aplicativo. As definições a seguir configuram o arquivo de fluxo de trabalho da GitHub Action.

1. Clique em **Avançar: Build >** para editar a configuração do build

1. Defina o _Local do aplicativo_ para **/** .

1. Defina o _Local do artefato do aplicativo_ como **público**.

   Não é necessário um valor para _Local da API_, pois você não está implantando uma API no momento.

### <a name="review-and-create"></a>Examinar e criar

1. Clique no botão **Revisar + Criar** para verificar se os detalhes estão corretos.

1. Clique em **Criar** para iniciar a criação dos Aplicativos Web Estáticos do Azure e provisionar uma GitHub Action para a implantação.

1. Aguarde a conclusão da GitHub Action.

1. Na janela _Visão geral_ do portal do Azure para o recurso de Aplicativos Web Estáticos do Azure recém-criados, clique no link _URL_ para abrir o aplicativo implantado.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Aplicativo implantado":::

#### <a name="custom-hugo-version"></a>Versão personalizada do Hugo

Quando você gera um Aplicativo Web Estático, é gerado um [arquivo de fluxo de trabalho](./github-actions-workflow.md), que contém as definições de configuração de publicação do aplicativo. Você pode designar uma versão específica do Hugo no arquivo de fluxo de trabalho fornecendo um valor para `HUGO_VERSION` na seção `env`. A configuração de exemplo a seguir demonstra como definir o Hugo com uma versão específica.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](custom-domain.md)
