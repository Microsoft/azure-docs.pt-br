---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 2b7d00335253772683b867acf0765b77fc493e79
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523933"
---
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
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Instalar a biblioteca de clientes

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Modelo de objeto

As classes e as interfaces a seguir lidam com alguns dos principais recursos da biblioteca de clientes de chat dos Serviços de Comunicação do Azure para Python.

| Name                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Essa classe é necessária para a funcionalidade de chat. Crie uma instância dela com suas informações de assinatura e use-a para criar, obter e excluir conversas. |
| ChatThreadClient | Essa classe é necessária para a funcionalidade de conversa de chat. Obtenha uma instância por meio de ChatClient e use-a para enviar/receber/atualizar/excluir mensagens, adicionar/remover/obter usuários e enviar notificações de digitação e confirmações de leitura. |

## <a name="create-a-chat-client"></a>Criar um cliente de chat

Para criar um cliente de chat, você usará o ponto de extremidade do Serviço de Comunicação e o `Access Token` que foi gerado como parte das etapas de pré-requisito. Saiba mais sobre os [tokens de acesso do usuário](../../access-tokens.md).

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>Iniciar uma conversa de chat

Use o método `create_chat_thread` para criar uma conversa de chat.

- Use `topic` para fornecer um tópico a essa conversa; o tópico pode ser atualizado depois que a conversa de chat é criada por meio da função `update_thread`.
- Use `members` para listar o `ChatThreadMember` a ser adicionado à conversa de chat; o `ChatThreadMember` usa o tipo `CommunicationUser` como `user`, que você obteve após a criação por meio de [Criar um usuário](../../access-tokens.md#create-an-identity)

A resposta `chat_thread_client` é usada para executar operações na conversa de chat recém-criada, como adicionar membros a ela, enviar mensagens, excluir mensagens etc. Ela contém uma propriedade `thread_id`, que é a ID exclusiva da conversa de chat.

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>Obter um cliente de conversa de chat
O método get_chat_conversa_client retorna um cliente de conversa para uma conversa que já existe. Ele pode ser usado para executar operações na conversa criada: adicionar membros, enviar mensagens etc. thread_id é a ID exclusiva da conversa de chat existente.

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Enviar uma mensagem para uma conversa de chat

Use o método `send_message` para enviar uma mensagem para uma conversa de chat recém-criada, identificada por threadId.

- Use `content` para fornecer o conteúdo da mensagem de chat;
- Use `priority` para especificar o nível de prioridade da mensagem, como 'Normal' ou 'Alta'; essa propriedade pode ser usada para que o indicador da interface do usuário destinatário no aplicativo dê atenção à mensagem ou execute uma lógica de negócios personalizada.
- Use `senderDisplayName` para especificar o nome de exibição do remetente;

A resposta `SendChatMessageResult` contém uma "ID", que é a ID exclusiva dessa mensagem.

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Receber mensagens de chat de uma conversa de chat

Recupere mensagens de chat sondando o método `list_messages` em intervalos especificados.

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` retorna a última versão da mensagem, incluindo as edições ou as exclusões que ocorreram na mensagem usando `update_message` e `delete_message`. Para as mensagens excluídas, `ChatMessage.deleted_on` retorna um valor de datetime indicando quando a mensagem foi excluída. Para as mensagens editadas, `ChatMessage.edited_on` retorna um datetime indicando quando a mensagem foi editada. A hora original da criação da mensagem pode ser acessada usando `ChatMessage.created_on`, que pode ser usado para ordenar as mensagens.

`list_messages` retorna diferentes tipos de mensagens que podem ser identificadas por `ChatMessage.type`. Esses tipos são:

- `Text`: mensagem de chat normal enviada por um membro da conversa.

- `ThreadActivity/TopicUpdate`: mensagem do sistema que indica que o tópico foi atualizado.

- `ThreadActivity/AddMember`: mensagem do sistema que indica que um ou mais membros foram adicionados à conversa de chat.

- `ThreadActivity/DeleteMember`: mensagem do sistema que indica que um membro foi removido da conversa de chat.

Para obter mais detalhes, confira [Tipos de mensagem](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Adicionar um usuário como membro à conversa de chat

Depois que uma conversa de chat é criada, você pode adicionar e remover usuários nela. Ao adicionar usuários, você permite a eles acesso para enviar mensagens à conversa de chat e adicionar/remover outros membros. Antes de chamar `add_members` método, adquira um novo token de acesso e uma identidade para esse usuário. O usuário precisará desse token de acesso para inicializar o cliente de chat.

Use o método `add_members` para adicionar membros de conversa à conversa identificada por threadId.

- Use `members` para listar os membros a serem adicionados à conversa de chat;
- `user`, obrigatório, é o `CommunicationUser` que você criou pelo `CommunicationIdentityClient` em [Criar um usuário](../../access-tokens.md#create-an-identity)
- `display_name`, opcional, é o nome de exibição do membro da conversa.
- `share_history_time`, opcional, é a hora a partir da qual o histórico de chats é compartilhado com o membro. Para compartilhar o histórico desde o início da conversa de chat, defina essa propriedade como qualquer data igual ou inferior à hora de criação da conversa. Para não compartilhar nenhum histórico anterior ao momento em que o membro foi adicionado, defina-a como a data atual. Para compartilhar o histórico parcial, defina-a como uma data intermediária.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>Remover um usuário de uma conversa de chat

De modo semelhante à adição de um membro, você também pode remover membros de uma conversa. Para removê-los, acompanhe as IDs dos membros que você adicionou.

Use o método `remove_member` para remover o membro da conversa identificada por threadId.
- `user` é o CommunicationUser a ser removido da conversa.

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>Executar o código

Execute o aplicativo do seu diretório de aplicativo com o comando `python`.

```console
python start-chat.py
```
