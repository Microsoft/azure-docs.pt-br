---
title: Escrever procedimentos armazenados e gatilhos usando a API de consulta JavaScript no Azure Cosmos DB
description: Saiba como escrever procedimentos armazenados e gatilhos usando a API de Consulta do JavaScript no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/07/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 449af63ef3409b8c8173edef7a5d77074cca02b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335833"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Como escrever procedimentos armazenados e gatilhos no Azure Cosmos DB usando a API de consulta do JavaScript
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O Azure Cosmos DB permite que você execute consultas otimizadas usando uma interface fluente do JavaScript sem nenhum conhecimento da linguagem SQL que pode ser usada para escrever procedimentos armazenados ou gatilhos. Para saber mais sobre o suporte à API de Consulta do JavaScript no Azure Cosmos DB, confira o artigo [Trabalhando com a API de consulta integrada da linguagem JavaScript no Azure Cosmos DB](javascript-query-api.md).

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>Procedimento armazenado usando a API de consulta do JavaScript

O exemplo de código a seguir mostra como a API de consulta JavaScript é usada no contexto de um procedimento armazenado. O procedimento armazenado insere um item do Azure Cosmos especificado por um parâmetro de entrada e atualiza um documento de metadados usando o método `__.filter()`, com minSize, maxSize e totalSize com base na propriedade de tamanho do item de entrada.

> [!NOTE]
> `__` (sublinhado duplo) é um alias para `getContext().getCollection()` ao usar a API de consulta do JavaScript.

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para saber mais sobre procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB:

* [Como trabalhar com procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Como registrar e usar procedimentos armazenados no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Como registrar e usar [pré-gatilhos](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) e [pós-gatilhos](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) no Azure Cosmos DB

* [Como registrar e usar funções definidas pelo usuário no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Chaves de partição sintética no Azure Cosmos DB](synthetic-partition-keys.md)
