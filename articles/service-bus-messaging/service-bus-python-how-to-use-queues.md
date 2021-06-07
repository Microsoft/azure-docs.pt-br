---
title: Usar filas do Barramento de Serviço do Azure com o pacote azure-servicebus versão 7.0.0 do Python
description: Este artigo mostra como usar o Python para enviar e receber mensagens em filas do Barramento de Serviço do Azure.
documentationcenter: python
author: spelluru
ms.author: spelluru
ms.date: 11/18/2020
ms.topic: quickstart
ms.devlang: python
ms.custom:
- seo-python-october2019
- devx-track-python
- mode-api
ms.openlocfilehash: e982b3f36dbfe2144170d53d07473201abb014ef
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534970"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>Enviar e receber mensagens em filas do Barramento de Serviço do Azure (Python)
Este artigo mostra como usar o Python para enviar e receber mensagens em filas do Barramento de Serviço do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure. Ative seus [benefícios de assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Caso você não tenha uma fila para trabalhar, siga as etapas do artigo [Usar o portal do Azure para criar uma fila do Barramento de Serviço](service-bus-quickstart-portal.md) para criar uma fila. Anote a **cadeia de conexão** do namespace do Barramento de Serviço e o nome da **fila** que você criou.
- Python 2.7 ou superior, com o pacote do [Barramento de Serviço do Azure para Python](https://pypi.python.org/pypi/azure-servicebus) instalado. Para obter mais informações, consulte o [Guia de instalação do Python](/azure/developer/python/azure-sdk-install). 

## <a name="send-messages-to-a-queue"></a>Enviar mensagens a uma fila

1. Adicione a instrução de importação a seguir. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Adicione as constantes a seguir. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    QUEUE_NAME = "<QUEUE NAME>"
    ```

    > [!IMPORTANT]
    > - Substitua `<NAMESPACE CONNECTION STRING>` pela cadeia de conexão para o namespace do Barramento de Serviço.
    > - Substitua `<QUEUE NAME>` pelo nome da fila. 
3. Adicione um método para enviar uma mensagem individual.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    O remetente é um objeto que funciona como um cliente para a fila criada. Você o criará posteriormente e o enviará como um argumento para essa função. 
4. Adicione um método para enviar uma lista de mensagens.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
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
        # send the batch of messages to the queue
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Crie um cliente do Barramento de Serviço e, depois, um objeto de remetente da fila para enviar mensagens.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Queue Sender object to send messages to the queue
        sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
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
 
## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
Adicione o código a seguir após a instrução print. Esse código recebe continuamente novas mensagens até que não receba nenhuma mensagem nova por 5 (`max_wait_time`) segundos. 

```python
with servicebus_client:
    # get the Queue Receiver object for the queue
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the queue
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Código completo

```python
# import os
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
QUEUE_NAME = "<QUEUE NAME>"

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
    sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
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

Selecione a fila nesta página **Visão geral** para acessar a página **Fila do Barramento de Serviço**. Você também poderá ver a contagem de mensagens de **entrada** e **saída** nessa página. Você também verá outras informações, como o **tamanho atual** da fila e a **contagem de mensagens ativas**. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="Detalhes da fila":::


## <a name="next-steps"></a>Próximas etapas
Confira os seguintes exemplos e a documentação: 

- [Biblioteca de clientes do Barramento de Serviço do Azure para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Exemplos](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - A pasta **sync_samples** traz exemplos que mostram como interagir com o Barramento de Serviço de maneira síncrona. Neste guia de início rápido, você usou esse método. 
    - A pasta **async_samples** traz exemplos que mostram como interagir com o Barramento de Serviço de maneira assíncrona. 
- [Documentação de referência de azure-servicebus](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)
