---
title: Guia de início rápido – Adicionar uma chamada de vídeo ao seu aplicativo (JavaScript)
titleSuffix: An Azure Communication Services quickstart
description: Neste guia de início rápido, você aprenderá a adicionar as funcionalidades de chamada de vídeo ao seu aplicativo usando os Serviços de Comunicação do Azure.
author: xumo-95
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5b7fd8e8cd5bd3ab0f15115365ed057fc67f1204
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604423"
---
# <a name="quickstart-add-11-video-calling-to-your-app-javascript"></a>Guia de início rápido: Adicionar uma chamada de vídeo 1:1 ao seu aplicativo (JavaScript)

## <a name="download-code"></a>Código de download

Encontre o código finalizado para este guia de início rápido no [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling)

## <a name="prerequisites"></a>Pré-requisitos
- Obtenha uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Versões de LTS Ativo e de LTS de Manutenção do [Node.js](https://nodejs.org/en/) (8.11.1 e 10.14.1)
- Crie um recurso ativo dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../create-communication-resource.md?pivots=platform-azp&tabs=windows).
- Crie um Token de Acesso do Usuário para criar uma instância do cliente de chamada. [Saiba como criar e gerenciar os tokens de acesso do usuário](../access-tokens.md?pivots=programming-language-csharp).

## <a name="setting-up"></a>Configurando
### <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js
Abra o terminal ou a janela de comando para criar um diretório para seu aplicativo e navegue até ele.
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>Instalar o pacote
Use o comando `npm install` para instalar o SDK de Chamada dos Serviços de Comunicação do Azure para JavaScript.

> [!IMPORTANT]
> Este guia de início rápido usa a versão `1.0.0.beta-10` do SDK da Chamada dos Serviços de Comunicação do Azure. 


```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

Este guia de início rápido usa o webpack para agrupar os ativos do aplicativo. Execute o seguinte comando para instalar os pacotes npm `webpack`, `webpack-cli` e `webpack-dev-server` e listá-los como dependências de desenvolvimento no `package.json`:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```
Crie um arquivo `index.html` no diretório raiz do projeto. Usaremos esse arquivo para configurar um layout básico que permitirá que o usuário faça uma chamada de vídeo 1:1.

O código é o seguinte:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - 1:1 Video Calling Sample</title>
</head>

<body>
    <h4>Azure Communication Services</h4>
    <h1>1:1 Video Calling Quickstart</h1>
    <input 
    id="callee-id-input"
    type="text"
    placeholder="Who would you like to call?"
    style="margin-bottom:1em; width: 200px;"
    />

    <div>
    <button id="call-button" type="button" disabled="true">
        start call
    </button>
        &nbsp;
    <button id="hang-up-button" type="button" disabled="true">
        hang up
    </button>
        &nbsp;
    <button id="start-Video" type="button" disabled="true">
        start video
    </button>
        &nbsp;
    <button id="stop-Video" type="button" disabled="true">
        stop video
    </button>     
    </div>

    <div>Local Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;">
      <div id="myVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>     
    </div>
    <div>Remote Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;"> 
      <div id="remoteVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>
    </div>

    <script src="./bundle.js"></script>
</body>
</html>
```

Crie um arquivo no diretório raiz do projeto chamado `client.js` para conter a lógica do aplicativo deste guia de início rápido. Adicione o código a seguir para importar o cliente de chamada e obter referências aos elementos DOM.

```JavaScript
import { CallClient, CallAgent, VideoStreamRenderer, LocalVideoStream } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
const stopVideoButton = document.getElementById("stop-Video");
const startVideoButton = document.getElementById("start-Video");

