---
title: Autorizar o acesso a BLOBs e filas com Azure Active Directory e identidades gerenciadas para recursos do Azure – armazenamento do Azure
description: Suporte ao armazenamento de BLOBs e filas do Azure autorizando o acesso a recursos com Azure Active Directory e identidades gerenciadas para recursos do Azure. Você pode usar identidades gerenciadas para recursos do Azure para autorizar o acesso a BLOBs e filas de aplicativos executados em máquinas virtuais do Azure, aplicativos de funções, conjuntos de dimensionamento de máquinas virtuais e outros.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 469790660e843816cc431420e7e1407c90a7de05
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249920"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autorizar o acesso a BLOBs e filas com Azure Active Directory e identidades gerenciadas para recursos do Azure

Os armazenamentos de blobs e de filas do Azure dão suporte à autenticação do Azure AD (Active Directory) com [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Identidades gerenciadas para recursos do Azure podem autorizar o acesso a dados de BLOB e de fila usando as credenciais do Azure AD de aplicativos em execução em VMs (máquinas virtuais) do Azure, aplicativos de funções, conjuntos de dimensionamento de máquinas virtuais e outros serviços. Usando identidades gerenciadas para recursos do Azure junto com a autenticação do Azure AD, você pode evitar o armazenamento de credenciais com seus aplicativos que são executados na nuvem.  

Este artigo mostra como autorizar o acesso a dados de BLOB ou de fila com uma identidade gerenciada de uma VM do Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM

Antes de poder usar identidades gerenciadas para recursos do Azure para autorizar o acesso a BLOBs e filas de sua VM, você deve primeiro habilitar identidades gerenciadas para recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Portal do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [PowerShell do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de cliente Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Conceder permissões a uma identidade gerenciada do Azure AD

Para autorizar uma solicitação ao BLOB ou serviço Fila de uma identidade gerenciada em seu aplicativo de armazenamento do Azure, primeiro configure as configurações de RBAC (controle de acesso baseado em função) para essa identidade gerenciada. O armazenamento do Azure define as funções RBAC que englobam permissões para dados de BLOB e fila. Quando a função RBAC é atribuída a uma identidade gerenciada, a identidade gerenciada recebe essas permissões para dados de BLOB ou fila no escopo apropriado.

Para obter mais informações sobre como atribuir funções RBAC, consulte um dos seguintes artigos:

- [Conceder acesso a dados de BLOB e de fila do Azure com RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
- [Conceder acesso ao blob do Azure e a dados da fila com o RBAC usando CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso ao blob do Azure e a dados da fila com o RBAC usando o PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>ID de recurso de armazenamento do Azure

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código do .NET: criar um blob de blocos

O exemplo de código mostra como obter um token OAuth 2,0 do Azure AD e usá-lo para autorizar uma solicitação para criar um blob de blocos. Para fazer esse exemplo funcionar, primeiro siga as etapas descritas nas seções anteriores.

A biblioteca de cliente de [autenticação de aplicativo Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) para .net (versão prévia) simplifica o processo de aquisição e renovação de um token do seu código. A biblioteca de cliente de autenticação de aplicativo gerencia a autenticação automaticamente. A biblioteca usa as credenciais do desenvolvedor para autenticar durante o desenvolvimento local. O uso das credenciais do desenvolvedor durante o desenvolvimento local é mais seguro, porque você não precisa criar credenciais do Azure AD nem compartilhar credenciais entre desenvolvedores. Quando a solução for implantada posteriormente no Azure, a biblioteca alternará automaticamente para o uso de credenciais do aplicativo.

### <a name="install-packages"></a>Instalar Pacotes

Para usar a biblioteca de autenticação de aplicativo em um aplicativo de armazenamento do Azure, instale o pacote de visualização mais recente do [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), bem como a versão mais recente da [biblioteca de cliente comum do armazenamento do Azure para .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) e a [biblioteca de cliente de armazenamento de BLOBs do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Adicione as seguintes instruções **using** ao seu código:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

### <a name="add-the-callback-method"></a>Adicionar o método de retorno de chamada

O método de retorno de chamada verifica o tempo de expiração do token e renova-o conforme necessário:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

### <a name="get-a-token-and-create-a-block-blob"></a>Obter um token e criar um blob de blocos

A biblioteca de autenticação de aplicativo fornece a classe **o azureservicetokenprovider** . Uma instância dessa classe pode ser passada para um retorno de chamada que obtém um token e, em seguida, renova o token antes de ele expirar.

O exemplo a seguir obtém um token e o usa para criar um novo BLOB e, em seguida, usa o mesmo token para ler o blob.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider,
                                            CancellationToken.None).GetAwaiter().GetResult();

// Create storage credentials using the initial token, and connect the callback function
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token,
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider,
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName),
                                            storageCredentials);

// Upload text to the blob.
blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage.
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}",
                        DateTimeOffset.UtcNow,
                        blob.DownloadTextAsync().Result);

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

Para obter mais informações sobre a biblioteca de autenticação de aplicativo, consulte autenticação de serviço [a serviço para Azure Key Vault usando o .net](../../key-vault/service-to-service-authentication.md).

Para saber mais sobre como adquirir um token de acesso, consulte [como usar identidades gerenciadas para recursos do Azure em uma VM do Azure para adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Para autorizar solicitações em dados de BLOB ou de fila com o Azure AD, você deve usar HTTPS para essas solicitações.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as funções RBAC para o armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Use o Microsoft Azure Active Directory com aplicativos de armazenamento](storage-auth-aad-app.md).
- Para saber como executar comandos do CLI do Azure e do PowerShell com as credenciais do Azure AD, consulte [executar CLI do Azure ou comandos do PowerShell com as credenciais do Azure ad para acessar dados de BLOB ou de fila](storage-auth-aad-script.md).
