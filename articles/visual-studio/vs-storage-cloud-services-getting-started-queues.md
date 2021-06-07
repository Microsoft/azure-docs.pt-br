---
title: Introdução ao armazenamento de filas usando o Visual Studio (serviços de nuvem)
description: Como começar a usar o armazenamento de Fila do Azure em um projeto de serviço de nuvem no Visual Studio após a conexão a uma conta de armazenamento usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 94f248edfebd6c6fedb78a54eee220c0ef38b4ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95545850"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução aos serviços conectados do armazenamento de Fila do Azure e Visual Studio (projetos de serviços de nuvem)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral
Este artigo descreve como começar a usar o Armazenamento de Filas do Azure no Visual Studio depois de criar ou consultar uma conta de armazenamento do Azure em um projeto de serviços de nuvem usando a caixa de diálogo **Adicionar Serviços Conectados** do Visual Studio.

Mostraremos como criar uma fila em código. Também mostraremos como realizar operações básicas de fila, como adicionar, modificar, ler e remover entidades de fila. Os exemplos são escritos em código C# e usam a [biblioteca de cliente armazenamento do Microsoft Azure para .net](/previous-versions/azure/dn261237(v=azure.100)).

A operação **Adicionar Serviços Conectados** instala os pacotes NuGet apropriados para acessar o armazenamento do Azure no seu projeto e adiciona a cadeia de conexão para a conta de armazenamento aos arquivos de configuração do projeto.

* Consulte a [Introdução ao Armazenamento de Filas do Azure usando o .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter mais informações sobre a manipulação de filas no código.
* Consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.
* Consulte a [documentação de serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços de nuvem do Azure.
* Consulte [ASP.NET](https://www.asp.net) para obter mais informações sobre como programar aplicativos ASP.NET.

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento.

## <a name="access-queues-in-code"></a>Acessar filas em código
Para acessar filas nos projetos de serviços de nuvem do Visual Studio, você precisa incluir os itens a seguir a qualquer arquivo de origem C# que acesse o armazenamento de fila do Azure.

1. Verifique se as declarações de namespace na parte superior do arquivo de C# incluem estas instruções de **uso** .
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
3. Obtenha um objeto **CloudQueueClient** para referenciar objetos de fila em sua conta de armazenamento.  
   
    ```csharp
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
4. Obtenha um objeto **CloudQueue** para referenciar uma fila específica.
   
    ```csharp
    // Get a reference to a queue named "messageQueue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");
    ```

**OBSERVAÇÃO:** use todo esse código antes do código nos exemplos a seguir.

## <a name="create-a-queue-in-code"></a>Criar uma fila em código
Para criar a fila do Azure em código, basta adicionar uma chamada a **CreateIfNotExists**.

```csharp
// Create the CloudQueue if it does not exist
messageQueue.CreateIfNotExists();
```

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem a uma fila
Para inserir uma mensagem em uma fila existente, crie um novo objeto **CloudQueueMessage** e chame o método **AddMessage**.

Um objeto **CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes.

Aqui está um exemplo que insere a mensagem “Hello, World”.

```csharp
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
messageQueue.AddMessage(message);
```

## <a name="read-a-message-in-a-queue"></a>Ler uma mensagem em uma fila
Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método **PeekMessage** .

```csharp
// Peek at the next message
CloudQueueMessage peekedMessage = messageQueue.PeekMessage();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Ler e remover uma mensagem em uma fila
Seu código pode remover uma mensagem de uma fila em duas etapas.

1. Chame **GetMessage** para obter a próxima mensagem em uma fila. Uma mensagem retornada de **GetMessage** torna-se invisível para qualquer outra mensagem de leitura de código dessa fila. Por padrão, essa mensagem permanece invisível por 30 segundos.
2. Para concluir a remoção da mensagem da fila, chame **DeleteMessage**.

Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. O código a seguir chamará **DeleteMessage** logo depois que a mensagem tiver sido processada.

```csharp
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

// Process the message in less than 30 seconds

// Then delete the message.
await messageQueue.DeleteMessage(retrievedMessage);
```


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Usar opções adicionais para processar e remover mensagens da fila
Há duas maneiras de personalizar a recuperação da mensagem de uma fila.

* Você pode obter um lote de mensagens (até 32).
* Você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O exemplo de código a seguir usa o método **GetMessages** para obter 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop **foreach** . Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem. Observe que os 5 minutos começam para todas as mensagens ao mesmo tempo; portanto, após 5 minutos desde a chamada para **GetMessages**, todas as mensagens que não foram excluídas ficarão visíveis novamente.

Veja um exemplo:

```csharp
foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.

    // Then delete the message after processing
    messageQueue.DeleteMessage(message);

}
```

## <a name="get-the-queue-length"></a>Obter o tamanho da fila
Você pode obter uma estimativa do número de mensagens em uma fila. O método **fetchattributes** solicita que o serviço fila recupere os atributos da fila, incluindo a contagem de mensagens. A propriedade **ApproximateMethodCount** retorna o último valor recuperado pelo método **FetchAttributes**, sem chamar o serviço Fila.

```csharp
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Usar o padrão Async-Await com APIs comuns de fila do Azure
Este exemplo mostra como usar o padrão Async-Await com APIs comuns de fila do Azure. O exemplo chama a versão assíncrona de cada um dos métodos fornecidos, isso pode ser visto pela postagem **assíncrona** de cada método. Quando um método assíncrono é usado, o padrão async-await suspende a execução local até que a chamada seja concluída. Esse comportamento permite que o thread atual faça outro trabalho que ajude a evitar gargalos de desempenho e melhora a capacidade de resposta geral do aplicativo. Para obter mais detalhes sobre como usar o padrão de Async-Await no .NET [, consulte Async e Await (C# e Visual Basic)](/previous-versions/hh191443(v=vs.140))

```csharp
// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Add the message asynchronously
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Delete the message asynchronously
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Excluir uma fila
Para excluir uma fila e todas as mensagens que ela contém, chame o método **Delete** no objeto fila.

```csharp
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]