let placeCallOptions;
let deviceManager;
let localVideoStream;
let rendererLocal;
let rendererRemote;
```
## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces cuidam de alguns dos principais recursos do SDK de Chamada da Interface do Usuário dos Serviços de Comunicação do Azure:

| Nome      | Descrição | 
| :---        |    :----   |
| CallClient  | O CallClient é o ponto de entrada principal para o SDK de Chamada.      |
| CallAgent  | O CallAgent é usado para iniciar e gerenciar chamadas.        |
| DeviceManager | O DeviceManager é usado para gerenciar os dispositivos de mídia.    |
| AzureCommunicationTokenCredential | A classe AzureCommunicationTokenCredential implementa a interface CommunicationTokenCredential, que é usada para criar uma instância do CallAgent.        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>Autenticar o cliente e acessar o DeviceManager

Você precisa substituir <USER_ACCESS_TOKEN> por um token de acesso de usuário válido para o seu recurso. Confira a documentação do token de acesso do usuário se você ainda não tiver um token disponível. Usando o `CallClient`, inicialize uma instância `CallAgent` com um `CommunicationUserCredential` que nos permitirá realizar e receber chamadas. Para acessar o `DeviceManager`, uma instância do callAgent precisa ser criada primeiro. Em seguida, você pode usar o método `getDeviceManager` na instância `CallClient` para obter o `DeviceManager`.

Adicione o seguinte código a `client.js`:

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}
init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>Realizar uma chamada de vídeo de saída 1:1 para um usuário

Adicione um ouvinte de eventos para iniciar uma chamada quando o `callButton` for clicado:

Primeiro, você precisa enumerar as câmeras locais usando a API `getCameraList` do deviceManager. Neste guia de início rápido, estamos usando a primeira câmera na coleção. Quando a câmera desejada for selecionada, uma instância de LocalVideoStream será construída e transmitida em `videoOptions` como um item dentro da matriz localVideoStream ao método de chamada. Quando a sua chamada for conectada, ela começará automaticamente o envio de um fluxo de vídeo para o outro participante. 

```JavaScript
callButton.addEventListener("click", async () => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};

    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;

    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        placeCallOptions
    );

    subscribeToRemoteParticipantInCall(call);

    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```  
Para renderizar um `LocalVideoStream`, você precisa criar uma instância de `VideoStreamRenderer` e uma instância `VideoStreamRendererView` usando o método `createView` assíncrono. Em seguida, você poderá anexar `view.target` a qualquer elemento de interface do usuário. 

```JavaScript
async function localVideoView() {
    rendererLocal = new VideoStreamRenderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
Todos os participantes remotos estão disponíveis por meio da coleção `remoteParticipants` em uma instância de chamada. Você precisa escutar o evento `remoteParticipantsUpdated` para ser notificado de quando um novo participante remoto for adicionado à chamada. Você também precisa iterar a coleção `remoteParticipants` para assinar cada uma delas a fim de assinar os fluxos de vídeo. 

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToParticipantVideoStreams(p);
        })
    }); 
    callInstance.remoteParticipants.forEach( p => {
        subscribeToParticipantVideoStreams(p);
    })
}
```
Assine o evento `videoStreamsUpdated` para processar fluxos de vídeos adicionados de participantes remotos. Inspecione as coleções `videoStreams` para listar os fluxos de cada participante enquanto examina a coleção `remoteParticipants` da chamada atual.

```JavaScript
function subscribeToParticipantVideoStreams(remoteParticipant) {
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
}
```
Você precisa assinar um evento `isAvailableChanged` para renderizar o `remoteVideoStream`. Se a propriedade `isAvailable` mudar para `true`, será um sinal de que um participante remoto está enviando um fluxo. Sempre que a disponibilidade de um fluxo remoto for alterada, você poderá optar por destruir todo o `Renderer`, um `RendererView` específico ou mantê-los, mas isso resultará na exibição do quadro de vídeo em branco.
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            remoteVideoView(remoteVideoStream);
        } else {
            rendererRemote.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        remoteVideoView(remoteVideoStream);
    }
}
```
Para renderizar um `RemoteVideoStream`, você precisa criar uma instância de `VideoStreamRenderer` e uma instância `VideoStreamRendererView` usando o método `createView` assíncrono. Em seguida, você poderá anexar `view.target` a qualquer elemento de interface do usuário. 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new VideoStreamRenderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```
## <a name="receive-an-incoming-call"></a>Receber uma chamada de entrada
Para lidar com as chamadas de entrada, você precisa escutar o evento `incomingCall` de `callAgent`. Quando houver uma chamada de entrada, você precisará enumerar câmeras locais e construir uma instância `LocalVideoStream` para enviar um fluxo de vídeo para o outro participante. Você também precisa assinar `remoteParticipants` para gerenciar fluxos de vídeo remotos. Você pode aceitar ou rejeitar a chamada por meio da instância `incomingCall`. 

Coloque a implementação no `init()` para administrar chamadas de entrada. 

```JavaScript
callAgent.on('incomingCall', async e => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    localVideoView();

    stopVideoButton.disabled = false;
    callButton.disabled = true;
    hangUpButton.disabled = false;

    const addedCall = await e.incomingCall.accept({videoOptions: {localVideoStreams:[localVideoStream]}});
    call = addedCall;

    subscribeToRemoteParticipantInCall(addedCall);  
});
```
## <a name="end-the-current-call"></a>Encerrar a chamada atual
Adicione um ouvinte de eventos para encerrar a chamada atual quando o `hangUpButton` for clicado:
```JavaScript
hangUpButton.addEventListener("click", async () => {
    // dispose of the renderers
    rendererLocal.dispose();
    rendererRemote.dispose();
    // end the current call
    await call.hangUp();
    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
## <a name="subscribe-to-call-updates"></a>Assinar atualizações de chamada
Você precisa assinar o evento quando o participante remoto terminar a chamada para descartar os renderizadores de vídeo e os estados do botão de alternância. 

Coloque a implementação em init() para assinar o evento `callsUpdated`. 
 ```JavaScript 
callAgent.on('callsUpdated', e => {
    e.removed.forEach(removedCall => {
        // dispose of video renders
        rendererLocal.dispose();
        rendererRemote.dispose();
        // toggle button states
        hangUpButton.disabled = true;
        callButton.disabled = false;
        stopVideoButton.disabled = true;
    })
})
```

## <a name="start-and-end-video-during-the-call"></a>Iniciar e encerrar o vídeo durante a chamada
Você pode interromper o vídeo durante a chamada atual adicionando um ouvinte de eventos ao botão Parar Vídeo para descartar o renderizador de `localVideoStream`. 
 ```JavaScript       
stopVideoButton.addEventListener("click", async () => {
    await call.stopVideo(localVideoStream);
    rendererLocal.dispose();
    startVideoButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
Você pode adicionar um ouvinte de eventos ao botão Iniciar Vídeo para ativar o vídeo novamente para o momento em que ele foi interrompido durante a chamada atual. 
```JavaScript
startVideoButton.addEventListener("click", async () => {
    await call.startVideo(localVideoStream);
    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;
});
```
## <a name="run-the-code"></a>Executar o código
Use `webpack-dev-server` para criar e executar o seu aplicativo. Execute o seguinte comando para agrupar o host de aplicativos em um servidor Web local:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Abra o navegador e navegue até http://localhost:8080/. Você deve ver o seguinte:

:::image type="content" source="./media/javascript/1-on-1-video-calling.png" alt-text="Página de chamada de vídeo 1:1":::

Você pode fazer uma chamada de vídeo de saída 1:1 fornecendo uma ID de usuário no campo de texto e clicando no botão Iniciar Chamada. 

## <a name="sample-code"></a>Exemplo de código
É possível baixar o aplicativo de exemplo do [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling).

## <a name="clean-up-resources"></a>Limpar os recursos
Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte os seguintes artigos:

- Confira nosso [exemplo de chamada da Web](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Conheça os recursos do [SDK de Chamada](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Saiba mais sobre [como a chamada funciona](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)

