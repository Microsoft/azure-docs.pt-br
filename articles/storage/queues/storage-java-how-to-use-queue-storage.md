---
title: Como usar o armazenamento de fila do Java – armazenamento do Azure
description: Saiba como usar o armazenamento de fila para criar e excluir filas. Aprenda a inserir, inspecionar, obter e excluir mensagens com a biblioteca de cliente de armazenamento do Azure para Java.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 08/19/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 997a37ac4252813abf1b35877cd34e192ec3e2ae
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585710"
---
# <a name="how-to-use-queue-storage-from-java"></a>Como usar o armazenamento de fila do Java

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Visão geral

Este guia mostrará como codificar para cenários comuns usando o serviço de armazenamento de filas do Azure. As amostras são escritas em Java e usam o [SDK de Armazenamento do Azure para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage). Os cenários incluem **inserção**, **exibição**, **obtenção** e **exclusão** de mensagens da fila. O código para **criar** e **excluir** filas também é abordado. Para obter mais informações sobre filas, consulte a seção [Próximas etapas](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar um aplicativo Java

# <a name="java-v12"></a>[Java V12](#tab/java)

Primeiro, verifique se o sistema de desenvolvimento atende aos pré-requisitos listados na [biblioteca de cliente de armazenamento de filas do Azure V12 para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue).

Para criar um aplicativo Java chamado `queues-how-to-v12` :

1. Em uma janela de console (como cmd, PowerShell ou bash), use o Maven para criar um novo aplicativo de console com o nome `queues-how-to-v12` . Digite o seguinte `mvn` comando para criar um projeto Java "Olá, mundo".

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.howto `
        --define artifactId=queues-howto-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

1. O resultado da geração do projeto deve ser algo similar a:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.775 s
    [INFO] Finished at: 2020-08-17T15:27:31-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created `queues-howto-v12` directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>Instalar o pacote

Abra o `pom.xml` arquivo em seu editor de texto. Adicione o seguinte elemento de dependência ao grupo de dependências.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Primeiro, verifique se o sistema de desenvolvimento atende aos pré-requisitos listados no [SDK de armazenamento do Azure para Java V8](https://github.com/azure/azure-storage-java). Siga as instruções para baixar e instalar as bibliotecas de armazenamento do Azure para Java. Em seguida, você pode criar um aplicativo Java usando os exemplos neste artigo.

---

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar seu aplicativo para acessar o Armazenamento de Filas

Adicione as seguintes instruções de importação na parte superior do arquivo Java em que você deseja usar as APIs de armazenamento do Azure para acessar filas:

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de Armazenamento do Azure

Um cliente de armazenamento do Azure usa uma cadeia de conexão de armazenamento para acessar os serviços de gerenciamento de dados. Obtenha o nome e a chave de acesso primária para sua conta de armazenamento listada no [portal do Azure](https://portal.azure.com). Use-os como `AccountName` os `AccountKey` valores e na cadeia de conexão. Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Você pode armazenar essa cadeia de caracteres no arquivo de configuração de serviço chamado `ServiceConfiguration.cscfg` . Para um aplicativo em execução em uma função de Microsoft Azure, acesse a cadeia de conexão chamando `RoleEnvironment.getConfigurationSettings` . Aqui está um exemplo de como obter a cadeia de conexão de um `Setting` elemento chamado `StorageConnectionString` :

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

Os exemplos a seguir pressupõem que você tenha um `String` objeto que contém a cadeia de conexão de armazenamento.

## <a name="how-to-create-a-queue"></a>Como criar uma fila

# <a name="java-v12"></a>[Java V12](#tab/java)

Um `QueueClient` objeto contém as operações para interagir com uma fila. O código a seguir cria um `QueueClient` objeto. Use o `QueueClient` objeto para criar a fila que você deseja usar.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Um `CloudQueueClient` objeto permite que você obtenha objetos de referência para filas. O código a seguir cria um `CloudQueueClient` objeto que fornece uma referência à fila que você deseja usar. Você poderá criar a fila se ela não existir.

> [!NOTE]
> Há outras maneiras de criar `CloudStorageAccount` objetos. Para obter mais informações, consulte `CloudStorageAccount` na [referência do SDK do cliente de armazenamento do Azure](https://azure.github.io/azure-sdk-for-java/storage.html).)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-add-a-message-to-a-queue"></a>Como adicionar uma mensagem a uma fila

# <a name="java-v12"></a>[Java V12](#tab/java)

Para inserir uma mensagem em uma fila existente, chame o `sendMessage` método. Uma mensagem pode ser uma cadeia de caracteres (em formato UTF-8) ou uma matriz de bytes. Aqui está o código que envia uma mensagem de cadeia de caracteres para a fila.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Para inserir uma mensagem em uma fila existente, primeiro crie uma nova `CloudQueueMessage` . Em seguida, chame o `addMessage` método. Um `CloudQueueMessage` pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes. Aqui está o código que cria uma fila (se ela não existir) e insere a mensagem `Hello, World` .

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-peek-at-the-next-message"></a>Como inspecionar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando `peekMessage` .

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como alterar o conteúdo de uma mensagem em fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status. O código a seguir atualiza uma mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender outros 30 segundos. Estender o tempo limite de visibilidade dá ao cliente mais 30 segundos para continuar a trabalhar na mensagem. Você também pode manter uma nova tentativa de contagem. Se a mensagem for repetida mais de *n* vezes, você a excluiria. Esse cenário protege contra uma mensagem que dispara um erro de aplicativo cada vez que é processado.

# <a name="java-v12"></a>[Java V12](#tab/java)

O exemplo de código a seguir pesquisa a fila de mensagens, localiza o primeiro conteúdo da mensagem que corresponde a uma cadeia de caracteres de pesquisa, modifica o conteúdo da mensagem e sai.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

O exemplo de código a seguir pesquisa a fila de mensagens, localiza o primeiro conteúdo da mensagem que corresponde `Hello, world` , modifica o conteúdo da mensagem e sai.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

O exemplo de código a seguir atualiza apenas a primeira mensagem visível na fila.

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-get-the-queue-length"></a>Como obter o comprimento da fila

Você pode obter uma estimativa do número de mensagens em uma fila.

# <a name="java-v12"></a>[Java V12](#tab/java)

O `getProperties` método retorna vários valores, incluindo o número de mensagens atualmente em uma fila. A contagem é aproximada apenas porque as mensagens podem ser adicionadas ou removidas após sua solicitação. O `getApproximateMessageCount` método retorna o último valor recuperado pela chamada para `getProperties` , sem chamar o armazenamento de fila.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

O `downloadAttributes` método recupera vários valores, incluindo o número de mensagens atualmente em uma fila. A contagem é aproximada apenas porque as mensagens podem ser adicionadas ou removidas após sua solicitação. O `getApproximateMessageCount` método retorna o último valor recuperado pela chamada para `downloadAttributes` , sem chamar o armazenamento de fila.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-dequeue-the-next-message"></a>Como remover a próxima mensagem da fila

# <a name="java-v12"></a>[Java V12](#tab/java)

Seu código remove uma mensagem de um fila em duas etapas. Ao chamar `receiveMessage` , você receberá a próxima mensagem em uma fila. Uma mensagem retornada de `receiveMessage` torna-se invisível para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para concluir a remoção da mensagem da fila, você também deve chamar `deleteMessage` . Se o código não processar uma mensagem, esse processo de duas etapas garantirá que você possa obter a mesma mensagem e tentar novamente. Seu código chama `deleteMessage` logo após a mensagem ser processada.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Seu código remove uma mensagem de um fila em duas etapas. Ao chamar `retrieveMessage` , você receberá a próxima mensagem em uma fila. Uma mensagem retornada de `retrieveMessage` torna-se invisível para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para concluir a remoção da mensagem da fila, você também deve chamar `deleteMessage` . Se o código não processar uma mensagem, esse processo de duas etapas garantirá que você possa obter a mesma mensagem e tentar novamente. Seu código chama `deleteMessage` logo após a mensagem ser processada.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, obtenha um lote de mensagens (até 32). Em segundo lugar, defina um tempo limite de invisibilidade mais longo ou mais curto, permitindo que seu código tenha mais ou menos tempo para processar totalmente cada mensagem.

# <a name="java-v12"></a>[Java V12](#tab/java)

O exemplo de código a seguir usa o método `receiveMessages` para receber 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop `for`. Ele também define o tempo limite de invisibilidade como cinco minutos (300 segundos) para cada mensagem. O tempo limite é iniciado para todas as mensagens ao mesmo tempo. Quando cinco minutos tiverem passado desde a chamada para `receiveMessages` , todas as mensagens não excluídas ficarão visíveis novamente.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

O exemplo de código a seguir usa o método `retrieveMessages` para receber 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop `for`. Ele também define o tempo limite de invisibilidade como cinco minutos (300 segundos) para cada mensagem. O tempo limite é iniciado para todas as mensagens ao mesmo tempo. Quando cinco minutos tiverem passado desde a chamada para `retrieveMessages` , todas as mensagens não excluídas ficarão visíveis novamente.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-list-the-queues"></a>Como: listar as filas

# <a name="java-v12"></a>[Java V12](#tab/java)

Para obter uma lista das filas atuais, chame o `QueueServiceClient.listQueues()` método, que retornará uma coleção de `QueueItem` objetos.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Para obter uma lista das filas atuais, chame o `CloudQueueClient.listQueues()` método, que retornará uma coleção de `CloudQueue` objetos.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-delete-a-queue"></a>Como excluir uma fila

# <a name="java-v12"></a>[Java V12](#tab/java)

Para excluir uma fila e todas as mensagens contidas nela, chame o `delete` método no `QueueClient` objeto.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[V8 Java](#tab/java8)

Para excluir uma fila e todas as mensagens contidas nela, chame o `deleteIfExists` método no `CloudQueue` objeto.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- [SDK de Armazenamento do Azure para Java](https://github.com/Azure/Azure-SDK-for-Java)
- [Referência do SDK do cliente de armazenamento do Azure](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)
- [API REST dos serviços de armazenamento do Azure](/rest/api/storageservices/)
- [Blog da equipe de armazenamento do Azure](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog)
