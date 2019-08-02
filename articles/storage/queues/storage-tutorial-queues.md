---
title: Tutorial - Trabalhar com filas de armazenamento do Azure - Armazenamento do Microsoft Azure
description: Um tutorial sobre como usar o serviço Fila do Azure para criar filas, bem como inserir, obter e excluir mensagens.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: cbrooks
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/24/2019
ms.openlocfilehash: 08ef140eb860637cc0c09619abe7051cc007e99f
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540289"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Tutorial: Trabalhar com filas de armazenamento do Azure

O armazenamento de Filas do Azure implementa filas baseadas em nuvem para permitir comunicação entre os componentes de um aplicativo distribuído. Cada fila mantém uma lista de mensagens que podem ser adicionadas por um componente emissor e processadas por um componente receptor. Com uma fila, o aplicativo pode ser dimensionado imediatamente para atender à demanda. Este artigo mostra as etapas básicas para trabalhar com uma fila de armazenamento do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar uma conta de armazenamento do Azure
> - Crie o aplicativo
> - Adicionar suporte para código assíncrono
> - Criar uma fila
> - Inserir mensagens em uma fila
> - Remover mensagens da fila
> - Excluir uma fila vazia
> - Verificar argumentos de linha de comando
> - Compilar e executar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

- Obtenha a cópia gratuita do editor [Visual Studio Code](https://code.visualstudio.com/download) multiplataforma.
- Baixe e instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).
- Caso não tenha uma assinatura atual do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

Primeiro, crie uma conta de armazenamento do Azure. Para obter um guia passo a passo sobre como criar uma conta de armazenamento, consulte o início rápido [Criar uma conta de armazenamento](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json).

## <a name="create-the-app"></a>Crie o aplicativo

Crie um aplicativo .NET Core nomeado **QueueApp**. Para simplificar, esse aplicativo enviará e receberá mensagens através da fila.

1. Em uma janela do console (como CMD, PowerShell ou CLI do Azure), use o comando `dotnet new` para criar um novo aplicativo do console com o nome **QueueApp**. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Alterne para a pasta **QueueApp** recém-criada e crie o aplicativo para verificar se tudo está correto.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Você deverá ver resultados semelhantes ao seguinte:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Adicionar suporte para código assíncrono

Como o aplicativo usa recursos de nuvem, o código executa assincronamente. No entanto, **async** e **await** do C# não eram palavras-chave válidas nos métodos **Main** até o C# 7.1. É possível alternar facilmente para esse compilador por meio de um sinalizador no arquivo **csproj**.

1. Na linha de comando no diretório do projeto, digite `code .` para abrir o Visual Studio Code no diretório atual. Mantenha a janela da linha de comando aberta. Haverá mais comandos para executar posteriormente. Se você receber uma solicitação para adicionar ativos C# necessários para build e depuração, clique no botão **Sim**.

2. Abra o arquivo **QueueApp.csproj** no editor.

3. Adicione `<LangVersion>7.1</LangVersion>` ao primeiro **PropertyGroup** no arquivo de build. Certifique-se de adicionar somente a marca **LangVersion**, já que o **TargetFramework** poderá ser diferente dependendo da versão do .NET instalada.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Salve o arquivo **QueueApp.csproj**.

5. Abra o arquivo de origem **Program.cs** e atualize o método **Main** para executar assincronamente. Substitua **void** por um valor retornado da **Tarefa assíncrona**.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Salve o arquivo **Program.cs**.

## <a name="create-a-queue"></a>Criar uma fila

1. Instale os pacotes **Microsoft.Storage. Common** e **Microsoft.Azure.Storage.Queue** para o projeto com o comando `dotnet add package`. Execute os seguintes comandos dotnet da pasta do projeto na janela do console.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. Na parte superior do arquivo **Program.cs**, adicione os seguintes namespaces logo após a instrução `using System;`. Esse aplicativo usa tipos desses namespaces para conectar-se ao Armazenamento do Azure e trabalhar com filas.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Salve o arquivo **Program.cs**.

### <a name="get-your-connection-string"></a>Obter sua cadeia de conexão

A biblioteca cliente usa uma cadeia de conexão para estabelecer a conexão. A cadeia de conexão está disponível na seção **Configurações** da conta de armazenamento no portal do Azure.

