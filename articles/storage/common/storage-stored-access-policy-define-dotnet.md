---
title: Criar uma política de acesso armazenada com o .NET
titleSuffix: Azure Storage
description: Use o armazenamento do Azure e o .NET para criar uma política de acesso armazenada. Exerça níveis adicionais de controle sobre as assinaturas de acesso compartilhado no nível de serviço no servidor.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cd6147fbe38710bcefd580e71be1d6f5d446a21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89010742"
---
# <a name="create-a-stored-access-policy-with-net"></a>Criar uma política de acesso armazenada com o .NET

Uma política de acesso armazenada fornece um nível adicional de controle sobre SAS (assinaturas de acesso compartilhado) de nível de serviço no lado do servidor. Definir uma política de acesso armazenada serve para agrupar assinaturas de acesso compartilhado e fornecer restrições adicionais para assinaturas de acesso compartilhado que são associadas pela política. Você pode usar uma política de acesso armazenada para alterar a hora de início, a hora de expiração ou as permissões para uma SAS ou para revogá-la após sua emissão.
  
Os seguintes recursos de armazenamento do Azure dão suporte a políticas de acesso armazenadas:  
  
- Contêineres de blobs  
- Compartilhamentos de arquivo  
- Filas  
- Tabelas  
  
> [!NOTE]
> Uma política de acesso armazenada em um contêiner pode ser associada a uma assinatura de acesso compartilhado que concede permissões ao próprio contêiner ou aos BLOBs que ele contém. Da mesma forma, uma política de acesso armazenada em um compartilhamento de arquivos pode ser associada a uma assinatura de acesso compartilhado que concede permissões ao compartilhamento em si ou aos arquivos que ele contém.  
>
> As políticas de acesso armazenadas têm suporte apenas para SAS de serviço. As políticas de acesso armazenadas não têm suporte para SAS da conta ou SAS de delegação de usuário.  

Para obter mais informações sobre políticas de acesso armazenado, consulte [definir uma política de acesso armazenada](/rest/api/storageservices/define-stored-access-policy).

## <a name="create-a-stored-access-policy"></a>Criar uma política de acesso armazenada

A operação REST subjacente para criar uma política de acesso armazenada é [definir ACL de contêiner](/rest/api/storageservices/set-container-acl). Você deve autorizar a operação para criar uma política de acesso armazenada por meio de chave compartilhada usando as chaves de acesso da conta em uma cadeia de conexão. Não há suporte para autorizar a operação **definir ACL de contêiner** com as credenciais do Azure AD. Para obter mais informações, consulte [permissões para chamar operações de BLOB e de dados de fila](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Os exemplos de código a seguir criam uma política de acesso armazenada em um contêiner. Você pode usar a política de acesso para especificar restrições para um serviço de SAS no contêiner ou seus blobs.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para criar uma política de acesso armazenada em um contêiner com a versão 12 da biblioteca de cliente .NET para o armazenamento do Azure, chame um dos seguintes métodos:

- [BlobContainerClient.SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

O exemplo a seguir cria uma política de acesso armazenada que está em vigor por um dia e concede permissões de leitura/gravação:

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para criar uma política de acesso armazenada em um contêiner com a versão 12 da biblioteca de cliente .NET para o armazenamento do Azure, chame um dos seguintes métodos:

- [CloudBlobContainer. SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer. SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

O exemplo a seguir cria uma política de acesso armazenada que está em vigor para um dia e que concede permissões de leitura, gravação e lista:

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>Confira também

- [Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md)
- [Definir uma política de acesso armazenada](/rest/api/storageservices/define-stored-access-policy)
- [Configurar cadeias de conexão do Armazenamento do Azure](storage-configure-connection-string.md)
