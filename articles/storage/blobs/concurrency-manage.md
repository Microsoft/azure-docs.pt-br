---
title: Como gerenciar a simultaneidade no armazenamento de Blobs
titleSuffix: Azure Storage
description: Saiba como gerenciar vários gravadores em um blob implementando a simultaneidade otimista ou pessimista em seu aplicativo. A simultaneidade otimista verifica o valor de ETag de um blob e o compara com o ETag fornecido. A simultaneidade pessimista usa uma concessão exclusiva para bloquear o blob para outros gravadores.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523375"
---
# <a name="managing-concurrency-in-blob-storage"></a>Gerenciando Simultaneidade em Armazenamento de Blobs

Os aplicativos modernos geralmente têm vários usuários exibindo e atualizando dados simultaneamente. Os desenvolvedores de aplicativos precisam pensar atentamente sobre como fornecer uma experiência previsível para seus usuários finais, especialmente para cenários em que vários usuários podem atualizar os mesmos dados. Existem três estratégias de simultaneidade de dados principais que os desenvolvedores normalmente consideram:  

- **Simultaneidade otimista**: um aplicativo que executa uma atualização, como parte de sua atualização, determina se os dados foram alterados desde que o aplicativo leu a última vez os dados. Por exemplo, se dois usuários que visualizam uma página wiki fizerem uma atualização para essa página, a plataforma wiki deverá garantir que a segunda atualização não substitua a primeira. Ele também deve garantir que ambos os usuários entendam se a atualização foi bem-sucedida. Essa estratégia é usada com mais frequência em aplicativos Web.

- **Simultaneidade pessimista**: um aplicativo que procura executar uma atualização usará um bloqueio em um objeto impedindo que outros usuários atualizem os dados até que o bloqueio seja liberado. Por exemplo, em um cenário de replicação de dados primário/secundário no qual somente o primário executa atualizações, o primário normalmente mantém um bloqueio exclusivo nos dados por um longo período para garantir que ninguém mais possa atualizá-lo.

- **Último gravador vence**: uma abordagem que permite que as operações de atualização continuem sem primeiro determinar se outro aplicativo atualizou os dados desde que foram lidos. Normalmente, essa abordagem é usada quando os dados são particionados de forma que vários usuários não acessem os mesmos dados ao mesmo tempo. Ela também pode ser útil em locais em que fluxos de dados de curta duração estão sendo processados.

O armazenamento do Azure dá suporte a todas as três estratégias, embora seja distintivo em sua capacidade de fornecer suporte completo para simultaneidade otimista e pessimista. O armazenamento do Azure foi projetado para adotar um modelo de consistência forte que garante que, depois que o serviço executar uma operação de inserção ou atualização, as operações de leitura subsequentes retornarão a atualização mais recente.

Além de selecionar uma estratégia de simultaneidade apropriada, os desenvolvedores também devem estar cientes de como uma plataforma de armazenamento isola as alterações, particularmente as alterações no mesmo objeto entre as transações. O armazenamento do Azure usa o isolamento de instantâneo para permitir operações de leitura simultaneamente com operações de gravação em uma única partição. O isolamento de instantâneo garante que todas as operações de leitura retornem um instantâneo consistente dos dados mesmo enquanto as atualizações estão ocorrendo.

Você pode optar por usar modelos de simultaneidade otimista ou pessimista para gerenciar o acesso a BLOBs e contêineres. Se você não especificar uma estratégia explicitamente, por padrão o último gravador vence.  

## <a name="optimistic-concurrency"></a>Simultaneidade otimista

O armazenamento do Azure atribui um identificador a todos os objetos armazenados. Esse identificador é atualizado toda vez que uma operação de gravação é executada em um objeto. O identificador é retornado ao cliente como parte de uma resposta HTTP GET no cabeçalho ETag que é definido pelo protocolo HTTP.

Um cliente que está executando uma atualização pode enviar a ETag original junto com um cabeçalho condicional para garantir que uma atualização ocorra somente se uma determinada condição for atendida. Por exemplo, se o cabeçalho **If-Match** for especificado, o armazenamento do Azure verificará se o valor da etag especificado na solicitação de atualização é o mesmo que o ETag para o objeto que está sendo atualizado. Para obter mais informações sobre cabeçalhos condicionais, consulte [especificando cabeçalhos condicionais para operações de serviço blob](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).

