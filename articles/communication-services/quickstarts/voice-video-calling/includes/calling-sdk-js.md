---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 7d391998e7f20cff0f77f6aab7938bc375f75c9e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616540"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Um recurso dos Serviços de Comunicação implantado. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um `User Access Token` para habilitar o cliente de chamada. Para obter mais informações sobre [como obter um `User Access Token`](../../access-tokens.md)
- Opcional: conclua o guia de início rápido para [começar a adicionar a chamada ao seu aplicativo](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configurando

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Use o `npm install` comando para instalar a chamada dos serviços de comunicação do Azure e as bibliotecas de cliente comuns para JavaScript.

```console
npm install @azure/communication-calling --save
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e as interfaces administram alguns dos principais recursos da biblioteca de clientes de Chamada dos Serviços de Comunicação do Azure:

| Name                             | Descrição                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | O CallClient é o ponto de entrada principal para a biblioteca de clientes de Chamada.                                                                       |
| CallAgent                        | O CallAgent é usado para iniciar e gerenciar chamadas.                                                                                            |
| Dispositivomanager                    | O DeviceManager é usado para gerenciar dispositivos de mídia                                                                                           |
| AzureCommunicationTokenCredential | A classe AzureCommunicationTokenCredential implementa a interface CommunicationTokenCredential, que é usada para instanciar o CallAgent. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Inicialize o CallClient, crie CallAgent e acesse o DeviceManager

Instanciar uma nova `CallClient` instância. Você pode configurá-lo com opções personalizadas, como uma instância de agente.
Depois que um `CallClient` é instanciado, você pode criar uma `CallAgent` instância chamando o `createCallAgent` método na `CallClient` instância. Isso retorna de forma assíncrona um `CallAgent` objeto de instância.
O `createCallAgent` método usa um `CommunicationTokenCredential` como argumento, que aceita um [token de acesso do usuário](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).
Para acessar a `DeviceManager` instância de callAgent, primeiro deve ser criado. Em seguida, você pode usar o `getDeviceManager` método na `CallClient` instância para obter o DeviceManager.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Fazer uma chamada de saída

Para criar e iniciar uma chamada, você precisa usar uma das APIs no CallAgent e fornecer um usuário que você criou por meio da biblioteca do cliente de administração dos serviços de comunicação.

A criação e o início da chamada são síncronos. A instância de chamada permite que você assine eventos de chamada.

## <a name="place-a-call"></a>Fazer uma chamada

### <a name="place-a-11-call-to-a-user-or-pstn"></a>Coloque uma chamada de 1:1 para um usuário ou PSTN
Para fazer uma chamada para outro usuário dos serviços de comunicação, invoque o `call` método em `callAgent` e passe o CommunicationUserIdentifier do receptor:

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.call([userCallee]);
```

Para fazer uma chamada para uma PSTN, invoque o `call` método em `callAgent` e passe o PhoneNumberIdentifier do receptor.
O recurso de serviços de comunicação deve ser configurado para permitir a chamada de PSTN.
Ao chamar um número PSTN, você deve especificar sua ID de chamador alternativa.
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.call([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Coloque uma chamada 1: n com usuários e PSTN
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.call([userCallee, pstnCallee], {alternateCallerId});
```

### <a name="place-a-11-call-with-video-camera"></a>Coloque uma chamada de 1:1 com câmera de vídeo
> [!WARNING]
> No momento, não pode haver mais de um fluxo de vídeo local de saída.
Para fazer uma chamada de vídeo, você precisa enumerar câmeras locais usando a API do DeviceManager `getCameraList` .
Depois de selecionar a câmera desejada, use-a para construir uma `LocalVideoStream` instância e passá-la no `videoOptions` como um item dentro da `localVideoStream` matriz para o `call` método.
Quando sua chamada for conectada, ele iniciará automaticamente o envio de um fluxo de vídeo da câmera selecionada para os outros participantes. Isso também se aplica às opções de vídeo Call. Accept () e CallAgent. Join ().
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>Ingressar em um grupo
Para iniciar uma nova chamada de grupo ou ingressar em uma chamada de grupo em andamento, use o método ' join ' e passe um objeto com uma `groupId` propriedade. O valor deve ser um GUID.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>Participe de uma reunião de equipes
Para ingressar em uma reunião de equipes, use o método ' join ' e passe um link de reunião ou as coordenadas de uma reunião
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

## <a name="receiving-an-incoming-call"></a>Recebendo uma chamada de entrada

A `CallAgent` instância emite um `incomingCall` evento quando a identidade registrada está recebendo uma chamada de entrada. Para escutar esse evento, assine o seguinte modo:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

O `incomingCall` evento fornecerá com uma instância do `IncomingCall` na qual você pode aceitar ou rejeitar uma chamada.


## <a name="call-management"></a>Gerenciamento de chamadas

Você pode acessar as propriedades da chamada e executar várias operações durante uma chamada para gerenciar as configurações relacionadas ao vídeo e ao áudio.

### <a name="call-properties"></a>Propriedades da chamada
* Obtenha a ID exclusiva (cadeia de caracteres) para esta chamada.
```js

const callId: string = call.id;

```

* Para saber mais sobre outros participantes na chamada, inspecione a `remoteParticipant` coleção na `call` instância. Matriz contém objetos de lista `RemoteParticipant`
```js
const remoteParticipants = call.remoteParticipants;
```

* A identidade do chamador se a chamada for recebida. Identity é um dos `CommunicationIdentifier` tipos
```js

const callerIdentity = call.callerInfo.identity;

```

* Obtenha o estado da chamada.
```js

const callState = call.state;

```
Isso retorna uma cadeia de caracteres que representa o estado atual de uma chamada:
* ' Nenhum '-estado de chamada inicial
* ' Entrada '-indica que uma chamada é recebida, que deve ser aceita ou rejeitada
* "Conectando"-estado de transição inicial depois que a chamada é colocada ou aceita
* "Tocando" – para uma chamada de saída – indica que a chamada está tocando para os participantes remotos, é "de entrada" no lado deles
* ' EarlyMedia ' – indica um estado no qual um comunicado é reproduzido antes que a chamada seja conectada
* ' Conectado '-a chamada está conectada
* ' Hold '-a chamada é colocada em espera, nenhuma mídia está fluindo entre o ponto de extremidade local e os participantes remotos
* ' Desconectando '-estado de transição antes que a chamada vá para o estado ' desconectado '
* ' Desconectado '-estado de chamada final
  * Se a conexão de rede for perdida, o estado vai para ' desconectado ' após cerca de 2 minutos.

* Para ver por que uma determinada chamada foi encerrada, inspecione a `callEndReason` propriedade.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Para saber se a chamada atual é uma chamada de entrada ou saída, inspecione a `direction` propriedade, que retorna `CallDirection` .
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  Para verificar se o microfone atual está mudo, inspecione a `muted` propriedade e ela retorna `Boolean` .
```js

const muted = call.isMicrophoneMuted;

```

* Para ver se o fluxo de compartilhamento de tela está sendo enviado de um determinado ponto de extremidade, verifique a `isScreenSharingOn` propriedade, retorna `Boolean` .
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Para inspecionar fluxos de vídeo ativos, verifique a `localVideoStreams` coleção, se ele contém `LocalVideoStream` objetos
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>Evento chamar finalizado

A `Call` instância emite um `callEnded` evento quando a chamada termina. Para escutar esse evento, assine o seguinte modo:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Ativar mudo e mudo

Para ativar mudo ou desativar mudo do ponto de extremidade local, você pode usar as `mute` `unmute` APIs assíncronas do e do:

```js

//mute local device 
await call.mute();

//unmute local device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Iniciar e parar de enviar vídeo local


Para iniciar um vídeo, você precisa enumerar câmeras usando o `getCameraList` método no `deviceManager` objeto. Em seguida, crie uma nova instância do `LocalVideoStream` passando a câmera desejada para o `startVideo` método como um argumento:


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

Depois que você começar a enviar o vídeo com êxito, uma `LocalVideoStream` instância será adicionada à `localVideoStreams` coleção em uma instância de chamada.

```js

call.localVideoStreams[0] === localVideoStream;

```

Para parar o vídeo local, passe a `localVideoStream` instância disponível na `localVideoStreams` coleção:

```js

await call.stopVideo(localVideoStream);

```

Você pode alternar para um dispositivo de câmera diferente enquanto o vídeo está sendo enviado invocando `switchSource` em uma `localVideoStream` instância:

```js
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```

## <a name="remote-participants-management"></a>Gerenciamento de participantes remotos

Todos os participantes remotos são representados por `RemoteParticipant` tipo e disponíveis por meio `remoteParticipants` de coleção em uma instância de chamada.

### <a name="list-participants-in-a-call"></a>Listar os participantes em uma chamada
A `remoteParticipants` coleção retorna uma lista de participantes remotos na chamada fornecida:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Propriedades do participante remoto
O participante remoto tem um conjunto de propriedades e coleções associadas a ele
#### <a name="communicationidentifier"></a>CommunicationIdentifier
Obtenha o identificador para este participante remoto.
```js
const identifier = remoteParticipant.identifier;
```
Pode ser um dos tipos ' CommunicationIdentifier ':
  * {communicationUserId: ' <ACS_USER_ID ' >}-objeto que representa o usuário ACS
  * {phoneNumber: ' <E. 164> '}-objeto que representa o número de telefone no formato E. 164
  * {microsoftTeamsUserId: ' <TEAMS_USER_ID> ', isanônimo?: booliano; nuvem?: "Public" | "DoD" | "gcch"}-objeto que representa o usuário de equipes

#### <a name="state"></a>Estado
Obter o estado deste participante remoto.
```js

const state = remoteParticipant.state;
```
O estado pode ser um de
* ' Idle '-estado inicial
* "Conectando"-estado de transição enquanto o participante está se conectando à chamada
* ' Conectado '-o participante está conectado à chamada
* ' Hold '-o participante está em espera
* ' EarlyMedia '-o comunicado é reproduzido antes de o participante estar conectado à chamada
* ' Desconectado '-estado final-o participante está desconectado da chamada
  * Se o participante remoto perder a conectividade de rede, o estado do participante remoto vai para ' desconectado ' após cerca de 2 minutos.

#### <a name="call-end-reason"></a>Motivo do término da chamada
Para saber por que o participante saiu da chamada, inspecione a `callEndReason` Propriedade:
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>Está sem áudio
Para verificar se esse participante remoto está mudo ou não, inspecione a `isMuted` propriedade, ele retorna `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>Está falando
Para verificar se esse participante remoto está falando ou não, inspecione a `isSpeaking` propriedade que ele retorna `Boolean`
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>Fluxos de vídeo
Para inspecionar todos os fluxos de vídeo que um determinado participante está enviando nesta chamada, verifique a `videoStreams` coleção e contém `RemoteVideoStream` objetos
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Adicionar um participante a uma chamada

Para adicionar um participante a uma chamada (um usuário ou um número de telefone), você pode invocar `addParticipant` .
Forneça um dos tipos de "identificador".
Isso retornará de forma síncrona a instância de participante remota.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>Remover participante de uma chamada

Para remover um participante de uma chamada (um usuário ou um número de telefone), você pode invocar `removeParticipant` .
Você precisa passar um dos tipos ' identifier ' que isso resolverá de forma assíncrona quando o participante for removido da chamada.
O participante também será removido da `remoteParticipants` coleção.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Renderizar fluxos de vídeo do participante remoto

Para listar fluxos de vídeo e fluxos de tela de compartilhamento de participantes remotos, inspecione as `videoStreams` coleções:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
Para renderizar um `RemoteVideoStream` , você precisa assinar um `isAvailableChanged` evento.
Se a `isAvailable` propriedade for alterada para `true` , um participante remoto estará enviando um fluxo.
Quando isso acontecer, crie uma nova instância do `Renderer` e, em seguida, crie uma nova `RendererView` instância usando o `createView` método assíncrono.  Em seguida, você pode anexar `view.target` a qualquer elemento de interface do usuário.
Sempre que a disponibilidade de um fluxo remoto for alterada, você poderá optar por destruir todo o renderizador, um específico `RendererView` ou mantê-los, mas isso resultará na exibição do quadro de vídeo em branco.

```js
let renderer: Renderer = new Renderer(remoteParticipantStream);
const displayVideo = () => {
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>Propriedades de fluxo de vídeo remoto
Fluxos de vídeo remotos têm as seguintes propriedades:

* `Id` -ID de um fluxo de vídeo remoto
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` -tamanho (largura/altura) de um fluxo de vídeo remoto
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` -pode ser ' vídeo ' ou ' ScreenSharing '
```js
const type: MediaStreamType = remoteVideoStream.type;
```
* `isAvailable` -Indica se o ponto de extremidade do participante remoto está enviando o fluxo ativamente
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Métodos e propriedades do processador

* Crie uma `RendererView` instância que pode ser anexada posteriormente na interface do usuário do aplicativo para renderizar o fluxo de vídeo remoto.
```js
renderer.createView()
```

* Descarte do renderizador e todas as `RendererView` instâncias associadas.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>Métodos e propriedades do RendererView
Ao criar um `RendererView` , você pode especificar `scalingMode` e `mirrored` Propriedades.
O modo de dimensionamento pode ser ' Stretch ', ' cortar ' ou ' ajustar ' se `Mirrored` for especificado, o fluxo renderizado será invertido verticalmente.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
Qualquer `RendererView` instância específica tem uma `target` propriedade que representa a superfície de renderização. Isso deve ser anexado na interface do usuário do aplicativo:
```js
document.body.appendChild(rendererView.target);
```

Posteriormente, você pode atualizar o modo de dimensionamento invocando o `updateScalingMode` método.
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Gerenciamento de dispositivo

`DeviceManager` permite que você enumere dispositivos locais que podem ser usados em uma chamada para transmitir seus fluxos de áudio/vídeo. Ele também permite solicitar permissão de um usuário para acessar o microfone e a câmera usando a API do navegador nativo.

Você pode acessar o `deviceManager` ao chamar o `callClient.getDeviceManager()` método.
> [!WARNING]
> Atualmente, um `callAgent` objeto deve ser instanciado primeiro para obter acesso ao DeviceManager

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Enumerar dispositivos locais

Para acessar dispositivos locais, você pode usar métodos de enumeração no Gerenciador de Dispositivos. A enumeração é uma ação síncrona.

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Definir microfone/orador padrão

O Gerenciador de dispositivos permite que você defina um dispositivo padrão que será usado ao iniciar uma chamada.
Se os padrões do cliente não estiverem definidos, os serviços de comunicação retornarão para OS padrões do sistema operacional.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceManager.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Visualização da câmera local

Você pode usar `DeviceManager` e `Renderer` para começar a renderizar fluxos de sua câmera local. Este fluxo não será enviado para outros participantes; é um feed de visualização local. Esta é uma ação assíncrona.

```js
const localVideoDevice = deviceManager.getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Solicitar permissão para câmera/microfone

Solicite que um usuário conceda permissões de câmera/microfone com o seguinte:

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
Isso será resolvido de forma assíncrona com um objeto que indica se `audio` `video` as permissões foram concedidas:
```js
console.log(result.audio);
console.log(result.video);
```

Você pode inspecionar o estado de permissão atual para um determinado tipo chamando `getPermissionState` :

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="call-recording-management"></a>Gerenciamento de registros de chamadas

A gravação de chamadas é um recurso estendido da `Call` API principal. Primeiro, você precisa obter o objeto de API do recurso de gravação:

```js
const callRecordingApi = call.api(Features.Recording);
```

Em seguida, para pode verificar se a chamada está sendo registrada, inspecione a `isRecordingActive` propriedade de `callRecordingApi` , ela retorna `Boolean` .

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

A transferência de chamadas é um recurso estendido da `Call` API principal. Primeiro, você precisa obter o objeto de API do recurso de transferência:

```js
const callTransferApi = call.api(Features.Transfer);
```

A transferência de chamadas envolve o transpedidor de *transferência*, *transferência* e *destino* de três partes. O fluxo de transferência está funcionando da seguinte maneira:

1. Já existe uma chamada conectada entre o *transferítor* e o *transfere*
2. o *transferítor* decide transferir a chamada (destino de transferência do *transfere*  ->  )
3. API de chamada do *transferítor* `transfer`
4. *transferir* para decidir se a `accept` solicitação de transferência `reject` deve ser *transferida* ou não por meio do `transferRequested` evento.
5. o *destino de transferência* receberá uma chamada de entrada somente se o *transfere* tiver feito `accept` a solicitação de transferência

### <a name="transfer-terminology"></a>Terminologia de transferência

- Transferência-aquele que inicia a solicitação de transferência
- Transferência-aquele que está sendo transferido pelo transferidar para o destino de transferência
- Destino de transferência-aquele que é o destino que está sendo transferido para

Para transferir a chamada atual, você pode usar a `transfer` API síncrona. `transfer` o usa opcional `TransferCallOptions` , o que permite que você defina o `disableForwardingAndUnanswered` sinalizador:

- `disableForwardingAndUnanswered` = false – se o *destino da transferência* não responder à chamada de transferência, ele seguirá as configurações de encaminhamento de destino de *transferência* e não respondidas
- `disableForwardingAndUnanswered` = true – se o *destino da transferência* não responder à chamada de transferência, a tentativa de transferência será encerrada

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

A transferência permite que você assine `transferStateChanged` e `transferRequested` eventos. `transferRequsted` o evento vem de `call` instância, `transferStateChanged` evento e transferência `state` e `error` vem da `transfer` instância

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

O transferência pode aceitar ou rejeitar a solicitação de transferência iniciada pelo transportador no `transferRequested` evento via `accept()` ou `reject()` no `transferRequestedEventArgs` . Você pode acessar `targetParticipant` informações, `accept` , `reject` métodos em `transferRequestedEventArgs` .

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

## <a name="eventing-model"></a>Modelo de eventos

Você pode assinar a maioria das propriedades e coleções a serem notificadas quando os valores forem alterados.

### <a name="properties"></a>Propriedades
Para assinar `property changed` eventos:

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>Coleções
Para assinar `collection updated` eventos:

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