1. No navegador da Web, entre no [portal do Azure](https://portal.azure.com/).

2. Navegue até sua conta de armazenamento no portal do Azure.

3. Selecione **Chaves de acesso**.

4. Clique no botão **Copiar** à direita do campo **Cadeia de conexão**.

![Cadeia de conexão](media/storage-tutorial-queues/get-connection-string.png)

A cadeia de conexão está neste formato:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Adicionar a cadeia de conexão ao aplicativo

Adicione a cadeia de conexão ao aplicativo para que ele possa acessar a conta de armazenamento.

1. Reverta para o Visual Studio Code.

2. Na classe **Programa**, adicione um membro `private const string connectionString =` para manter a cadeia de conexão.

3. Após o sinal de igual, cole o valor da cadeia de caracteres que você copiou anteriormente no portal do Azure. O valor **connectionString** será exclusivo para a conta.

4. Remova o código "Olá, Mundo" do método **Main**. O código deverá ser semelhante ao seguinte, mas com o valor da cadeia de conexão exclusivo.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. Atualize **Main** para criar um objeto **CloudQueue**, que posteriormente será passado para os métodos de envio e recebimento.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Salve o arquivo.

## <a name="insert-messages-into-the-queue"></a>Inserir mensagens na fila

Criar um novo método para enviar uma mensagem na fila. Adicione o seguinte método à sua classe **Programa**. Esse método obtém uma referência de fila e, em seguida, cria uma nova fila, se ela ainda não existir, chamando [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Em seguida, o método adiciona a mensagem à fila chamando [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Adicione o seguinte método **SendMessageAsync** à sua classe **Programa**.

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Salve o arquivo.

Uma mensagem deve estar em um formato que possa ser incluído em uma solicitação XML com codificação UTF-8 e pode ter tamanho de até 64 KB. Se uma mensagem contém dados binários, recomendamos codificar a mensagem como Base64.

Por padrão, a vida útil máxima de uma mensagem é definida como 7 dias. Você pode especificar qualquer número positivo para a vida útil da mensagem. Para adicionar uma mensagem que não expira, use `Timespan.FromSeconds(-1)` em sua chamada para **AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Remover mensagens da fila

Crie um novo método chamado **ReceiveMessageAsync**. Esse método recebe uma mensagem da fila, chamando [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Quando a mensagem for recebida com êxito, será importante excluí-la da fila para que a mensagem não seja processada mais de uma vez. Após receber a mensagem, exclua-a da fila, chamando [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Adicione o seguinte método **ReceiveMessageAsync** à classe **Programa**.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Salve o arquivo.

## <a name="delete-an-empty-queue"></a>Excluir uma fila vazia

É uma melhor prática no final de um projeto identificar se os recursos criados ainda serão necessários. Recursos deixados em execução podem custar dinheiro. Se a fila existir, mas estiver vazia, pergunte ao usuário se ele deseja excluí-la.

1. Expanda o método **ReceiveMessageAsync** para incluir uma solicitação e excluir a fila vazia.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Salve o arquivo.

## <a name="check-for-command-line-arguments"></a>Verificar argumentos de linha de comando

Se houver argumentos de linha de comando inseridos no aplicativo, considere que esses argumentos são uma mensagem a ser adicionada à fila. Junte os argumentos para criar uma cadeia de caracteres. Adicione essa cadeia de caracteres à fila de mensagens, chamando o método **SendMessageAsync** adicionado anteriormente.

Se não houver argumentos de linha de comando, execute uma operação de recuperação. Chame o método **ReceiveMessageAsync** para recuperar a primeira mensagem na fila.

Por fim, aguarde a entrada do usuário antes de sair, chamando **Console.ReadLine**.

1. Expanda o método **Main** para verificar os argumentos de linha de comandos e aguarde a entrada do usuário.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Salve o arquivo.

## <a name="complete-code"></a>Código completo

Aqui é apresentada a lista completa de códigos para este projeto.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. A partir da linha de comando no diretório do projeto, execute o seguinte comando dotnet para compilar o projeto.

   ```console
   dotnet build
   ```

2. Depois que o projeto for compilado com êxito, execute o seguinte comando para adicionar a primeira mensagem à fila.

   ```console
   dotnet run First queue message
   ```

Você deverá ver este resultado:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Execute o aplicativo sem argumentos de linha de comando para receber e remover a primeira mensagem na fila.

   ```console
   dotnet run
   ```

4. Continue a executar o aplicativo até que todas as mensagens sejam removidas. Se executá-lo mais uma vez, você receberá uma mensagem informando que a fila está vazia e uma solicitação para excluir a fila.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

1. Criar uma fila
2. Adicionar e remover mensagens de uma fila
3. Excluir uma fila de armazenamento do Azure

Para obter mais informações, confira o início rápido das Filas do Azure.

> [!div class="nextstepaction"]
> [Início rápido das Filas](storage-quickstart-queues-portal.md)
