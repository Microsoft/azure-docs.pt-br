---
title: Como usar os tópicos de Barramento de Serviço (Ruby) | Microsoft Docs
description: Aprenda a usar assinaturas e tópicos do Barramento de Serviço no Azure. Exemplos de código são escritos para aplicativos Ruby.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: b2a05a4695ee80873a2d7464c0a1cf4d46ed30f5
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543644"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Como usar tópicos e assinaturas do Barramento de Serviço com Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar tópicos do Barramento de Serviço e assinaturas de aplicativos Ruby. Os cenários abordados incluem:

- Criar tópicos e assinaturas 
- Criar filtros de assinatura 
- Enviar mensagens para um tópico 
- Receber mensagens de uma assinatura
- Excluir tópicos e assinaturas


## <a name="prerequisites"></a>Pré-requisitos
1. Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Você pode ativar sua [benefícios de assinante do MSDN ou Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou se inscreva em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga as etapas no [guia de início rápido: Use o portal do Azure para criar um tópico do barramento de serviço e assinaturas do tópico](service-bus-quickstart-topics-subscriptions-portal.md) para criar um barramento de serviço **namespace** e obtenha o **cadeia de caracteres de conexão**. 

    > [!NOTE]
    > Você aprenderá a criar uma **tópico** e uma **assinatura** para o tópico usando **Ruby** neste início rápido. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Criar um tópico
O objeto **Azure::ServiceBusService** permite que você trabalhe com tópicos. O código a seguir cria um objeto **Azure::ServiceBusService**. Para criar um tópico, use o método `create_topic()`. O exemplo a seguir cria um tópico ou imprime os erros, se houver algum.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Você também pode informar um objeto **Azure::ServiceBus::Topic** com opções adicionais, que permitem a substituição de configurações padrão do tópico como a vida útil da mensagem ou o tamanho máximo da fila. O exemplo a seguir mostra a configuração do tamanho máximo da fila para 5 GB e a vida útil para 1 minuto:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Criar assinaturas
As assinaturas do tópico também são criadas com o objeto **Azure::ServiceBusService**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens entregues à fila virtual da assinatura.

Por padrão, as assinaturas são persistentes. Elas continuarão existindo até que elas ou o tópico ao qual estão associadas sejam excluídos. Se seu aplicativo contiver a lógica para criar uma assinatura, ele deve primeiro verificar se a assinatura já existe usando o método getSubscription.

Você pode ter as assinaturas são excluídas automaticamente definindo a [AutoDeleteOnIdle propriedade](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)
Se nenhum filtro for especificado quando uma nova assinatura for criada, o filtro **MatchAll** (padrão) será utilizado. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada "all-messages" e usa o filtro padrão **MatchAll**.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Criar assinaturas com os filtros
Você também pode definir filtros que permitem especificar quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica.

O tipo de filtro mais flexível com suporte pelas assinaturas é o **Azure::ServiceBus::SqlFilter**, que implementa um subconjunto de SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [SqlFilter](service-bus-messaging-sql-filter.md).

É possível adicionar filtros a uma assinatura usando o método `create_rule()` do objeto **Azure::ServiceBusService**. Este método permite que você adicione novos filtros a uma assinatura existente.

Como o filtro padrão é aplicado automaticamente em todas as assinaturas novas, você deve primeiro remover o filtro padrão; caso contrário, o filtro **MatchAll** substituirá todos os outros filtros que você possa especificar. Você pode remover a regra padrão usando o método `delete_rule()` do objeto **Azure::ServiceBusService**.

O exemplo a seguir cria uma assinatura chamada "high-messages" com um **Azure::ServiceBus::SqlFilter** que seleciona apenas mensagens que têm uma propriedade `message_number` personalizada maior que 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Da mesma forma, o seguinte exemplo cria uma assinatura denominada `low-messages` com um **Azure::ServiceBus::SqlFilter** que seleciona apenas mensagens que têm uma propriedade `message_number` menor ou igual a 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Quando uma mensagem é agora enviada para `test-topic`, sempre será entregue aos destinatários inscritos na assinatura do tópico `all-messages` e entregue de forma seletiva aos destinatários inscritos nas assinaturas do tópico `high-messages` e `low-messages` (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem a um tópico do Barramento de Serviço, o aplicativo deve usar o método `send_topic_message()` do objeto **Azure::ServiceBusService**. As mensagens enviadas aos tópicos do Barramento de Serviço são instâncias dos objetos **Azure::ServiceBus::BrokeredMessage**. Os objetos **Azure::ServiceBus::BrokeredMessage** têm um conjunto de propriedades padrão (como `label` e `time_to_live`), um dicionário que é usado para manter propriedades personalizadas específicas ao aplicativo e um corpo de dados da cadeia de caracteres. Um aplicativo pode definir o corpo da mensagem, passando um valor de cadeia de caracteres para o método `send_topic_message()` e todas as propriedades padrão necessárias são preenchidas por valores padrão.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para `test-topic`. O valor da propriedade personalizada `message_number` de cada mensagem varia de acordo com a iteração do loop (determina qual assinatura o recebe):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Os tópicos do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há nenhum limite no número de mensagens mantidas em um tópico, mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
As mensagens são recebidas de uma assinatura usando o método `receive_subscription_message()` no objeto **Azure::ServiceBusService**. Por padrão, as mensagens são lidas (pico) e bloqueadas sem excluí-las da assinatura. Você pode ler e excluir a mensagem da assinatura definindo a opção `peek_lock` como **false**.

O comportamento padrão faz com que a leitura e a exclusão se dividam em uma operação de dois estágios, o que também torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma segura para um processamento futuro), ele conclui o segundo estágio do processo de recebimento, chamando o método `delete_subscription_message()` e fornecendo a mensagem a ser excluída como um parâmetro. O método `delete_subscription_message()` marca a mensagem como sendo consumida e a remove da assinatura.

Se o parâmetro `:peek_lock` estiver definido como **false**, a leitura e a exclusão da mensagem se tornarão o modelo mais simples e funcionarão melhor em cenários nos quais o aplicativo poderá tolerar o não processamento de uma mensagem quando ocorrer uma falha. Considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço marcou a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo `receive_subscription_message()` padrão. O exemplo primeiro recebe e exclui uma mensagem da assinatura `low-messages` usando `:peek_lock` definido como **false**, então recebe outra mensagem do `high-messages` e exclui a mensagem usando `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis
O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método `unlock_subscription_message()` no objeto **Azure::ServiceBusService**. Isso faz com que o Barramento de Serviço desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura e, se houver falha no processamento da mensagem pelo aplicativo antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e vai disponibilizá-la para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método `delete_subscription_message()` seja chamado, a mensagem será fornecida novamente ao aplicativo quando ele for reiniciado. Isso é frequentemente chamado de *processamento de pelo menos uma vez*, ou seja, cada mensagem será processada pelo menos uma vez, mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Essa lógica normalmente acontece usando a propriedade `message_id` da mensagem, que permanecerá constante nas tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas
Tópicos e assinaturas são persistentes, a menos que o [AutoDeleteOnIdle propriedade](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) está definido. Eles podem ser excluídos por meio de [portal do Azure][Azure portal] ou programaticamente. O exemplo a seguir demonstra como excluir o tópico denominado `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

A exclusão de um tópico também exclui todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir uma assinatura chamada `high-messages` do tópico `test-topic`:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas
Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais.

* Confira [Filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md).
* Referência da API para [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Visite o repositório [SDK do Azure para o Ruby](https://github.com/Azure/azure-sdk-for-ruby) no GitHub.

[Azure portal]: https://portal.azure.com
