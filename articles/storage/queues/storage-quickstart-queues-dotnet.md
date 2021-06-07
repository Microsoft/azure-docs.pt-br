---
title: 'Início Rápido: Biblioteca de clientes do Armazenamento de Filas do Azure v12 – .NET'
description: Aprenda a usar a biblioteca de clientes do Armazenamento de Filas do Azure v12 para o .NET para criar uma fila e adicionar mensagens a ela. Em seguida, você aprenderá a ler e excluir mensagens da fila. Você também aprenderá a excluir uma fila.
author: twooley
ms.author: twooley
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c4af7afd96f6c503f35cdb4896c3b49d4ef6ed3
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276222"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Início Rápido: Biblioteca de clientes do Armazenamento de Filas do Azure v12 para .NET

Introdução à biblioteca de clientes do Armazenamento de Filas do Azure versão 12 para .NET. O Armazenamento de Filas do Azure é um serviço usado para armazenar grandes quantidades de mensagens para recuperação e processamento posteriores. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

Use a biblioteca de clientes do Armazenamento de Filas do Azure v12 para .NET para:

- Criar uma fila
- Adicionar mensagens a uma fila
- Espiar mensagens em uma fila
- Atualizar uma mensagem em uma fila
- Receber mensagens de uma fila
- Excluir mensagens de uma fila
- Excluir uma fila

Recursos adicionais:

- [Documentação de referência da API](/dotnet/api/azure.storage.queues)
- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues)
- [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0)
- [Amostras](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- Conta de armazenamento do Azure - [criar uma conta de armazenamento](../common/storage-account-create.md)
- O [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) atual para o seu sistema operacional. Obtenha o SDK e não o runtime.

## <a name="setting-up"></a>Configurando

Esta seção descreve como preparar um projeto para funcionar com a biblioteca de clientes do Armazenamento de Filas do Azure v12 para .NET.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo .NET Core chamado `QueuesQuickstartV12`.

1. Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `QueuesQuickstartV12`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único chamado `Program.cs`.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Alterne para o diretório `QueuesQuickstartV12` recém-criado.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale o pacote da biblioteca de clientes do Armazenamento de Filas do Azure para .NET usando o comando `dotnet add package`.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Abra o arquivo `Program.cs` em seu editor
1. Remova a instrução `Console.WriteLine("Hello, World");`
1. Adicione diretivas `using`
1. Atualize a declaração de método `Main` para [dar suporte ao código assíncrono](/dotnet/csharp/whats-new/csharp-7#async-main)

O código é o seguinte:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de Filas do Azure é um serviço usado para armazenar grandes quantidades de mensagens. Uma mensagem da fila pode ter até 64 KB. Uma fila pode conter milhões de mensagens, até o limite da capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processamento assíncrono. O Armazenamento de Filas oferece três tipos de recursos:

- A conta de armazenamento
- Uma fila na conta de armazenamento
- Mensagens na fila

O diagrama a seguir mostra a relação entre esses recursos.

![Diagrama da arquitetura do armazenamento de Filas](./media/storage-queues-introduction/queue1.png)

Use as seguintes classes .NET para interagir com esses recursos:

- [`QueueServiceClient`](/dotnet/api/azure.storage.queues.queueserviceclient): O `QueueServiceClient` permite que você gerencie todas as filas em sua conta de armazenamento.
- [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient): A classe `QueueClient` permite que você gerencie e manipule uma fila individual e as mensagens dela.
- [`QueueMessage`](/dotnet/api/azure.storage.queues.models.queuemessage): A classe `QueueMessage` representa os objetos individuais retornados ao chamar [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) em uma fila.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código de exemplo mostram como realizar as seguintes ações com a biblioteca de clientes do Armazenamento de Filas do Azure para .NET:

- [Obter a cadeia de conexão](#get-the-connection-string)
- [Criar uma fila](#create-a-queue)
- [Adicionar mensagens a uma fila](#add-messages-to-a-queue)
- [Espiar mensagens em uma fila](#peek-at-messages-in-a-queue)
- [Atualizar uma mensagem em uma fila](#update-a-message-in-a-queue)
- [Receber mensagens de uma fila](#receive-messages-from-a-queue)
- [Excluir mensagens de uma fila](#delete-messages-from-a-queue)
- [Excluir uma fila](#delete-a-queue)

### <a name="get-the-connection-string"></a>Obtenha a cadeia de conexão

O código a seguir recupera a cadeia de conexão da conta de armazenamento. A cadeia de conexão é armazenada na variável de ambiente criada na seção [Configurar sua cadeia de conexão de armazenamento](#configure-your-storage-connection-string).

Adicione esse código dentro do método `Main`:

```csharp
Console.WriteLine("Azure Queue Storage client library v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Criar uma fila

Escolha um nome para a nova fila. O código a seguir acrescenta um valor de GUID ao nome da fila para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes de fila podem conter apenas letras minúsculas, números e hifens e precisam começar com uma letra ou um número. Cada hífen deve ser precedido e seguido por um caractere que não seja um hífen. O nome também precisa ter entre 3 e 63 caracteres. Para obter mais informações, confira [Como nomear filas e metadados](/rest/api/storageservices/naming-queues-and-metadata).

Crie uma instância da classe [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient). Em seguida, chame o método [`CreateAsync`](/dotnet/api/azure.storage.queues.queueclient.createasync) para criar a fila na sua conta de armazenamento.

Adicione este código ao final do método `Main`:

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Adicionar mensagens a uma fila

O snippet de código a seguir adiciona mensagens à fila de maneira assíncrona chamando o método [`SendMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync). Ele também salva um [`SendReceipt`](/dotnet/api/azure.storage.queues.models.sendreceipt) retornado de uma chamada `SendMessageAsync`. O recibo é usado para atualizar a mensagem posteriormente no programa.

Adicione este código ao final do método `Main`:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Espiar mensagens em uma fila

Espie as mensagens na fila chamando o método [`PeekMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync). Esse método recupera uma ou mais mensagens do início da fila, mas não altera a visibilidade da mensagem.

Adicione este código ao final do método `Main`:

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Atualizar uma mensagem em uma fila

Atualize o conteúdo de uma mensagem chamando o método [`UpdateMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync). Esse método pode alterar o conteúdo e o tempo limite da visibilidade de uma mensagem. O conteúdo da mensagem precisa ser uma cadeia de caracteres codificada em UTF-8 com até 64 KB. Junto com o novo conteúdo da mensagem, transmita os valores do `SendReceipt` que foi salvo anteriormente no código. Os valores de `SendReceipt` identificam a mensagem a ser atualizada.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

Baixe as mensagens adicionadas anteriormente chamando o método [`ReceiveMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync).

Adicione este código ao final do método `Main`:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Excluir mensagens de uma fila

Exclua as mensagens da fila depois que elas forem processadas. Nesse caso, o processamento só exibe a mensagem no console.

O aplicativo pausa a entrada do usuário chamando `Console.ReadLine` antes de processar e excluir as mensagens. Verifique no [portal do Azure](https://portal.azure.com) se os recursos foram criados corretamente antes de serem excluídos. As mensagens que não forem explicitamente excluídas acabarão se tornando visíveis na fila novamente para outra oportunidade de processamento.

Adicione este código ao final do método `Main`:

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Excluir uma fila

O código a seguir limpa os recursos que o aplicativo criou ao excluir a fila usando o método [`DeleteAsync`](/dotnet/api/azure.storage.queues.queueclient.deleteasync).

Adicione este código ao final do método `Main`:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Executar o código

Esse aplicativo cria e adiciona três mensagens a uma fila do Azure. O código lista as mensagens na fila e, em seguida, recupera essas mensagens e as exclui, antes de excluir a fila.

Na janela do console, navegue até seu diretório de aplicativo e compile e execute o aplicativo.

```console
dotnet build
```

```console
dotnet run
```

A saída do aplicativo é semelhante ao seguinte exemplo:

```output
Azure Queue Storage client library v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

Quando o aplicativo pausar antes de receber mensagens, verifique sua conta de armazenamento no [portal do Azure](https://portal.azure.com). Verifique se as mensagens estão na fila.

Pressione a tecla `Enter` para receber e excluir as mensagens. Quando solicitado, pressione a tecla `Enter` novamente para excluir a fila e concluir a demonstração.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma fila e adicionar mensagens a ela usando o código .NET assíncrono. Em seguida, você aprendeu a espiar, recuperar e excluir mensagens. Por fim, você aprendeu a excluir uma fila de mensagens.

Para obter tutoriais, amostras, inícios rápidos e outros tipos de documentação, visite:

> [!div class="nextstepaction"]
> [Azure para desenvolvedores .NET e .NET Core](/dotnet/azure/)

- Para saber mais, confira as [bibliotecas do Armazenamento do Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage).
- Para mais aplicativos de exemplo do Armazenamento de Filas do Azure, confira [Biblioteca de clientes do Armazenamento de Filas do Azure para exemplos do .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples).
- Para saber mais sobre o núcleo do .NET, confira [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
