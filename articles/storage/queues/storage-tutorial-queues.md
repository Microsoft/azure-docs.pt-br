---
title: 'Tutorial: Trabalhar com filas do Armazenamento de Filas do Azure no .NET'
description: Um tutorial sobre o uso do Armazenamento de Filas do Azure para criar filas, bem como inserir, obter e excluir mensagens usando código .NET.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 06/09/2020
ms.topic: tutorial
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: dda6fb816fcba4ab9350edd956d895a3b9b204d4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276120"
---
# <a name="tutorial-work-with-azure-queue-storage-queues-in-net"></a>Tutorial: Trabalhar com filas do Armazenamento de Filas do Azure no .NET

O Armazenamento de Filas do Azure implementa filas baseadas em nuvem para permitir comunicação entre os componentes de um aplicativo distribuído. Cada fila mantém uma lista de mensagens que podem ser adicionadas por um componente emissor e processadas por um componente receptor. Com uma fila, o aplicativo pode ser dimensionado imediatamente para atender à demanda. Este artigo mostra as etapas básicas para trabalhar com uma fila do Armazenamento de Filas do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Criar uma conta de Armazenamento do Azure
> - Criar o aplicativo
> - Adicionar as bibliotecas de clientes do Azure
> - Adicionar suporte para código assíncrono
> - Criar uma fila
> - Inserir mensagens em uma fila
> - Remover mensagens da fila
> - Excluir uma fila vazia
> - Verificar argumentos de linha de comando
> - Compilar e executar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

