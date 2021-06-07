---
title: Como usar o armazenamento de filas do Azure de Node.js armazenamento do Azure
description: Aprenda a usar o armazenamento de filas do Azure para criar e excluir filas. Aprenda a inserir, obter e excluir mensagens usando Node.js.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/21/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 12ae05e10cdf0fa9a5f0725acaa1784eedc3612c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803673"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Como usar o armazenamento de filas do Azure de Node.js

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Visão geral

Este guia mostra como realizar cenários comuns usando o armazenamento de filas do Azure. As amostras são gravadas usando a API do Node.js. Os cenários abordados incluem inserção, inspeção, obtenção e exclusão de mensagens da fila. Aprenda também a criar e excluir filas.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js

Para criar um aplicativo Node.js em branco, consulte [criar um aplicativo Web do Node.js no serviço de Azure app](../../app-service/quickstart-nodejs.md), [criar e implantar um aplicativo de Node.js nos serviços de nuvem do Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) usando o PowerShell ou o [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Configurar seu aplicativo para acessar o armazenamento

A [biblioteca de cliente de armazenamento do Azure para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript) inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST de armazenamento.

<!-- docutune:ignore Terminal -->

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usar o Gerenciador de pacotes de nó (NPM) para obter o pacote

1. Use uma interface de linha de comando, como PowerShell (Windows), terminal (Mac) ou bash (Unix), navegue até a pasta em que você criou o aplicativo de exemplo.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

1. Digite `npm install @azure/storage-queue` na janela de comando.

1. Verifique se uma `node_modules` pasta foi criada. Dentro dessa pasta, você encontrará o `@azure/storage-queue` pacote, que contém a biblioteca de cliente que você precisa para acessar o armazenamento.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. Digite `npm install azure-storage` na janela de comando.

1. Verifique se uma `node_modules` pasta foi criada. Dentro dessa pasta, você encontrará o `azure-storage` pacote, que contém as bibliotecas necessárias para acessar o armazenamento.

---

### <a name="import-the-package"></a>Importar o pacote

Usando seu editor de código, adicione o seguinte ao início do arquivo JavaScript em que você pretende usar filas.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Como criar uma fila

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

O código a seguir obtém o valor de uma variável de ambiente chamada `AZURE_STORAGE_CONNECTION_STRING` e a usa para criar um [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient) objeto. Esse objeto é usado para criar um [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) objeto que permite que você trabalhe com uma fila específica.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Se a fila já existir, uma exceção será lançada.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

O módulo do Azure lerá as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY` , ou `AZURE_STORAGE_CONNECTION_STRING` para obter informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta ao chamar `createQueueService` .

O código a seguir cria um `QueueService` objeto, que permite que você trabalhe com filas.

```javascript
var queueSvc = azure.createQueueService();
```

Chame o `createQueueIfNotExists` método para criar uma nova fila com o nome especificado ou retorne a fila se ela já existir.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Se a fila for criada, `result.created` é true. Se a fila existir, `result.created` é false.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Como inserir uma mensagem em uma fila

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Para adicionar uma mensagem a uma fila, chame o [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) método.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Para inserir uma mensagem em uma fila, chame o `createMessage` método para criar uma nova mensagem e adicioná-la à fila.

```javascript
queueSvc.createMessage('myqueue', "Hello, World", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Como inspecionar a próxima mensagem

Você pode inspecionar mensagens na fila sem removê-las da fila chamando o `peekMessages` método.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Por padrão, [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) o inspeciona uma única mensagem. O exemplo a seguir inspeciona as primeiras cinco mensagens na fila. Se menos de cinco mensagens estiverem visíveis, apenas as mensagens visíveis serão retornadas.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Por padrão, `peekMessages` o inspeciona uma única mensagem.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

O `result` contém a mensagem.

---

Chamar `peekMessages` quando não há mensagens na fila não retornará um erro. No entanto, nenhuma mensagem é retornada.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como alterar o conteúdo de uma mensagem em fila

O exemplo a seguir atualiza o texto de uma mensagem.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Altere o conteúdo de uma mensagem in-loco na fila chamando [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-) .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Altere o conteúdo de uma mensagem in-loco na fila chamando `updateMessage` .

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>Como remover uma mensagem da fila

A remoção da fila de uma mensagem é um processo de duas etapas:

1. Obter a mensagem.

1. Excluir a mensagem.

O exemplo a seguir obtém uma mensagem e, em seguida, a exclui.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Para obter uma mensagem, chame o [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) método. Essa chamada torna as mensagens invisíveis na fila, portanto, nenhum outro cliente pode processá-las. Depois que o aplicativo tiver processado uma mensagem, chame [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) para excluí-la da fila.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Por padrão, uma mensagem é ocultada apenas por 30 segundos. Depois de 30 segundos, ele é visível para outros clientes. Você pode especificar um valor diferente definindo [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) ao chamar `receiveMessages` .

Chamar `receiveMessages` quando não há mensagens na fila não retornará um erro. No entanto, nenhuma mensagem será retornada.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Para obter uma mensagem, chame o `getMessages` método. Essa chamada torna as mensagens invisíveis na fila, portanto, nenhum outro cliente pode processá-las. Depois que o aplicativo tiver processado uma mensagem, chame `deleteMessage` para excluí-la da fila.

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

Por padrão, uma mensagem é ocultada apenas por 30 segundos. Depois de 30 segundos, ele é visível para outros clientes. Você pode especificar um valor diferente usando `options.visibilityTimeout` com `getMessages` .

`getMessages`O uso de quando não há mensagens na fila não retornará um erro. No entanto, nenhuma mensagem será retornada.

---

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para remover mensagens da fila

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Há duas maneiras de personalizar a recuperação da mensagem de uma fila:

- [`options.numberOfMessages`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages): Recuperar um lote de mensagens (até 32).
- [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout): Defina um tempo limite de invisibilidade mais longo ou mais curto.

O exemplo a seguir usa o `receiveMessages` método para obter cinco mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop `for`. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem retornada por este método.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Há duas maneiras de personalizar a recuperação da mensagem de uma fila:

- `options.numOfMessages`: Recuperar um lote de mensagens (até 32).
- `options.visibilityTimeout`: Defina um tempo limite de invisibilidade mais longo ou mais curto.

O exemplo a seguir usa o `getMessages` método para obter 15 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop `for`. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem retornada por este método.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>Como obter o comprimento da fila

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

O [`getProperties`](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) método retorna metadados sobre a fila, incluindo o número aproximado de mensagens aguardando na fila.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

O `getQueueMetadata` método retorna metadados sobre a fila, incluindo o número aproximado de mensagens aguardando na fila.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Como listar filas

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Para recuperar uma lista de filas, chame [`QueueServiceClient.listQueues`](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) . Para recuperar uma lista filtrada por um prefixo específico, defina [Options. Prefix](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) em sua chamada para `listQueues` .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Para recuperar uma lista de filas, use `listQueuesSegmented` . Para recuperar uma lista filtrada por um prefixo específico, use `listQueuesSegmentedWithPrefix` .

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Se todas as filas não puderem ser retornadas, passe `result.continuationToken` como o primeiro parâmetro de `listQueuesSegmented` ou o segundo parâmetro de `listQueuesSegmentedWithPrefix` para recuperar mais resultados.

---

## <a name="how-to-delete-a-queue"></a>Como excluir uma fila

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Para excluir uma fila e todas as mensagens contidas nela, chame o [`DeleteQueue`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) método no `QueueClient` objeto.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Para limpar todas as mensagens de uma fila sem excluí-las, chame [`ClearMessages`](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-) .

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Para excluir uma fila e todas as mensagens contidas nela, chame o `deleteQueue` método no objeto de fila.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Para limpar todas as mensagens de uma fila sem excluí-las, chame `clearMessages` .

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- Visite o [blog da equipe de armazenamento do Azure](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog) para saber o que há de novo
- Visite o repositório [biblioteca de cliente do armazenamento do Azure para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#Azure-storage-client-library-for-JavaScript) no github
