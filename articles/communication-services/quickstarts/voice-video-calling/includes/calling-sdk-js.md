---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 195e8fefbf0dbf3ff55e1bd4d043de772000248b
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103439065"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Um recurso dos Serviços de Comunicação implantado. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um `User Access Token` para habilitar o cliente de chamada. Para obter mais informações sobre [como obter um `User Access Token`](../../access-tokens.md)
- Opcional: conclua o guia de início rápido para [começar a adicionar chamadas ao seu aplicativo](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configurando

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

> [!NOTE]
> Este documento usa a versão 1.0.0-beta.6 da biblioteca de clientes de chamada.

Use o comando `npm install` para instalar as bibliotecas de clientes Comuns e de Chamada dos Serviços de Comunicação do Azure para JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e as interfaces administram alguns dos principais recursos da biblioteca de clientes de Chamada dos Serviços de Comunicação do Azure:

| Name                             | Descrição                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | O CallClient é o ponto de entrada principal para a biblioteca de clientes de Chamada.                                                                       |
| CallAgent                        | O CallAgent é usado para iniciar e gerenciar chamadas.                                                                                            |
| DeviceManager                    | O DeviceManager é usado para gerenciar dispositivos de mídia                                                                                           |
| AzureCommunicationTokenCredential | A classe AzureCommunicationTokenCredential implementa a interface CommunicationTokenCredential, que é usada para criar uma instância do CallAgent. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Inicialize o CallClient, crie o CallAgent e acesse o DeviceManager

Crie uma instância `CallClient`. Você pode configurá-la com opções personalizadas, como uma instância do Agente.
Depois que for criada a instância do `CallClient`, você poderá criar uma instância `CallAgent` chamando o método `createCallAgent` na instância `CallClient`. Isso retorna de modo assíncrono um objeto de instância `CallAgent`.
O método `createCallAgent` usa um `CommunicationTokenCredential` como argumento, que aceita um [token de acesso do usuário](../../access-tokens.md).
Para acessar o `DeviceManager`, uma instância do callAgent precisa ser criada primeiro. Em seguida, você pode usar o método `getDeviceManager` na instância `CallClient` para obter o DeviceManager.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Realizar uma chamada de saída

Para criar e iniciar uma chamada, você precisa usar uma das APIs no CallAgent e fornecer um usuário que você criou por meio da biblioteca de cliente de identidade dos serviços de comunicação.

A criação e o início da chamada são síncronos. A instância de chamada permite que você assine eventos de chamada.

## <a name="place-a-call"></a>Fazer uma chamada

### <a name="place-a-11-call-to-a-user-or-pstn"></a>Realizar uma chamada 1:1 para um usuário ou PSTN
Para fazer uma chamada para outro usuário dos serviços de comunicação, invoque o `startCall` método em `callAgent` e passe o CommunicationUserIdentifier do receptor que você [criou com a biblioteca de identidades dos serviços de comunicação](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Para realizar uma chamada para uma PSTN, invoque o método `startCall` em `callAgent` e passe o PhoneNumberIdentifier do receptor da chamada.
O recurso Serviços de Comunicação precisa estar configurado para permitir a chamada de PSTN.
Ao chamar um número de PSTN, você precisa especificar a sua ID de Chamadas alternativa. Uma ID de Chamadas alternativa refere-se a um número de telefone (com base no padrão E.164) que identifica o chamador em uma chamada PSTN. Por exemplo, quando você fornece uma ID de Chamadas alternativa para a chamada PSTN, esse número de telefone será mostrado ao receptor da chamada quando a chamada for recebida.

> [!WARNING]
> No momento, a chamada PSTN está em versão prévia privada. Para obter acesso, [inscreva-se no programa de usuário pioneiro](https://aka.ms/ACS-EarlyAdopter).
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Realizar uma chamada 1:n com usuários e PSTN
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Realizar uma chamada 1:1 com a câmera de vídeo
> [!WARNING]
> No momento, não pode haver mais de um fluxo de vídeo local de saída.
Para realizar uma chamada de vídeo, você precisa enumerar as câmeras locais usando a API `getCameras()` do deviceManager.
Depois de selecionar a câmera desejada, use-a para construir uma instância `LocalVideoStream` e passá-la no `videoOptions` como um item dentro da matriz `localVideoStream` para o método `startCall`.
Quando a sua chamada for conectada, ela começará automaticamente o envio de um fluxo de vídeo da câmera selecionada para os outros participantes. Isso também se aplica às opções de vídeo Call.Accept() e CallAgent.join().
```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>Ingressar em um grupo
Para iniciar uma nova chamada de grupo ou ingressar em uma chamada de grupo em andamento, use o método 'join' e passe um objeto com uma propriedade `groupId`. O valor deve ser um GUID.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>Ingressar em uma reunião do Teams
Para ingressar em uma reunião do Teams, use o método 'join' e passe um link de reunião ou as coordenadas de uma reunião
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receiving-an-incoming-call"></a>Recebimento de uma chamada de entrada

A instância `CallAgent` emitirá um evento `incomingCall` quando a identidade registrada estiver recebendo uma chamada de entrada. Para escutar esse evento, assine da seguinte maneira:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo
    
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

O evento `incomingCall` fornecerá uma instância `IncomingCall` na qual você pode aceitar ou rejeitar uma chamada.


## <a name="call-management"></a>Gerenciamento de chamadas

Você pode acessar as propriedades da chamada e executar várias operações durante uma chamada para gerenciar as configurações relacionadas ao vídeo e ao áudio.

### <a name="call-properties"></a>Propriedades da chamada
* Obtenha a ID exclusiva (cadeia de caracteres) desta chamada.
```js

const callId: string = call.id;

```

* Para saber mais sobre outros participantes na chamada, inspecione a coleção `remoteParticipant` na instância `call`. A matriz contém objetos de lista `RemoteParticipant`
```js
const remoteParticipants = call.remoteParticipants;
```

* O identificador do chamador se a chamada for de entrada. O identificador é um de um dos tipos `CommunicationIdentifier`
```js

const callerIdentity = call.callerInfo.identifier;

* Get the state of the Call.
```js

const callState = call.state;

```
Isso retorna uma cadeia de caracteres que representa o estado atual de uma chamada:
* 'None' – Estado inicial da chamada
* 'Incoming' – Indica que uma chamada é de entrada. Ela deve ser aceita ou rejeitada
* 'Connecting' – Estado de transição inicial após a chamada ser realizada ou aceita
* 'Ringing' – Em uma chamada de saída, indica que a chamada está tocando para os participantes remotos. No lado deles, o estado é 'Incoming'
* 'EarlyMedia' – Indica um estado no qual um comunicado é reproduzido antes da chamada ser conectada
* 'Connected' – A chamada está conectada
* 'LocalHold' – A chamada é colocada em espera pelo participante local, nenhuma mídia está sendo transmitida entre o ponto de extremidade local e os participantes remotos
* 'RemoteHold' – A chamada é colocada em espera pelo participante remoto, nenhuma mídia está sendo transmitida entre o ponto de extremidade local e os participantes remotos
* 'Disconnecting' – Estado de transição antes que a chamada vá para o estado 'Disconnected'
* 'Disconnected' – Estado final da chamada
  * Se a conexão de rede for perdida, o estado mudará para 'Disconnected' após cerca de dois minutos.

* Para ver por que uma determinada chamada foi encerrada, inspecione a propriedade `callEndReason`.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Para saber se a chamada atual é uma chamada de entrada ou saída, inspecione a propriedade `direction`, que retorna `CallDirection`.
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  Para verificar se o microfone atual está no mudo, inspecione a propriedade `muted`, que retorna `Boolean`.
```js

const muted = call.isMicrophoneMuted;

```

* Para ver se o fluxo de compartilhamento de tela está sendo enviado de um determinado ponto de extremidade, verifique a propriedade `isScreenSharingOn`, que retorna `Boolean`.
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Para inspecionar fluxos de vídeo ativos, verifique a coleção `localVideoStreams`, que contém objetos `LocalVideoStream`
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>Evento de chamada encerrada

A instância `Call` emitirá um evento `callEnded` quando a chamada for encerrada. Para escutar esse evento, assine da seguinte maneira:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Ativar e desativar mudo

Para ativar ou desativar o mudo no ponto de extremidade local, você pode usar as APIs `mute` e `unmute` assíncronas:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Iniciar e parar o envio do vídeo local


Para iniciar um vídeo, você precisa enumerar as câmeras usando o método `getCameras` no objeto `deviceManager`. Em seguida, crie uma instância do `LocalVideoStream` passando a câmera desejada para o método `startVideo` como um argumento:


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

Depois que você começar a enviar o vídeo, uma instância `LocalVideoStream` será adicionada à coleção `localVideoStreams` em uma instância de chamada.

```js

call.localVideoStreams[0] === localVideoStream;

```

Para parar o vídeo local, passe a instância `localVideoStream` disponível na coleção `localVideoStreams`:

```js

await call.stopVideo(localVideoStream);

```

Você poderá alternar para um dispositivo de câmera diferente enquanto o vídeo estiver sendo enviado invocando `switchSource` em uma instância `localVideoStream`:

```js
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);

```

## <a name="remote-participants-management"></a>Gerenciamento de participantes remotos

Todos os participantes remotos são representados pelo tipo `RemoteParticipant` e estão disponíveis por meio da coleção `remoteParticipants` em uma instância de chamada.

### <a name="list-participants-in-a-call"></a>Listar os participantes em uma chamada
A coleção `remoteParticipants` retorna uma lista de participantes remotos na chamada fornecida:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Propriedades do participante remoto
O participante remoto tem um conjunto de propriedades e coleções associadas a ele
#### <a name="communicationidentifier"></a>CommunicationIdentifier
Obtenha o identificador desse participante remoto.
A identidade é um dos tipos do 'CommunicationIdentifier':
```js
const identifier = remoteParticipant.identifier;
```
Pode ser um dos tipos do 'CommunicationIdentifier':
  * { communicationUserId: '<ACS_USER_ID'> } – Objeto que representa o usuário do ACS
  * { phoneNumber: '<E.164>' } – Objeto que representa o número de telefone no formato E.164
  * { microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" } – Objeto que representa o usuário do Teams

#### <a name="state"></a>Estado
Obtém o estado deste participante remoto.
```js

const state = remoteParticipant.state;
```
O estado pode ser um dos seguintes
* 'Idle' – Estado inicial
* 'Connecting' – Estado de transição enquanto o participante está se conectando à chamada
* 'Ringing'– O participante está tocando
* 'Connected' – O participante está conectado à chamada
* 'Hold' – O participante está em espera
* 'EarlyMedia' – O comunicado é reproduzido antes de o participante estar conectado à chamada
* 'Disconnected' – O estado final, em que o participante está desconectado da chamada
  * Se o participante remoto perder a conectividade de rede, o estado do participante remoto mudará para 'Disconnected' após cerca de dois minutos.

#### <a name="call-end-reason"></a>Motivo do término da chamada
Para saber por que o participante saiu da chamada, inspecione a propriedade `callEndReason`:
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>Está no mudo
Para verificar se este participante remoto está com o mudo ativado ou não, inspecione a propriedade `isMuted`, que retorna `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>Está falando
Para verificar se este participante remoto está falando ou não, inspecione a propriedade `isSpeaking`, que retorna `Boolean`
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>Fluxos de vídeo
Para inspecionar todos os fluxos de vídeo que um determinado participante está enviando nesta chamada, verifique a coleção `videoStreams`, que contém objetos `RemoteVideoStream`
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Adicionar um participante a uma chamada

Para adicionar um participante a uma chamada (um usuário ou um número de telefone), você pode invocar `addParticipant`.
Forneça um dos tipos de 'Identifier'.
Isso retornará de modo síncrono a instância do participante remoto.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>Remover um participante de uma chamada

Para remover um participante de uma chamada (um usuário ou um número de telefone), você pode invocar `removeParticipant`.
Você precisa passar um dos tipos de 'Identifier'. Isso se resolverá de modo assíncrono quando o participante for removido da chamada.
O participante também será removido da coleção `remoteParticipants`.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Renderizar fluxos de vídeo do participante remoto

Para listar os fluxos de vídeo e os fluxos de tela de compartilhamento de participantes remotos, inspecione as coleções `videoStreams`:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

Para renderizar um `RemoteVideoStream`, você precisa assinar um evento `isAvailableChanged`.
Se a propriedade `isAvailable` mudar para `true`, será um sinal de que um participante remoto está enviando um fluxo.
Quando isso acontecer, crie uma instância de `Renderer` e uma instância `RendererView` usando o método assíncrono `createView`.  Em seguida, você poderá anexar `view.target` a qualquer elemento de interface do usuário.
Sempre que a disponibilidade de um fluxo remoto for alterada, você poderá optar por destruir todo o renderizador, um `RendererView` específico ou mantê-los, mas isso resultará na exibição do quadro de vídeo em branco.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let renderer: Renderer = new Renderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            renderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Propriedades de fluxo de vídeo remoto
Os fluxos de vídeo remotos têm as seguintes propriedades:

* `Id` – ID de um fluxo de vídeo remoto
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` – Tamanho (largura/altura) de um fluxo de vídeo remoto
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` – Pode ser 'Video' ou 'ScreenSharing'
```js
const type: MediaStreamType = remoteVideoStream.mediaStreamType;
```
* `isAvailable` – Indica se o ponto de extremidade do participante remoto está enviando o fluxo ativamente
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Métodos e propriedades do renderizador

* Crie uma instância `RendererView` que pode ser anexada posteriormente na interface do usuário do aplicativo para renderizar o fluxo de vídeo remoto.
```js
renderer.createView()
```

* Descarte o renderizador e todas as instâncias `RendererView` associadas.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>Métodos e propriedades do RendererView
Ao criar um `RendererView`, você pode especificar as propriedades `scalingMode` e `isMirrored`.
O modo de escala pode ser 'Stretch', 'Crop' ou 'Fit'. Se `isMirrored` for especificado, o fluxo renderizado será invertido verticalmente.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```
Qualquer instância `RendererView` específica tem uma propriedade `target` que representa a superfície de renderização. Isso deve ser anexado à interface do usuário do aplicativo:
```js
document.body.appendChild(rendererView.target);
```

Posteriormente, você pode atualizar o modo de escala invocando o método `updateScalingMode`.
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Gerenciamento de dispositivo

O `DeviceManager` permite que você enumere dispositivos locais que podem ser usados em uma chamada para transmitir os seus fluxos de áudio/vídeo. Ele também permite solicitar permissão de um usuário para acessar o microfone e a câmera usando a API do navegador nativo.

Você pode acessar o `deviceManager` chamando o método `callClient.getDeviceManager()`.
> [!WARNING]
> Atualmente, um objeto `callAgent` precisa criar uma instância primeiro para obter acesso ao DeviceManager

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Enumerar dispositivos locais

Para acessar dispositivos locais, você pode usar métodos de enumeração no Gerenciador de Dispositivos. A enumeração é uma ação assíncrona.

```js

//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Definir o microfone/alto-falante padrão

O gerenciador de dispositivos permite que você defina um dispositivo padrão que será usado ao iniciar uma chamada.
Se os padrões do cliente não estiverem definidos, os Serviços de Comunicação retornarão para os padrões do SO.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Visualização da câmera local

Você pode usar `DeviceManager` e `Renderer` para começar a renderizar fluxos da sua câmera local. Esse fluxo não será enviado para outros participantes; ele é um feed de visualização local. Essa é uma ação assíncrona.

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Solicitar permissão de acesso à câmera/microfone

Solicite que um usuário conceda permissões de câmera/microfone com o seguinte:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```
Isso será resolvido de modo assíncrono com um objeto que indicará se as permissões `audio` e `video` foram concedidas:
```js
console.log(result.audio);
console.log(result.video);
```


## <a name="call-recording-management"></a>Gerenciamento de gravação de chamadas

A gravação de chamadas é um recurso estendido da API `Call` principal. Primeiro, você precisa obter o objeto de API do recurso de gravação:

```js
const callRecordingApi = call.api(Features.Recording);
```

Em seguida, verifique se a chamada está sendo gravada, inspecione a propriedade `isRecordingActive` de `callRecordingApi`, que retorna `Boolean`.

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

Você também pode assinar as alterações de gravação:

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="call-transfer-management"></a>Gerenciamento de transferência de chamadas

A transferência de chamadas é um recurso estendido da API `Call` principal. Primeiro, você precisa obter o objeto de API do recurso de transferência:

```js
const callTransferApi = call.api(Features.Transfer);
```

A transferência de chamadas envolve três partes: o *emitente da transferência*, o *destinatário da transferência* e o *destino da transferência*. O fluxo de transferência está funcionando da seguinte maneira:

1. Já existe uma chamada conectada entre o *emitente da transferência* e o *destinatário da transferência*
2. O *emitente da transferência* decide transferir a chamada (*destinatário da transferência* -> *destino da transferência*)
3. API `transfer` de chamada do *emitente da transferência*
4. O *destinatário da transferência* decide se deve realizar `accept` ou `reject` da solicitação de transferência para o *destino de transferência* por meio do evento `transferRequested`.
5. O *destino de transferência* receberá uma chamada de entrada somente se o *destinatário da transferência* tiver realizado `accept` da solicitação de transferência

### <a name="transfer-terminology"></a>Terminologia de transferência

- Emitente da transferência – Aquele que inicia a solicitação de transferência
- Destinatário da transferência – Aquele que está sendo transferido pelo emitente da transferência para o destino de transferência
- Destino de transferência – O destino para o qual ele está sendo transferido

Para transferir a chamada atual, você pode usar a API síncrona de `transfer`. O `transfer` usa um `TransferCallOptions` opcional, que permite que você defina o sinalizador `disableForwardingAndUnanswered`:

- `disableForwardingAndUnanswered` = false – Se o *destino da transferência* não responder à chamada de transferência, ele seguirá as configurações não respondidas e de encaminhamento do *destino de transferência*
- `disableForwardingAndUnanswered` = true – Se o *destino da transferência* não responder à chamada de transferência, a tentativa de transferência será encerrada

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

A transferência permite que você assine os eventos `transferStateChanged` e `transferRequested`. O evento `transferRequsted` vem da instância `call`, do evento `transferStateChanged` e da transferência `state` e o `error` vem da instância `transfer`

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

O destinatário da transferência pode aceitar ou rejeitar a solicitação de transferência iniciada pelo emitente da transferência no evento `transferRequested` por meio do `accept()` ou do `reject()` no `transferRequestedEventArgs`. Você pode acessar as informações do `targetParticipant` e os métodos `accept` e `reject` em `transferRequestedEventArgs`.

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="eventing-model"></a>Modelo de evento
Você precisa inspecionar os valores atuais e assine os eventos de atualização para valores futuros.

### <a name="properties"></a>Propriedades

```js
// Inspect current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Coleções
```js
// Inspect current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});



// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
