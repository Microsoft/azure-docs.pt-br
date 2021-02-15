---
title: Guia de Início Rápido – Participar de uma reunião do Teams
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 780ef2bbb7851d8bef5fc52a51421a7938043ecb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932324"
---
## <a name="join-the-meeting-chat"></a>Participar do chat da reunião 

Depois que a interoperabilidade do Teams estiver habilitada, um usuário dos Serviços de Comunicação poderá participar da chamada do Teams como um usuário convidado usando a biblioteca de clientes de chamada. A participação na chamada o adicionará como um participante do chat da reunião também, em que ele poderá enviar mensagens para outros usuários na chamada e receber mensagens deles. O usuário não terá acesso às mensagens de chat que foram enviadas antes de ele se conectar à chamada. 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Obter um thread de chat da reunião do Teams para um usuário dos Serviços de Comunicação

Primeiro, crie uma instância de `ChatThreadClient` para o thread do chat da reunião. Analise o link da reunião ou use as APIs do Graph com a ID da reunião para obter a ID do thread. 

- Um link de reunião do Teams é parecido com este: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. A ID do thread será o local em que a `meeting_chat_thread_id` está nesse link. 
- Se você tiver a ID da reunião, use a [API do Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) para obter a ID do thread. A resposta [OBTER API](/graph/api/onlinemeeting-get?tabs=http%22+%5c&view=graph-rest-beta) terá um objeto `chatInfo` que contém a `threadID`. 

Quando tiver a ID do thread do chat, obtenha o cliente do thread do chat usando a biblioteca de clientes de chat do JavaScript: 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
O `chatThreadClient` pode ser usado para listar os membros do thread do chat, obter o histórico de chats e enviar mensagens.  

## <a name="send-and-receive-messages"></a>Enviar e receber mensagens  

Use a `SendMessage` para enviar uma mensagem para o chat da reunião. Para receber mensagens de entrada, não há suporte para a capacidade de assinar eventos como `chatMessageReceived`, pois a sinalização em tempo real ainda não está habilitada para este cenário. Para obter as mensagens mais recentes, sonde a API `ListMessages`. Para o cenário de interoperabilidade, a API `ListMessages` já dá suporte ao retorno de três novos tipos de mensagens:
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

Para obter mais informações sobre os tipos de mensagens, acesse [aqui](../../../concepts/chat/concepts.md). 

**Observação**: atualmente, só há suporte para o envio e o recebimento de mensagens em cenários de interoperabilidade com o Teams. Ainda não há suporte para outros recursos como indicadores de digitação e adição ou remoção de outros usuários da reunião do Teams pelos usuários dos Serviços de Comunicação.  

