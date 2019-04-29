---
title: Como usar as filas do Barramento de Serviço do Azure com Ruby | Microsoft Docs
description: Aprenda a usar as filas do barramento de serviço no Azure. Exemplos de códigos escritos em Ruby.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 6c42fbffd0b4569a9b04dede94061e716c48ecf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474582"
---
# <a name="how-to-use-service-bus-queues-with-ruby"></a>Como usar filas do Barramento de Serviço com Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Neste tutorial, você aprenderá como criar aplicativos Ruby para enviar e receber mensagens de uma fila do barramento de serviço. Os exemplos são gravados no Ruby e usam a gema do Azure.

## <a name="prerequisites"></a>Pré-requisitos
1. Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Você pode ativar sua [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou se inscrever para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga as etapas na [portal do Azure de uso para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) artigo.
    1. Leia o quick **visão geral** do barramento de serviço **filas**. 
    2. Criar um barramento de serviço **namespace**. 
    3. Obter o **cadeia de caracteres de conexão**. 

        > [!NOTE]
        > Você aprenderá a criar uma **fila** no namespace do barramento de serviço usando o Ruby neste tutorial. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Como criar uma fila
O objeto**Azure::ServiceBusService** permite que você trabalhe com filas. Para criar uma fila, use o método `create_queue()`. O exemplo a seguir cria uma fila ou imprime os erros, se houver algum.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Você também pode passar em um objeto **Azure::ServiceBus::Queue** com opções adicionais, que permite a substituição de configurações padrão da fila, assim como a vida útil da mensagem ou o tamanho máximo da fila. O exemplo a seguir mostra como definir o tamanho máximo da fila como 5 GB e a vida útil como 1 minuto:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Como enviar mensagens para uma fila
Para enviar uma mensagem a uma fila do Barramento de Serviço, o aplicativo chama o método `send_queue_message()` no objeto **Azure::ServiceBusService**. Mensagens enviadas para (e recebidas de) as filas do Barramento de Serviço são objetos **Azure::ServiceBus::BrokeredMessage** e têm um conjunto de propriedades padrão (como `label` e `time_to_live`), um dicionário que é usado para manter propriedades personalizadas específicas ao aplicativo e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem, passando um valor da cadeia de caracteres como a mensagem, e qualquer propriedade padrão necessária será preenchida com valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste à fila chamada `test-queue` usando `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

As filas do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Como receber mensagens de uma fila
As mensagens são recebidas de uma fila usando o método `receive_queue_message()` no objeto **Azure::ServiceBusService**. Por padrão, as mensagens são lidas e bloqueadas sem excluí-las da fila. Entretanto, você pode excluir mensagens da fila à medida que são lidas ao configurar a opção `:peek_lock` como **false**.

O comportamento padrão faz com que a leitura e a exclusão se dividam em uma operação de dois estágios, o que também torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma segura para um processamento futuro), ele conclui o segundo estágio do processo de recebimento, chamando o método `delete_queue_message()` e fornecendo a mensagem a ser excluída como um parâmetro. O método `delete_queue_message()` marcará a mensagem como tendo sido consumida e a removerá da fila.

Se o `:peek_lock` parâmetro é definido como **falso**, ler e excluir a mensagem se torna o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço marca a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

O exemplo a seguir demonstra como receber e processar mensagens usando `receive_queue_message()`. O exemplo primeiro recebe e exclui uma mensagem usando `:peek_lock` definido como **false**, então recebe outra mensagem e exclui a mensagem usando `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis
O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método `unlock_queue_message()` no objeto **Azure::ServiceBusService**. Essa chamada fará com que o Barramento de Serviço desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se houver falha no processamento da mensagem pelo aplicativo da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método `delete_queue_message()` seja chamado, a mensagem será fornecida novamente ao aplicativo quando ele for reiniciado. Esse processo é frequentemente chamado de *Processamento de pelo menos uma vez*, ou seja, cada mensagem será processada pelo menos uma vez, mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é realizado usando a propriedade `message_id` da mensagem, que permanecerá constante nas tentativas da entrega.

## <a name="next-steps"></a>Próximas etapas
Agora que você já sabe as noções básicas das filas de Barramento de Serviço, siga estes links para saber mais.

* Visão geral de [filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md).
* Visite o repositório [SDK do Azure para o Ruby](https://github.com/Azure/azure-sdk-for-ruby) no GitHub.

Para fazer uma comparação entre as filas de Barramento de Serviço do Azure discutidas nesse artigo e as filas do Azure discutidas no artigo [Como usar o armazenamento de Filas no Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md), confira [Filas do Azure e filas de Barramento de Serviço do Azure - Comparadas e Contrastadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