A estrutura desse processo é a seguinte:  

1. Recuperar um blob do armazenamento do Azure. A resposta inclui um valor de cabeçalho HTTP ETag que identifica a versão atual do objeto.
1. Ao atualizar o blob, inclua o valor de ETag recebido na etapa 1 no cabeçalho condicional **If-Match** da solicitação de gravação. O armazenamento do Azure compara o valor de ETag na solicitação com o valor de ETag atual do blob.
1. Se o valor de ETag atual do blob for diferente daquele especificado no cabeçalho condicional **If-Match** fornecido na solicitação, o armazenamento do Azure retornará o código de status http 412 (falha na pré-condição). Esse erro indica ao cliente que outro processo atualizou o blob desde que o cliente o recuperou pela primeira vez.
1. Se o valor de ETag atual do blob for a mesma versão que o ETag no cabeçalho condicional **If-Match** na solicitação, o armazenamento do Azure executará a operação solicitada e atualizará o valor de eTag atual do blob.  

Os exemplos de código a seguir mostram como construir uma condição **If-Match** na solicitação de gravação que verifica o valor de eTag para um blob. O armazenamento do Azure avalia se o ETag atual do blob é o mesmo que o ETag fornecido na solicitação e executa a operação de gravação somente se os dois valores de ETag correspondem. Se outro processo tiver atualizado o blob no interim, o armazenamento do Azure retornará uma mensagem de status HTTP 412 (falha na pré-condição).  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

O armazenamento do Azure também dá suporte a outros cabeçalhos condicionais, incluindo **If-Modified-Since**, **If-inmodified-Since** e **If-None-Match**. Para obter mais informações, confira [Como especificar cabeçalhos condicionais para operações de serviço Blob](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

## <a name="pessimistic-concurrency-for-blobs"></a>Simultaneidade pessimista para blobs

Para bloquear um blob para uso exclusivo, é possível obter uma concessão sobre ele. Ao adquirir a concessão, você especifica a duração da concessão. Uma concessão finita pode ser válida entre 15 e 60 segundos. Uma concessão também pode ser infinita, que tem como valor um bloqueio exclusivo. Você pode renovar uma concessão finita para estendê-la e pode liberar a concessão quando tiver terminado. O armazenamento do Azure lança automaticamente concessões finitas quando elas expiram.  

As concessões permitem que diferentes estratégias de sincronização sejam suportadas, incluindo operações exclusivas de leitura/gravação compartilhada, operações exclusivas de gravação/leitura exclusivas e operações de leitura exclusiva/gravação compartilhada. Quando existe uma concessão, o armazenamento do Azure impõe o acesso exclusivo às operações de gravação para o titular da concessão. No entanto, garantir que exclusividade para operações de leitura exija que o desenvolvedor certifique-se de que todos os aplicativos cliente usem uma ID de concessão e que apenas um cliente de cada vez tenha uma ID de concessão válida. As operações de leitura que não incluem uma ID de concessão resultam em leituras compartilhadas.  

Os exemplos de código a seguir mostram como adquirir uma concessão exclusiva em um blob, atualizar o conteúdo do blob fornecendo a ID de concessão e, em seguida, liberar a concessão. Se a concessão estiver ativa e a ID da concessão não for fornecida em uma solicitação de gravação, a operação de gravação falhará com o código de erro 412 (falha na pré-condição).  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>Simultaneidade pessimista para contêineres

As concessões em contêineres habilitam as mesmas estratégias de sincronização com suporte para BLOBs, incluindo gravação exclusiva/leitura compartilhada, leitura exclusiva/gravação exclusiva e gravação compartilhada/leitura exclusiva. Para contêineres, no entanto, o bloqueio exclusivo é imposto somente em operações de exclusão. Para excluir um contêiner com uma concessão ativa, o cliente deve incluir a ID da concessão ativa com a solicitação de exclusão. Todas as outras operações de contêiner terão sucesso em um contêiner concedido sem a ID da concessão.  

## <a name="next-steps"></a>Próximas etapas

* [Especificando cabeçalhos condicionais para operações do serviço blob](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [Lease Container](/rest/api/storageservices/lease-container)
* [Concessão de blob](/rest/api/storageservices/lease-blob)
