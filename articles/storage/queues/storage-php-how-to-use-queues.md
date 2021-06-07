---
title: Como usar o armazenamento de fila do PHP-armazenamento do Azure
description: Saiba como usar o serviço de armazenamento de filas do Azure para criar e excluir filas e inserir, obter e excluir mensagens. As amostras são escritas em PHP.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 01/11/2018
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 69369d81892a10c390aa31a2c46f79fdfa41206d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592017"
---
# <a name="how-to-use-queue-storage-from-php"></a>Como usar o armazenamento de fila do PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

Este guia mostra como executar cenários comuns usando o serviço de armazenamento de filas do Azure. Os exemplos são gravados por meio de classes da [biblioteca de cliente de armazenamento do Azure para php](https://github.com/Azure/azure-storage-php). Os cenários abrangidos incluem inserir, exibir, obter e excluir mensagens da fila, bem como criar e excluir filas.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Criar um aplicativo PHP

O único requisito para a criação de um aplicativo PHP que acessa o armazenamento de filas do Azure é a referência de classes na [biblioteca de cliente de armazenamento do Azure para php](https://github.com/Azure/azure-storage-php) de dentro do seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

Neste guia, você usa os recursos do serviço de armazenamento de fila que podem ser chamados em um aplicativo PHP localmente ou no código em execução em um aplicativo Web no Azure.

## <a name="get-the-azure-client-libraries"></a>Obter as bibliotecas de cliente do Azure

### <a name="install-via-composer"></a>Instalar por meio do Composer

1. Crie um arquivo chamado `composer.json` na raiz do seu projeto e adicione o seguinte código a ele:

    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```

2. Baixe [`composer.phar`](https://getcomposer.org/composer.phar) na raiz do projeto.

3. Abra um prompt de comando e execute o seguinte comando na raiz do projeto:

    ```console
    php composer.phar install
    ```

Como alternativa, vá para a [biblioteca de cliente php do armazenamento do Azure](https://github.com/Azure/azure-storage-php) no GitHub para clonar o código-fonte.

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar seu aplicativo para acessar o Armazenamento de Filas

Para usar as APIs para o armazenamento de filas do Azure, você precisa:

1. Referencie o arquivo de carregador automático usando a [`require_once`](https://www.php.net/manual/en/function.require-once.php) instrução.
2. Fazer referência a qualquer classe que possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe `QueueRestProxy`.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

Nos exemplos a seguir, a `require_once` instrução é mostrada sempre, mas somente as classes necessárias para executar o exemplo são referenciadas.

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma conexão do Armazenamento do Azure

Para criar uma instância de um cliente de armazenamento de filas do Azure, você deve primeiro ter uma cadeia de conexão válida. O formato para a cadeia de conexão de armazenamento de fila é o seguinte.

Para acessar um serviço ao vivo:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Para acessar o armazenamento do emulador:

```php
UseDevelopmentStorage=true
```

Para criar um cliente de armazenamento de filas do Azure, você precisa usar a `QueueRestProxy` classe. É possível usar qualquer uma das técnicas a seguir:

- Passar a cadeia de conexão diretamente para ele.
- Use variáveis de ambiente em seu aplicativo Web para armazenar a cadeia de conexão. Consulte o documento [Configurações do aplicativo web do Azure](../../app-service/configure-common.md) para configurar cadeias de conexão.

Para os exemplos descritos aqui, a cadeia de conexão é passada diretamente.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Criar uma fila

Um `QueueRestProxy` objeto permite que você crie uma fila usando o `CreateQueue` método. Ao criar uma fila, você pode definir opções na fila, mas fazer isso não é necessário. Este exemplo mostra como definir metadados em uma fila.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Você não deve depender de maiúsculas e minúsculas para as chaves de metadados. Todas as chaves são lidas do serviço em letras minúsculas.

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem a uma fila

Para adicionar uma mensagem a uma fila, use `QueueRestProxy->createMessage` . O método utiliza o nome da fila, o texto da mensagem e opções de mensagem (que são opcionais).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello, World");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Espiar a próxima mensagem

Você pode inspecionar uma ou mais mensagens na frente de uma fila sem removê-las da fila chamando `QueueRestProxy->peekMessages` . Por padrão, o `peekMessage` método retorna uma única mensagem, mas você pode alterar esse valor usando o `PeekMessagesOptions->setNumberOfMessages` método.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Remover a próxima mensagem da fila

Seu código remove uma mensagem de uma fila em duas etapas. Primeiro, você chama `QueueRestProxy->listMessages` , que torna a mensagem invisível para qualquer outro código que está lendo da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. (Se a mensagem não for excluída nesse período de tempo, ela se tornará visível na fila novamente.) Para concluir a remoção da mensagem da fila, você deve chamar `QueueRestProxy->deleteMessage` . Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama `deleteMessage` logo após a mensagem ser processada.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila chamando `QueueRestProxy->updateMessage` . Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender mais 60 segundos. Isso salva o estado do trabalho que está associado à mensagem e dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você poderia usar essa técnica para rastrear fluxos de trabalho com várias etapas em mensagens de fila, sem precisar começar desde o início se uma etapa de processamento falhar devido a uma falha de hardware ou de software. Normalmente, você mantém uma contagem de repetições e, se a mensagem for repetida mais de *n* vezes, você a exclui. Isso protege contra uma mensagem que dispara um erro do aplicativo sempre que for processada.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de visibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O exemplo de código a seguir usa o `getMessages` método para obter 16 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um `for` loop. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O `QueueRestProxy->getQueueMetadata` método recupera metadados sobre a fila. Chamar o `getApproximateMessageCount` método no objeto retornado fornece uma contagem de quantas mensagens estão em uma fila. A contagem é aproximada apenas porque as mensagens podem ser adicionadas ou removidas após o armazenamento de filas responder à sua solicitação.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Excluir uma fila

Para excluir uma fila e todas as mensagens nela, chame o `QueueRestProxy->deleteQueue` método.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de filas do Azure, siga estes links para saber mais sobre tarefas de armazenamento mais complexas:

- Visite a [referência de API para a biblioteca de cliente php do armazenamento do Azure](https://azure.github.io/azure-storage-php/)
- Consulte o [exemplo de fila avançada](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Para obter mais informações, consulte a [central de desenvolvedores do PHP](https://azure.microsoft.com/develop/php/).
