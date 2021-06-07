---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 31704e705b828cc0070e3b79f5d527cfa9deb0c3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386948"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, é preciso:

- Criar uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instalar o [Python](https://www.python.org/downloads/)
- Criar um recurso dos Serviços de Comunicação do Azure. Para obter detalhes, confira [Criar um recurso de comunicação do Azure](../../create-communication-resource.md). Você precisará registrar o **ponto de extremidade** do recurso para este guia de início rápido
- Um [token de acesso do usuário](../../access-tokens.md). Defina o escopo como "chat" e anote a cadeia de caracteres do token, bem como a cadeia de caracteres da userId.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Abra o terminal ou a janela de comando para criar um diretório para seu aplicativo e navegue até ele.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Use um editor de texto para criar um arquivo chamado **start-chat.py** no diretório raiz do projeto e adicione a estrutura do programa, incluindo o tratamento básico de exceções. Você adicionará todo o código-fonte deste guia de início rápido a esse arquivo nas seções a seguir.

```python
import os
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>Instalar o SDK

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Modelo de objeto

As classes e as interfaces a seguir lidam com alguns dos principais recursos do SDK de Chat dos Serviços de Comunicação do Azure para Python.

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Essa classe é necessária para a funcionalidade de chat. Crie uma instância dela com suas informações de assinatura e use-a para criar, obter e excluir conversas. |
| ChatThreadClient | Essa classe é necessária para a funcionalidade de conversa de chat. Obtenha uma instância por meio de ChatClient e use-a para enviar/receber/atualizar/excluir mensagens, adicionar/remover/obter usuários e enviar notificações de digitação e confirmações de leitura. |

## <a name="create-a-chat-client"></a>Criar um cliente de chat

Para criar um cliente de chat, você usará o ponto de extremidade do Serviço de Comunicação e o `Access Token` que foi gerado como parte das etapas de pré-requisito. Saiba mais sobre os [tokens de acesso do usuário](../../access-tokens.md).

Este guia de início rápido não abrange a criação de uma camada de serviço para gerenciar tokens no seu aplicativo de chat, embora isso seja recomendado. Confira a documentação a seguir para obter mais detalhes sobre a [Arquitetura de Chat](../../../concepts/chat/concepts.md)

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```

## <a name="start-a-chat-thread"></a>Iniciar uma conversa de chat

Use o método `create_chat_thread` para criar uma conversa de chat.

- Use `topic` para fornecer um tópico a essa conversa; o tópico pode ser atualizado depois que a conversa de chat é criada por meio da função `update_thread`.
- Use `thread_participants` para listar o `ChatParticipant` a ser adicionado à conversa de chat; o `ChatParticipant` usa o tipo `CommunicationUserIdentifier` como `user`, que você obteve após a criação por meio de [Criar um usuário](../../access-tokens.md#create-an-identity)

`CreateChatThreadResult` é o resultado retornado pela criação de um thread; você pode usá-lo para buscar o `id` do thread de chat que foi criado. Esse `id` pode ser usado para buscar um objeto `ChatThreadClient` usando o método `get_chat_thread_client`. `ChatThreadClient` pode ser usado para executar outras operações de chat para esse thread de chat.

```python
topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Obter um cliente de conversa de chat
O método `get_chat_thread_client` retorna um cliente de conversa para uma conversa que já existe. Ele pode ser usado para executar operações na conversa criada: adicionar participantes, enviar mensagens etc. A thread_id é a ID exclusiva da conversa de chat existente.

`ChatThreadClient` pode ser usado para executar outras operações de chat para esse thread de chat.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```


## <a name="list-all-chat-threads"></a>Listar todas as conversas de chat
O método `list_chat_threads` retorna um iterador do tipo `ChatThreadItem`. Ele pode ser usado para listar todas as conversas de chat.

- Use `start_time` para especificar o ponto mais antigo no tempo e obter conversas de chat desde esse ponto.
- Use `results_per_page` para especificar o número máximo de conversas de chat retornadas por página.

Um iterador de `[ChatThreadItem]` é a resposta retornada pela listagem de threads

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Enviar uma mensagem para uma conversa de chat

Use o método `send_message` para enviar uma mensagem para uma conversa de chat recém-criada, identificada por thread_id.

- Use `content` para fornecer o conteúdo da mensagem de chat;
- Use `chat_message_type` para especificar o tipo do conteúdo da mensagem. Os valores possíveis são "Text" e "HTML"; se nenhum for especificado, o valor padrão de "Text" será atribuído.
- Use `sender_display_name` para especificar o nome de exibição do remetente;

`SendChatMessageResult` é a resposta retornada do envio de uma mensagem; ela contém uma ID, que é a ID exclusiva da mensagem.

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_w_enum.id)
```


## <a name="receive-chat-messages-from-a-chat-thread"></a>Receber mensagens de chat de uma conversa de chat

Recupere mensagens de chat sondando o método `list_messages` em intervalos especificados.

- Use `results_per_page` para especificar o número máximo de mensagens retornadas por página.
- Use `start_time` para especificar o ponto mais antigo no tempo e obter mensagens desde esse ponto.

Um iterador de `[ChatMessage]` é a resposta retornada pela listagem de mensagens

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` retorna a última versão da mensagem, incluindo as edições ou as exclusões que ocorreram na mensagem usando `update_message` e `delete_message`. Para as mensagens excluídas, `ChatMessage.deleted_on` retorna um valor de datetime indicando quando a mensagem foi excluída. Para as mensagens editadas, `ChatMessage.edited_on` retorna um datetime indicando quando a mensagem foi editada. A hora original da criação da mensagem pode ser acessada usando `ChatMessage.created_on`, que pode ser usado para ordenar as mensagens.

`list_messages` retorna diferentes tipos de mensagens que podem ser identificadas por `ChatMessage.type`. 

Leia mais sobre os tipos de mensagem aqui: [Tipos de mensagem](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Enviar confirmação de leitura
O método `send_read_receipt` pode ser usado para postar um evento de confirmação de leitura em uma conversa, em nome de um usuário.

- Use `message_id` para especificar a ID da mensagem mais recente lida pelo usuário atual.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adicionar um usuário como participante ao thread de chat

Depois que uma conversa de chat é criada, você pode adicionar e remover usuários nela. Ao adicionar usuários, você permite a eles acesso para poderem enviar mensagens à conversa de chat e adicionar/remover outros participantes. Antes de chamar `add_participants` método, adquira um novo token de acesso e uma identidade para esse usuário. O usuário precisará desse token de acesso para inicializar o cliente de chat.

Um ou mais usuários podem ser adicionados à conversa de chat usando o método `add_participants`, desde que o novo token de acesso e a identificação sejam disponibilizados a todos os usuários.

Um `list(tuple(ChatParticipant, CommunicationError))` é retornado. Quando o participante é adicionado com êxito, uma lista vazia é esperada. No caso de um erro encontrado ao adicionar o participante, a lista é populada com os participantes com falha, em conjunto com o erro que foi encontrado.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>Listar participantes do thread em um thread de chat

Assim como adicionar um participante, você também pode listar participantes de uma conversa.

Use `list_participants` para recuperar os participantes do thread de chat.
- Use `results_per_page`, opcional, o número máximo de participantes a serem retornados por página.
- Use `skip`, opcional, para ignorar participantes até uma posição especificada em resposta.

Um iterador de `[ChatParticipant]` é a resposta retornada pela listagem de participantes

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>Executar o código

Execute o aplicativo do seu diretório de aplicativo com o comando `python`.

```console
python start-chat.py
```
