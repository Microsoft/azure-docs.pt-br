---
title: Adicionar uma API a aplicativos Web Estáticos do Azure com o Azure Functions
description: Introdução aos Aplicativos Web Estáticos do Azure adicionando uma API sem servidor ao seu aplicativo Web estático usando o Azure Functions.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: 53be2ee9b6b3ab46f081747aec58b9c99fc54f0d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349949"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Adicionar uma API a aplicativos Web Estáticos do Azure com o Azure Functions

Você pode adicionar APIs sem servidor a aplicativos Web Estáticos do Azure por meio da integração com o Azure Functions. Este artigo demonstra como adicionar e implantar uma API em um site de Aplicativos Web Estáticos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Conta do Azure com uma assinatura ativa.
  - Se você não tiver uma conta, poderá [criá-la gratuitamente](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Extensão do Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code
- Extensão do [Código do Servidor do Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer).
- [Node.js](https://nodejs.org/download/) executar o aplicativo de API localmente

## <a name="create-a-git-repository"></a>Crie um repositório Git

As etapas a seguir demonstram como criar um novo repositório e clonar os arquivos para o computador.

1. Verifique se você está conectado ao GitHub e, em seguida, navegue até [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate) para criar um novo repositório.
1. Na caixa _Nome do repositório_, insira **my-vanilla-api**.
1. Clique em **Criar repositório do modelo**.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Criar um novo repositório do vanilla-basic":::

Depois que o projeto for criado, copie a URL no navegador para o novo repositório. Use essa URL no Visual Studio Code para clonar o repositório git.

1. Pressione **F1** para abrir o comando da Paleta de Comandos.
1. Cole a URL no arquivo no _Git: Clone_ o prompt e pressione **Enter**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Clonar um projeto do GitHub usando o Visual Studio Code":::

    Siga os prompts para selecionar um local de repositório para clonar o projeto.

## <a name="create-the-api"></a>Criar a API

Em seguida, você cria um projeto Azure Functions como a API do aplicativo. 

1. Dentro do projeto _my-vanilla-api_, crie uma subpasta chamada **api**.
1. Pressione **F1** para abrir a Paleta de Comandos
1. Digite **Azure Functions: Crie um Novo Projeto...**
1. Pressione **Enter**
1. Escolha **Procurar**
1. Selecione a pasta **API** como o diretório para o espaço de trabalho do projeto
1. Escolha **Selecionar**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Captura de tela mostra a pasta P I e o botão Selecionar.":::

1. Forneça as seguintes informações nos prompts:

    - _Selecione um idioma_: escolha **JavaScript**
    - _Selecione um modelo para a primeira função do projeto_: Escolha o **gatilho HTTP**
    - _Forneça um nome de função_: digite **GetMessage**
    - _Nível de autorização_: Escolha **Anônimo**, que permite que qualquer pessoa chame seu ponto de extremidade de função.
        - Para saber mais sobre os níveis de autorização, consulte [Chaves de autorização](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

Visual Studio Code gera um projeto de Azure Functions com uma função disparada por HTTP.

Seu aplicativo agora tem uma estrutura de projeto semelhante ao exemplo a seguir.

```files
├── api
│   ├── GetMessage
│   │   ├── function.json
│   │   ├── index.js
│   │   └── sample.dat
│   ├── host.json
│   ├── local.settings.json
│   ├── package.json
│   └── proxies.json
├── index.html
├── readme.md
└── styles.css
```

Em seguida, você alterará a `GetMessage` função para retornar uma mensagem para o front-end.

1. Atualize a `GetMessage` função em _API/GetMessage/index.js_ com o código a seguir.

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. Atualize a `GetMessage` configuração em `api/GetMessage/function.json` com as configurações a seguir.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```

Com as configurações acima, o ponto de extremidade da API é:

- Disparado quando uma solicitação HTTP é feita à função
- Disponível para todas as solicitações, independentemente do status de autenticação
- Exposto por meio da rota _/api/message_ rota

## <a name="run-the-api-locally"></a>Executar a API localmente

O Visual Studio Code integra-se ao [Azure Functions Core Tools](../azure-functions/functions-run-local.md) para permitir que você execute esse projeto em seu computador de desenvolvimento local antes de publicar no Azure.

> [!TIP]
> Verifique se você tem todos os recursos listados na seção [pré-requisitos](#prerequisites) instalados antes de continuar.

1. Execute a função pressionando **F5** para iniciar o aplicativo functions.

1. Se o Azure Functions Core Tools ainda não estiver instalado, selecione **Instalar** no prompt.

    As ferramentas principais mostram a saída do aplicativo em execução no painel do _terminal_ . Como parte da saída, você pode ver o ponto de extremidade da URL de sua função disparada por HTTP localmente.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Captura de tela mostra a guia TERMINAL em que você pode ver o U R L.":::

1. Com as ferramentas principais em execução, navegue até a seguinte URL para verificar se a API está sendo executada corretamente: `http://localhost:7071/api/message` .

   A resposta no navegador deve ser semelhante ao exemplo a seguir:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Alt textscreenion mostra uma resposta de texto em um navegador.":::

1. Pressione **Shift + F5** para interromper a sessão de depuração.

### <a name="call-the-api-from-the-application"></a>Chamar a API do aplicativo

Quando implantado no Azure, as solicitações para a API são automaticamente roteadas para o aplicativo do Functions para solicitações enviadas à `api` rota. Trabalhando localmente, você precisa definir as configurações do aplicativo para solicitações de proxy para a API local.

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>Atualizar arquivos HTML para acessar a API

1. Em seguida, atualize o conteúdo do arquivo _index. html_ com o seguinte código para buscar o texto da função da API e exibi-lo na tela:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading content from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

1. Pressione **F5** para iniciar o projeto de API.

1. Pressione **F1** e escolha **Live Server: Abrir com Live Server**.

    Agora você deve ver a mensagem da API na página da Web.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Captura de tela mostra a mensagem P I em um navegador.":::

   > [!NOTE]
   > Você pode usar outros servidores HTTP ou proxies para atender ao arquivo `index.html`. O acesso ao `index.html` do `file:///` não funcionará.

1. Pressione **Shift + F5** para interromper o projeto de API.

### <a name="commit-and-push-your-changes-to-github"></a>Confirmar e enviar por push suas alterações para o GitHub

Ao usar o Visual Studio Code, confirme e envie por push suas alterações para o repositório Git remoto.

1. Pressione **F1** para abrir a Paleta de Comandos
1. Digite **Git: Confirme tudo**
1. Adicione uma mensagem de confirmação e pressione **Enter**
1. Pressione **F1**
1. Digite **git: Push** e pressione **Enter**

## <a name="create-a-static-web-app"></a>Criar um aplicativo Web estático

1. Navegue até o [portal do Azure](https://portal.azure.com)
1. Clique em **Criar um Recurso**.
1. Pesquisar **aplicativo Web estático**
1. Clique em **aplicativo Web estático (visualização)**
1. Clique em **Criar**

Em seguida, adicione as configurações específicas do aplicativo.

1. Selecione sua _Assinatura do Azure_.
1. Selecione ou crie um novo _Grupo de Recursos_
1. Nomeie o aplicativo **my-baunilha-api**.
1. Selecione a _Região_ mais próxima de você
1. Selecione o **SKU** _gratuito_
1. Clique no botão **Entrar com o GitHub** e autentique-se com o GitHub.
1. Selecione sua _Organização_ de preferência.
1. Selecione **my-vanilla-api** do menu suspenso do _Repositório_
1. Selecione **mestre** na lista suspensa _Branch_
1. Selecione a estrutura de sua escolha para a configuração de Build na lista suspensa _criar predefinições de compilação_

 > Esses campos refletirão a estrutura de projeto padrão do tipo de aplicativo. Altere os valores para se adequar ao seu aplicativo.

Em seguida, adicione os seguintes detalhes de compilação.

1. Insira **/** para o _local do aplicativo_.
1. Insira a **API** na caixa da _localização de API_.
1. Limpe o valor padrão fora do _Local do artefato de aplicativo_, deixando a caixa vazia.
1. Clique em **Revisar + Criar**.
1. Clique no botão **Criar**

    Depois de clicar no botão _criar_ , o Azure faz duas coisas. Primeiro, os serviços de nuvem subjacentes são criados para dar suporte ao aplicativo. Em seguida, um processo em segundo plano começa a compilar e implantar o aplicativo.

1. Clique no botão **ir para recurso** para levá-lo até a página de _visão geral_ do aplicativo Web.

    À medida que o aplicativo está sendo criado em segundo plano, você pode clicar na faixa que contém um link para exibir o status da compilação.

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="Fluxo de trabalho do GitHub":::

1. Depois que a implantação for concluída, a UO poderá navegar para o aplicativo Web clicando no link da _URL_ mostrado na página _visão geral_ .

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Acessar a URL do aplicativo estático no portal do Azure":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser manter esse aplicativo para uso adicional, poderá usar as etapas a seguir para excluir o aplicativo Web Estático do Azure e seus recursos relacionados.

1. Navegue até o [portal do Azure](https://portal.azure.com)
1. Na barra de pesquisa superior, digite **Grupos de recursos**
1. Clique em **Grupos de recursos**
1. Selecione **myResourceGroup**
1. Na página _myResourceGroup_, certifique-se de que os recursos listados são aqueles que deseja excluir.
1. Selecione **Excluir**
1. Digite **myResourceGroup** na caixa de entrada
1. Selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Definir as configurações do aplicativo](./application-settings.md)
