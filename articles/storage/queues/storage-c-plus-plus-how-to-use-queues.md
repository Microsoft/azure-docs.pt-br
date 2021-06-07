---
title: Como usar o armazenamento de filas (C++)-armazenamento do Azure
description: Saiba como usar o serviço de armazenamento de filas no Azure. As amostras são escritas em C++.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 07/16/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 44d64c54049c02b6602f01b97effcc33b03dbcfe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97591320"
---
# <a name="how-to-use-queue-storage-from-c"></a>Como usar o armazenamento de filas do C++

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral

Este guia mostrará como executar cenários comuns usando o serviço de armazenamento de filas do Azure. Os exemplos são escritos em C++ e usam a [biblioteca de cliente de armazenamento do Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Os cenários abrangidos incluem **inserir**, **exibir**, **obter** e **excluir** mensagens da fila, bem como **criar e excluir filas**.

> [!NOTE]
> Este guia destina-se à biblioteca de cliente de armazenamento do Azure para C++ v 1.0.0 e superior. A versão recomendada é a biblioteca de cliente de armazenamento do Azure v 2.2.0, que está disponível via [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar um aplicativo em C++

Neste guia, você usará os recursos de armazenamento que podem ser executados em um aplicativo C++.

Para fazer isso, você precisará instalar a biblioteca de cliente do armazenamento do Azure para C++ e criar uma conta de armazenamento do Azure em sua assinatura do Azure.

<!-- docutune:casing "Getting Started on Linux" -->

Para instalar a biblioteca de cliente de armazenamento do Azure para C++, você pode usar os seguintes métodos:

- **Linux:** Siga as instruções fornecidas no [Leiame da biblioteca de cliente do armazenamento do Azure para C++: introdução na](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) página do Linux.
- **Windows:** No Windows, use [vcpkg](https://github.com/microsoft/vcpkg) como o gerenciador de dependências. Siga o guia de [início rápido](https://github.com/microsoft/vcpkg#quick-start) para inicializar `vcpkg` . Em seguir, use este comando para instalar a biblioteca:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Você pode encontrar um guia sobre como criar o código-fonte e exportar para o NuGet no arquivo [Leiame](https://github.com/Azure/azure-storage-cpp#download--install) .

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar seu aplicativo para acessar o Armazenamento de Filas

Adicione as seguintes instruções include à parte superior do arquivo C++ em que você deseja usar as APIs de armazenamento do Azure para acessar as filas:

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de Armazenamento do Azure

Um cliente de Armazenamento do Azure usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais para acessar serviços de gerenciamento de dados. Ao executar em um aplicativo cliente, você deve fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome da sua conta de armazenamento e a chave de acesso de armazenamento para a conta de armazenamento listada no [portal do Azure](https://portal.azure.com) para os `AccountName` `AccountKey` valores e. Para obter informações sobre contas de armazenamento e chaves de acesso, consulte [sobre contas de armazenamento do Azure](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Para testar seu aplicativo no computador local do Windows, você pode usar o [emulador de armazenamento azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Azurite é um utilitário que simula o armazenamento de BLOBs do Azure e o armazenamento de filas no computador de desenvolvimento local. Este exemplo mostra como você pode declarar um campo estático para manter a cadeia de conexão em seu emulador de armazenamento local:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Para iniciar o azurite, consulte [usar o emulador azurite para o desenvolvimento de armazenamento local do Azure](../common/storage-use-azurite.md).

Os exemplos abaixo pressupõem que você usou um desses dois métodos para obter a cadeia de conexão do armazenamento.

## <a name="retrieve-your-connection-string"></a>Recuperar sua cadeia de conexão

Você pode usar a classe `cloud_storage_account` para representar as informações de sua conta de armazenamento. Para recuperar as informações da conta de armazenamento da cadeia de conexão de armazenamento, você pode usar o `parse` método.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Como criar uma fila

Um `cloud_queue_client` objeto permite que você obtenha objetos de referência para filas. O código a seguir cria um `cloud_queue_client` objeto.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Use o `cloud_queue_client` objeto para obter uma referência à fila que você deseja usar. Você poderá criar a fila se ela não existir.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila existente, primeiro crie uma nova `cloud_queue_message` . Em seguida, chame o `add_message` método. Um `cloud_queue_message` pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes. Aqui está o código que cria uma fila (se ela não existir) e insere a mensagem `Hello, World` :

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Como inspecionar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o `peek_message` método.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como alterar o conteúdo de uma mensagem em fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender mais 60 segundos. Isso salva o estado do trabalho associado à mensagem e dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você poderia usar essa técnica para rastrear fluxos de trabalho com várias etapas em mensagens de fila, sem precisar começar desde o início se uma etapa de processamento falhar devido a uma falha de hardware ou de software. Normalmente, você mantém uma contagem de repetições e, se a mensagem for repetida mais de n vezes, você a exclui. Isso protege contra uma mensagem que dispara um erro do aplicativo sempre que for processada.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-dequeue-the-next-message"></a>Como remover a próxima mensagem da fila

Seu código remove uma mensagem de um fila em duas etapas. Ao chamar `get_message` , você receberá a próxima mensagem em uma fila. Uma mensagem retornada de `get_message` torna-se invisível para todas as outras mensagens de leitura de código da fila. Para concluir a remoção da mensagem da fila, você também deve chamar `delete_message` . Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama `delete_message` logo após a mensagem ser processada.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-use-additional-options-for-dequeuing-messages"></a>Como: usar opções adicionais para remover mensagens do enfileiramento

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O exemplo de código a seguir usa o método `get_messages` para receber 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop `for`. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem. Observe que os cinco minutos começam para todas as mensagens ao mesmo tempo; portanto, depois de cinco minutos passaram desde a chamada para `get_messages` , todas as mensagens que não foram excluídas ficarão visíveis novamente.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Como obter o comprimento da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O `download_attributes` método retorna propriedades de fila, incluindo a contagem de mensagens. O `approximate_message_count` método obtém o número aproximado de mensagens na fila.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Como excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o `delete_queue_if_exists` método no objeto de fila.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links para saber mais sobre o armazenamento do Azure.

- [Como usar o armazenamento de BLOBs do C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
- [Como usar o Armazenamento de Tabelas do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Listar recursos do Armazenamento do Azure no C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Referência da biblioteca de cliente do armazenamento do Azure para C++](https://azure.github.io/azure-storage-cpp)
- [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
