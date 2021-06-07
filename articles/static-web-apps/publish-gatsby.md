---
title: 'Tutorial: Publicar um site do Gatsby em Aplicativos Web Estáticos do Azure'
description: Este tutorial mostra como implantar um aplicativo Gatsby em Aplicativos Web Estáticos do Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 4430ed34858077b13b4fec69756c1c7e9f3ef7ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652337"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps-preview"></a>Tutorial: Publicar um site do Gatsby em Aplicativos Web Estáticos do Azure (versão prévia)

Este artigo demonstra como criar e implantar um aplicativo Web [Gatsby](https://gatsbyjs.org) em [Aplicativos Web Estáticos do Azure](overview.md). O resultado final é um novo site do Aplicativos Web Estáticos do Azure (com GitHub Actions associadas) que lhe dão controle sobre como o aplicativo é criado e publicado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar um aplicativo Gatsby
> - Configurar um site do Aplicativos Web Estáticos do Azure
> - Implantar o aplicativo Gatsby no Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. Se você não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/).
- Uma conta do GitHub. Se você não tiver uma, [crie uma conta gratuita](https://github.com/join).
- [Node. js](https://nodejs.org) instalado.

## <a name="create-a-gatsby-app"></a>Criar um aplicativo Gatsby

Crie um aplicativo Gatsby usando a CLI (interface de linha de comando) do Gatsby:

1. Abra um terminal.
1. Use a ferramenta [npx](https://www.npmjs.com/package/npx) para criar um novo aplicativo com a CLI do Gatsby. Isso pode levar alguns minutos.

   ```bash
   npx gatsby new static-web-app
   ```

1. Navegue até o aplicativo recém-criado.

   ```bash
   cd static-web-app
   ```

1. Inicialize um repositório Git

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Envie por push seu aplicativo para o GitHub.

Você precisa ter um repositório no GitHub para criar um novo recurso do Aplicativos Web Estáticos do Azure.

1. Crie um repositório GitHub em branco (não crie um README) utilizando [https://github.com/new](https://github.com/new) chamado **gatsby-static-web-app**.

1. Em seguida, adicione o repositório do GitHub recém-criado ao seu repositório local como remoto. Adicione o nome de usuário do GitHub no lugar do espaço reservado `<YOUR_USER_NAME>` no comando a seguir.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
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

   :::image type="content" source="./media/publish-gatsby/create-in-portal.png" alt-text="Criar Aplicativos Web Estáticos (Versão Prévia) no portal":::

1. Em _Assinatura_, aceite a assinatura que está listada ou selecione uma nova na lista suspensa.

1. Em _Grupo de recursos_, selecione **Novo**. Em _Novo nome do grupo de recursos_, insira **gatsby-static-web-app** e selecione **OK**.

1. Em seguida, um nome para o seu aplicativo na caixa **Nome**. Os caracteres válidos incluem `a-z`, `A-Z`, `0-9` e `-`.

1. Para _Região_, selecione uma região disponível próxima de você.

1. Para _SKU_, selecione **Gratuito**.

   :::image type="content" source="./media/publish-gatsby/basic-app-details.png" alt-text="Detalhes preenchidos":::

1. Clique no botão **Entrar com o GitHub**.

1. Selecione a **Organização** na qual você criou o repositório.

1. Selecione **gatsby-static-web-app** como _Repositório_.

1. Para _Branch_, selecione **main**.

   :::image type="content" source="./media/publish-gatsby/completed-github-info.png" alt-text="Informações do GitHub concluídas":::

### <a name="build"></a>Build

Em seguida, adicione as definições de configuração que o processo de build usa para compilar seu aplicativo.

1. Clique em **Avançar: Build >** para editar a configuração do build

1. Para definir as configurações da etapa em GitHub Actions, defina _Local do aplicativo_ como **/** .

1. Defina o _Local do artefato do aplicativo_ como **público**.

   Não é necessário um valor para _Local da API_, pois você não está implantando uma API no momento.

   :::image type="content" source="./media/publish-gatsby/build-details.png" alt-text="Configurações do Build":::

### <a name="review-and-create"></a>Examinar e criar

1. Clique no botão **Revisar + Criar** para verificar se os detalhes estão corretos.

1. Clique em **Criar** para iniciar a criação do aplicativo Web estático do Serviço de Aplicativo e forneça uma GitHub Action para a implantação.

1. Assim que a implantação for concluída, clique em **Ir para o recurso**.

1. Na tela de recursos, clique no link _URL_ para abrir o aplicativo implantado. Talvez seja necessário aguardar um minuto ou dois para que a GitHub Action seja concluída.

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="Aplicativo implantado":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](custom-domain.md)
