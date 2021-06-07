---
title: Criar uma SAS de conta com .NET
titleSuffix: Azure Storage
description: Saiba como criar uma assinatura de acesso compartilhado de conta (SAS) usando a biblioteca de cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2918b845430a6fc6dc59eca7041c114fc9d06515
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97092203"
---
# <a name="create-an-account-sas-with-net"></a>Criar uma SAS de conta com .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar a chave da conta de armazenamento para criar uma SAS de conta com a [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage).

## <a name="create-an-account-sas"></a>Criar uma SAS de conta

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Uma SAS de conta é assinada com a chave de acesso da conta. Use a classe [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) para criar a credencial usada para assinar a SAS. Em seguida, crie um novo objeto [AccountSasBuilder](/dotnet/api/azure.storage.sas.accountsasbuilder) e chame o [ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters) para obter a cadeia de caracteres do token SAS.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Para criar uma SAS de conta para um contêiner, chame o método [CloudStorageAccount. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) .

O exemplo de código a seguir cria uma SAS de conta que é válida para os serviços de arquivo e de blob e dá ao cliente permissões de leitura, gravação e listagem de permissões para acessar as APIs de nível de serviço. A SAS de conta restringe o protocolo para HTTPS, para que a solicitação deva ser feita com HTTPS. Lembre-se de substituir os valores dos espaços reservados entre colchetes angulares pelos seus próprios valores:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

---

## <a name="use-an-account-sas-from-a-client"></a>Usar uma SAS de conta de um cliente

Para usar a SAS da conta para acessar APIs de nível de serviço para o serviço BLOB, construa um objeto de cliente do serviço BLOB usando a SAS e o ponto de extremidade do armazenamento de BLOBs para sua conta de armazenamento.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Neste trecho, substitua o `<storage-account>` espaço reservado pelo nome da sua conta de armazenamento.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

---

## <a name="next-steps"></a>Próximas etapas

- [Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md)
- [Criar uma SAS de conta](/rest/api/storageservices/create-account-sas)
