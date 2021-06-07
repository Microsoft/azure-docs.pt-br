---
title: 'Início Rápido: Biblioteca de clientes do Armazenamento de Filas do Azure v12 – JavaScript'
description: Saiba como usar a biblioteca de clientes do Armazenamento de Filas do Azure v12 para JavaScript para criar uma fila e adicionar mensagens a ela. Depois, descubra como ler e excluir mensagens da fila. Você também aprenderá a excluir uma fila.
author: twooley
ms.author: twooley
ms.date: 12/13/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 943678debc32116ff7a2e54810c4edcf2d331bd6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534428"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Início Rápido: Biblioteca de clientes do Armazenamento de Filas do Azure v12 para JavaScript

Introdução à biblioteca de clientes do Armazenamento de Filas do Azure v12 para JavaScript. O Armazenamento de Filas do Azure é um serviço usado para armazenar grandes quantidades de mensagens para recuperação e processamento posteriores. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

Use a biblioteca de clientes do Armazenamento de Filas do Azure v12 para JavaScript a fim de:

- Criar uma fila
- Adicionar mensagens a uma fila
- Espiar mensagens em uma fila
- Atualizar uma mensagem em uma fila
- Receber mensagens de uma fila
- Excluir mensagens de uma fila
- Excluir uma fila

Recursos adicionais:

