---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 68b5fc7c958f611c43ba3f38bf30ceb63608886a
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113334"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/), versões Active LTS e Maintenance LTS (8.11.1 e 10.14.1 recomendadas).
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../create-communication-resource.md).

## <a name="setting-up"></a>Ao configurar o

### <a name="create-a-new-nodejs-application"></a>Criar um aplicativo Node.js

Abra o terminal ou a janela de comando para criar um diretório para seu aplicativo e navegue até ele.

```console
mkdir access-tokens-quickstart && cd access-tokens-quickstart
```

Execute `npm init -y` para criar um arquivo **package.json** com as configurações padrão.

```console
npm init -y
```

### <a name="install-the-package"></a>Instalar o pacote

Use o comando `npm install` para instalar o SDK de Identidade dos Serviços de Comunicação do Azure para JavaScript.

```console

npm install @azure/communication-identity --save

```

A opção `--save` lista a biblioteca como uma dependência no arquivo **package.json**.

## <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Abra um arquivo de texto novo no editor de código
1. Adicione uma chamada `require` para carregar o `CommunicationIdentityClient`
1. Crie a estrutura do programa, incluindo um tratamento de exceções básico

Use o seguinte código para começar:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');

const main = async () => {
  console.log("Azure Communication Services - Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered an error");
  console.log(error);
})
```

1. Salve o novo arquivo como **issue-access-token.js** no diretório *access-tokens-quickstart*.

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie a instância de um `CommunicationIdentityClient` com a sua cadeia de conexão. O código abaixo recupera a cadeia de conexão do recurso de uma variável de ambiente chamada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Saiba como [gerenciar a cadeia de conexão do seu recurso](../create-communication-resource.md#store-your-connection-string).

Adicione o seguinte código ao método `main`:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

Como alternativa, você pode separar o ponto de extremidade e a chave de acesso.
```javascript
// This code demonstrates how to fetch your endpoint and access key
// from an environment variable.
const endpoint = process.env["COMMUNICATION_SERVICES_ENDPOINT"];
const accessKey = process.env["COMMUNICATION_SERVICES_ACCESSKEY"];
const tokenCredential = new AzureKeyCredential(accessKey);
// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(endpoint, tokenCredential)
```

Se você tiver configurado a identidade gerenciada, consulte [Usar identidades gerenciadas](../managed-identity.md). Também é possível autenticar com a identidade gerenciada.
```javascript
const endpoint = process.env["COMMUNICATION_SERVICES_ENDPOINT"];
const tokenCredential = new DefaultAzureCredential();
const identityClient = new CommunicationIdentityClient(endpoint, tokenCredential);
```

## <a name="create-an-identity"></a>Criar uma identidade

Os Serviços de Comunicação do Azure mantêm um diretório de identidade leve. Use o método `createUser` para criar uma entrada no diretório com um `Id` exclusivo. Armazene a identidade recebida com o mapeamento para os usuários do aplicativo. Por exemplo, armazenando-os no banco de dados do servidor de aplicativos. A identidade é necessária posteriormente para emitir tokens de acesso.

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="issue-access-tokens"></a>Emitir tokens de acesso

Use o método `getToken` a fim de emitir um token de acesso para uma identidade existente dos Serviços de Comunicação. O parâmetro `scopes` define o conjunto de primitivos, que autorizará esse token de acesso. Confira a [lista de ações compatíveis](../../concepts/authentication.md). A nova instância do parâmetro `communicationUser` pode ser construída com base na representação da cadeia de caracteres da identidade do Serviço de Comunicação do Azure.

```javascript
// Issue an access token with the "voip" scope for an identity
let tokenResponse = await identityClient.getToken(identityResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Os tokens de acesso são credenciais de curta duração que precisam ser reemitidas. Deixar de fazer isso pode causar a interrupção da experiência dos usuários do aplicativo. A propriedade de resposta `expiresOn` indica o tempo de vida do token de acesso.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Criar uma identidade e emitir um token de acesso na mesma solicitação

Use o método `createUserAndToken` para criar uma identidade dos Serviços de Comunicação e emitir um token de acesso para ela. O parâmetro `scopes` define o conjunto de primitivos, que autorizará esse token de acesso. Confira a [lista de ações compatíveis](../../concepts/authentication.md).

```javascript
// Issue an identity and an access token with the "voip" scope for the new identity
let identityTokenResponse = await identityClient.createUserAndToken(["voip"]);
const { token, expiresOn, user } = identityTokenResponse;
console.log(`\nCreated an identity with ID: ${user.communicationUserId}`);
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

## <a name="refresh-access-tokens"></a>Tokens de acesso de atualização

A atualização de tokens de acesso é tão fácil quanto chamar `getToken` com a mesma identidade que foi usada para emitir os tokens. Também é preciso fornecer a `scopes` dos tokens atualizados.

```javascript
// // Value of identityResponse represents the Azure Communication Services identity stored during identity creation and then used to issue the tokens being refreshed
let refreshedTokenResponse = await identityClient.getToken(identityResponse, ["voip"]);
```


## <a name="revoke-access-tokens"></a>Revogar tokens de acesso

Em alguns casos, você pode revogar explicitamente os tokens de acesso. Por exemplo, quando o usuário de um aplicativo altera a senha usada para realizar a autenticação no serviço. O método `revokeTokens` invalida todos os tokens de acesso ativos que foram emitidos para a identidade.

```javascript
await identityClient.revokeTokens(identityResponse);
console.log(`\nSuccessfully revoked all access tokens for identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="delete-an-identity"></a>Excluir uma identidade

A exclusão de uma identidade revoga todos os tokens de acesso ativos e impede que você emita novos tokens de acesso para a identidade. Isso também remove todo o conteúdo persistente associado à identidade.

```javascript
await identityClient.deleteUser(identityResponse);
console.log(`\nDeleted the identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Executar o código

Do prompt de um console, navegue até o diretório que contém o arquivo *issue-access-token.js* e execute o comando `node` a seguir para executar o aplicativo.

```console
node ./issue-access-token.js
```
