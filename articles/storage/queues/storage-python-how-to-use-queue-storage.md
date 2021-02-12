---
title: Como usar o armazenamento de filas do Azure do Python – armazenamento do Azure
description: Saiba como usar o armazenamento de filas do Azure do Python para criar e excluir filas, e inserir, obter e excluir mensagens.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 01/19/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 82d18fd79b10a8500cfd9191f143438d69fda401
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600800"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Como usar o armazenamento de filas do Azure do Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Visão geral

Este artigo demonstra cenários comuns usando o serviço de armazenamento de filas do Azure. Os cenários abordados incluem inserção, inspeção, obtenção e exclusão de mensagens da fila. O código para criar e excluir filas também é abordado.

Os exemplos neste artigo são escritos em Python e usam a [biblioteca de cliente de armazenamento de filas do Azure para Python](https://github.com/Azure/Azure-SDK-for-Python/tree/master/sdk/storage/azure-storage-queue). Para obter mais informações sobre filas, consulte a seção [Próximas etapas](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Baixe e instale o SDK do Armazenamento do Azure para Python

O [SDK do armazenamento do Azure para Python](https://github.com/azure/azure-storage-python) requer Python v 2.7, v 3.3 ou posterior.

### <a name="install-via-pypi"></a>Instalar via PyPI

Para instalar por meio do Índice de Pacote do Python (PyPI), digite:

# <a name="python-v12"></a>[Python V12](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> Se você estiver atualizando do SDK do armazenamento do Azure para Python v 0.36 ou anterior, desinstale o SDK mais antigo usando `pip uninstall azure-storage` antes de instalar o pacote mais recente.

Para métodos de instalação alternativos, consulte [SDK do Azure para Python](https://github.com/Azure/Azure-SDK-for-Python).

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Configurar seu aplicativo para acessar o Armazenamento de Filas

# <a name="python-v12"></a>[Python V12](#tab/python)

O [`QueueClient`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient) objeto permite que você trabalhe com uma fila. Adicione o código a seguir próximo à parte superior de qualquer arquivo Python no qual você deseja acessar programaticamente uma fila do Azure:

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O [`QueueService`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true) objeto permite que você trabalhe com filas. O código a seguir cria um `QueueService` objeto. Adicione o código a seguir próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o armazenamento do Azure programaticamente:

```python
from azure.storage.queue import (
        QueueService,
        QueueMessageFormat
)

import os, uuid
```

---

O `os` pacote fornece suporte para recuperar uma variável de ambiente. O `uuid` pacote fornece suporte para gerar um identificador exclusivo para um nome de fila.

## <a name="create-a-queue"></a>Criar uma fila

A cadeia de conexão é recuperada da `AZURE_STORAGE_CONNECTION_STRING` variável de ambiente definida anteriormente.

# <a name="python-v12"></a>[Python V12](#tab/python)

O código a seguir cria um `QueueClient` objeto usando a cadeia de conexão de armazenamento.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O código a seguir cria um `QueueService` objeto usando a cadeia de conexão de armazenamento.

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

## <a name="insert-a-message-into-a-queue"></a>Inserir uma mensagem em uma fila

# <a name="python-v12"></a>[Python V12](#tab/python)

Para inserir uma mensagem em uma fila, use o [`send_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) método.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Para inserir uma mensagem em uma fila, use o [`put_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) método para criar uma nova mensagem e adicioná-la à fila.

```python
message = u"Hello, World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

As mensagens da fila do Azure são armazenadas como texto. Se você quiser armazenar dados binários, configure as funções codificação Base64 e decodificação antes de colocar uma mensagem na fila.

# <a name="python-v12"></a>[Python V12](#tab/python)

Configure a codificação Base64 e decodificação de funções no objeto de cliente fila.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Configurar a codificação Base64 e decodificar funções no objeto de armazenamento de fila.

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="peek-at-messages"></a>Espiar mensagens

# <a name="python-v12"></a>[Python V12](#tab/python)

Você pode inspecionar mensagens sem removê-las da fila chamando o [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) método. Por padrão, esse método inspeciona uma única mensagem.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Você pode inspecionar mensagens sem removê-las da fila chamando o [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#peek-messages-queue-name--num-messages-none--timeout-none-) método. Por padrão, esse método inspeciona uma única mensagem.

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa, você poderá usar esse recurso para atualizar o status da tarefa.

# <a name="python-v12"></a>[Python V12](#tab/python)

O código a seguir usa o [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) método para atualizar uma mensagem. O tempo limite de visibilidade está definido como 0, indicando que a mensagem será exibida imediatamente e o conteúdo será atualizado.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O código a seguir usa o [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) método para atualizar uma mensagem. O tempo limite de visibilidade está definido como 0, indicando que a mensagem será exibida imediatamente e o conteúdo será atualizado.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello, World Again")
```

---

## <a name="get-the-queue-length"></a>Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens em uma fila.

# <a name="python-v12"></a>[Python V12](#tab/python)

O método [get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) retorna propriedades de fila, incluindo o `approximate_message_count` .

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O [`get_queue_metadata`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-queue-metadata-queue-name--timeout-none-) método retorna propriedades de fila, incluindo `approximate_message_count` .

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

O resultado é aproximado apenas porque as mensagens podem ser adicionadas ou removidas depois que o serviço responde à sua solicitação.

## <a name="dequeue-messages"></a>Remover mensagens da fila

Remova uma mensagem de uma fila em duas etapas. Se o código não processar uma mensagem, esse processo de duas etapas garantirá que você possa obter a mesma mensagem e tentar novamente. Chame `delete_message` depois que a mensagem tiver sido processada com êxito.

# <a name="python-v12"></a>[Python V12](#tab/python)

Ao chamar [receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-), você obtém a próxima mensagem na fila por padrão. Uma mensagem retornada de `receive_messages` torna-se invisível para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para concluir a remoção da mensagem da fila, você também deve chamar [Delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-).

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Ao chamar [get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), você obtém a próxima mensagem na fila por padrão. Uma mensagem retornada de `get_messages` torna-se invisível para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para concluir a remoção da mensagem da fila, você também deve chamar [Delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-message-queue-name--message-id--pop-receipt--timeout-none-).

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem.

# <a name="python-v12"></a>[Python V12](#tab/python)

O exemplo de código a seguir usa o [`receive_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) método para obter mensagens em lotes. Em seguida, ele processa cada mensagem em cada lote usando um `for` loop aninhado. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O exemplo de código a seguir usa o [`get_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) método para obter 16 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop `for`. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem.

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>Excluir uma fila

# <a name="python-v12"></a>[Python V12](#tab/python)

Para excluir uma fila e todas as mensagens contidas nela, chame o [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) método.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Para excluir uma fila e todas as mensagens contidas nela, chame o [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-queue-queue-name--fail-not-exist-false--timeout-none-) método.

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links para saber mais.

- [Referência da API do Python de armazenamento de filas do Azure](/python/api/azure-storage-queue)
- [Central de desenvolvedores do Python](https://azure.microsoft.com/develop/python/)
- [Referência da API REST do armazenamento do Azure](/rest/api/storageservices/)
