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
ms.openlocfilehash: 18282bbe902599c471775a853704e459ea44bac1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661607"
---
## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, é preciso:

- Criar uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instalar o [Node.js](https://nodejs.org/en/download/), versões Active LTS e Maintenance LTS (8.11.1 e 10.14.1 são recomendadas).
- Criar um recurso dos Serviços de Comunicação do Azure. Para obter detalhes, confira [Criar um recurso de comunicação do Azure](../../create-communication-resource.md). Você precisará **registrar o ponto de extremidade do recurso** para este guia de início rápido.
- Crie *três* usuários do ACS e emita-os como um [token de acesso do usuário](../../access-tokens.md) token de acesso do usuário. Defina o escopo como **chat** e **anote a cadeia de caracteres do token, bem como a cadeia de caracteres da userId**. A demonstração completa cria um thread com dois participantes iniciais e adiciona um terceiro participante ao thread.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-web-application"></a>Criar um aplicativo Web

Primeiro, abra o terminal ou a janela de comando para criar um diretório para seu aplicativo e navegue até ele.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Execute `npm init -y` para criar um arquivo **package.json** com as configurações padrão.

```console
npm init -y
```

### <a name="install-the-packages"></a>Instalar os pacotes

Use o comando `npm install` para instalar as bibliotecas de clientes dos Serviços de Comunicação para JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

A opção `--save` lista a biblioteca como uma dependência no arquivo **package.json**.

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

Este guia de início rápido usa o webpack para agrupar os ativos do aplicativo. Execute o seguinte comando para instalar os pacotes npm webpack, webpack-cli e webpack-dev-server e listá-los como dependências de desenvolvimento no **package.json**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Crie um arquivo **index.html** no diretório raiz do projeto. Usaremos esse arquivo como modelo para adicionar a funcionalidade de chat usando a biblioteca de clientes de chat da Comunicação do Azure para JavaScript.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Crie um arquivo no diretório raiz do projeto chamado **client.js** para conter a lógica do aplicativo deste guia de início rápido.

### <a name="create-a-chat-client"></a>Criar um cliente de chat

Para criar um cliente de chat no aplicativo Web, você usará o **ponto de extremidade** do Serviço de Comunicação e o **token de acesso** que foi gerado como parte das etapas de pré-requisito.

Os tokens de acesso do usuário permitem que você crie aplicativos cliente que se autenticam diretamente nos Serviços de Comunicação do Azure. Este guia de início rápido não abrange a criação de uma camada de serviço para gerenciar tokens no seu aplicativo de chat. Confira os [conceitos de chat](../../../concepts/chat/concepts.md) para obter mais informações sobre a arquitetura de chat e os [tokens de acesso do usuário](../../access-tokens.md) para obter mais informações sobre tokens de acesso.

Dentro de **client.js**, use o ponto de extremidade e o token de acesso no código abaixo para adicionar a funcionalidade de chat usando a biblioteca de clientes de chat de comunicação do Azure para JavaScript.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- Substitua **endpointUrl** pelo ponto de extremidade de recurso dos Serviços de Comunicação, confira [Criar um Recurso de Comunicação do Azure](../../create-communication-resource.md) se você ainda não tiver feito isso.
- Substitua **userAccessToken** pelo token que você emitiu.


### <a name="run-the-code"></a>Executar o código

Use `webpack-dev-server` para criar e executar o seu aplicativo. Execute o seguinte comando para agrupar o host de aplicativos em um servidor Web local:
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Abra o navegador e navegue até http://localhost:8080/.
No console de ferramentas para desenvolvedores do navegador, você verá o seguinte:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Modelo de objeto
As classes e as interfaces a seguir lidam com alguns dos principais recursos da biblioteca de clientes de Chat dos Serviços de Comunicação do Azure para JavaScript.

| Name                                   | Descrição                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Essa classe é necessária para a funcionalidade de chat. Crie uma instância dela com suas informações de assinatura e use-a para criar, obter e excluir conversas. |
| ChatThreadClient | Essa classe é necessária para a funcionalidade de conversa de chat. Obtenha uma instância por meio de ChatClient e use-a para enviar/receber/atualizar/excluir mensagens, adicionar/remover/obter usuários, enviar notificações de digitação e confirmações de leitura e assinar eventos de chat. |


## <a name="start-a-chat-thread"></a>Iniciar uma conversa de chat

Use o método `createThread` para criar uma conversa de chat.

`createThreadRequest` é usado para descrever a solicitação de conversa:

- Use `topic` para fornecer um tópico para esse chat. Os tópicos podem ser atualizados após a conversa do chat ser criada usando a função `UpdateThread`.
- Use `participants` para listar os participantes a serem adicionados ao thread de chat.

Quando resolvido, o método `createChatThread` retorna um `CreateChatThreadResponse`. Esse modelo contém uma propriedade `chatThread` na qual você pode acessar o `id` do thread recém-criado. Você pode usar o `id` para obter uma instância de um `ChatThreadClient`. O `ChatThreadClient` pode então ser usado para executar a operação dentro do thread, como enviar mensagens ou listar participantes.

```JavaScript
async function createChatThread() {
    let createThreadRequest = {
        topic: 'Preparation for London conference',
        participants: [{
                    user: { communicationUserId: '<USER_ID_FOR_JACK>' },
                    displayName: 'Jack'
                }, {
                    user: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                    displayName: 'Geeta'
                }]
    };
    let createThreadResponse = await chatClient.createChatThread(createThreadRequest);
    let threadId = createThreadResponse.chatThread.id;
    return threadId;
    }

createChatThread().then(async threadId => {
    console.log(`Thread created:${threadId}`);
    // PLACEHOLDERS
    // <CREATE CHAT THREAD CLIENT>
    // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
    // <SEND MESSAGE TO A CHAT THREAD>
    // <LIST MESSAGES IN A CHAT THREAD>
    // <ADD NEW PARTICIPANT TO THREAD>
    // <LIST PARTICIPANTS IN A THREAD>
    // <REMOVE PARTICIPANT FROM THREAD>
    });
```

Substitua **USER_ID_FOR_JACK** e **USER_ID_FOR_GEETA** pelas IDs de usuário obtidas da criação de usuários e tokens ([Tokens de Acesso do Usuário](../../access-tokens.md))

Ao atualizar a guia do navegador, você verá a seguinte mensagem no console:
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>Obter um cliente de conversa de chat

O método `getChatThreadClient` retorna um `chatThreadClient` para uma conversa que já existe. Ele pode ser usado para executar operações no thread criado: adicionar participantes, enviar mensagens etc. threadId é a ID exclusiva do thread de chat existente.

```JavaScript
let chatThreadClient = await chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
Adicione este código no lugar do comentário `<CREATE CHAT THREAD CLIENT>` em **client.js**, atualize a guia do navegador e verifique o console. Você verá:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Enviar uma mensagem para uma conversa de chat

Use o método `sendMessage` para enviar uma mensagem de chat para a conversa recém-criada, identificada por threadId.

`sendMessageRequest` descreve os campos obrigatórios da solicitação de mensagem de chat:

- Use `content` para fornecer o conteúdo da mensagem de chat;

`sendMessageOptions` descreve os campos opcionais da solicitação de mensagem de chat:

- Use `priority` para especificar o nível de prioridade da mensagem do chat, como 'Normal' ou 'Alto'. Essa propriedade pode ser usada para exibir um indicador de interface do usuário para o usuário destinatário no seu aplicativo a fim de chamar atenção para a mensagem ou executar uma lógica de negócios personalizada.
- Use `senderDisplayName` para especificar o nome de exibição do remetente;

A resposta `sendChatMessageResult` contém uma ID, que é a ID exclusiva dessa mensagem.

```JavaScript

let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    priority: 'Normal',
    senderDisplayName : 'Jack'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);

