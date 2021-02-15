---
title: Como usar os tópicos do Barramento de Serviço do Azure com o PHP
description: Neste tutorial, você aprenderá a usar os tópicos e as assinaturas do Barramento de Serviço do Azure em um aplicativo PHP.
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 706f523fdfb3c710bb16b048cfc68ce98875adb1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "88066195"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Início Rápido: Como usar tópicos e assinaturas do Barramento de Serviço com PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo mostra como usar os tópicos e as assinaturas do Barramento de Serviço. As amostras são escritas em PHP e usam o [SDK do Azure para PHP](https://github.com/Azure/azure-sdk-for-php). Os cenários abordados incluem:

- Criar tópicos e assinaturas 
- Criar filtros de assinatura 
- Enviar mensagens para um tópico 
- Receber mensagens de uma assinatura
- Excluir tópicos e assinaturas

## <a name="prerequisites"></a>Pré-requisitos
1. Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Ative seus [benefícios de assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga as etapas no [Início Rápido: Usar o portal do Azure para criar um tópico e assinaturas do Barramento de Serviço para o tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um **namespace** do Barramento de Serviço e obter a **cadeia de conexão**.

    > [!NOTE]
    > Você criará um **tópico** e uma **assinatura** para o tópico usando o **PHP** neste início rápido. 

## <a name="create-a-php-application"></a>Criar um aplicativo PHP
O único requisito para a criação de um aplicativo PHP que acessa o serviço Blob do Azure é a referência de classes no [SDK do Azure para PHP](https://github.com/Azure/azure-sdk-for-php) em seu código. Você pode usar quaisquer ferramentas de desenvolvimento para criar seu aplicativo, ou o Bloco de Notas.

> [!NOTE]
> A instalação do PHP também deve ter a [extensão OpenSSL](https://php.net/openssl) instalada e habilitada.
> 
> 

Este artigo descreve como usar os recursos de serviços que podem ser chamados em um aplicativo PHP localmente ou no código em execução em uma função web, uma função de trabalho ou um site do Azure.

## <a name="get-the-azure-client-libraries"></a>Obter as bibliotecas de cliente do Azure

### <a name="install-via-composer"></a>Instalar por meio do Composer
1. Crie um arquivo chamado **composer.json** na raiz do seu projeto e adicione o seguinte código a ele:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Baixe o **[composer.phar][composer-phar]** na raiz do projeto.
3. Abra um prompt de comando e execute o seguinte comando na raiz do projeto
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o Barramento de serviço
Para usar as APIs do barramento de serviço:

1. Faça referência ao arquivo do carregador automático usando a instrução [require_once][require-once].
2. Fazer referência a qualquer classe que você possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe **ServicesBuilder**.

> [!NOTE]
> Este exemplo (e outros exemplos neste artigo) pressupõe que você instalou as Bibliotecas de Cliente PHP para Azure por meio do Compositor. Se você instalou as bibliotecas manualmente ou como um pacote PEAR, será necessário fazer referência ao arquivo de carregador automático **WindowsAzure.php**.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos seguintes exemplos, a instrução `require_once` é sempre mostrada, mas somente as classes necessárias para executar o exemplo são referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma conexão do Barramento de Serviço
Para criar uma instância do cliente do Barramento de Serviço, você deve ter primeiro uma cadeia de conexão válida neste formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Em que `Endpoint` geralmente está no formato `https://[yourNamespace].servicebus.windows.net`.

Para criar um cliente de serviço do Azure, é necessário usar a classe `ServicesBuilder`. Você pode:

* Passar a cadeia de conexão diretamente para ele.
* Usar **CloudConfigurationManager (CCM)** para verificar várias fontes externas da cadeia de conexão:
  * Por padrão, ele vem com suporte para uma origem externa: variáveis de ambiente
  * Você pode adicionar novas origens estendendo a classe `ConnectionStringSource`.

Para os exemplos descritos aqui, a cadeia de conexão é passada diretamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Criar um tópico
Você pode executar operações de gerenciamento de tópicos do Barramento de Serviço por meio da classe `ServiceBusRestProxy` . Um objeto `ServiceBusRestProxy` é construído com o método de fábrica `ServicesBuilder::createServiceBusService` com uma cadeia de conexão apropriada que encapsula as permissões de token para gerenciá-lo.

O exemplo a seguir mostra como instanciar um `ServiceBusRestProxy` e chamar um `ServiceBusRestProxy->createTopic` para criar um tópico denominado `mytopic` dentro de um namespace `MySBNamespace`:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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
> Você pode usar o método `listTopics` em objetos `ServiceBusRestProxy` para verificar se já existe um tópico com um nome especificado em um namespace de serviço.
> 
> 

## <a name="create-a-subscription"></a>Criar uma assinatura
As assinaturas do tópico também são criadas com o método `ServiceBusRestProxy->createSubscription`. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para a fila virtual da assinatura.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)
Se nenhum filtro for especificado quando uma nova assinatura for criada, o filtro **MatchAll** (padrão) será utilizado. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada `mysubscription` e usa o filtro padrão **MatchAll**.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
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

### <a name="create-subscriptions-with-filters"></a>Criar assinaturas com os filtros
Você também pode configurar filtros que permitem especificar quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico. O tipo de filtro mais flexível compatível com as assinaturas é o [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para saber mais sobre SqlFilters, consulte [Propriedade SqlFilter.SqlExpression][sqlfilter].

> [!NOTE]
> Cada regra uma assinatura processa as mensagens de entrada independentemente, adicionando suas mensagens de resultado à assinatura. Além disso, cada nova assinatura tem um objeto **Regra** padrão com um filtro que adiciona todas as mensagens do tópico à assinatura. Para receber apenas as mensagens correspondentes ao filtro, você deve remover a regra padrão. Você pode remover a regra padrão usando o método `ServiceBusRestProxy->deleteRule`.
> 
> 

O exemplo a seguir cria uma assinatura denominada `HighMessages` com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade personalizada `MessageNumber` maior que 3. Confira [Enviar mensagens para um tópico](#send-messages-to-a-topic) para saber mais sobre como adicionar propriedades a mensagens.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Este código requer o uso de um namespace adicional: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Da mesma forma, o seguinte exemplo cria uma assinatura denominada `LowMessages` com um `SqlFilter` que seleciona apenas mensagens que têm uma propriedade `MessageNumber` menor ou igual a 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Agora, quando uma mensagem é enviada ao tópico `mytopic`, ela sempre será entregue aos destinatários inscritos na assinatura `mysubscription` e entregue de forma seletiva aos destinatários inscritos nas assinaturas do `HighMessages` e `LowMessages` (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem para um tópico do Barramento de Serviço, seu aplicativo chamará o método `ServiceBusRestProxy->sendTopicMessage`. O código abaixo demonstra como enviar uma mensagem ao tópico `mytopic` que criamos acima no namespace de serviço `MySBNamespace`.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
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

As mensagens enviadas aos tópicos de Barramento de Serviço são instâncias da classe [BrokeredMessage][BrokeredMessage]. Os objetos [BrokeredMessage][BrokeredMessage] têm um conjunto de propriedades e métodos padrão, além de propriedades que podem ser usadas para manter as propriedades personalizadas específicas do aplicativo. O exemplo a seguir mostra como enviar cinco mensagens de teste para o tópico `mytopic` criado anteriormente. O método `setProperty` é usado para adicionar uma propriedade personalizada (`MessageNumber`) a cada mensagem. O valor da propriedade `MessageNumber` varia em cada mensagem (ele pode ser usado para determinar quais assinaturas o receberão, conforme mostrado na seção [Criar uma assinatura](#create-a-subscription)):

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Os tópicos do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há nenhum limite no número de mensagens mantidas em um tópico, mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. Este limite superior do tamanho do tópico é 5 GB. Para saber mais sobre cotas, consulte [Cotas do Barramento de Serviço][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
A maneira mais fácil de receber mensagens de uma assinatura é usar um método `ServiceBusRestProxy->receiveSubscriptionMessage`. As mensagens podem ser recebidas em dois modos diferentes: [*ReceiveAndDelete* e *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** é o padrão.

Quando o modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) for usado, o recebimento será uma operação única, ou seja, quando o Barramento de Serviço receber uma solicitação de leitura de uma mensagem em uma assinatura, ele marcará a mensagem como sendo consumida e a retornará ao aplicativo. O modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) * é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço marcou a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) padrão, o recebimento de uma mensagem se torna uma operação de dois estágios, o que possibilita o suporte a aplicativos que não podem tolerar ausência de mensagens. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento encaminhando a mensagem recebida para `ServiceBusRestProxy->deleteMessage`. Quando o Barramento de Serviço vê a chamada `deleteMessage`, ele marca a mensagem como tendo sido consumida e a remove da fila.

O exemplo a seguir mostra como receber e processar uma mensagem usando o modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) (o modo padrão). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como: tratar falhas do aplicativo e mensagens ilegíveis
O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele poderá chamar o método `unlockMessage` na mensagem recebida (em vez do método `deleteMessage`). Isso fará com que o Barramento de Serviço desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo falhar ao processar a mensagem antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem, mas antes que a solicitação `deleteMessage` seja emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Esse tipo de processamento é frequentemente chamado de processamento de *pelo menos uma vez*, ou seja, cada mensagem será processada pelo menos uma vez, mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra aos aplicativos para tratar a entrega de mensagem duplicada. Isso geralmente é realizado usando o método `getMessageId` da mensagem, que permanecerá constante nas tentativas da entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas
Para excluir um tópico ou uma assinatura, use os métodos `ServiceBusRestProxy->deleteTopic` ou `ServiceBusRestProxy->deleteSubscripton`, respectivamente. A exclusão de um tópico também exclui todas as assinaturas registradas com o tópico.

O exemplo a seguir mostra como excluir um tópico denominado `mytopic` e suas assinaturas registradas.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
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

Ao usar o método `deleteSubscription`, você poderá excluir uma assinatura de forma independente:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, confira [Filas, tópicos e assinaturas][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md