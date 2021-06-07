---
title: Serviço Confiável em JavaScript
description: Essa é a versão JavaScript da criação de um Serviço Confiável para Serviços de Comunicação.
author: dademath
manager: nimag
services: azure-communication-services
ms.author: ddematheu2
ms.date: 03/10/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 5b71a0581bf4f9d8239171e6abc56f87e7ae8183
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105152805"
---
## <a name="download-code"></a>Código de download

Encontre o código finalizado para este guia de início rápido no [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/trusted-authentication-service)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Visual Studio Code](https://code.visualstudio.com/) em uma das [plataformas compatíveis](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
- [Node.js](https://nodejs.org/), versões Active LTS e versões LTS de manutenção (10.14.1 recomendada). Use o comando `node --version` para verificar sua versão.
- A [Extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para Visual Studio Code.
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../../quickstarts/create-communication-resource.md).

## <a name="overview"></a>Visão geral

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="Diagrama para arquitetura de serviço confiável":::

Para este tutorial, vamos criar uma Função do Azure que funcionará como um serviço de provisionamento de token confiável. Você pode usar este tutorial para inicializar seu próprio serviço de provisionamento de token.

Esse serviço é responsável por autenticar usuários nos Serviços de Comunicação do Azure. Os usuários dos seus aplicativos de Serviços de Comunicação precisarão de um `Access Token` para participarem de conversas de chat e chamadas VoIP. A Função do Azure atuará como um middleware confiável entre o usuário e os Serviços de Comunicação. Isso permite que você provisione tokens de acesso sem expor a cadeia de conexão do recurso aos usuários.

Para obter mais informações, confira a documentação conceitual de [arquitetura cliente-servidor](../../concepts/client-and-server-architecture.md) e [autenticação e autorização](../../concepts/authentication.md).

## <a name="setting-up"></a>Configurando

### <a name="azure-functions-set-up"></a>Configuração do Azure Functions

Primeiro, vamos configurar a estrutura básica para nossa função do Azure. Instruções passo a passo sobre a configuração podem ser encontradas aqui: [Criar uma função usando o Visual Studio Code](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript)

Nossa Função do Azure requer a seguinte configuração:

- Linguagem: JavaScript
- Modelo: Gatilho de HTTP
- Nível de autorização: anônimo (poderá ser alternado mais tarde se você preferir um modelo de autorização diferente)
- Nome da função: definido pelo usuário

Depois de seguir as [instruções do Azure Functions](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript) com a configuração acima, você deve ter um projeto no Visual Studio Code para a Função do Azure com um arquivo `index.js` contendo a própria função. O código nesse arquivo deve ser o seguinte:

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

Agora, vamos continuar instalando as bibliotecas dos Serviços de Comunicação do Azure.

### <a name="install-communication-services-libraries"></a>Instalar bibliotecas dos serviços de comunicação

Usaremos a biblioteca `Identity` para gerar `User Access Tokens`.

Use o comando `npm install` para instalar o SDK de Identidade dos Serviços de Comunicação do Azure para JavaScript.

```console

npm install @azure/communication-identity --save

```

A opção `--save` lista a biblioteca como uma dependência no arquivo **package.json**.

Na parte superior do arquivo `index.js`, importe a interface para o `CommunicationIdentityClient`

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');
```

## <a name="access-token-generation"></a>Geração de token de acesso

Para permitir que nossa Função do Azure gere `User Access Tokens`, primeiro vamos precisar usar a cadeia de conexão para o recurso de Serviços de Comunicação.

Acesse o [guia de início rápido provisionamento de recursos](../../quickstarts/create-communication-resource.md) para obter mais informações sobre como recuperar a cadeia de conexão.

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

Em seguida, modificaremos a função original para gerar `User Access Tokens`.

`User Access Tokens` são gerados criando um usuário pelo método `createUser`. Depois que o usuário é criado, podemos usar o método `getToken` para gerar um token para ele, que a função do Azure retorna.

Para este exemplo, vamos configurar o escopo do token para `voip`. Outros escopos podem ser necessários para seu aplicativo. Saiba mais sobre os [escopos](../../quickstarts/access-tokens.md)

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.getToken(user, ["voip"]);

    context.res = {
        body: userToken
    };
}
```

Para `CommunicationUser` de Serviços de Comunicação existente, você pode ignorar a etapa de criação e apenas gerar um token de acesso. Veja mais detalhes no [guia de início rápido para criar tokens de acesso do usuário](../../quickstarts/access-tokens.md).

## <a name="test-the-azure-function"></a>Testar a Função do Azure

Execute a Função do Azure localmente usando `F5`. Isso inicializa a Função do Azure localmente e a torna acessível por meio de: `http://localhost:7071/api/FUNCTION_NAME`. Confira a documentação adicional sobre [execução local](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-locally)

Abra a URL no navegador e veja um corpo da resposta com a ID de usuário de comunicação, o token e o término do token.

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="Captura de tela mostrando um exemplo de resposta para a Função do Azure criada.":::

## <a name="deploy-the-function-to-azure"></a>Implantar a Função no Azure

Para implantar sua Função do Azure, siga as [instruções passo a passo](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#sign-in-to-azure)

Em resumo, você precisará:
1. Entrar no Azure por meio do Visual Studio
2. Publicar seu projeto em sua conta do Azure. Aqui, você precisará escolher uma assinatura existente.
3. Crie um recurso de Função do Azure usando o assistente do Visual Studio ou use um recurso existente. Para um novo recurso, será necessário configurá-lo para a região, o runtime e o identificador exclusivo desejados.
4. Aguarde o fim da implantação
5. Execute a função 🎉

## <a name="run-azure-function"></a>Executar a Função do Azure

Executar a Função do Azure usando a URL `http://<function-appn-ame>.azurewebsites.net/api/<function-name>`

Você pode encontrar a URL clicando com o botão direito do mouse na função em Visual Studio Code e copiando a URL da Função.

Para obter mais informações sobre [como executar a função do Azure](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-in-azure)