- [Documentação de referência da API](/javascript/api/@azure/storage-queue/)
- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- [Pacote (npm)](https://www.npmjs.com/package/@azure/storage-queue)
- [Amostras](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- Conta de armazenamento do Azure - [criar uma conta de armazenamento](../common/storage-account-create.md)
- [Node.js](https://nodejs.org/en/download/) atual para o seu sistema operacional.

## <a name="setting-up"></a>Configurando

Esta seção descreve como preparar um projeto para trabalhar com a biblioteca de clientes do Armazenamento de Filas do Azure v12 para JavaScript.

### <a name="create-the-project"></a>Criar o projeto

Criar um aplicativo Node.js chamado `queues-quickstart-v12`

1. Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para o projeto.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Alterne para o diretório `queues-quickstart-v12` recém-criado.

    ```console
    cd queues-quickstart-v12
    ```

1. Crie um novo arquivo de texto chamado `package.json`. Esse arquivo define o projeto Node.js. Salve esse arquivo no diretório `queues-quickstart-v12`. Este é o conteúdo do arquivo:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Você pode colocar seu próprio nome no campo `author`, se desejar.

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório `queues-quickstart-v12`, instale a biblioteca de clientes do Armazenamento de Filas do Azure para o pacote JavaScript usando o comando `npm install`.

```console
npm install
```

Esse comando lê o arquivo `package.json` e instala a biblioteca de clientes do Armazenamento de Filas do Azure v12 para o pacote JavaScript e todas as bibliotecas das quais ela depende.

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Abra outro arquivo de texto novo no editor de código
1. Adicione chamadas `require` para carregar os módulos do Azure e do Node.js
1. Crie a estrutura para o programa, incluindo uma manipulação de exceção muito básica

    O código é o seguinte:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue Storage client library v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Salve o novo arquivo como `queues-quickstart-v12.js` no diretório `queues-quickstart-v12`.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de Filas do Azure é um serviço usado para armazenar grandes quantidades de mensagens. Uma mensagem da fila pode ter até 64 KB. Uma fila pode conter milhões de mensagens, até o limite da capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processamento assíncrono. O Armazenamento de Filas oferece três tipos de recursos:

- A conta de armazenamento
- Uma fila na conta de armazenamento
- Mensagens na fila

O diagrama a seguir mostra a relação entre esses recursos.

![Diagrama da arquitetura do armazenamento de Filas](./media/storage-queues-introduction/queue1.png)

Use as seguintes classes de JavaScript para interagir com esses recursos:

- [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient): O `QueueServiceClient` permite que você gerencie todas as filas em sua conta de armazenamento.
- [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient): A classe `QueueClient` permite que você gerencie e manipule uma fila individual e as mensagens dela.
- [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage): A classe `QueueMessage` representa os objetos individuais retornados ao chamar [`ReceiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) em uma fila.

## <a name="code-examples"></a>Exemplos de código

Esses snippets de código de exemplo mostram como executar as seguintes ações com a biblioteca de clientes do Armazenamento de Filas do Azure para JavaScript:

- [Obter a cadeia de conexão](#get-the-connection-string)
- [Criar uma fila](#create-a-queue)
- [Adicionar mensagens a uma fila](#add-messages-to-a-queue)
- [Espiar mensagens em uma fila](#peek-at-messages-in-a-queue)
- [Atualizar uma mensagem em uma fila](#update-a-message-in-a-queue)
- [Receber mensagens de uma fila](#receive-messages-from-a-queue)
- [Excluir mensagens de uma fila](#delete-messages-from-a-queue)
- [Excluir uma fila](#delete-a-queue)

### <a name="get-the-connection-string"></a>Obter a cadeia de conexão

O código a seguir recupera a cadeia de conexão da conta de armazenamento da variável de ambiente criada na seção [Configurar a cadeia de conexão do armazenamento](#configure-your-storage-connection-string).

Adicione esse código à função `main`:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Criar uma fila

Escolha um nome para a nova fila. O código a seguir acrescenta um valor de UUID ao nome da fila para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes de fila podem conter apenas letras minúsculas, números e hifens e precisam começar com uma letra ou um número. Cada hífen deve ser precedido e seguido por um caractere que não seja um hífen. O nome também precisa ter entre 3 e 63 caracteres. Para obter mais informações, confira [Como nomear filas e metadados](/rest/api/storageservices/naming-queues-and-metadata).

Crie uma instância da classe [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient). Em seguida, chame o método [`create`](/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) para criar a fila na sua conta de armazenamento.

Adicione este código ao final da função `main`:

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Adicionar mensagens a uma fila

O snippet de código a seguir adiciona mensagens à fila chamando o método [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-). Ele também salva a [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage) retornada da terceira chamada `sendMessage`. O `sendMessageResponse` retornado é usado para atualizar o conteúdo da mensagem posteriormente no programa.

Adicione este código ao final da função `main`:

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Espiar mensagens em uma fila

Espie as mensagens na fila chamando o método [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-). Esse método recupera uma ou mais mensagens do início da fila, mas não altera a visibilidade da mensagem.

Adicione este código ao final da função `main`:

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Atualizar uma mensagem em uma fila

Atualize o conteúdo de uma mensagem chamando o método [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-). Esse método pode alterar o conteúdo e o tempo limite da visibilidade de uma mensagem. O conteúdo da mensagem precisa ser uma cadeia de caracteres codificada em UTF-8 com até 64 KB. Junto com o novo conteúdo, transmita `messageId` e `popReceipt` da resposta que foi salva anteriormente no código. As propriedades de `sendMessageResponse` identificam qual mensagem atualizar.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

Baixe as mensagens adicionadas anteriormente chamando o método [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-). No campo `numberOfMessages`, transmita o número máximo de mensagens a serem recebidas para esta chamada.

Adicione este código ao final da função `main`:

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Excluir mensagens de uma fila

Exclua mensagens da fila depois que elas forem recebidas e processadas. Nesse caso, o processamento só exibe a mensagem no console.

Exclua as mensagens chamando o método [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-). As mensagens que não forem explicitamente excluídas acabarão se tornando visíveis na fila novamente para outra oportunidade de processamento.

Adicione este código ao final da função `main`:

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Excluir uma fila

O código a seguir limpa os recursos que o aplicativo criou ao excluir a fila usando o método [`delete`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-).

Adicione este código ao final da função `main` e salve o arquivo:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>Executar o código

Esse aplicativo cria e adiciona três mensagens a uma fila do Azure. O código lista as mensagens na fila e, em seguida, recupera essas mensagens e as exclui, antes de excluir a fila.

Na janela do console, procure o diretório que contém o arquivo `queues-quickstart-v12.js` e use o comando `node` a seguir para executar o aplicativo.

```console
node queues-quickstart-v12.js
```

A saída do aplicativo é semelhante ao seguinte exemplo:

```output
Azure Queue Storage client library v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Percorra o código em seu depurador e verifique o [portal do Azure](https://portal.azure.com) durante todo o processo. Verifique sua conta de armazenamento para saber se as mensagens da fila são criadas e excluídas.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma fila e a adicionar mensagens a ela usando código JavaScript. Em seguida, você aprendeu a espiar, recuperar e excluir mensagens. Por fim, você aprendeu a excluir uma fila de mensagens.

Para obter tutoriais, amostras, inícios rápidos e outros tipos de documentação, visite:

> [!div class="nextstepaction"]
> [Documentação do Azure para JavaScript](/azure/developer/javascript/)

- Para saber mais, confira a [Biblioteca de clientes do Armazenamento de Filas do Azure para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue).
- Para ver mais aplicativos de exemplo do Armazenamento de Filas do Azure, confira [Biblioteca de clientes do Armazenamento de Filas do Azure v12 para JavaScript – exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