```
Adicione este código no lugar do comentário `<SEND MESSAGE TO A CHAT THREAD>` em **client.js**, atualize a guia do navegador e verifique o console.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Receber mensagens de chat de uma conversa de chat

Com a sinalização em tempo real, você pode criar assinaturas para escutar as novas mensagens de entrada e atualizar as mensagens atuais na memória de acordo. Os Serviços de Comunicação do Azure dão suporte a uma [lista de eventos que você pode assinar](../../../concepts/chat/concepts.md#real-time-signaling).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
Adicione este código no lugar do comentário `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` em **client.js**.
Atualize a guia do navegador e você verá uma mensagem `Notification chatMessageReceived` no console.

Como alternativa, você pode recuperar mensagens de chat sondando o método `listMessages` em intervalos especificados.

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
    while (!nextMessage.done) {
        let chatMessage = nextMessage.value;
        console.log(`Message :${chatMessage.content}`);
        // your code here
        nextMessage = await pagedAsyncIterableIterator.next();
    }

```
Adicione este código no lugar do comentário `<LIST MESSAGES IN A CHAT THREAD>` em **client.js**.
Atualize a guia e, no console, você encontrará a lista de mensagens enviadas nesse thread de chat.


`listMessages` retorna a última versão da mensagem, incluindo as edições ou as exclusões que ocorreram na mensagem usando `updateMessage` e `deleteMessage`.
Para as mensagens excluídas, `chatMessage.deletedOn` retorna um valor de datetime indicando quando a mensagem foi excluída. Para as mensagens editadas, `chatMessage.editedOn` retorna um datetime indicando quando a mensagem foi editada. A hora original da criação da mensagem pode ser acessada usando `chatMessage.createdOn`, que pode ser usado para ordenar as mensagens.

