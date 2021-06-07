---
title: 'Início Rápido: Biblioteca de clientes do Armazenamento de Filas do Azure v12 – Python'
description: Saiba como usar a biblioteca de clientes do Armazenamento de Filas do Azure v12 para Python para criar uma fila e adicionar mensagens a ela. Depois, descubra como ler e excluir mensagens da fila. Você também aprenderá a excluir uma fila.
author: twooley
ms.author: twooley
ms.date: 12/10/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 68f68c32b160757e82d59b1dd5ee4847a06ec698
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534357"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Início Rápido: Biblioteca de clientes do Armazenamento de Filas do Azure v12 para Python

Introdução à biblioteca de clientes do Armazenamento de Filas do Azure v12 para Python. O Armazenamento de Filas do Azure é um serviço usado para armazenar grandes quantidades de mensagens para recuperação e processamento posteriores. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

Use a biblioteca de clientes do Armazenamento de Filas do Azure v12 para Python a fim de:

- Criar uma fila
- Adicionar mensagens a uma fila
- Espiar mensagens em uma fila
- Atualizar uma mensagem em uma fila
- Receber mensagens de uma fila
- Excluir mensagens de uma fila
- Excluir uma fila

Recursos adicionais:

- [Documentação de referência da API](/python/api/azure-storage-queue/index)
- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
- [Pacote (Índice de Pacotes do Python)](https://pypi.org/project/azure-storage-queue/)
- [Amostras](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- Conta de armazenamento do Azure - [criar uma conta de armazenamento](../common/storage-account-create.md)
- Ter o [Python](https://www.python.org/downloads/) 2.7 ou 3.6+ em seu sistema operacional

## <a name="setting-up"></a>Configurando

Esta seção descreve como preparar um projeto para trabalhar com a biblioteca de clientes do Armazenamento de Filas do Azure v12 para Python.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo Python chamado `queues-quickstart-v12`.

1. Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para o projeto.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Alterne para o diretório `queues-quickstart-v12` recém-criado.

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Instalar o pacote

Instale a biblioteca de clientes do Armazenamento de Blobs do Azure para o pacote Python usando o comando `pip install`.

```console
pip install azure-storage-queue
```

Esse comando instala a biblioteca de clientes do Armazenamento de Filas do Azure para o pacote Python e todas as bibliotecas das quais ela depende. Nesse caso, essa é apenas a biblioteca principal do Azure para Python.

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

1. Abra um arquivo de texto novo no editor de código
1. Adicione instruções `import`
1. Crie a estrutura para o programa, incluindo uma manipulação de exceção muito básica

    O código é o seguinte:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Salve o novo arquivo como `queues-quickstart-v12.py` no diretório `queues-quickstart-v12`.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de Filas do Azure é um serviço usado para armazenar grandes quantidades de mensagens. Uma mensagem da fila pode ter até 64 KB. Uma fila pode conter milhões de mensagens, até o limite da capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processamento assíncrono. O Armazenamento de Filas oferece três tipos de recursos:

- A conta de armazenamento
- Uma fila na conta de armazenamento
- Mensagens na fila

O diagrama a seguir mostra a relação entre esses recursos.

![Diagrama da arquitetura do armazenamento de Filas](./media/storage-queues-introduction/queue1.png)

Use as classes Python a seguir para interagir com esses recursos:

- [`QueueServiceClient`](/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): O `QueueServiceClient` permite que você gerencie todas as filas em sua conta de armazenamento.
- [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient): A classe `QueueClient` permite que você gerencie e manipule uma fila individual e as mensagens dela.
- [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage): A classe `QueueMessage` representa os objetos individuais retornados ao chamar [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) em uma fila.

## <a name="code-examples"></a>Exemplos de código

Esses snippets de código de exemplo mostram como executar as seguintes ações com a biblioteca de clientes do Armazenamento de Filas do Azure para Python:

- [Obter a cadeia de conexão](#get-the-connection-string)
- [Criar uma fila](#create-a-queue)
- [Adicionar mensagens a uma fila](#add-messages-to-a-queue)
- [Espiar mensagens em uma fila](#peek-at-messages-in-a-queue)
- [Atualizar uma mensagem em uma fila](#update-a-message-in-a-queue)
- [Receber mensagens de uma fila](#receive-messages-from-a-queue)
- [Excluir mensagens de uma fila](#delete-messages-from-a-queue)
- [Excluir uma fila](#delete-a-queue)

### <a name="get-the-connection-string"></a>Obtenha a cadeia de conexão

O código a seguir recupera a cadeia de conexão da conta de armazenamento. A cadeia de conexão é armazenada na variável de ambiente criada na seção [Configurar a cadeia de conexão do armazenamento](#configure-your-storage-connection-string).

Adicione esse código ao bloco `try`:

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Criar uma fila

Escolha um nome para a nova fila. O código a seguir acrescenta um valor de UUID ao nome da fila para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes de fila podem conter apenas letras minúsculas, números e hifens e precisam começar com uma letra ou um número. Cada hífen deve ser precedido e seguido por um caractere que não seja um hífen. O nome também precisa ter entre 3 e 63 caracteres. Para obter mais informações, confira [Como nomear filas e metadados](/rest/api/storageservices/naming-queues-and-metadata).

Crie uma instância da classe [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient). Em seguida, chame o método [`create_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) para criar a fila na sua conta de armazenamento.

Adicione este código ao final do bloco `try`:

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Adicionar mensagens a uma fila

O snippet de código a seguir adiciona mensagens à fila chamando o método [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-). Ele também salva a [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage) retornada da terceira chamada `send_message`. O `saved_message` é usado para atualizar o conteúdo da mensagem posteriormente no programa.

Adicione este código ao final do bloco `try`:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Espiar mensagens em uma fila

Espie as mensagens na fila chamando o método [`peek_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-). Esse método recupera uma ou mais mensagens do início da fila, mas não altera a visibilidade da mensagem.

Adicione este código ao final do bloco `try`:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Atualizar uma mensagem em uma fila

Atualize o conteúdo de uma mensagem chamando o método [`update_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-). Esse método pode alterar o conteúdo e o tempo limite da visibilidade de uma mensagem. O conteúdo da mensagem precisa ser uma cadeia de caracteres codificada em UTF-8 com até 64 KB. Junto com o novo conteúdo, transmita os valores da mensagem que foi salva anteriormente no código. Os valores de `saved_message` identificam a mensagem a ser atualizada.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

Baixe as mensagens adicionadas anteriormente chamando o método [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-).

Adicione este código ao final do bloco `try`:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Excluir mensagens de uma fila

Exclua mensagens da fila depois que elas forem recebidas e processadas. Nesse caso, o processamento só exibe a mensagem no console.

O aplicativo pausa a entrada do usuário chamando `input` antes de processar e excluir as mensagens. Verifique no [portal do Azure](https://portal.azure.com) se os recursos foram criados corretamente antes de serem excluídos. As mensagens que não forem explicitamente excluídas acabarão se tornando visíveis na fila novamente para outra oportunidade de processamento.

Adicione este código ao final do bloco `try`:

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Excluir uma fila

O código a seguir limpa os recursos que o aplicativo criou ao excluir a fila usando o método [`delete_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-).

Adicione este código ao final do bloco `try` e salve o arquivo:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Executar o código

Esse aplicativo cria e adiciona três mensagens a uma fila do Azure. O código lista as mensagens na fila e, em seguida, recupera essas mensagens e as exclui, antes de excluir a fila.

Na janela do console, procure o diretório que contém o arquivo `queues-quickstart-v12.py` e use o comando `python` a seguir para executar o aplicativo.

```console
python queues-quickstart-v12.py
```

A saída do aplicativo é semelhante ao seguinte exemplo:

```output
Azure Queue Storage client library v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

Quando o aplicativo pausar antes de receber mensagens, verifique sua conta de armazenamento no [portal do Azure](https://portal.azure.com). Verifique se as mensagens estão na fila.

Pressione a tecla `Enter` para receber e excluir as mensagens. Quando solicitado, pressione a tecla `Enter` novamente para excluir a fila e concluir a demonstração.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma fila e a adicionar mensagens a ela usando código Python. Em seguida, você aprendeu a espiar, recuperar e excluir mensagens. Por fim, você aprendeu a excluir uma fila de mensagens.

Para obter tutoriais, amostras, inícios rápidos e outros tipos de documentação, visite:

> [!div class="nextstepaction"]
> [Azure para desenvolvedores do Python](/azure/python/)

- Para saber mais, confira as [bibliotecas do Armazenamento do Azure para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage).
- Para obter mais aplicativos de exemplo do Armazenamento de Filas do Azure, confira [Biblioteca de clientes do Armazenamento de Filas do Azure v12 para Python – exemplos](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
