---
title: incluir arquivo
description: incluir arquivo
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 16ab569428510b3b423d6727fd31ee450a8d197e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381590"
---
### <a name="default"></a>Padrão

Você pode usar os seguintes tipos de parâmetros para o blob que está disparando:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Requer associação "inout" `direction` em *function.json* ou `FileAccess.ReadWrite` em uma biblioteca de classes C#.

Se você tentar associar a um dos tipos de SDK de armazenamento e obter uma mensagem de erro, certifique-se de que você tem uma referência a [a versão correta do SDK de armazenamento](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

A associação com `string` ou `Byte[]` só é recomendada se o tamanho do blob for pequeno, pois o conteúdo inteiro do blob é carregado na memória. Geralmente, é preferível usar um tipo `Stream` ou `CloudBlockBlob`. Para obter mais informações, consulte [Concorrência e uso de memória](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) mais adiante neste artigo.

### <a name="additional-types"></a>Tipos adicionais

Os aplicativos que usam a [versão 5.0.0 ou posterior da extensão de Armazenamento](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) também poderão usar tipos do [SDK do Azure para .NET](/dotnet/api/overview/azure/storage.blobs-readme). Essa versão removerá o suporte dos tipos herdados `ICloudBlob`, `CloudBlockBlob`, `CloudPageBlob` e `CloudAppendBlob` para beneficiar os seguintes tipos:

- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>1</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>1</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>1</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>1</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>1</sup>

<sup>1</sup> Requer associação "inout" `direction` em *function.json* ou `FileAccess.ReadWrite` em uma biblioteca de classes C#.

Para obter exemplos de como usar esses tipos, confira [o repositório GitHub da extensão](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples).
