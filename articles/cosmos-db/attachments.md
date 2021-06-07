---
title: Azure Cosmos DB anexos
description: Este artigo apresenta uma visão geral de Azure Cosmos DB anexos.
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: a8e968d05a1f844a79d2e42d10c323ed4c392424
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521213"
---
# <a name="azure-cosmos-db-attachments"></a>Azure Cosmos DB anexos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB anexos são itens especiais que contêm referências a um metadado associado com um blob externo ou arquivo de mídia.

O Azure Cosmos DB dá suporte a dois tipos de anexos:

* Os **anexos não gerenciados** são um wrapper em volta de uma referência de URI para um blob que é armazenado em um serviço externo (por exemplo, armazenamento do Azure, onedrive, etc.). Essa abordagem é semelhante ao armazenamento de uma propriedade URI em um item de Azure Cosmos DB padrão.
* Os **anexos gerenciados** são BLOBs gerenciados e armazenados internamente por Azure Cosmos DB e expostos por meio de um mediaLink gerado pelo sistema.


> [!NOTE]
> O anexo é um recurso herdado. Seu suporte é definido para oferecer a funcionalidade contínua se você já estiver usando esse recurso.
> 
> Em vez de usar anexos, recomendamos que você use o armazenamento de BLOBs do Azure como um serviço de armazenamento de BLOBs criado especificamente para armazenar dados de BLOB. Você pode continuar a armazenar metadados relacionados a BLOBs, juntamente com links de URI de referência, em Azure Cosmos DB como propriedades de item. O armazenamento desses dados no Azure Cosmos DB fornece a capacidade de consultar metadados e links para BLOBs armazenados no armazenamento de BLOBs do Azure.
> 
> A Microsoft está comprometida em fornecer um aviso mínimo de 36 meses antes de substituir completamente os anexos, que serão anunciados em uma data adicional.

## <a name="known-limitations"></a>Limitações conhecidas

Os anexos gerenciados do Azure Cosmos DB são diferentes de seu suporte para itens padrão – para os quais ele oferece escalabilidade ilimitada, distribuição global e integração com outros serviços do Azure.

- Não há suporte para anexos em todas as versões dos SDKs do Azure Cosmos DB.
- Os anexos gerenciados são limitados a 2 GB de armazenamento por conta de banco de dados.
- Os anexos gerenciados não são compatíveis com a distribuição global do Azure Cosmos DB e não são replicados entre regiões.

> [!NOTE]
> Azure Cosmos DB API para MongoDB versão 3,2 utiliza anexos gerenciados para GridFS e estão sujeitas às mesmas limitações.
>
> Recomendamos que os desenvolvedores que usam o conjunto de recursos do MongoDB GridFS sejam atualizados para Azure Cosmos DB API para MongoDB versão 3,6 ou superior, que é dissociada de anexos e fornece uma experiência melhor. Como alternativa, os desenvolvedores que usam o conjunto de recursos MongoDB GridFS também devem considerar o uso do armazenamento de BLOBs do Azure, que é criado especificamente para armazenar conteúdo de BLOB e oferece funcionalidade expandida a um custo menor em comparação com GridFS.

## <a name="migrating-attachments-to-azure-blob-storage"></a>Migrando anexos para o armazenamento de BLOBs do Azure

É recomendável migrar Azure Cosmos DB anexos para o armazenamento de BLOBs do Azure seguindo estas etapas:

1. Copie os dados de anexos do seu contêiner de origem Azure Cosmos DB para o contêiner de armazenamento de BLOBs do Azure de destino.
2. Valide os dados de blob carregados no contêiner de destino do armazenamento de BLOBs do Azure.
3. Se aplicável, adicione referências de URI aos BLOBs contidos no armazenamento de BLOBs do Azure como propriedades de cadeia de caracteres dentro de seu conjunto de Azure Cosmos DB.
4. Refatore o código do aplicativo para ler e gravar BLOBs do novo contêiner de armazenamento de BLOBs do Azure.

O exemplo de código dotnet a seguir mostra como copiar anexos de Azure Cosmos DB para o armazenamento de BLOBs do Azure como parte de um fluxo de migração usando o SDK do .NET do Azure Cosmos DB V2 e o SDK do .NET do armazenamento de BLOBs do Azure V12. Certifique-se de substituir o `<placeholder values>` para a conta de Azure Cosmos DB de origem e o contêiner de armazenamento de BLOBs do Azure de destino.

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;
                    PartitionKey docPartitionKey = new PartitionKey(document.Id);

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = docPartitionKey,
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;

                            // Clean up attachment from Azure Cosmos DB.
                            // Warning: please verify you've succesfully migrated attachments to blog storage prior to cleaning up Azure Cosmos DB.
                            // await cosmosClient.DeleteAttachmentAsync(
                            //     attachment.SelfLink,
                            //     new RequestOptions { PartitionKey = docPartitionKey }
                            // );

                            // Console.WriteLine("Cleaned up attachment ... Document Id: {0} , Attachment Id: {1}", document.Id, attachment.Id);
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>Próximas etapas

- Introdução ao [armazenamento de BLOBs do Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- Obter referências para usar anexos por meio [do SDK do .net v2 do Azure Cosmos DB](/dotnet/api/microsoft.azure.documents.attachment)
- Obter referências para usar anexos por meio [do SDK do Java v2 de Azure Cosmos DB](/java/api/com.microsoft.azure.documentdb.attachment)
- Obter referências para usar anexos via [API REST do Azure Cosmos DB](/rest/api/cosmos-db/attachments)
