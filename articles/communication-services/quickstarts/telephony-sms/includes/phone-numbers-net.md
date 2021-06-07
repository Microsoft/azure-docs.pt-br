---
ms.openlocfilehash: 0bfb23977f6553568da24df614621bdf1eb9d06d
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113139"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A versão mais recente da [biblioteca de clientes do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operacional.
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

- Em um terminal ou janela de comando, execute o comando `dotnet` para verificar se a biblioteca de clientes do .NET está instalada.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `PhoneNumbersQuickstart`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: **Program.cs**.

```console
dotnet new console -o PhoneNumbersQuickstart
```

Altere o seu diretório para a pasta de aplicativo recém-criada e use o comando `dotnet build` para compilar o seu aplicativo.

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale o pacote da biblioteca de clientes de Números de Telefone da Comunicação do Azure para .NET usando o comando `dotnet add package`.

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0-beta.6
```

Adicione uma diretiva `using` à parte superior de **Program.cs** para incluir os namespaces.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
```

Atualize a assinatura de função `Main` para ser assíncrona.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>Autenticar o cliente

Os clientes de Número de Telefone podem ser autenticados usando a cadeia de conexão adquirida dos Recursos de Comunicação do Azure no [portal do Azure][azure_portal].

```csharp
// Get a connection string to our Azure Communication resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

Os clientes de Número de Telefone também têm a opção de fazer a autenticação com o Azure Active Directory. Com essa opção, as variáveis de ambiente `AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` e `AZURE_TENANT_ID` precisam ser configuradas para autenticação.

```csharp
// Get an endpoint to our Azure Communication resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>Gerenciar números de telefone

### <a name="search-for-available-phone-numbers"></a>Pesquisar números de telefone disponíveis

Para comprar números de telefone, primeiro você precisa procurar números de telefone disponíveis. Para procurar números de telefone, forneça o código de área, o tipo de atribuição, as [funcionalidades do número de telefone](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), o [tipo de número de telefone](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services) e a quantidade. Observe que para números de telefone do tipo chamada gratuita, fornecer o código de área é opcional.

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>Comprar números de telefone

O resultado da pesquisa por números de telefone é um `PhoneNumberSearchResult`. Ela contém um `SearchId` que pode ser passado para a API de comprar números para adquirir os números na pesquisa. Observe que chamar a API de comprar números de telefone resultará em um encargo para sua conta do Azure.

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionResponseAsync();
```

### <a name="get-phone-numbers"></a>Obter números de telefone

Depois de comprar um número, você poderá recuperá-lo do cliente.
```csharp
var getPhoneNumberResponse = await client.GetPurchasedPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

Você também pode recuperar todos os números de telefone comprados.
``` csharp
var purchasedPhoneNumbers = client.GetPurchasedPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>Atualizar as funcionalidades do número de telefone

Com um número comprado, você pode atualizar as funcionalidades.

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>Liberar número de telefone

Você pode liberar um número de telefone comprado.

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionResponseAsync();
````

## <a name="run-the-code"></a>Executar o código

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```console
dotnet run
```

## <a name="sample-code"></a>Exemplo de código

É possível baixar o aplicativo de exemplo do [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers)
