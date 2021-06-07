---
title: 'Tutorial: Publicar um site do VuePress em Aplicativos Web Estáticos do Azure'
description: Este tutorial mostra como implantar um aplicativo VuePress em Aplicativos Web Estáticos do Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 6f0616df885a7f8fcd76337c810bc368aa02f3c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650432"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>Tutorial: Publicar um site do VuePress na Versão Prévia dos Aplicativos Web Estáticos do Azure

Este artigo demonstra como criar e implantar um aplicativo web [VuePress](https://vuepress.vuejs.org/) para [Aplicativos Web Estáticos do Azure](overview.md). O resultado final é um novo aplicativo de Aplicativos Web Estáticos do Azure com GitHub Actions associadas que lhe dão controle sobre como o aplicativo é criado e publicado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar um aplicativo VuePress
> - Configurar Aplicativos Web Estáticos do Azure
> - Implantar o aplicativo VuePress no Azure

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. Se você não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/).
- Uma conta do GitHub. Se você não tiver uma, [crie uma conta gratuita](https://github.com/join).
- [Node. js](https://nodejs.org) instalado.

## <a name="create-a-vuepress-app"></a>Criar um Aplicativo VuePress

Crie um aplicativo VuePress na CLI (Interface de Linha de Comando):

1. Crie uma nova pasta para o aplicativo VuePress.

   ```bash
   mkdir static-site
   ```

1. Adicione um arquivo _README.md_ à pasta.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. Inicialize o arquivo _package.json_.

   ```bash
   npm init -y
   ```

1. Adicione VuePress como um `devDependency`.

   ```bash
   npm install --save-dev vuepress
   ```

1. Abra o arquivo _package.json_ em um editor de texto e adicione um comando de compilação à seção [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html).

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. Crie um arquivo _.gitignore_ para excluir a pasta _módulos\_do nó_.

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Inicialize um repositório Git.

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Envie por push seu aplicativo para o GitHub.

Você precisa de um repositório no GitHub para conectar os Aplicativos Web Estáticos do Azure. As etapas a seguir mostram como criar um repositório para seu site.

1. Crie um repositório GitHub em branco (não crie um README) a partir do [https://github.com/new](https://github.com/new)vuepress-static-app **nomeado**.

1. Adicione o repositório do GitHub como sendo remoto ao seu repositório local. Adicione o nome de usuário do GitHub no lugar do espaço reservado `<YOUR_USER_NAME>` no comando a seguir.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. Envie por push seu repositório local para o GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Implantar o aplicativo Web

As etapas a seguir mostram como criar um novo aplicativo de Aplicativos Web Estáticos e implantá-lo em um ambiente de produção.

### <a name="create-the-application"></a>Criar o aplicativo

1. Navegue até o [portal do Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**
1. Pesquise por **Aplicativos Web Estáticos**
1. Clique em **Aplicativos Web Estáticos (Versão Prévia)**
1. Clique em **Criar**

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="Criar Aplicativos Web Estáticos (Versão Prévia) no portal":::

1. Em **Assinatura**, aceite a assinatura que está listada ou selecione uma nova na lista suspensa.

1. Em _Grupo de recursos_, selecione **Novo**. Em _Novo nome do grupo de recursos_, insira **vuepress-static-app** e selecione **OK**.

1. Em seguida, um nome para o seu aplicativo na caixa **Nome**. Os caracteres válidos incluem `a-z`, `A-Z`, `0-9` e `-`.

1. Para _Região_, selecione uma região disponível próxima de você.

1. Para _SKU_, selecione **Gratuito**.

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="Detalhes preenchidos":::

1. Clique no botão **Entrar com o GitHub**.

1. Selecione a **Organização** na qual você criou o repositório.

1. Selecione **vuepress-static-app** como o _Repositório_.

1. Para _Branch_, selecione **main**.

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="Informações do GitHub concluídas":::

### <a name="build"></a>Build

Em seguida, você adiciona as definições de configuração que o processo de compilação usa para criar seu aplicativo. As definições a seguir configuram o arquivo de fluxo de trabalho da GitHub Action.

1. Clique em **Avançar: Build >** para editar a configuração do build

1. Defina o _Local do aplicativo_ para **/** .

1. Defina o _Local do artefato do aplicativo_ como **.vuepress/dist**.

Não é necessário um valor para _Local da API_, pois você não está implantando uma API no momento.

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="Configurações do Build":::

### <a name="review-and-create"></a>Examinar e criar

1. Clique no botão **Revisar + Criar** para verificar se os detalhes estão corretos.

1. Clique em **Criar** para iniciar a criação dos Aplicativos Web Estáticos do Azure e provisionar uma GitHub Action para a implantação.

1. Assim que a implantação for concluída, clique em **Ir para o recurso**.

1. Na tela de recursos, clique no link _URL_ para abrir o aplicativo implantado. Talvez seja necessário aguardar um minuto ou dois para que a GitHub Action seja concluída.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="Aplicativo implantado":::

### <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](custom-domain.md)
