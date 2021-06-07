---
title: Guia de Início Rápido – Participar de uma reunião do Teams
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 49f9bac40ae803f980a22c19fd5d44d85fa99e9e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564568"
---
## <a name="joining-the-meeting-chat"></a>Como participar do chat da reunião 

Quando a interoperabilidade do Teams estiver habilitada, um usuário dos Serviços de Comunicação poderá participar da chamada do Teams como um usuário externo usando o SDK de Chamada. A participação na chamada o adicionará como um participante do chat da reunião também, em que ele poderá enviar mensagens para outros usuários na chamada e receber mensagens deles. O usuário não terá acesso às mensagens de chat que foram enviadas antes de ele se conectar à chamada. Para ingressar na reunião e iniciar o bate-papo, você pode seguir as próximas etapas.

## <a name="install-the-chat-packages"></a>Instalar os pacotes de chat

Use o comando `npm install` para instalar os SDKs dos Serviços de Comunicação necessários para JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

A opção `--save` lista a biblioteca como uma dependência no arquivo **package.json**.

## <a name="add-the-teams-ui-controls"></a>Adicionar os controles de interface do usuário do Teams

Substitua o código em index.html pelo snippet a seguir.
As caixas de texto na parte superior da página serão usadas para inserir o contexto de reunião do Teams e a ID do thread de reunião. O botão 'Ingressar na reunião do Teams' será usado para ingressar na reunião especificada.
Um pop-up de chat será exibido na parte inferior da página. Ele pode ser usado para enviar mensagens no thread de reunião e exibirá em tempo real todas as mensagens enviadas no thread enquanto o usuário ACS é membro.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Habilitar os controles de interface do usuário do Teams

Substitua o conteúdo do arquivo client.js pelo snippet a seguir.

Dentro do snippet, substitua 
- `SECRET CONNECTION STRING` pela cadeia de conexão do Serviço de Comunicação 
- `ENDPOINT URL` pela URL do ponto de extremidade do Serviço de Comunicação

```javascript
// run using
// npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var messages = '';

async function init() {
  const connectionString = "<SECRET CONNECTION STRING>";
  const endpointUrl = "<ENDPOINT URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(
    `\nCreated an identity with ID: ${identityResponse.communicationUserId}`
  );

  let tokenResponse = await identityClient.issueToken(identityResponse, [
    "voip",
    "chat",
  ]);
  const { token, expiresOn } = tokenResponse;
  console.log(
    `\nIssued an access token that expires at ${expiresOn}:`
  );
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationUserCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationUserCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
  call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = messages;
  
  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    
    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.content);
    }
    else {
       renderSentMessage(e.content);
    }
  });
  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
   messages += '<div class="container lighter">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

async function renderSentMessage(message) {
   messages += '<div class="container darker">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
      let message = messagebox.value;

      let sendMessageRequest = { content: message };
      let sendMessageOptions = { senderDisplayName : 'Jack' };
      let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
      let messageId = sendChatMessageResult.id;

      messagebox.value = '';
      console.log(`Message sent!, message id:${messageId}`);
  });
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Obter um thread de chat da reunião do Teams para um usuário dos Serviços de Comunicação

O link de reunião do Teams e o chat podem ser recuperados usando as APIs do Graph, detalhadas na [documentação do Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true). O SDK de Chamada de Serviços de Comunicação aceita um link de reunião completo do Teams. Esse link é retornado como parte do recurso `onlineMeeting`, acessível na [`joinWebUrl` propriedade](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Com as [APIs do Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true), você também pode obter o `threadId`. A resposta terá um objeto `chatInfo` que contém o `threadID`. 

Você também pode obter as informações da reunião necessárias e a ID do thread na URL **Participar da Reunião** indicada no convite da própria reunião do Teams.
Um link de reunião do Teams é parecido com este: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. A `threadId` estará onde `meeting_chat_thread_id` estiver no link. Verifique se o `meeting_chat_thread_id` está sem escape antes de usar. Ele deve estar no seguinte formato: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>Executar o código

Os usuários do webpack podem usar o `webpack-dev-server` para compilar e executar seu aplicativo. Execute o seguinte comando para empacotar o host de aplicativos em um servidor Web local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Abra o navegador e navegue até http://localhost:8080/. Você deve ver o seguinte:

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="Captura de tela do Aplicativo JavaScript concluído.":::

Insira o link de reunião do Teams e a ID do thread nas caixas de texto. Pressione *Ingressar na reunião do Teams* para participar da reunião. Depois que o usuário do ACS tiver sido admitido na reunião, você poderá bater papo no seu aplicativo de Serviços de Comunicação. Navegue até a caixa na parte inferior da página para começar a conversar.

> [!NOTE] 
> Atualmente, só há suporte para enviar, receber e editar mensagens em cenários de interoperabilidade com o Teams. Ainda não há suporte para outros recursos como indicadores de digitação e adição ou remoção de outros usuários da reunião do Teams pelos usuários dos Serviços de Comunicação.  