`listMessages` retorna diferentes tipos de mensagens que podem ser identificadas por `chatMessage.type`. Esses tipos são:

- `Text`: mensagem de chat regular enviada por um participante do thread.

- `ThreadActivity/TopicUpdate`: mensagem do sistema que indica que o tópico foi atualizado.

- `ThreadActivity/AddParticipant`: mensagem do sistema que indica que um ou mais participantes foram adicionados ao thread do chat.

- `ThreadActivity/RemoveParticipant`: mensagem do sistema que indica que um participante foi removido do thread de chat.

Para obter mais detalhes, confira [Tipos de mensagem](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adicionar um usuário como participante ao thread de chat

Depois que uma conversa de chat é criada, você pode adicionar e remover usuários nela. Ao adicionar usuários, você permite a eles acesso para enviar mensagens ao thread de chat e adicionar/remover outros participantes.

Antes de chamar o método `addParticipants`, adquira um novo token de acesso e uma identidade para esse usuário. O usuário precisará desse token de acesso para inicializar o cliente de chat.

`addParticipantsRequest` descreve o objeto de solicitação, em que `participants` lista os participantes a serem adicionados ao thread de chat;
- `user`, obrigatório, é o usuário de comunicação a ser adicionado à conversa de chat.
- `displayName`, opcional, é o nome de exibição do participante do thread.
- `shareHistoryTime`, opcional, é a hora a partir da qual o histórico de chats é compartilhado com o participante. Para compartilhar o histórico desde o início da conversa de chat, defina essa propriedade como qualquer data igual ou inferior à hora de criação da conversa. Para não compartilhar nenhum histórico anterior ao momento em que o participante foi adicionado, defina-a como a data atual. Para compartilhar o histórico parcial, defina-a como a data de sua escolha.

```JavaScript

let addParticipantsRequest =
{
    participants: [
        {
            user: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
Substitua **NEW_PARTICIPANT_USER_ID** por uma [nova ID de usuário](../../access-tokens.md). Adicione este código no lugar do comentário `<ADD NEW PARTICIPANT TO THREAD>` em **client.js**

## <a name="list-users-in-a-chat-thread"></a>Listar os usuários de uma conversa de chat
```JavaScript
async function listParticipants() {
   let pagedAsyncIterableIterator = await chatThreadClient.listParticipants();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listParticipants();
```
Adicione este código no lugar do comentário `<LIST PARTICIPANTS IN A THREAD>` em **client.js**, atualize a guia do navegador e verifique o console. Você verá informações sobre os usuários de uma conversa.

## <a name="remove-user-from-a-chat-thread"></a>Remover um usuário de uma conversa de chat

Assim como adicionar um participante, você pode remover participantes de um thread de chat. Para removê-los, acompanhe as IDs dos participantes que você adicionou.

Use o método `removeParticipant`, em que `participant` é o usuário de comunicação a ser removido da conversa.

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
Substitua **PARTICIPANT_ID** por uma ID de usuário usada na etapa anterior (<NEW_PARTICIPANT_USER_ID>).
Adicione este código no lugar do comentário `<REMOVE PARTICIPANT FROM THREAD>` em **client.js**.
