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
ms.openlocfilehash: 653692812507c05c6cfc58b00d3c93ece19019bb
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112992"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A versão mais recente do [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operacional.
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../create-communication-resource.md).

## <a name="setting-up"></a>Ao configurar o

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `AccessTokensQuickstart`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: **Program.cs**.

```console
dotnet new console -o AccessTokensQuickstart
```

Altere o seu diretório para a pasta de aplicativo recém-criada e use o comando `dotnet build` para compilar o seu aplicativo.

```console
cd AccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale o pacote da biblioteca de identidade dos Serviços de Comunicação do Azure para .NET usando o comando `dotnet add package`.

```console
dotnet add package Azure.Communication.Identity --version 1.0.0
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Abra o arquivo **Program.cs** em um editor de texto
1. Adicione uma diretiva `using` para incluir o namespace `Azure.Communication.Identity`
1. Atualize a declaração do método `Main` para dar suporte ao código assíncrono

Use o seguinte código para começar:

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Identity;

namespace AccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```
## <a name="authenticate-the-client"></a>Autenticar o cliente

Inicialize um `CommunicationIdentityClient` com a sua cadeia de conexão. O código abaixo recupera a cadeia de conexão do recurso de uma variável de ambiente chamada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Saiba como [gerenciar a cadeia de conexão do seu recurso](../create-communication-resource.md#store-your-connection-string).

Adicione o seguinte código ao método `Main`:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

Como alternativa, você pode separar o ponto de extremidade e a chave de acesso.
```csharp
// This code demonstrates how to fetch your endpoint and access key
// from an environment variable.
string endpoint = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_ENDPOINT");
string accessKey = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_ACCESSKEY");
var client = new CommunicationIdentityClient(new Uri(endpoint), new AzureKeyCredential(accessKey));
```

Se você tiver configurado a identidade gerenciada, consulte [Usar identidades gerenciadas](../managed-identity.md). Também é possível autenticar com a identidade gerenciada.
```csharp
TokenCredential tokenCredential = new DefaultAzureCredential();
var client = new CommunicationIdentityClient(endpoint, tokenCredential);
```

## <a name="create-an-identity"></a>Criar uma identidade

Os Serviços de Comunicação do Azure mantêm um diretório de identidade leve. Use o método `createUser` para criar uma entrada no diretório com um `Id` exclusivo. Armazene a identidade recebida com um mapeamento para os usuários do aplicativo. Por exemplo, armazenando-os no banco de dados do servidor de aplicativos. A identidade é necessária posteriormente para emitir tokens de acesso.

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

## <a name="issue-identity-access-tokens"></a>Emitir tokens de acesso da identidade

Use o método `GetToken` a fim de emitir um token de acesso para uma identidade existente dos Serviços de Comunicação. O parâmetro `scopes` define o conjunto de primitivos, que autorizará esse token de acesso. Confira a [lista de ações compatíveis](../../concepts/authentication.md). A nova instância do parâmetro `communicationUser` pode ser construída com base na representação da cadeia de caracteres da identidade do Serviço de Comunicação do Azure.

```csharp
// Issue an access token with the "voip" scope for an identity
var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Os tokens de acesso são credenciais de curta duração que precisam ser reemitidas. Deixar de fazer isso pode causar a interrupção da experiência dos usuários do aplicativo. A propriedade de resposta `expiresOn` indica o tempo de vida do token de acesso.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Criar uma identidade e emitir um token de acesso na mesma solicitação

Use o método `CreateUserAndTokenAsync` para criar uma identidade dos Serviços de Comunicação e emitir um token de acesso para ela. O parâmetro `scopes` define o conjunto de primitivos, que autorizará esse token de acesso. Confira a [lista de ações compatíveis](../../concepts/authentication.md).

```csharp
// Issue an identity and an access token with the "voip" scope for the new identity
var identityAndTokenResponse = await client.CreateUserAndTokenAsync(scopes: new[] { CommunicationTokenScope.VoIP });
var identity = identityAndTokenResponse.Value.User;
var token = identityAndTokenResponse.Value.AccessToken.Token;
var expiresOn = identityAndTokenResponse.Value.AccessToken.ExpiresOn;

Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

## <a name="refresh-access-tokens"></a>Tokens de acesso de atualização

Para atualizar um token de acesso, passe uma instância do objeto `CommunicationUserIdentifier` para `GetTokenAsync`. Se você tiver armazenado essa `Id` e precisar criar um `CommunicationUserIdentifier`, poderá fazer isso passando o `Id` armazenado para o construtor `CommunicationUserIdentifier` da seguinte maneira:

```csharp
var identityToRefresh = new CommunicationUserIdentifier(identity.Id);
var tokenResponse = await client.GetTokenAsync(identityToRefresh, scopes: new [] { CommunicationTokenScope.VoIP });
```

## <a name="revoke-access-tokens"></a>Revogar tokens de acesso

Em alguns casos, você pode revogar explicitamente os tokens de acesso. Por exemplo, quando o usuário de um aplicativo altera a senha usada para realizar a autenticação no serviço. O método `RevokeTokensAsync` invalida todos os tokens de acesso ativos que foram emitidos para a identidade.

```csharp
await client.RevokeTokensAsync(identity);
Console.WriteLine($"\nSuccessfully revoked all access tokens for identity with ID: {identity.Id}");
```

## <a name="delete-an-identity"></a>Excluir uma identidade

A exclusão de uma identidade revoga todos os tokens de acesso ativos e impede que você emita novos tokens de acesso para as identidades. Isso também remove todo o conteúdo persistente associado à identidade.

```csharp
await client.DeleteUserAsync(identity);
Console.WriteLine($"\nDeleted the identity with ID: {identity.Id}");
```

## <a name="run-the-code"></a>Executar o código

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```console
dotnet run
```
