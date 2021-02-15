---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 9a9f8fdda1bc853057f3eb858e85b938357397cd
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886085"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/dotnet/).
- A versão mais recente da [biblioteca de clientes do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operacional.
- Obtenha a versão mais recente da [biblioteca de clientes de Identidade do .NET](/dotnet/api/azure.identity?view=azure-dotnet).
- Obtenha a versão mais recente da [biblioteca de clientes do Gerenciamento do .NET](../../concepts/sdk-options.md).

## <a name="installing-the-client-library"></a>Como instalar a biblioteca de clientes

Primeiro, inclua a biblioteca de clientes de Gerenciamento dos Serviços de Comunicação no seu projeto C#:

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>ID da assinatura

Você precisará saber a ID da sua assinatura do Azure. Isso pode ser adquirido no portal:

1.  Fazer logon na sua conta do Azure
2.  Selecione Assinaturas na barra lateral à esquerda
3.  Selecione qualquer assinatura necessária
4.  Clique em Visão Geral
5.  Selecione a sua ID de Assinatura

Neste guia de início rápido, vamos pressupor que você armazenou a ID da assinatura em uma variável de ambiente chamada `AZURE_SUBSCRIPTION_ID`.

## <a name="authentication"></a>Autenticação

Para se comunicar com os Serviços de Comunicação do Azure, primeiro você precisa se autenticar no Azure. Normalmente, você faz isso usando uma identidade de entidade de serviço.

### <a name="option-1-managed-identity"></a>Opção 1: Identidade Gerenciada

Se o seu código está sendo executado como um serviço no Azure, a maneira mais fácil de se autenticar é adquirir uma identidade gerenciada do Azure. Saiba mais sobre as [identidades gerenciadas](../../../active-directory/managed-identities-azure-resources/overview.md).

[Serviços do Azure com suporte a Identidades Gerenciadas](../../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

[Como usar identidades gerenciadas para o Serviço de Aplicativo e o Azure Functions](../../../app-service/overview-managed-identity.md?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[Identidade Gerenciada atribuída pelo sistema](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[Identidade Gerenciada atribuída pelo usuário](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)

O ClientId da identidade gerenciada que você criou precisa ser passado para `ManagedIdentityCredential` explicitamente.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>Opção 2: Entidade de Serviço

Em vez de usar uma identidade gerenciada, talvez você queira autenticar-se no Azure usando uma entidade de serviço que você mesmo gerencia. Saiba mais sobre como usar a documentação em [criar e gerenciar uma entidade de serviço no Azure Active Directory](../../../active-directory/develop/howto-create-service-principal-portal.md).

Depois de criar a sua entidade de serviço, você precisará coletar as seguintes informações sobre ela no portal do Azure:

- **ID do Cliente**
- **Segredo do Cliente**
- **ID do locatário**

Armazene esses valores em variáveis de ambiente chamadas `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` e `AZURE_TENANT_ID`, respectivamente. Em seguida, você pode criar um cliente de gerenciamento de Serviços de Comunicação como este:

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>Opção 3: Identidade do Usuário

Se você quiser chamar o Azure em nome de um usuário interativo, em vez de usar uma identidade de serviço, poderá usar o código a seguir para criar um cliente de Gerenciamento dos Serviços de Comunicação do Azure. Isso abrirá uma janela do navegador para solicitar ao usuário as credenciais do MSA ou do Azure AD.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>Como gerenciar os recursos dos Serviços de Comunicação

### <a name="interacting-with-azure-resources"></a>Interação com os recursos do Azure

Agora que você está autenticado, você pode usar o seu cliente de gerenciamento para fazer chamadas à API.

Para cada um dos exemplos a seguir, atribuiremos os recursos dos Serviços de Comunicação a um grupo de recursos existente.

Se você precisar criar um grupo de recursos, poderá fazer isso usando o [portal do Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md) ou a [biblioteca de clientes do Azure Resource Manager](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md).

### <a name="create-and-manage-a-communication-services-resource"></a>Criar e gerenciar um recurso dos Serviços de Comunicação

Nossa instância do cliente da biblioteca de clientes de Gerenciamento dos Serviços de Comunicação (``Azure.ResourceManager.Communication.CommunicationManagementClient``) pode ser usada para executar operações em recursos de Serviços de Comunicação.

#### <a name="create-a-communication-services-resource"></a>Criar um recurso dos Serviços de Comunicação

Ao criar um recurso dos Serviços de Comunicação, você especificará o nome do grupo de recursos e o nome do recurso. Observe que a propriedade `Location` sempre será `global` e, durante a versão prévia pública, o valor `DataLocation` precisa ser `UnitedStates`.

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>Atualizar um recurso dos Serviços de Comunicação

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>Listar todos os recursos dos Serviços de Comunicação

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>Excluir um recurso dos Serviços de Comunicação

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>Como gerenciar chaves e cadeias de conexão

Cada recurso dos Serviços de Comunicação tem um par de chaves de acesso e cadeias de conexão correspondentes. Essas chaves podem ser acessadas com a biblioteca de clientes de Gerenciamento e, em seguida, usadas por outras bibliotecas de clientes dos Serviços de Comunicação para se autenticarem aos Serviços de Comunicação do Azure.

#### <a name="get-access-keys-for-a-communication-services-resource"></a>Obter chaves de acesso para um recurso dos Serviços de Comunicação

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>Regenerar uma chave de acesso para um recurso dos Serviços de Comunicação

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```