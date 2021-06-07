---
title: 'Tutorial: Implantar sites do Nuxt.js renderizados no lado do servidor no serviço Aplicativos Web Estáticos do Azure'
description: Gere e implante sites dinâmicos do Nuxt.js com o serviço Aplicativos Web Estáticos do Azure.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: 8f21f5fa8ee9035fe594cecff37a63b1ef2115cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97563459"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps-preview"></a>Implantar sites do Nuxt.js renderizados no lado do servidor na Versão Prévia do Aplicativos Web Estáticos do Azure

Neste tutorial, você aprenderá a implantar um site estático gerado por [Nuxt.js](https://nuxtjs.org) no serviço [Aplicativos Web Estáticos do Azure](overview.md). Para começar, você aprenderá a configurar e implantar um aplicativo Nuxt.js. Durante esse processo, você também aprende a lidar com desafios comuns frequentemente enfrentados ao gerar páginas estáticas com o Nuxt.js

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/).
- Uma conta do GitHub. [Crie uma conta gratuitamente](https://github.com/join).
- [Node. js](https://nodejs.org) instalado.

## <a name="set-up-a-nuxtjs-app"></a>Configurar um aplicativo Nuxt.js

Você pode configurar um novo projeto Nuxt.js usando `create-nuxt-app`. Em vez de um novo projeto, neste tutorial, você começa clonando um repositório existente. Esse repositório é configurado para demonstrar como implantar um aplicativo dinâmico Nuxt.js como um site estático.

1. Crie um novo repositório em sua conta do GitHub a partir de um modelo de repositório.
1. Navegue para [http://github.com/staticwebdev/nuxtjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nuxtjs-starter/generate)
1. Nomeie o repositório **nuxtjs-starter**
1. Em seguida, clone o novo repositório em seu computador. Certifique-se de substituir <YOUR_GITHUB_ACCOUNT_NAME> pelo nome da sua conta.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. Navegue até o novo aplicativo Nuxt.js clonado:

   ```bash
   cd nuxtjs-starter
   ```

1. Instale as dependências:

    ```bash
    npm install
    ```

1. Inicie o aplicativo Nuxt.js em desenvolvimento:

    ```bash
    npm run dev
    ```

Navegue até `http://localhost:3000` para abrir o aplicativo, onde você deverá ver o seguinte site aberto em seu navegador preferido:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="Iniciar aplicativo Nuxt.js":::

Ao clicar em uma estrutura/biblioteca, você verá uma página de detalhes sobre o item selecionado:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="Página de detalhes":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>Gerar um site estático a partir do build do Nuxt.js

Quando você cria um site Nuxt.js usando `npm run build`, o aplicativo é criado como um aplicativo Web tradicional, não um site estático. Para gerar um site estático, use a seguinte configuração de aplicativo.

1. Atualize o script de build do _package.json_ para gerar apenas um site estático usando o comando `nuxt generate`:

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate",
    },
    ```

    Agora, com esse comando em vigor, o serviço Aplicativos Web Estáticos executará o script `build` sempre que você enviar por push uma confirmação.

1. Gerar um site estático:

    ```bash
    npm run build
    ```

    O Nuxt.js gerará o site estático e o copiará em uma pasta _dist_ na raiz do seu diretório de trabalho.

    > [!NOTE]
    > Essa pasta está listada no arquivo _.gitignore_ porque ele deve ser gerado pelo CI/CD quando você faz a implantação.

## <a name="push-your-static-website-to-github"></a>Enviar por push seu site estático para o GitHub

O serviço Aplicativos Web Estáticos do Azure implanta seu aplicativo de um repositório GitHub e continua fazendo isso para todas as confirmações enviadas por push a um branch designado. Use os comandos a seguir para sincronizar suas alterações com o GitHub.

1. Estagie todos arquivos alterados:

    ```bash
    git add .
    ```

1. Confirmar todas as alterações

    ```bash
    git commit -m "Update build config"
    ```

1. Envie por push suas alterações para o GitHub.

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>Implantar seu site estático

As etapas a seguir mostram como vincular o aplicativo que você acabou de enviar por push para o GitHub ao serviço Aplicativos Web Estáticos do Azure. No Azure, você pode implantar o aplicativo em um ambiente de produção.

### <a name="create-an-azure-static-web-apps-preview-resource"></a>Criar uma versão prévia do recurso Aplicativos Web Estáticos do Azure

1. Navegue até o [portal do Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**
1. Pesquise por **Aplicativos Web Estáticos**
1. Clique em **Aplicativos Web Estáticos (Versão Prévia)**
1. Clique em **Criar**

1. Selecione uma assinatura na lista suspensa *Assinatura* ou use o valor padrão.
1. Clique no link **Novo** abaixo da lista suspensa *Grupo de recursos*. Em *Novo nome do grupo de recursos*, digite **mystaticsite** e clique em **OK**
1. Forneça um nome globalmente exclusivo para seu aplicativo na caixa de texto **Nome**. Os caracteres válidos incluem `a-z`, `A-Z`, `0-9` e `-`. Esse valor é usado como o prefixo de URL para seu aplicativo estático no formato `https://<APP_NAME>.azurestaticapps.net`.
1. Na lista suspensa *Região*, escolha a região mais próxima de você.
1. Selecione **Gratuito** na lista suspensa SKU.

   :::image type="content" source="media/deploy-nuxtjs/create-static-web-app.png" alt-text="Criar Aplicativo Web Estático":::

### <a name="add-a-github-repository"></a>Adicionar um repositório GitHub

A nova conta do serviço Aplicativos Web Estáticos precisa acessar o repositório com seu aplicativo Nuxt.js para que ele possa implantar confirmações automaticamente.

1. Clique no botão **Entrar com o GitHub**
1. Selecione a **Organização** sob o qual você criou o repositório para o seu projeto Nuxt.js, que pode ser seu nome de usuário do GitHub.
1. Encontre e selecione o nome do repositório que você criou anteriormente.
1. Escolha **principal** como o branch na lista suspensa *Branch*.

   :::image type="content" source="media/deploy-nuxtjs/connect-github.png" alt-text="Conectar o GitHub":::

### <a name="configure-the-build-process"></a>Configurar o processo de compilação

O serviço Aplicativos Web Estáticos do Azure é criado para executar automaticamente tarefas comuns, como a instalação de módulos npm e a execução de `npm run build` durante cada implantação. No entanto, há algumas configurações, como a pasta de origem do aplicativo e a pasta de destino do build, que você precisa configurar manualmente.

1. Clique na guia **Build** para configurar a pasta de saída estática.

      :::image type="content" source="media/deploy-nuxtjs/build-tab.png" alt-text="Guia Build":::

1. Digite **dist** na caixa de texto *Local do artefato do aplicativo*.

### <a name="review-and-create"></a>Examinar e criar

1. Clique no botão **Revisar + Criar** para verificar se os detalhes estão corretos.
1. Clique em **Criar** para iniciar a criação recurso e provisionar uma GitHub Action para a implantação.
1. Quando a implantação estiver concluída, clique em **Ir para o recurso**.
1. Na janela _Visão geral_, clique no link da *URL* para abrir o aplicativo implantado. 

Se o site não carregar imediatamente, então o fluxo de trabalho de ações do GitHub Actions ainda está em execução em segundo plano. Depois que o fluxo de trabalho for concluído, você poderá clicar para atualizar o navegador e exibir seu aplicativo Web.

Você pode verificar o status dos fluxos de trabalho do Actions navegando até o Actions do seu repositório:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>Sincronizar alterações

Quando você criou o aplicativo, o serviço Aplicativos Web Estáticos do Azure criou um arquivo de fluxo de trabalho do GitHub Actions em seu repositório. Você precisa colocar esse arquivo em seu repositório local para que seu histórico de git seja sincronizado.

Retorne ao terminal e execute o comando `git pull origin main`.

## <a name="configure-dynamic-routes"></a>Configurar rotas dinâmicas

Navegue até o novo site implantado e clique em um dos logotipos de estrutura ou de biblioteca. Em vez de obter uma página de detalhes, você recebe uma página de erro 404.

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="404 em rotas dinâmicas":::

O motivo disso é que quando o Nuxt.js gerou o site estático, ele fez isso apenas para a página inicial. O Nuxt. js pode gerar arquivos `.html` estáticos equivalentes para cada arquivo de páginas `.vue`, mas há uma exceção. 

Se a página for uma página dinâmica, por exemplo `_id.vue`, ela não terá informações suficientes para gerar um HTML estático a partir dessa página dinâmica. Você precisará fornecer explicitamente os caminhos possíveis para as rotas dinâmicas.

## <a name="generate-static-pages-from-dynamic-routes"></a>Gerar páginas estáticas utilizando rotas dinâmicas

1. Atualize o arquivo _nuxt.config.js_ para que o Nuxt. js use uma lista de todos os dados disponíveis para gerar páginas estáticas para cada estrutura/biblioteca:

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` é uma função assíncrona. Portanto, você pode fazer uma solicitação para uma API nessa função e usar a lista retornada para gerar os caminhos.

2. Envie por push as novas alterações para seu repositório GitHub e aguarde alguns minutos enquanto o GitHub Actions compila o site novamente. Após a conclusão da compilação, o erro 404 desaparece.

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="404 em rotas dinâmicas corrigido":::

> [!div class="nextstepaction"]
> [Configurar um domínio personalizado](custom-domain.md)
