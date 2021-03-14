---
title: Assinar uma solicitação HTTP com o C#
description: Esta é a versão C# da assinatura de uma solicitação HTTP com uma assinatura HMAC para os Serviços de Comunicação.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 01/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 3c1b56f81e5164bbdfa94fdaeca5f5f1f55b3b51
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551300"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:
- Criar uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Instalar o [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Criar um recurso dos Serviços de Comunicação do Azure. Para obter detalhes, confira [Criar um recurso de comunicação do Azure](../../quickstarts/create-communication-resource.md). Você precisará registrar o **resourceEndpoint** e a **resourceAccessKey** para este tutorial.



## <a name="sign-an-http-request-with-c"></a>Assinar uma solicitação HTTP com o C#
A autenticação de chave de acesso usa uma chave secreta compartilhada para gerar uma assinatura HMAC para cada solicitação HTTP. Essa assinatura é gerada com o algoritmo SHA256 e é enviada no cabeçalho `Authorization` usando o esquema `HMAC-SHA256`. Por exemplo:

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

A `hmac-sha256-signature` é formada por: 

- Verbo HTTP (por exemplo, `GET` ou `PUT`)
- Caminho de solicitação HTTP
- Data
- Host
- x-ms-content-sha256

## <a name="setting-up"></a>Configurando
As seguintes etapas descrevem como construir o cabeçalho de autorização:

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `SignHmacTutorial`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: **Program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Altere o seu diretório para a pasta de aplicativo recém-criada e use o comando `dotnet build` para compilar o seu aplicativo.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Instalar o pacote

Instale o pacote `Newtonsoft.Json`, usado para a serialização do corpo:

```console
dotnet add package Newtonsoft.Json
```

Atualize a declaração de método `Main` para dar suporte ao código assíncrono. Use o seguinte código para começar:

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here
        }
    }
}

```
## <a name="create-a-request-message"></a>Criar uma mensagem de solicitação

Para este exemplo, assinaremos uma solicitação para criar uma identidade usando a API de Autenticação dos Serviços de Comunicação (versão `2021-03-07`)

Adicione o seguinte código ao `Main` método:

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create an uri you are going to call
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
//Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

Substitua `resourceEndpoint` pelo valor real do ponto de extremidade de recurso.

## <a name="create-content-hash"></a>Criar um hash de conteúdo

O hash de conteúdo faz parte da sua assinatura HMAC. Use o código a seguir para calcular o hash de conteúdo. Você pode adicionar esse método a `Progam.cs` no método `Main`.

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>Computar uma assinatura
Use o código a seguir para criar um método para computar sua assinatura HMAC.

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

Substitua `resourceAccessKey` pela chave de acesso do recurso real dos Serviços de Comunicação do Azure.

## <a name="create-an-authorization-header-string"></a>Criar uma cadeia de caracteres do cabeçalho de autorização

Agora, vamos construir a cadeia de caracteres que adicionaremos ao cabeçalho de autorização:

1. Computar um hash de conteúdo
2. Especificar o carimbo de data/hora UTC (Tempo Universal Coordenado)
3. Preparar uma cadeia de caracteres para assinatura
4. Computar a assinatura
5. Concatenar a cadeia de caracteres, que será usada no cabeçalho de autorização
 
Adicione o seguinte código ao `Main` método:

```csharp
// Compute a content hash
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in authorization header
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Adicionar cabeçalhos a requestMessage

Use o seguinte código para adicionar os cabeçalhos necessários a `requestMessage`:

```csharp
//Add content hash header
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//add date header
requestMessage.Headers.Add("Date", date);
//add Authorization header
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Como testar o cliente
Chame o ponto de extremidade usando `HttpClient` e verifique a resposta.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
