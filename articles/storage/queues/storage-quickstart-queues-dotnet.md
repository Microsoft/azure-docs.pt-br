---
title: 'Início Rápido: Usar o .NET para criar uma fila no Armazenamento do Azure'
description: Neste início rápido, você aprenderá como usar a biblioteca de clientes do Armazenamento do Azure para o .NET criar uma fila e adicionar mensagens a ela. Em seguida, você aprenderá como ler e processar mensagens da fila.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/06/2018
ms.author: mhopkins
ms.reviewer: cbrooks
ms.openlocfilehash: c3743c62dcbdccc2a119cfec570df96c622390c7
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540326"
---
# <a name="quickstart-use-net-to-create-a-queue-in-azure-storage"></a>Início Rápido: Usar o .NET para criar uma fila no Armazenamento do Azure

Neste início rápido, você aprenderá como usar a biblioteca de clientes do Armazenamento do Azure para o .NET criar uma fila e adicionar mensagens a ela. Em seguida, você aprenderá como ler e processar mensagens da fila. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Em seguida, baixe e instale o .NET Core 2.0 para seu sistema operacional. Caso esteja executando o Windows, você pode instalar o Visual Studio e usar o .NET Framework se preferir. Você também pode optar por instalar um editor para usar com o sistema operacional.

### <a name="windows"></a>Windows

