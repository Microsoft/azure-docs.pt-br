---
title: Autenticar o acesso a blobs e filas com identidades gerenciadas para recursos do Azure - armazenamento do Azure | Microsoft Docs
description: Armazenamento de Blob e fila do Azure dão suporte a autenticação do Active Directory com identidades gerenciadas para recursos do Azure. Você pode usar identidades gerenciadas para recursos do Azure para autenticar o acesso a blobs e filas de aplicativos em execução em máquinas virtuais do Azure, aplicativos de função, conjuntos de dimensionamento de máquinas virtuais e outros.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9209161f9c9e34320b1388e0e1edbd5069e73727
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438283"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autenticar o acesso a blobs e filas com o Azure Active Directory e identidades gerenciadas para recursos do Azure

Os armazenamentos de blobs e de filas do Azure dão suporte à autenticação do Azure AD (Active Directory) com [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Identidades gerenciadas para recursos do Azure podem autorizar o acesso ao blob e os dados da fila usando credenciais do AD do Azure em aplicativos executados em máquinas virtuais (VMs), aplicativos de função, conjuntos de dimensionamento de máquina virtual e outros serviços. Usando identidades gerenciadas para recursos do Azure junto com a autenticação do Azure AD, você pode evitar o armazenamento de credenciais com os aplicativos que são executados na nuvem.  

Este artigo mostra como autorizar o acesso aos dados de blob ou fila com uma identidade gerenciada de uma VM do Azure. 

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM

Antes de usar identidades gerenciadas para recursos do Azure para autorizar o acesso a filas e blobs de sua VM, você deve primeiro habilitar identidades gerenciadas para recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Portal do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [PowerShell do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [SDKs do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Conceder permissões para uma identidade do AD do Azure gerenciado

Para autorizar uma solicitação ao serviço Blob ou fila de uma identidade gerenciada em seu aplicativo de armazenamento do Azure, primeiro configure as configurações de RBAC (controle) de acesso baseado em função dessa identidade gerenciada. O armazenamento do Azure define as funções RBAC que abrangem as permissões para dados de blob e fila. Quando a função RBAC é atribuída a uma identidade gerenciada, a identidade gerenciada recebe essas permissões para dados de blob ou fila no escopo apropriado. 

Para obter mais informações sobre como atribuir funções RBAC, consulte um dos seguintes artigos:

- [Conceder acesso a dados do Azure blob e fila com o RBAC no portal do Azure](storage-auth-aad-rbac-portal.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC usando a CLI do Azure](storage-auth-aad-rbac-cli.md)
- [Conceder acesso a dados de blob e fila do Azure com o RBAC, usando o PowerShell](storage-auth-aad-rbac-powershell.md)

## <a name="authorize-with-a-managed-identity-access-token"></a>Autorizar com um token de acesso de identidade gerenciada

Para autorizar solicitações em relação ao armazenamento de Blob e fila com uma identidade gerenciada, seu aplicativo ou script deve adquirir um token OAuth. O [autenticação de aplicativo do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) biblioteca de cliente para .NET (visualização) simplifica o processo de aquisição e a renovação de um token do seu código.

A biblioteca de cliente de autenticação do aplicativo gerencia a autenticação automaticamente. A biblioteca usa as credenciais do desenvolvedor para autenticação durante o desenvolvimento local. O uso das credenciais do desenvolvedor durante o desenvolvimento local é mais seguro, porque você não precisa criar credenciais do Azure AD nem compartilhar credenciais entre desenvolvedores. Quando a solução é implantada posteriormente no Azure, a biblioteca alterna automaticamente usando credenciais do aplicativo.

Para usar a biblioteca de autenticação de aplicativo em um aplicativo de armazenamento do Azure, instale o pacote de visualização mais recente do [Nuget]((https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)), bem como a versão mais recente do [biblioteca de cliente de armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). Adicione o seguinte **usando** instruções ao seu código:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
```

A biblioteca de autenticação de aplicativo fornece o **o AzureServiceTokenProvider** classe. Uma instância dessa classe pode ser passada para um retorno de chamada que obtém um token e, em seguida, renova o token antes de expirar.

O exemplo a seguir obtém um token e usa-o para criar um novo blob e usa o mesmo token para ler o blob.

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

O método de retorno de chamada verifica o tempo de expiração do token e renova-a conforme necessário:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
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

Para obter mais informações sobre a biblioteca de autenticação do aplicativo, consulte [autenticação de serviço a serviço para o Azure Key Vault usando o .NET](../../key-vault/service-to-service-authentication.md). 

Para saber mais sobre como adquirir um token de acesso, consulte [como usar identidades gerenciadas para recursos do Azure em uma VM do Azure para adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

> [!NOTE]
> Para autorizar solicitações em relação aos dados de blob ou fila com o Azure AD, você deve usar HTTPS para essas solicitações.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as funções RBAC para o armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados do armazenamento com RBAC](storage-auth-aad-rbac.md).
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Use o Microsoft Azure Active Directory com aplicativos de armazenamento](storage-auth-aad-app.md).
- Para saber como executar comandos da CLI do Azure e do PowerShell com credenciais do Azure AD, consulte [comandos de execução da CLI do Azure ou o PowerShell com credenciais do Azure AD para acessar dados de blob ou fila](storage-auth-aad-script.md).