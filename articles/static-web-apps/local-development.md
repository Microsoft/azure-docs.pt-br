---
title: Configurar o desenvolvimento local de Aplicativos Web Estáticos do Azure
description: Aprenda a definir seu ambiente de desenvolvimento local para Aplicativos Web Estáticos do Azure
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 4d6dae8a4f4ed83af3103e95e711bacdb62cf522
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91326160"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Configurar o desenvolvimento local da Versão para de Aplicativos Web Estáticos do Azure

Uma instância de Aplicativos Web Estáticos do Azure é composta de dois tipos diferentes de aplicativos. O primeiro é um aplicativo Web para seu conteúdo estático. Os aplicativos Web são frequentemente criados com estruturas e bibliotecas de front-end ou com geradores de site estáticos. O segundo é a API, que é um aplicativo Azure Functions que fornece um ambiente de desenvolvimento de back-end avançado.

Quando são executados na nuvem, os Aplicativos Web Estáticos do Azure mapeiam diretamente as solicitações à rota de `api` do aplicativo Web para o aplicativo Azure Functions sem exigir configuração de CORS. Localmente, você precisa configurar seu aplicativo para imitar esse comportamento.

Este artigo demonstra as práticas recomendadas para o desenvolvimento local, incluindo os seguintes conceitos:

