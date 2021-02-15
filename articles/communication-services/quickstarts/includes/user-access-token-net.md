---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 4c05b654b6714c5317e1334d3a3ea5c327a5ff18
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770800"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- A versão mais recente da [biblioteca de clientes do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operacional.
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

Ainda no diretório do aplicativo, instale o pacote da biblioteca da Administração dos Serviços de Comunicação do Azure para .NET usando o comando `dotnet add package`.

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.3
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Abra o arquivo **Program.cs** em um editor de texto
1. Adicione uma diretiva `using` para incluir o namespace `Azure.Communication.Administration`
1. Atualize a declaração do método `Main` para dar suporte ao código assíncrono

Use o seguinte código para começar:

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Administration;

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

Inicialize um `CommunicationIdentityClient` com a sua cadeia de conexão. O código abaixo recupera a cadeia de conexão do recurso de uma variável de ambiente chamada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Saiba como [gerenciar a cadeia de conexão do recurso](../create-communication-resource.md#store-your-connection-string).

Adicione o seguinte código ao método `Main`:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>Criar uma identidade

Os Serviços de Comunicação do Azure mantêm um diretório de identidade leve. Use o método `createUser` para criar uma entrada no diretório com um `Id` exclusivo. Armazene a identidade recebida com um mapeamento para os usuários do aplicativo. Por exemplo, armazenando-os no banco de dados do servidor de aplicativos. A identidade é necessária posteriormente para emitir tokens de acesso.

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

## <a name="issue-identity-access-tokens"></a>Emitir tokens de acesso da identidade

Use o método `issueToken` a fim de emitir um token de acesso para uma identidade existente dos Serviços de Comunicação. O parâmetro `scopes` define o conjunto de primitivos, que autorizará esse token de acesso. Confira a [lista de ações compatíveis](../../concepts/authentication.md). A nova instância do parâmetro `communicationUser` pode ser construída com base na representação da cadeia de caracteres da identidade do Serviço de Comunicação do Azure.

```csharp
// Issue an access token with the "voip" scope for an identity
var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Os tokens de acesso são credenciais de curta duração que precisam ser reemitidas. Deixar de fazer isso pode causar a interrupção da experiência dos usuários do aplicativo. A propriedade de resposta `expiresOn` indica o tempo de vida do token de acesso. 

## <a name="refresh-access-tokens"></a>Tokens de acesso de atualização

Para atualizar um token de acesso, passe uma instância do objeto `CommunicationUser` para `IssueTokenAsync`. Se você tiver armazenado essa `Id` e precisar criar um `CommunicationUser`, poderá fazer isso passando o `Id` armazenado para o construtor `CommunicationUser` da seguinte maneira:

```csharp  
// In this example, userId is a string containing the Id property of a previously-created CommunicationUser
identityToRefresh = new CommunicationUser(userId);
tokenResponse = await client.IssueTokenAsync(identityToRefresh, scopes: new [] { CommunicationTokenScope.VoIP });
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
