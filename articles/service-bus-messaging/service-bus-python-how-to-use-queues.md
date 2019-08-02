---
title: Como usar as filas de Barramento de Serviço do Azure com Python | Microsoft Docs
description: Saiba como usar as filas do barramento de serviço do Azure do Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: fa3aedf138564fedafe555adfbaf6c56efc1813e
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360848"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Como usar filas do Barramento de Serviço com Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Neste tutorial, você aprenderá a criar aplicativos Python para enviar e receber mensagens de uma fila do barramento de serviço. 

## <a name="prerequisites"></a>Pré-requisitos
1. Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Você pode ativar os [benefícios](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) de assinante do MSDN ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga as etapas no artigo [usar portal do Azure para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) .
    1. Leia a **visão geral** rápida das **filas**do barramento de serviço. 
    2. Crie um **namespace**do barramento de serviço. 
    3. Obter a **cadeia de conexão**. 

        > [!NOTE]
        > Você criará uma **fila** no namespace do barramento de serviço usando o Python neste tutorial. 
1. Instale o Python ou o [pacote de barramento de serviço do Python Azure][Python Azure Service Bus package], consulte o [Guia de instalação do Python](../python-how-to-install.md). Consulte a documentação completa do SDK do Python do barramento de serviço [aqui](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="create-a-queue"></a>Criar uma fila
O objeto **ServiceBusClient** permite que você trabalhe com filas. Adicione o seguinte código próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Barramento de Serviço de forma programática:

```python
from azure.servicebus import ServiceBusClient
```

O código a seguir cria um objeto **ServiceBusClient** . Substitua `<CONNECTION STRING>` pela ConnectionString do ServiceBus.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Os valores para o nome e o valor da chave SAS podem ser encontrados no [portal do Azure][Azure portal] informações de conexão ou no painel **Propriedades** do Visual Studio ao selecionar o namespace do barramento de serviço no Gerenciador de servidores (conforme mostrado na seção anterior).

```python
sb_client.create_queue("taskqueue")
```

O método `create_queue` também dá suporte para opções adicionais, que permitem a substituição de configurações padrão da fila, como a vida útil (TTL) da mensagem ou o tamanho máximo da fila. O exemplo a seguir define o tamanho máximo da fila como 5 GB e o valor de TTL como um minuto:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

Para obter mais informações, consulte a [documentação do Python do barramento de serviço do Azure](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Enviar mensagens a uma fila
Para enviar uma mensagem para uma fila do barramento de serviço, seu aplicativo `send` chama o método `ServiceBusClient` no objeto.

O exemplo a seguir demonstra como enviar uma mensagem de teste à fila chamada `taskqueue` usando `send_queue_message`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

As filas do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB. Para saber mais sobre cotas, consulte [Cotas do Barramento de Serviço][Service Bus quotas].

Para obter mais informações, consulte a [documentação do Python do barramento de serviço do Azure](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
As mensagens são recebidas de uma fila `get_receiver` usando o método `ServiceBusService` no objeto:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Para obter mais informações, consulte a [documentação do Python do barramento de serviço do Azure](/python/api/overview/azure/servicebus?view=azure-python).


As mensagens são excluídas da fila conforme são lidas quando o parâmetro `peek_lock` é definido como **False**. Você pode ler (espiar) e bloquear a mensagem sem excluí-la da fila ao definir o parâmetro `peek_lock` como **True**.

O comportamento da leitura e da exclusão da mensagem como parte da operação de recebimento é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo possa tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

Se o parâmetro `peek_lock` estiver definido como **True**, o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando o método **delete** no objeto **Message**. O método **delete** marcará a mensagem como tendo sido consumida e a removerá da fila.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis
O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não conseguir processar a mensagem por algum motivo, ele chamará o método **unlock** no objeto **Message**. Isso fará com que o Service Bus desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não conseguir processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método **delete** seja chamado, a mensagem será fornecida novamente ao aplicativo quando ele for reiniciado. Isso é frequentemente chamado **de processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade **MessageId** da mensagem, que permanecerá constante nas tentativas da entrega.

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas
Agora que você já aprendeu sobre as noções básicas das filas do Barramento de Serviço, confira estes artigo para saber mais.

* [Filas, tópicos e assinaturas][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