- Configurar o aplicativo Web para conteúdo estático
- Configurar o aplicativo Azure Functions para a API do seu aplicativo
- Depurar e executar o aplicativo
- Práticas recomendadas para a estrutura de pastas e arquivos do aplicativo

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio Code](https://code.visualstudio.com/)
- [Extensão do Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code
- [Extensão Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) para Visual Studio Code
  - Necessário somente se você não estiver usando uma estrutura de JavaScript de front-end ou a CLI do gerador de site estático

## <a name="run-projects-locally"></a>Executar projetos localmente

A execução local de um Aplicativo Web Estático do Azure envolve três processos, dependendo de o projeto conter ou não uma API.

- Executar um servidor Web local
- Executar a API
- Conectar o projeto Web à API

Dependendo de como um site é criado, um servidor Web local pode ser exigido ou não para a execução do aplicativo no navegador. Quando você usa estruturas de JavaScript de front-end e geradores de site estáticos, essa funcionalidade é interna nas suas respectivas CLIs (Interfaces de Linha de Comando). Os links a seguir apontam para a referência da CLI de um conjunto de estruturas, bibliotecas e geradores.

### <a name="javascript-frameworks-and-libraries"></a>Estruturas e bibliotecas de JavaScript

- [CLI do Angular](https://angular.io/cli)
- [CLI do Vue](https://cli.vuejs.org/guide/creating-a-project.html)
- [CLI do React](https://create-react-app.dev/)

### <a name="static-site-generators"></a>Geradores de site estáticos

- [CLI do Gatsby](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Jekyll](https://jekyllrb.com/docs/usage/)

Se você estiver usando uma ferramenta de CLI para atender seu site, pule para a seção [Executando a API](#run-api-locally).

### <a name="running-a-local-web-server-with-live-server"></a>Executando um servidor Web local com o Live Server

A extensão Live Server para Visual Studio Code fornece um servidor Web de desenvolvimento local que fornece conteúdo estático.

#### <a name="create-a-repository"></a>Criar um repositório

1. Verifique se você está conectado ao GitHub e, navegue até [https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) e crie um novo projeto do GitHub chamado **baunilha-API**, usando este modelo.

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="Janela do novo repositório do GitHub":::

1. Abra o Visual Studio Code.

1. Pressione **F1** para abrir a Paleta de Comandos.

1. Digite **clone** na caixa de pesquisa e selecione **Git: Clone**.

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="Opção Clone do Git no Visual Studio Code":::

1. Insira o valor a seguir como **URL do Repositório**.

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. Selecione um local de pasta para o novo projeto.

1. Quando for solicitada a abertura do repositório clonado, selecione **Abrir**.

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="Abrir em nova janela":::

O Visual Studio Code abre o projeto clonado no editor.

### <a name="run-the-website-locally-with-live-server"></a>Executar o site localmente com o Live Server

1. Pressione **F1** para abrir a Paleta de Comandos.

1. Digite **Live Server** na caixa de pesquisa e selecione **Live Server: Open with Live Server**

    Uma guia do navegador é aberta e exibe o aplicativo.

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="Site estático simples em execução no navegador":::

    É um aplicativo que faz uma solicitação HTTP ao ponto de extremidade `api/message`. No momento, a solicitação está falhando porque a parte da API do aplicativo precisa ser iniciada.

### <a name="run-api-locally"></a>Executar API localmente

As APIs de Aplicativos Web Estáticos do Azure são alimentadas pelo Azure Functions. Confira [Adicionar uma API aos Aplicativos Web Estáticos do Azure com o Azure Functions](add-api.md) para obter detalhes sobre como adicionar uma API a um projeto de Aplicativos Web Estáticos do Azure.

Como parte do processo de criação da API, uma configuração de inicialização é criada para o Visual Studio Code. Essa configuração está localizada na pasta _.vscode_. A pasta contém todas as configurações necessárias para criar e executar a API localmente.

1. No Visual Studio Code, pressione **F5** para iniciar a API.

1. Uma nova instância de terminal é aberta e mostra a saída do processo de compilação da API.

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="API em execução no terminal do Visual Studio Code":::

   A barra de status no Visual Studio Code agora está laranja. Essa cor indica que a API está em execução e o depurador está anexado.

1. Pressione **Ctrl/Cmd** e clique na URL no terminal para abrir uma janela do navegador que chamará a API.

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="Resultado da chamada à API exibido pelo navegador":::

### <a name="debugging-the-api"></a>Depurando a API

1. Abra o arquivo _API/GetMessage/index.js_ no Visual Studio Code.

1. Clique na margem esquerda da linha 2 para definir um ponto de interrupção. Um ponto vermelho é exibido, o que indica que o ponto de interrupção foi definido.

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Ponto de interrupção no Visual Studio Code":::

1. No navegador, atualize a página em execução em <http://127.0.0.1:7071/api/message>.

1. O Visual Studio Code atinge o ponto de interrupção e a execução do programa é pausada.

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="Alcance do ponto de interrupção no Visual Studio Code":::

   Uma [experiência de depuração completa está disponível no Visual Studio Code](https://code.visualstudio.com/Docs/editor/debugging) para sua API.

1. Pressione o botão **Continuar** na barra de depuração para continuar a execução.

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Botão Continuar no Visual Studio Code":::

### <a name="calling-the-api-from-the-application"></a>Chamando a API do aplicativo

Quando implantados, os Aplicativos Web Estáticos do Azure mapeiam automaticamente essas solicitações para os pontos de extremidade na pasta _api_. Esse mapeamento faz com que as solicitações do aplicativo à API sejam semelhantes ao exemplo a seguir.

```javascript
let response = await fetch("/api/message");
```

Dependendo de seu aplicativo ter sido ou não criado com uma CLI de estrutura de JavaScript, há duas maneiras de configurar o caminho para a rota de `api` durante a execução local do seu aplicativo.

- Arquivos de configuração do ambiente (recomendado para estruturas e bibliotecas de JavaScript)
- Proxy local

### <a name="environment-configuration-files"></a>Arquivos de configuração de ambiente

Se você está criando seu aplicativo com estruturas de front-end que possuem uma CLI, deve usar arquivos de configuração de ambiente. Cada estrutura ou biblioteca tem uma maneira diferente de lidar com esses arquivos de configuração de ambiente. É comum ter um arquivo de configuração para desenvolvimento, que é usado quando seu aplicativo está sendo executado localmente, e outro para produção, que é usado quando seu aplicativo está em execução na produção. A CLI da estrutura de JavaScript ou do gerador de site estático que você está usando saberá automaticamente usar o arquivo de desenvolvimento localmente e o arquivo de produção quando seu aplicativo for compilado por Aplicativos Web Estáticos do Azure.

No arquivo de configuração de desenvolvimento, você pode especificar o caminho para a API, que aponta para o local `http:127.0.0.1:7071` onde a API para seu site está sendo executada localmente.

```
API=http:127.0.0.1:7071/api
```

No arquivo de configuração de produção, especifique o caminho para a API como `api`. Assim, seu aplicativo chamará a API por meio de "yoursite.com/api" durante a execução na produção.

```
API=api
```

Esses valores de configuração podem ser referenciados como variáveis de ambiente de Node no JavaScript do aplicativo Web.

```js
let response = await fetch(`${process.env.API}/message`);
```

Quando a CLI é usada para executar seu site no modo de desenvolvimento ou a fim de compilar o site para produção, o valor de `process.env.API` é substituído pelo valor do arquivo de configuração apropriado.

Para obter mais informações sobre como configurar arquivos de ambiente para bibliotecas e estruturas de JavaScript de front-end, confira estes artigos:

- [Variáveis de ambiente Angular](https://angular.io/guide/build#configuring-application-environments)
- [React: adicionando variáveis de ambiente personalizadas](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Vue: modos e variáveis de ambiente](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>Reiniciar Live Server

1. Pressione **F1** para abrir a Paleta de Comandos no Visual Studio Code.

1. Digite **Live Server** e selecione **Live Server: Parar o Live Server**.

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="Comando Stop Live Server na paleta de comandos do Visual Studio":::

1. Pressione **F1** para abrir a Paleta de Comandos.

1. Digite **Live Server** e selecione **Live Server: Abrir com Live Server**.

1. Atualize o aplicativo em execução em `http://locahost:3000`. O navegador agora exibe a mensagem retornada pela API.

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="Olá da API exibida no navegador":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Definir as configurações do aplicativo](application-settings.md)
