---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: peiliu
manager: rejooyan
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: 5fd209c612f90e3912e244daf60d20edf30a08c6
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112994"
---
Comece a usar os Serviços de Comunicação do Azure usando o SDK de C# do Java dos Serviços de Comunicação para enviar mensagens SMS.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A versão mais recente do [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operacional.
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um número de telefone habilitado para SMS. [Obter um número de telefone](../get-phone-number.md).

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

- Em um terminal ou em uma janela de comando, execute o comando `dotnet` para verificar se o SDK do .NET está instalado.
- Para exibir os números de telefone associados ao seu recurso dos Serviços de Comunicação, entre no [portal do Azure](https://portal.azure.com/), localize o recurso em questão e abra a guia **números de telefone** no painel de navegação esquerdo.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `SmsQuickstart`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: **Program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Altere o seu diretório para a pasta de aplicativo recém-criada e use o comando `dotnet build` para compilar o seu aplicativo.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale o pacote do SDK de SMS dos Serviços de Comunicação do Azure para .NET usando o comando `dotnet add package`.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0
```

Adicione uma diretiva `using` à parte superior de **Program.cs** para incluir o namespace `Azure.Communication`.

```csharp

using System;
using System.Collections.Generic;

using Azure;
using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir lidam com alguns dos principais recursos do SDK de SMS dos Serviços de Comunicação do Azure para C#.

| Nome                                       | Descrição                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Essa classe é necessária para toda a funcionalidade de SMS. Crie uma instância dela com suas informações de assinatura e use-a para enviar mensagens SMS.                           |
| SmsSendOptions | Essa classe fornece opções para configurar o relatório de entrega. Se enable_delivery_report for definido como True, um evento será emitido quando a entrega for bem-sucedida |
| SmsSendResult               | Essa classe contém o resultado do serviço de SMS.                                          |

## <a name="authenticate-the-client"></a>Autenticar o cliente

 Abra **Program.cs** em um editor de texto e substitua o corpo do método `Main` pelo código para inicializar um `SmsClient` com a cadeia de conexão. O código abaixo recupera a cadeia de conexão do recurso de uma variável de ambiente chamada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Saiba como [gerenciar a cadeia de conexão do seu recurso](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-a-11-sms-message"></a>Enviar uma mensagem SMS 1:1

Para enviar uma mensagem SMS para um só destinatário, chame a função `Send` ou `SendAsync` no SmsClient. Adicione este código ao final do método `Main` no **Program.cs**:

```csharp
SmsSendResult sendResult = smsClient.Send(
    from: "<from-phone-number>",
    to: "<to-phone-number>",
    message: "Hello World via SMS"
);

Console.WriteLine($"Sms id: {sendResult.MessageId}");
```
Você deve substituir `<from-phone-number>` pelo número de telefone habilitado para SMS associado ao seu recurso dos Serviços de Comunicação e `<to-phone-number>` pelo número de telefone para o qual você deseja enviar uma mensagem.

> [!WARNING]
> Observe que os números de telefone devem ser fornecidos no formato padrão internacional E.164. (por exemplo: +14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Enviar uma mensagem SMS 1:N com opções
Para enviar uma mensagem SMS para uma lista de destinatários, chame a função `Send` ou `SendAsync` no SmsClient com uma lista de números de telefone de destinatários. Você também pode transmitir parâmetros opcionais para especificar se o relatório de entrega deve ser habilitado e para definir marcas personalizadas.

```csharp
Response<IReadOnlyList<SmsSendResult>> response = smsClient.Send(
    from: "<from-phone-number>",
    to: new string[] { "<to-phone-number-1>", "<to-phone-number-2>" },
    message: "Weekly Promotion!",
    options: new SmsSendOptions(enableDeliveryReport: true) // OPTIONAL
    {
        Tag = "marketing", // custom tags
    });

IEnumerable<SmsSendResult> results = response.Value;
foreach (SmsSendResult result in results)
{
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

Substitua `<from-phone-number>` pelo número de telefone habilitado para SMS associado ao seu recurso dos Serviços de Comunicação e `<to-phone-number-1>` e `<to-phone-number-2>` pelos números de telefones para os quais você deseja enviar uma mensagem.

> [!WARNING]
> Observe que os números de telefone devem ser fornecidos no formato padrão internacional E.164. (por exemplo: +14255550123).

O parâmetro `enableDeliveryReport` é um parâmetro opcional que você pode usar para configurar o Relatório de Entrega. Isso é útil para cenários em que você deseja emitir eventos quando mensagens SMS são entregues. Confira o guia de início rápido [Manipular eventos de SMS](../handle-sms-events.md) para configurar o Relatório de Entrega das suas mensagens SMS.

`Tag` é usado para aplicar uma marca ao Relatório de Entrega

## <a name="run-the-code"></a>Executar o código

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```console
dotnet run
```

## <a name="sample-code"></a>Exemplo de código

É possível baixar o aplicativo de exemplo do [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS)
