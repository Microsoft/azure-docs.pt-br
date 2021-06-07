---
title: Como usar filas do Barramento de Serviço do Azure com o PHP
description: Neste tutorial, você aprenderá a criar aplicativos PHP para enviar e receber mensagens em uma fila do Barramento de Serviço.
services: service-bus-messaging
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: a7e0d1fa321f1b7c1295b5a640fe78b46adf1c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85341126"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Guia de início rápido: como usar filas do Barramento de Serviço com o PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Neste tutorial, você aprenderá a criar aplicativos PHP para enviar e receber mensagens em uma fila do Barramento de Serviço. 

## <a name="prerequisites"></a>Pré-requisitos
1. Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Ative seus [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Caso você não tenha uma fila para trabalhar, siga as etapas do artigo [Usar o portal do Azure para criar uma fila do Barramento de Serviço](service-bus-quickstart-portal.md) para criar uma fila.
    1. Leia a **visão geral** rápida das **filas** do Barramento de Serviço. 
    2. Crie um **namespace** do Barramento de Serviço. 
    3. Obtenha a **cadeia de conexão**. 

        > [!NOTE]
        > Você criará uma **fila** no namespace do Barramento de Serviço usando o PHP neste tutorial. 
3. [SDK do Azure para PHP](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>Criar um aplicativo PHP
O único requisito para a criação de um aplicativo PHP que acessa o serviço Blob do Azure é a referência de classes no [SDK do Azure para PHP](https://github.com/Azure/azure-sdk-for-php) em seu código. Você pode usar quaisquer ferramentas de desenvolvimento para criar seu aplicativo, ou o Bloco de Notas.

> [!NOTE]
> A instalação do PHP também deve ter a [extensão OpenSSL](https://php.net/openssl) instalada e habilitada.

Neste guia, você usará os recursos de serviço, que podem ser chamados em um aplicativo PHP localmente ou no código em execução em uma função Web do Azure, uma função de trabalho ou um site.

## <a name="get-the-azure-client-libraries"></a>Obter as bibliotecas de cliente do Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o Barramento de serviço
Para usar as APIs da fila do Barramento de Serviço, faça o seguinte:

1. Faça referência ao arquivo do carregador automático usando a instrução [require_once][require_once].
2. Fazer referência a qualquer classe que você possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe `ServicesBuilder`.

> [!NOTE]
> Este exemplo (e outros exemplos neste artigo) pressupõe que você instalou as Bibliotecas de Cliente PHP para Azure por meio do Compositor. Se você instalou as bibliotecas manualmente ou como um pacote PEAR, será necessário fazer referência ao arquivo de carregador automático **WindowsAzure.php**.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos abaixo, a instrução `require_once` será mostrada sempre, mas somente as classes necessárias para executar o exemplo serão referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão do Barramento de Serviço
Para criar uma instância do cliente do Barramento de Serviço, você deve ter primeiro uma cadeia de conexão válida neste formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Em que `Endpoint` geralmente está no formato `[yourNamespace].servicebus.windows.net`.

Para criar um cliente de serviço do Azure, é necessário usar a classe `ServicesBuilder`. Você pode:

* Passar a cadeia de conexão diretamente para ele.
* Usar **CloudConfigurationManager (CCM)** para verificar várias fontes externas da cadeia de conexão:
  * Por padrão, ele vem com suporte para uma origem externa: variáveis de ambiente
  * você pode adicionar novas origens ao estender a classe `ConnectionStringSource`

Para os exemplos descritos aqui, a cadeia de conexão é passada diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Criar uma fila
Você pode executar operações de gerenciamento de filas do Barramento de Serviço por meio da classe `ServiceBusRestProxy`. Um objeto `ServiceBusRestProxy` é construído com o método de fábrica `ServicesBuilder::createServiceBusService` com uma cadeia de conexão apropriada que encapsula as permissões de token para gerenciá-lo.

O exemplo a seguir mostra como instanciar um `ServiceBusRestProxy` e chamar um `ServiceBusRestProxy->createQueue` para criar uma fila denominada `myqueue` dentro de um namespace de serviço `MySBNamespace`:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Você pode usar o método `listQueues` em objetos `ServiceBusRestProxy` para verificar se já existe uma fila com um nome especificado em um namespace.
> 
> 

## <a name="send-messages-to-a-queue"></a>Enviar mensagens a uma fila
Para enviar uma mensagem para uma fila do Barramento de Serviço, seu aplicativo chamará o método `ServiceBusRestProxy->sendQueueMessage`. O código abaixo demonstra como enviar uma mensagem à fila `myqueue` que criamos acima no namespace de serviço `MySBNamespace`.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

As mensagens enviadas (e recebidas) nas filas do Barramento de Serviço são instâncias da classe [BrokeredMessage][BrokeredMessage]. Os objetos [BrokeredMessage][BrokeredMessage] têm um conjunto de propriedades e métodos padrão que são usados para armazenar as propriedades personalizadas específicas do aplicativo e um corpo de dados de aplicativos arbitrários.

As filas do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. Este limite superior do tamanho da fila é 5 GB.

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

A melhor maneira de receber mensagens de uma fila é usar um método `ServiceBusRestProxy->receiveQueueMessage`. As mensagens podem ser recebidas de dois modos diferentes: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) e [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** é o padrão.

Ao usar o modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode), o recebimento é uma operação única, isto é, quando o Barramento de Serviço recebe uma solicitação de leitura de uma mensagem em uma fila, ele marca a mensagem como sendo consumida e a retorna para o aplicativo. O modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) padrão, o recebimento de uma mensagem se torna uma operação de dois estágios, o que possibilita o suporte a aplicativos que não podem tolerar ausência de mensagens. Quando o Barramento de Serviço recebe uma solicitação, ele localiza a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e, em seguida, retorna-a para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento encaminhando a mensagem recebida para `ServiceBusRestProxy->deleteMessage`. Quando o Barramento de Serviço vê a chamada `deleteMessage`, ele marca a mensagem como tendo sido consumida e a remove da fila.

O exemplo a seguir mostra como receber e processar uma mensagem usando o modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) (o modo padrão).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele poderá chamar o método `unlockMessage` na mensagem recebida (em vez do método `deleteMessage`). Isso fará com que o Service Bus desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não conseguir processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem, mas antes que a solicitação `deleteMessage` seja gerada, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado de *Processamento de pelo menos uma vez*, ou seja, cada mensagem será processada pelo menos uma vez, mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não puder tolerar o processamento duplicado, é recomendável adicionar lógica extra ao aplicativo para tratar a entrega de mensagens duplicadas. Isso geralmente é realizado usando o método `getMessageId` da mensagem, que permanecerá constante nas tentativas da entrega.

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu as noções básicas sobre as filas do Barramento de Serviço, veja [Filas, tópicos e assinaturas][Queues, topics, and subscriptions] para saber mais.

Para saber mais, visite também o [Centro de Desenvolvedores em PHP](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