- Instalar o [.NET Core para Windows](https://www.microsoft.com/net/download/windows) ou o [.NET Framework](https://www.microsoft.com/net/download/windows) (incluído no Visual Studio para Windows)
- Instalar o [Visual Studio para Windows](https://www.visualstudio.com/). Caso esteja usando o .NET Core, a instalação do Visual Studio é opcional.  

Para obter informações sobre como escolher entre o .NET Core e o .NET Framework, consulte [Como escolher entre o .NET Core e o .NET Framework para aplicativos de servidor](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

### <a name="linux"></a>Linux

- Instalar o [.NET Core para Linux](https://www.microsoft.com/net/download/linux)
- Opcionalmente, instalar o [Visual Studio Code](https://www.visualstudio.com/) e a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)

### <a name="macos"></a>macOS

- Instalar o [.NET Core para macOS](https://www.microsoft.com/net/download/macos).
- Opcionalmente, instalar o [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O aplicativo de exemplo usado neste guia de início rápido é um aplicativo de console básico. Você pode explorar o aplicativo de exemplo no [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Este comando clona o repositório para sua pasta do git local. Para abrir a solução do Visual Studio, procure a pasta *storage-queues-dotnet-quickstart*, abra-a e clique duas vezes em *storage-queues-dotnet-quickstart.sln*. 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

Para executar o aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. O aplicativo de exemplo lê a cadeia de conexão em uma variável de ambiente e utiliza-a para autorizar solicitações no Armazenamento do Azure.

Depois de copiar a cadeia de conexão, grave-a em uma nova variável de ambiente no computador local que executa o aplicativo. Para definir a variável de ambiente, abra uma janela de console e siga as instruções do seu sistema operacional. Substitua `<yourconnectionstring>` pela sua cadeia de conexão atual:

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente, talvez seja necessário reiniciar todos os programas em execução que precisarem ler a variável de ambiente, incluindo a janela do console. Por exemplo, se estiver usando o Visual Studio como seu editor, reinicie-o antes de executar o exemplo. 

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela de console para que as alterações entrem em vigor.

### <a name="macos"></a>macOS

Edite seu .bash_profile e adicione a variável de ambiente:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela de console para que as alterações entrem em vigor.

## <a name="run-the-sample"></a>Execute o exemplo

O aplicativo de exemplo cria uma fila e adiciona uma mensagem a ela. Primeiro, o aplicativo espia a mensagem sem removê-la da fila; depois, ele a recupera e a exclui da fila.

### <a name="windows"></a>Windows

Se estiver usando o Visual Studio como seu editor, você pode pressionar **F5** para executar. 

Caso contrário, navegue até seu diretório de aplicativo e execute o aplicativo com o comando `dotnet run`.

```
dotnet run
```

### <a name="linux"></a>Linux

Navegue até seu diretório de aplicativo e execute o aplicativo com o comando `dotnet run`.

```
dotnet run
```

### <a name="macos"></a>macOS

Navegue até seu diretório de aplicativo e execute o aplicativo com o comando `dotnet run`.

```
dotnet run
```

A saída do aplicativo de exemplo é semelhante ao seguinte exemplo:

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Em seguida, vamos percorrer o código de exemplo para que você possa entender como ele funciona.

### <a name="try-parsing-the-connection-string"></a>Tentar analisar a cadeia de conexão

Primeiro, o exemplo verificar se a variável de ambiente contém uma cadeia de conexão que pode ser analisada para criar um objeto [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) apontando para a conta de armazenamento. Para verificar se a cadeia de conexão é válida, o exemplo usa o método [TryParse](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse). Se **TryParse** for bem-sucedido, ele inicializará a variável *storageAccount* e retornará **true**.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...    
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>Criar a fila

Primeiro, o exemplo cria uma fila e adiciona uma mensagem a ela. 

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name 
// is unique in your storage account. 
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Adicionar uma mensagem

Em seguida, o exemplo adiciona uma mensagem à parte de trás da fila. 

Uma mensagem deve estar em um formato que possa ser incluído em uma solicitação XML com codificação UTF-8 e pode ter tamanho de até 64 KB. Se uma mensagem contém dados binários, recomendamos codificá-la como Base64.

Por padrão, a vida útil máxima de uma mensagem é definida como 7 dias. Você pode especificar qualquer número positivo para a vida útil da mensagem.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

Para adicionar uma mensagem que não expira, use `Timespan.FromSeconds(-1)` na chamada para [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>Espiar uma mensagem da fila

O exemplo mostra como espiar uma mensagem de uma fila. Quando você espia uma mensagem, pode ler o conteúdo dela. No entanto, a mensagem permanece visível para outros clientes; assim, outro cliente pode, subsequentemente, recuperar e processar a mensagem.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's 
// visibility, so that another client can still retrieve and process it. 
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Remover uma mensagem da fila

O exemplo mostra como remover uma mensagem da fila. Quando você remove uma mensagem da fila, você a recupera da frente da fila e a deixa temporariamente invisível para outros clientes. Por padrão, uma mensagem permanece invisível por 30 segundos. Durante esse tempo, seu código pode processá-la. Para concluir a remoção da mensagem da fila, exclua a mensagem imediatamente após o processamento para outro cliente não remover a mesma mensagem da fila.

Se o código falhar em processar uma mensagem devido a uma falha de hardware ou de software, ela ficará visível novamente após a expiração do período de invisibilidade. Outro cliente pode recuperar a mesma mensagem e tentar novamente.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for 
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>Limpar recursos

O exemplo limpa os recursos criados por meio da exclusão da fila. Excluir a fila também exclui as mensagens contidas nela.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>Recursos para desenvolvimento de aplicativos .NET com filas

Confira estes recursos adicionais para o desenvolvimento em .NET com as Filas do Azure:

### <a name="binaries-and-source-code"></a>Binários e código-fonte

- Baixar o pacote NuGet para a versão mais recente da [biblioteca de clientes do Armazenamento do Azure para .NET](/dotnet/api/overview/azure/storage/client)
    - [Comum](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
    - [Filas](https://www.nuget.org/packages/Azure.Storage.Queues/)
- Exiba o [código-fonte da biblioteca de clientes .NET](https://github.com/Azure/azure-storage-net) no GitHub.

### <a name="client-library-reference-and-samples"></a>Exemplos e referência da biblioteca de clientes

- Confira a [referência da API .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage) para saber mais sobre a biblioteca de clientes .NET.
- Explore [Exemplos de armazenamento da fila](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) gravados usando a biblioteca de clientes de .NET.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como adicionar mensagens a uma fila, espiar mensagens de uma fila e remover da fila e processar mensagens usando o .NET. 

> [!div class="nextstepaction"]
> [Comunicar-se entre aplicativos com o armazenamento de Filas do Azure](https://docs.microsoft.com/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- Para saber mais sobre o núcleo do .NET, confira [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
