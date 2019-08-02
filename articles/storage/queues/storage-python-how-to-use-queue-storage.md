---
title: Como usar o armazenamento de fila do Python – armazenamento do Azure
description: Saiba como usar o serviço Fila do Azure do Python para criar e excluir filas, bem como para inserir, obter e excluir mensagens.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 141999f4119ac92e2b8846477c50edf8fba027d0
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360031"
---
# <a name="how-to-use-queue-storage-from-python"></a>Como usar o Armazenamento de fila do Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral
Este guia mostra como executar cenários comuns usando o serviço de armazenamento de Fila do Azure. Os exemplos são escritos em Python e usam o [SDK do Armazenamento do Microsoft Azure para Python]. Os cenários abrangidos incluem **inserir**, **exibir**, **obter** e **excluir** mensagens da fila, bem como **criar e excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Baixar e instalar o SDK do Armazenamento do Azure para Python

O [SDK do Armazenamento do Azure para Python](https://github.com/azure/azure-storage-python) requer o Python 2.7, 3.3, 3.4, 3.5 ou 3.6.
 
### <a name="install-via-pypi"></a>Instalar por meio de PyPi

Para instalar por meio do Índice de Pacote do Python (PyPI), digite:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Se você estiver fazendo upgrade do SDK de Armazenamento do Azure para Python versão 0.36 ou anterior, desinstale o SDK mais antigo usando `pip uninstall azure-storage` antes de instalar o pacote mais recente.

Para métodos de instalação alternativos, confira o [SDK do Armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Visualizar o aplicativo de exemplo

Para exibir e executar um aplicativo de exemplo que mostra como usar o Python com Filas do Azure, confira [Armazenamento do Azure: Introdução às Filas do Azure no Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Para executar o aplicativo de exemplo, verifique se você instalou os pacotes `azure-storage-queue` e `azure-storage-common`.

## <a name="how-to-create-a-queue"></a>Como: criar uma fila

O objeto **QueueService** permite que você trabalhe com filas. O código a seguir cria um objeto **QueueService** . Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o Armazenamento do Azure programaticamente:

```python
from azure.storage.queue import QueueService
```

O código a seguir cria um objeto **QueueService** usando o nome da conta de armazenamento e a chave da conta. Substitua “myaccount” e “mykey” pelo nome da sua conta e sua chave.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: inserir uma mensagem em uma fila
Para inserir uma mensagem em uma fila, use o método **put\_message** para criar uma nova mensagem e adicioná-la à fila.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Como: espiar a próxima mensagem
Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método **peek\_messages**. Por padrão, **peek\_messages()** inspeciona uma única mensagem.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Como: remover mensagens da fila
Seu código remove uma mensagem de uma fila em duas etapas. Ao chamar **get\_messages**, você receberá a próxima mensagem em uma fila por padrão. Uma mensagem retornada de **get\_messages** torna-se invisível para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para terminar de remover a mensagem da fila, você também deve chamar **delete\_message**. Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama **delete\_message** logo depois que a mensagem é processada.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.
Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O seguinte exemplo de código usa o método **get\_messages** para receber 16 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop for. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: alterar o conteúdo de uma mensagem na fila
Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir usa o método **update\_message** para atualizar uma mensagem. O tempo limite de visibilidade está definido como 0, indicando que a mensagem será exibida imediatamente e o conteúdo será atualizado.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Como: obter o tamanho da fila
Você pode obter uma estimativa do número de mensagens em uma fila. O método **get\_queue\_metadata** solicita que o serviço Fila retorne os metadados sobre a fila e a **approximate_message_count**. O resultado é aproximado apenas porque as mensagens podem ser adicionadas ou removidas depois que o serviço de fila responde à sua solicitação.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Como: excluir uma fila
Para excluir uma fila e todas as mensagens contidas nela, chame o método **delete\_queue**.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os conceitos básicos do Armazenamento de Filas, siga estes links para saber mais.

* [Centro de desenvolvedores do Python](https://azure.microsoft.com/develop/python/)
* [API REST de serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[SDK do Armazenamento do Microsoft Azure para Python]: https://github.com/Azure/azure-storage-python
