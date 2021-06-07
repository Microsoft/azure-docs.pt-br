---
title: 'Tutorial: Publicar um site do Jekyll nos Aplicativos Web Estáticos do Azure'
description: Saiba como implantar um aplicativo do Jekyll nos Aplicativos Web Estáticos do Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: cshoe
ms.openlocfilehash: 8c6764ad5b63aa2fde07326ab986404ea4312316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99585170"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>Tutorial: Publicar um site do Jekyll na Versão Prévia dos Aplicativos Web Estáticos do Azure

Este artigo demonstra como criar e implantar um aplicativo Web do [Jekyll](https://jekyllrb.com/) nos [Aplicativos Web Estáticos do Azure](overview.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar um site do Jekyll
> - Configurar Aplicativos Web Estáticos do Azure
> - Implantar o aplicativo do Jekyll no Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Instalar o [Jekyll](https://jekyllrb.com/docs/installation/)
  - Use o Subsistema do Windows para Linux e siga as instruções do Ubuntu, se necessário.
- Uma conta do Azure com uma assinatura ativa. Se você não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/).
- Uma conta do GitHub. Se você não tiver uma, [crie uma conta gratuita](https://github.com/join).

## <a name="create-jekyll-app"></a>Criar aplicativo do Jekyll

Crie um aplicativo do Jekyll usando a CLI (interface de linha de comando) do Jekyll:

1. No terminal, execute a CLI do Jekyll para criar um aplicativo.

   ```bash
   jekyll new static-app
   ```

1. Navegue até o aplicativo recém-criado.

   ```bash
   cd static-app
   ```

1. Inicialize um novo repositório Git.

   ```bash
    git init
   ```

1. Confirme as alterações.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Envie por push seu aplicativo para o GitHub.

Os Aplicativos Web Estáticos do Azure usam o GitHub para publicar seu site. As etapas a seguir mostram como criar um repositório GitHub.

1. Crie um repositório GitHub em branco (não crie um README) em [https://github.com/new](https://github.com/new) chamado **jekyll-azure-static**.

1. Adicione o repositório do GitHub como sendo remoto ao seu repositório local. Adicione o nome de usuário do GitHub no lugar do espaço reservado `<YOUR_USER_NAME>` no comando a seguir.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. Envie por push seu repositório local para o GitHub.

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > O seu git branch pode ter um nome diferente de `main`. Substitua `main` neste comando pelo valor correto.

## <a name="deploy-your-web-app"></a>Implantar o aplicativo Web

As etapas a seguir mostram como criar um novo aplicativo de site estático e implantá-lo em um ambiente de produção.

### <a name="create-the-application"></a>Criar o aplicativo

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Clique em **Criar um Recurso**.

1. Pesquise **Aplicativos Web Estáticos**.

1. Clique em **Aplicativos Web Estáticos (Versão Prévia)** .

1. Clique em **Criar**.

1. Em **Assinatura**, aceite a assinatura que está listada ou selecione uma nova na lista suspensa.

1. Em _Grupo de recursos_, selecione **Novo**. Em _Novo nome do grupo de recursos_, insira **jekyll-static-app** e selecione **OK**.

1. Em seguida, forneça um nome para o seu aplicativo na caixa _Nome_. Os caracteres válidos incluem `a-z`, `A-Z`, `0-9` e `-`.

1. Para _Região_, selecione uma região disponível próxima de você.

1. Para _SKU_, selecione **Gratuito**.

    :::image type="content" source="./media/publish-jekyll/basic-app-details.png" alt-text="Detalhes preenchidos":::

1. Clique no botão **Entrar com o GitHub**.

1. Selecione a **Organização** na qual você criou o repositório.

1. Selecione **jekyll-static-app** como o _Repositório_.

1. Para _Branch_, selecione **main**.

    :::image type="content" source="./media/publish-jekyll/completed-github-info.png" alt-text="Informações do GitHub concluídas":::

### <a name="build"></a>Build

Em seguida, você adiciona as definições de configuração que o processo de compilação usa para criar seu aplicativo. As definições a seguir configuram o arquivo de fluxo de trabalho da GitHub Action.

1. Clique em **Avançar: Compilar >** para editar a configuração de build.

1. Defina a _Localização do aplicativo_ como **/_site**.

1. Deixe _Local do artefato do aplicativo_ em branco.

   Não é necessário um valor para _Local da API_, pois você não está implantando uma API no momento.

### <a name="review-and-create"></a>Examinar e criar

1. Clique no botão **Revisar + Criar** para verificar se os detalhes estão corretos.

1. Clique em **Criar** para iniciar a criação dos Aplicativos Web Estáticos do Azure e provisionar uma GitHub Action para a implantação.

1. A implantação primeiro falhará, pois o arquivo de fluxo de trabalho precisa de algumas configurações específicas do Jekyll. Para adicionar essas configurações, navegue até o terminal e efetue pull do commit com a GitHub Action para o computador.

   ```bash
   git pull
   ```

1. Abra o aplicativo do Jekyll em um editor de texto e abra o arquivo _.github/workflows/azure-pages-<NOME_DO_FLUXO_DE_TRABALHO>.yml_.

1. Antes da linha `- name: Build And Deploy`, adicione o bloco de configuração a seguir.

    ```yml
    - name: Set up Ruby
      uses: ruby/setup-ruby@v1.59.1
      with:
        ruby-version: 2.6
    - name: Install dependencies
      run: bundle install
    - name: Jekyll build
      run: jekyll build
    ```

1. Confirme o fluxo de trabalho atualizado e envie por push para o GitHub.

    ```bash
    git add -A
    git commit -m "Updating GitHub Actions workflow"
    git push
    ```

1. Aguarde a conclusão da GitHub Action.

1. Na janela _Visão geral_ do portal do Azure, clique no link da _URL_ para abrir o aplicativo implantado.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="Aplicativo implantado":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](custom-domain.md)
