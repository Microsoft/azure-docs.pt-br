---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c1784111cd2fc2c93b67510f310b9e513cf2b86e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171717"
---
Os [gatilhos e associações](../articles/azure-functions/functions-triggers-bindings.md) do Azure Functions se comunicam com vários serviços do Azure. Ao se integrar com esses serviços, você pode ter erros gerados com origem nas APIs dos serviços do Azure subjacentes. Também podem ocorrer erros quando você tenta se comunicar com outros serviços do seu código de função usando bibliotecas REST ou de cliente. Para evitar perda de dados e garantir o bom comportamento das funções, é importante tratar os erros de qualquer fonte.

Os seguintes gatilhos têm suporte interno de repetição:

* [Armazenamento de Blobs do Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Armazenamento de Filas do Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Barramento de Serviço do Azure (fila/tópico)](../articles/azure-functions/functions-bindings-service-bus.md)

Por padrão, esses gatilhos são tentados novamente até cinco vezes. Depois da quinta nova tentativa, esses gatilhos podem gravar uma mensagem para uma [fila de suspeitas](../articles/azure-functions/functions-bindings-storage-queue.md#trigger---poison-messages) especial.

Para outros gatilhos de Funções, não há nenhuma nova tentativa interna quando ocorrerem erros durante a execução da função. Para evitar a perda de informações do gatilho no caso de um erro na sua função, recomendamos que você use bloqueios try-catch em seu código de função para capturar erros. Quando ocorre um erro, grave as informações transmitidas para a função pelo gatilho em uma fila de mensagens "suspeitas" especial. Essa abordagem é a mesma usada pelo [gatilho de armazenamento de Blob](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---poison-blobs).

Dessa forma, você pode capturar eventos de gatilho que poderiam ser perdidos devido a erros e tentá-los novamente mais tarde usando outra função para processar mensagens da fila de suspeitas usando as informações armazenadas.  
