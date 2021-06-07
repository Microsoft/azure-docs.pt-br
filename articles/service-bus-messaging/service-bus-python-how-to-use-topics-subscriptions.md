---
title: Usar tópicos e assinaturas do Barramento de Serviço do Azure com o pacote azure-servicebus versão 7.0.0 do Python
description: Este artigo mostra como usar o Python para enviar mensagens a um tópico e receber mensagens da assinatura.
documentationcenter: python
author: spelluru
ms.author: spelluru
ms.date: 11/18/2020
ms.topic: quickstart
ms.devlang: python
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 49e80e277c6df5372341293861d5bda0580f3e8c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537166"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Enviar mensagens para um tópico do Barramento de Serviço do Azure e receber mensagens de assinaturas para o tópico (Python)
Este artigo mostra como usar o Python para enviar mensagens a um tópico do Barramento de Serviço e receber mensagens de uma assinatura para o tópico. 

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure. Ative seus [benefícios de assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Siga as etapas no [Início Rápido: Usar o portal do Azure para criar um tópico do Barramento de Serviço e assinaturas para o tópico](service-bus-quickstart-topics-subscriptions-portal.md). Anote a cadeia de conexão, o nome do tópico e o nome de uma assinatura. Você usará apenas uma assinatura neste guia de início rápido. 
- Python 2.7 ou superior, com o pacote [SDK do Azure para Python][pacote do Azure para Python] instalado. Para obter mais informações, consulte o [Guia de instalação do Python](/azure/developer/python/azure-sdk-install).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

1. Adicione a instrução de importação a seguir. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Adicione as constantes a seguir. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    TOPIC_NAME = "<TOPIC NAME>"
    SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"
    ```
    
    > [!IMPORTANT]
    > - Substitua `<NAMESPACE CONNECTION STRING>` pela cadeia de conexão do namespace.
    > - Substitua `<TOPIC NAME>` pelo nome do tópico.
    > - Substitua `<SUBSCRIPTION NAME>` pelo nome da assinatura para o tópico. 
3. Adicione um método para enviar uma mensagem individual.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    O remetente é um objeto que funciona como um cliente para o tópico criado. Você o criará posteriormente e o enviará como um argumento para essa função. 
4. Adicione um método para enviar uma lista de mensagens.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. Adicione um método para enviar um lote de mensagens.

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Crie um cliente do Barramento de Serviço e, depois, um objeto de remetente do tópico para enviar mensagens.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Topic Sender object to send messages to the topic
        sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
Adicione o código a seguir após a instrução print. Esse código recebe continuamente novas mensagens até que não receba nenhuma mensagem nova por 5 (`max_wait_time`) segundos. 

```python
with servicebus_client:
    # get the Subscription Receiver object for the subscription    
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the subscription
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Código completo

```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
TOPIC_NAME = "<TOPIC NAME>"
SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>Executar o aplicativo
Ao executar o aplicativo, você verá a seguinte saída: 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

No portal do Azure, acesse o namespace do Barramento de Serviço. Na página **Visão geral**, confirme se as contagens de mensagens de **entrada** e **saída** são 16. Se as contagens não forem exibidas, atualize a página depois de aguardar alguns minutos. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Contagem de mensagens de entrada e saída":::

Selecione o tópico no painel inferior para ver a página **Tópico do Barramento de Serviço** do tópico. Nessa página, você verá três mensagens de entrada e três mensagens de saída no gráfico **Mensagens**. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Mensagens de entrada e saída":::

Nessa página, se você selecionar uma assinatura, chegará à página **Assinatura do Barramento de Serviço**. Você poderá ver a contagem de mensagens ativas, a contagem de mensagens mortas, entre outros nessa página. Neste exemplo, todas as mensagens foram recebidas e, portanto, a contagem de mensagens ativas é zero. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Contagem de mensagens ativas":::

Se você comentar o código de recebimento, verá a contagem de mensagens ativas como 16. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="Contagem de mensagens ativas – sem recebimento":::

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes exemplos e a documentação: 

- [Biblioteca de clientes do Barramento de Serviço do Azure para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Exemplos](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - A pasta **sync_samples** traz exemplos que mostram como interagir com o Barramento de Serviço de maneira síncrona. Neste guia de início rápido, você usou esse método. 
    - A pasta **async_samples** traz exemplos que mostram como interagir com o Barramento de Serviço de maneira assíncrona. 
- [Documentação de referência de azure-servicebus](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)