- Obtenha a cópia gratuita do editor [Visual Studio Code](https://code.visualstudio.com/download) multiplataforma.
- Baixe e instale o [SDK do .NET Core](https://dotnet.microsoft.com/download) versão 3.1 ou posterior.
- Caso não tenha uma assinatura atual do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-azure-storage-account"></a>Criar uma conta do Armazenamento do Azure

Primeiro, crie uma conta do Armazenamento do Microsoft Azure. Para obter um guia passo a passo sobre como criar uma conta de armazenamento, confira [Criar uma conta de armazenamento](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json). Essa é uma etapa separada que você executa depois de criar uma conta gratuita do Azure nos pré-requisitos.

## <a name="create-the-app"></a>Criar o aplicativo

Crie um aplicativo .NET Core nomeado `QueueApp`. Para simplificar, esse aplicativo enviará e receberá mensagens através da fila.

1. Em uma janela do console (como cmd, PowerShell ou CLI do Azure), use o comando `dotnet new` para criar um aplicativo do console com o nome `QueueApp`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único chamado `Program.cs`.

   ```console
   dotnet new console -n QueueApp
   ```

2. Alterne para a pasta `QueueApp` recém-criada e crie o aplicativo para verificar se está tudo bem.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Você verá resultados semelhantes à seguinte saída:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.63 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp3.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

<!-- markdownlint-disable MD023 -->

## <a name="add-the-azure-client-libraries"></a>Adicionar as bibliotecas de clientes do Azure

1. Adicione as bibliotecas de clientes do Armazenamento do Azure ao projeto usando o comando `dotnet add package`.

   # <a name="net-v12"></a>[.NET v12](#tab/dotnet)

   Execute o comando a seguir na pasta do projeto na janela do console.

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   Execute os comandos a seguir na pasta do projeto na janela do console.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   ```

   ```console
   dotnet add package Microsoft.Azure.Storage.Queue
   ```
   ---

### <a name="add-using-statements"></a>Adicionar instruções using

1. Na linha de comando no diretório do projeto, digite `code .` para abrir o Visual Studio Code no diretório atual. Mantenha a janela da linha de comando aberta. Haverá mais comandos para executar posteriormente. Se você receber uma solicitação para adicionar ativos C# necessários para build e depuração, clique no botão **Sim**.

1. Abra o arquivo de origem `Program.cs` e adicione os namespaces a seguir, logo após a instrução `using System;`. Esse aplicativo usa tipos desses namespaces para conectar-se ao Armazenamento do Azure e trabalhar com filas.

   # <a name="net-v12"></a>[.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. Salve o arquivo `Program.cs`.

## <a name="add-support-for-asynchronous-code"></a>Adicionar suporte para código assíncrono

Como o aplicativo usa recursos de nuvem, o código executa assincronamente.

1. Atualize o método `Main` para ser executado de maneira assíncrona. Substitua `void` por um valor retornado da `async Task`.

   ```csharp
   static async Task Main(string[] args)
   ```

1. Salve o arquivo `Program.cs`.

## <a name="create-a-queue"></a>Criar uma fila

Antes de fazer chamadas para as APIs do Azure, você precisa obter suas credenciais do portal do Azure.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>Adicionar a cadeia de conexão ao aplicativo

Adicione a cadeia de conexão ao aplicativo para que ele possa acessar a conta de armazenamento.

1. Reverta para o Visual Studio Code.

1. No método `Main`, substitua o código `Console.WriteLine("Hello, World");` pela linha a seguir que obtém a cadeia de conexão da variável de ambiente.

   # <a name="net-v12"></a>[.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. Adicione o código a seguir a `Main` para criar um objeto queue, que posteriormente será passado para os métodos de envio e recebimento.

   # <a name="net-v12"></a>[.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. Salve o arquivo.

## <a name="insert-messages-into-the-queue"></a>Inserir mensagens na fila

Criar um novo método para enviar uma mensagem na fila.

1. Adicione o método `InsertMessageAsync` a seguir à sua classe `Program`.

   # <a name="net-v12"></a>[.NET v12](#tab/dotnet)

   Esse método é passado por uma referência de fila. Ao chamar [`CreateIfNotExistsAsync`](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync), uma fila será criada, se ela ainda não existir. Em seguida, ele adiciona o `newMessage` à fila chamando [`SendMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   Esse método é passado por uma referência de fila. Ao chamar [`CreateIfNotExistsAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync), uma fila será criada, se ela ainda não existir. Em seguida, ele adiciona o `newMessage` à fila chamando [`AddMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. **Opcional:** Por padrão, a vida útil máxima de uma mensagem é definida como sete dias. Você pode especificar qualquer número positivo para a vida útil da mensagem. O snippet de código a seguir adiciona uma mensagem que **nunca** expira.

   # <a name="net-v12"></a>[.NET v12](#tab/dotnet)

    Para adicionar uma mensagem que não expira, use `Timespan.FromSeconds(-1)` na chamada para `SendMessageAsync`.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

    Para adicionar uma mensagem que não expira, use `Timespan.FromSeconds(-1)` na chamada para `AddMessageAsync`.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. Salve o arquivo.

Uma mensagem da fila deve estar em um formato compatível com uma solicitação XML usando a codificação UTF-8. Uma mensagem pode ter tamanho de até 64 KB. Se uma mensagem contém dados binários, [codifique a mensagem como Base64](/dotnet/api/system.convert.tobase64string).

## <a name="dequeue-messages"></a>Remover mensagens da fila

Criar um método para recuperar uma mensagem da fila. Quando a mensagem for recebida com êxito, será importante excluí-la da fila para que a mensagem não seja processada mais de uma vez.

1. Adicione um novo método chamado `RetrieveNextMessageAsync` à sua classe `Program`.

   # <a name="net-v12"></a>[.NET v12](#tab/dotnet)

   Esse método recebe uma mensagem da fila ao chamar [`ReceiveMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync), passando `1` no primeiro parâmetro para recuperar apenas a próxima mensagem na fila. Após receber a mensagem, exclua-a da fila, chamando [`DeleteMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync).

   Quando uma mensagem é enviada para a fila com uma versão do SDK anterior à v12, ela é codificada automaticamente em Base64. Da v12 em diante, essa funcionalidade foi removida. Quando você recupera uma mensagem usando o SDK v12, ela não é automaticamente decodificada em Base64. Você precisa realizar explicitamente a [decodificação em Base64](/dotnet/api/system.convert.frombase64string) do conteúdo por conta própria.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   Esse método recebe uma mensagem da fila, chamando [`GetMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Após receber a mensagem, exclua-a da fila, chamando [`DeleteMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. Salve o arquivo.

## <a name="delete-an-empty-queue"></a>Excluir uma fila vazia

É uma melhor prática no final de um projeto identificar se os recursos criados ainda serão necessários. Recursos deixados em execução podem custar dinheiro. Se a fila existir, mas estiver vazia, pergunte ao usuário se ele deseja excluí-la.

1. Expanda o método `RetrieveNextMessageAsync` para incluir uma solicitação e excluir a fila vazia.

   # <a name="net-v12"></a>[.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. Salve o arquivo.

## <a name="check-for-command-line-arguments"></a>Verificar argumentos de linha de comando

Se houver argumentos de linha de comando inseridos no aplicativo, considere que esses argumentos são uma mensagem a ser adicionada à fila. Junte os argumentos para criar uma cadeia de caracteres. Adicione essa cadeia de caracteres à fila de mensagens, chamando o método `InsertMessageAsync` adicionado anteriormente.

Se não houver argumentos de linha de comando, tente realizar uma operação de recuperação. Chame o método `RetrieveNextMessageAsync` para recuperar a próxima mensagem na fila.

Por fim, aguarde a entrada do usuário antes de sair, chamando `Console.ReadLine`.

1. Expanda o método `Main` para verificar os argumentos de linha de comandos e aguarde a entrada do usuário.

   # <a name="net-v12"></a>[.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. Salve o arquivo.

## <a name="complete-code"></a>Código completo

Aqui é apresentada a lista completa de códigos para este projeto.

   # <a name="net-v12"></a>[.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_AllCode":::
   ---

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. A partir da linha de comando no diretório do projeto, execute o seguinte comando dotnet para compilar o projeto.

   ```console
   dotnet build
   ```

1. Depois que o projeto for compilado com êxito, execute o seguinte comando para adicionar a primeira mensagem à fila.

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

1. Execute o aplicativo sem argumentos de linha de comando para receber e remover a primeira mensagem na fila.

   ```console
   dotnet run
   ```

1. Continue a executar o aplicativo até que todas as mensagens sejam removidas. Se executá-lo mais uma vez, você receberá uma mensagem informando que a fila está vazia e uma solicitação para excluir a fila.

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

Neste tutorial, você aprendeu a:

1. Criar uma fila
1. Adicionar e remover mensagens de uma fila
1. Excluir uma fila do Armazenamento de Filas do Azure

Para obter mais informações, confira os guias de início rápido do Armazenamento de Filas do Azure.

> [!div class="nextstepaction"]
> [Início rápido de Filas para o portal](storage-quickstart-queues-portal.md)

- [Início rápido de Filas para .NET](storage-quickstart-queues-dotnet.md)
- [Início rápido de Filas para Java](storage-quickstart-queues-java.md)
- [Início rápido de Filas para Python](storage-quickstart-queues-python.md)
- [Início rápido de Filas para JavaScript](storage-quickstart-queues-nodejs.md)
