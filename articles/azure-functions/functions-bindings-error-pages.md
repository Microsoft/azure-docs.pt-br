---
title: Diretrizes de tratamento de erros de funções do Azure Functions | Microsoft Docs
description: Fornece diretrizes gerais para tratamento de erros que ocorrem quando as funções são executadas e links para tópicos de erros específicos da associação.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 02/01/2018
ms.date: 11/22/2018
ms.author: v-junlch
ms.openlocfilehash: bf54d312de5625a7fa44cea4d5107e83cf15583c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105501"
---
# <a name="azure-functions-error-handling"></a>Tratamento de erros do Azure Functions

Este tópico fornece diretrizes gerais para tratamento de erros que ocorrem quando as funções são executadas. Ele também fornece links para tópicos que descrevem os erros específicos da associação que podem ocorrer. 

## <a name="handing-errors-in-functions"></a>Tratamento de erros em funções
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Códigos de erro de associação

Ao se integrar com os serviços do Azure, você pode ter erros gerados com origem nas APIs dos serviços subjacentes. Links para a documentação do código de erro para esses serviços podem ser encontrados na seção **Exceções e códigos de retorno** dos seguintes tópicos de gatilho e referência da associação:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Armazenamento de Blobs](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Hubs de Evento](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Hubs de Notificação](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Armazenamento de filas](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Barramento de Serviço](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Armazenamento de tabelas](functions-bindings-storage-table.md#exceptions-and-return-codes)

<!-- Update_Description: update metedata properties -->