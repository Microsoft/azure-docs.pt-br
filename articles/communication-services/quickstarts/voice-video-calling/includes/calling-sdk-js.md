---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 49054d9bbde67dc3670ec444e4b60c3ddf503db5
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645332"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Um recurso dos Serviços de Comunicação implantado. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um token de acesso de usuário para habilitar o cliente de chamada. Para obter mais informações, consulte [criar e gerenciar tokens de acesso](../../access-tokens.md).
- Opcional: conclua o guia de início rápido para [Adicionar chamada de voz ao seu aplicativo](../getting-started-with-calling.md).

## <a name="install-the-sdk"></a>Instalar o SDK

> [!NOTE]
> Este documento usa a versão 1.0.0-beta. 10 do SDK de chamada.

Use o `npm install` comando para instalar a chamada dos serviços de comunicação do Azure e os SDKs comuns para JavaScript.
Este documento faz referência a tipos na versão 1.0.0-beta. 10 da biblioteca de chamada.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir tratam de alguns dos principais recursos dos serviços de comunicação do Azure que chamam o SDK:

| Nome                             | Descrição                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | O ponto de entrada principal para o SDK de chamada.                                                                       |
| `CallAgent`                        | Usado para iniciar e gerenciar chamadas.                                                                                            |
| `DeviceManager`                    | Usado para gerenciar dispositivos de mídia.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementa a `CommunicationTokenCredential` interface, que é usada para instanciar `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Inicializar uma instância do CallClient, criar uma instância do CallAgent e acessar o DeviceManager

Crie uma nova `CallClient` instância. Você pode configurá-la com opções personalizadas, como uma instância do Agente.

Quando você tiver uma `CallClient` instância do, poderá criar uma `CallAgent` instância chamando o `createCallAgent` método na `CallClient` instância. Isso retorna de modo assíncrono um objeto de instância `CallAgent`.

O `createCallAgent` método usa `CommunicationTokenCredential` como um argumento. Ele aceita um [token de acesso do usuário](../../access-tokens.md).

Depois de criar uma `callAgent` instância, você pode usar o `getDeviceManager` método na `CallClient` instância para acessar `deviceManager` .

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Fazer uma chamada

Para criar e iniciar uma chamada, use uma das APIs no `callAgent` e forneça um usuário que você criou por meio do SDK de identidade dos serviços de comunicação.

A criação e o início da chamada são síncronos. A instância de chamada permite que você assine eventos de chamada.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Coloque uma chamada 1: n para um usuário ou PSTN

Para chamar outro usuário dos serviços de comunicação, use o `startCall` método em `callAgent` e passe o destinatário `CommunicationUserIdentifier` que você [criou com a biblioteca de administração dos serviços de comunicação](../../access-tokens.md).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Para fazer uma chamada para uma rede telefônica pública comutada (PSTN), use o `startCall` método em `callAgent` e passe o destinatário `PhoneNumberIdentifier` . O recurso Serviços de Comunicação precisa estar configurado para permitir a chamada de PSTN.

Ao chamar um número PSTN, especifique sua ID de chamador alternativa. Uma ID de chamador alternativa é um número de telefone (baseado no padrão E. 164) que identifica o chamador em uma chamada PSTN. É o número de telefone que o destinatário da chamada vê para uma chamada de entrada.

> [!NOTE]
> No momento, a chamada PSTN está em versão prévia privada. Para acesso, [aplique ao programa pioneiro](https://aka.ms/ACS-EarlyAdopter).

Para uma chamada de 1:1, use o seguinte código:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Para uma chamada 1: n, use o seguinte código:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Realizar uma chamada 1:1 com a câmera de vídeo

> [!IMPORTANT]
> No momento, não pode haver mais de um fluxo de vídeo local de saída.

Para fazer uma chamada de vídeo, você precisa especificar suas câmeras usando o `getCameras()` método em `deviceManager` .

Depois de selecionar uma câmera, use-a para construir uma `LocalVideoStream` instância. Passe-o dentro de `videoOptions` um item dentro da `localVideoStream` matriz para o `startCall` método.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Quando a chamada se conecta, ela inicia automaticamente o envio de um fluxo de vídeo da câmera selecionada para o outro participante. Isso também se aplica às opções de vídeo `Call.Accept()` e `CallAgent.join()` Opções de vídeo.

### <a name="join-a-group-call"></a>Ingressar em um grupo

> [!NOTE]
> O `groupId` parâmetro é considerado metadados do sistema e pode ser usado pela Microsoft para operações que são necessárias para executar o sistema. Não inclua dados pessoais no `groupId` valor. A Microsoft não trata esse parâmetro como dados pessoais e seu conteúdo pode estar visível para funcionários da Microsoft ou armazenados a longo prazo.
>
> O `groupId` parâmetro requer que os dados estejam no formato Guid. É recomendável usar GUIDs gerados aleatoriamente que não são considerados dados pessoais em seus sistemas.
>

Para iniciar uma nova chamada de grupo ou ingressar em uma chamada de grupo em andamento, use o `join` método e passe um objeto com uma `groupId` propriedade. O `groupId` valor deve ser um GUID.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Participe de uma reunião de equipes

Para ingressar em uma reunião de equipes, use o `join` método e passe um link de reunião ou coordenadas.

Ingressar usando um link de reunião:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Ingressar usando as coordenadas da reunião:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Receber uma chamada de entrada

A `callAgent` instância emite um `incomingCall` evento quando a identidade conectada recebe uma chamada de entrada. Para escutar esse evento, assine usando uma destas opções:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {

    //Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

O `incomingCall` evento inclui uma `incomingCall` instância que você pode aceitar ou rejeitar.

## <a name="manage-calls"></a>Gerenciar chamadas

Durante uma chamada, você pode acessar as propriedades da chamada e gerenciar as configurações de vídeo e áudio.

### <a name="check-call-properties"></a>Verificar Propriedades da chamada

Obter a ID exclusiva (cadeia de caracteres) para uma chamada:

   ```js
    const callId: string = call.id;
   ```

Saiba mais sobre outros participantes na chamada inspecionando a `remoteParticipants` coleção na instância ' call ':

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Identifique o chamador de uma chamada de entrada:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` é um dos `CommunicationIdentifier` tipos.

Obter o estado de uma chamada:

   ```js
   const callState = call.state;
   ```

   Isso retorna uma cadeia de caracteres que representa o estado atual de uma chamada:

  - `None`: Estado de chamada inicial.
  - `Connecting`: Estado de transição inicial quando uma chamada é colocada ou aceita.
  - `Ringing`: Para uma chamada de saída, indica que uma chamada está tocando para os participantes remotos. Ele está `Incoming` no seu lado.
  - `EarlyMedia`: Indica um estado no qual um comunicado é reproduzido antes que a chamada seja conectada.
  - `Connected`: Indica que a chamada está conectada.
  - `LocalHold`: Indica que a chamada é colocada em espera por um participante local. Nenhuma mídia está fluindo entre o ponto de extremidade local e os participantes remotos.
  - `RemoteHold`: Indica que a chamada foi colocada em espera pelo participante remoto. Nenhuma mídia está fluindo entre o ponto de extremidade local e os participantes remotos.
  - `Disconnecting`: Estado de transição antes que a chamada vá para um `Disconnected` estado.
  - `Disconnected`: Estado da chamada final. Se a conexão de rede for perdida, o estado será alterado para `Disconnected` depois de dois minutos.

Descubra por que uma chamada terminou inspecionando a `callEndReason` Propriedade:

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

Saiba se a chamada atual é de entrada ou saída inspecionando a `direction` propriedade. Ele retorna `CallDirection`.

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Verifique se o microfone atual está mudo. Ele retorna `Boolean`.

   ```js
   const muted = call.isMuted;
   ```

Descubra se o fluxo de compartilhamento de tela está sendo enviado de um determinado ponto de extremidade, verificando a `isScreenSharingOn` propriedade. Ele retorna `Boolean`.

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Inspecione os fluxos de vídeo ativos verificando a `localVideoStreams` coleção. Ele retorna `LocalVideoStream` objetos.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```

### <a name="check-a-callended-event"></a>Verificar um evento callEnded

A instância `call` emitirá um evento `callEnded` quando a chamada for encerrada. Para escutar esse evento, assine usando o seguinte código:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Ativar e desativar mudo

Para ativar mudo ou desativar mudo do ponto de extremidade local, você pode usar as `mute` APIs do e `unmute` assíncronas:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Iniciar e parar o envio do vídeo local

Para iniciar um vídeo, você precisa especificar câmeras usando o `getCameras` método no `deviceManager` objeto. Em seguida, crie uma nova instância do `LocalVideoStream` passando a câmera desejada para o `startVideo` método como um argumento:

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

Depois que você começar a enviar o vídeo com êxito, uma `LocalVideoStream` instância será adicionada à `localVideoStreams` coleção em uma instância de chamada.

```js
call.localVideoStreams[0] === localVideoStream;
```

Para parar o vídeo local, passe a `localVideoStream` instância que está disponível na `localVideoStreams` coleção:

```js
await call.stopVideo(localVideoStream);
```

Você pode alternar para um dispositivo de câmera diferente enquanto um vídeo está sendo enviado invocando `switchSource` em uma `localVideoStream` instância:

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>Gerenciar participantes remotos

Todos os participantes remotos são representados pelo tipo `RemoteParticipant` e estão disponíveis por meio da coleção `remoteParticipants` em uma instância de chamada.

### <a name="list-the-participants-in-a-call"></a>Listar os participantes em uma chamada

A `remoteParticipants` coleção retorna uma lista de participantes remotos em uma chamada:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Acessar propriedades do participante remoto

Os participantes remotos têm um conjunto de propriedades e coleções associadas:

- `CommunicationIdentifier`: Obter o identificador para um participante remoto. Identity é um dos `CommunicationIdentifier` tipos:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Pode ser um dos seguintes `CommunicationIdentifier` tipos:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: Objeto que representa o usuário ACS.
  - `{ phoneNumber: '<E.164>' }`: Objeto que representa o número de telefone no formato E. 164.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Objeto que representa o usuário de equipes.
  - `{ id: string }`: identificador de repredenting de objeto que não se ajusta a nenhum dos outros tipos de identificador

- `state`: Obter o estado de um participante remoto.

  ```js
  const state = remoteParticipant.state;
  ```

  O estado pode ser:

  - `Idle`: Estado inicial.
  - `Connecting`: Estado de transição enquanto um participante está se conectando à chamada.
  - `Ringing`: O participante está tocando.
  - `Connected`: O participante está conectado à chamada.
  - `Hold`: O participante está em espera.
  - `EarlyMedia`: Comunicado que é reproduzido antes que um participante se conecte à chamada.
  - `Disconnected`: Estado final. O participante está desconectado da chamada. Se o participante remoto perder sua conectividade de rede, seu estado mudará para `Disconnected` depois de dois minutos.

- `callEndReason`: Para saber por que um participante saiu da chamada, verifique a `callEndReason` Propriedade:

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` status: para descobrir se um participante remoto está mudo, verifique a `isMuted` propriedade. Ele retorna `Boolean`.

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` status: para descobrir se um participante remoto está falando, verifique a `isSpeaking` propriedade. Ele retorna `Boolean`.

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Para inspecionar todos os fluxos de vídeo que um determinado participante está enviando nesta chamada, verifique a `videoStreams` coleção. Ele contém `RemoteVideoStream` objetos.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`: Para obter o nome de exibição para este participante remoto, inspecione a `displayName` propriedade que ele retorna String. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>Adicionar um participante a uma chamada

Para adicionar um participante (um usuário ou um número de telefone) a uma chamada, você pode usar `addParticipant` . Forneça um dos `Identifier` tipos. Ele retorna a `remoteParticipant` instância.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Remover um participante de uma chamada

Para remover um participante (um número de telefone ou usuário) de uma chamada, você pode invocar `removeParticipant` . Você precisa passar um dos `Identifier` tipos. Isso é resolvido de forma assíncrona depois que o participante é removido da chamada. O participante também é removido da `remoteParticipants` coleção.

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

Para renderizar `RemoteVideoStream` , você precisa assinar um `isAvailableChanged` evento. Se a propriedade `isAvailable` mudar para `true`, será um sinal de que um participante remoto está enviando um fluxo. Após isso acontecer, crie uma nova instância do `VideoStreamRenderer` e, em seguida, crie uma nova `VideoStreamRendererView` instância usando o `createView` método assíncrono.  Em seguida, você pode anexar `view.target` a qualquer elemento de interface do usuário.

Sempre que a disponibilidade de um fluxo remoto for alterada, você poderá optar por destruir todo o `VideoStreamRenderer`, um `VideoStreamRendererView` específico ou mantê-los, mas isso resultará na exibição do quadro de vídeo em branco.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Propriedades de fluxo de vídeo remoto

Os fluxos de vídeo remotos têm as seguintes propriedades:

- `id`: A ID de um fluxo de vídeo remoto.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`: Pode ser `Video` ou `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Se um ponto de extremidade de participante remoto está enviando ativamente um fluxo.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>Métodos e propriedades do VideoStreamRenderer

Criar uma `VideoStreamRendererView` instância que pode ser anexada na interface do usuário do aplicativo para renderizar o fluxo de vídeo remoto, usar o `createView()` método assíncrono, ela é resolvida quando o fluxo está pronto para ser renderizado e retorna um objeto com `target` propriedade que representa o `video` elemento que pode ser anexado em qualquer lugar na árvore DOM

  ```js
  videoStreamRenderer.createView()
  ```

Descartar `videoStreamRenderer` e todas as `VideoStreamRendererView` instâncias associadas:

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>Métodos e propriedades do VideoStreamRendererView

Ao criar um `VideoStreamRendererView` , você pode especificar as `scalingMode` Propriedades e `isMirrored` . `scalingMode` pode ser `Stretch` , `Crop` ou `Fit` . Se `isMirrored` for especificado, o fluxo renderizado será invertido verticalmente.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Cada `VideoStreamRendererView` instância tem uma `target` propriedade que representa a superfície de renderização. Anexe esta propriedade na interface do usuário do aplicativo:

```js
htmlElement.appendChild(view.target);
```

Você pode atualizar `scalingMode` invocando o `updateScalingMode` método:

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Gerenciamento de dispositivo

No `deviceManager` , você pode especificar dispositivos locais que podem transmitir seus fluxos de áudio e vídeo em uma chamada. Ele também ajuda a solicitar permissão para acessar o microfone e a câmera de outro usuário usando a API de navegador nativo.

Você pode acessar `deviceManager` chamando o `callClient.getDeviceManager()` método:

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Obter dispositivos locais

Para acessar dispositivos locais, você pode usar métodos de enumeração no `deviceManager` .

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Definir o microfone e o viva-voz padrão

No `deviceManager` , você pode definir um dispositivo padrão que será usado para iniciar uma chamada. Se os padrões do cliente não estiverem definidos, os serviços de comunicação usarão os padrões do sistema operacional.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>Visualização da câmera local

Você pode usar `deviceManager` e `VideoStreamRenderer` para começar a renderizar fluxos da sua câmera local. Esse fluxo não será enviado para outros participantes; ele é um feed de visualização local.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Solicitar permissão para câmera e microfone

Solicitar que um usuário conceda permissões de câmera e microfone:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Isso resolve com um objeto que indica se `audio` `video` as permissões foram concedidas:

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Chamadas de registro

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

A gravação de chamadas é um recurso estendido da API `Call` principal. Primeiro, você precisa obter o objeto de API do recurso de gravação:

```js
const callRecordingApi = call.api(Features.Recording);
```

Em seguida, para verificar se a chamada está sendo registrada, inspecione a `isRecordingActive` propriedade de `callRecordingApi` . Ele retorna `Boolean`.

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

## <a name="transfer-calls"></a>Transferir chamadas

A transferência de chamadas é um recurso estendido da API `Call` principal. Primeiro, você precisa obter o objeto de API do recurso de transferência:

```js
const callTransferApi = call.api(Features.Transfer);
```

As transferências de chamada envolvem três partes:

- *Transferência*: a pessoa que inicia a solicitação de transferência.
- *Transferência*: a pessoa que está sendo transferida.
- *Destino da transferência*: a pessoa que está sendo transferida para.

As transferências seguem estas etapas:

1. Já existe uma chamada conectada entre o *transferidar* e o *transferidar*. O *transferítor* decide transferir a chamada do *transferida* para o *destino de transferência*.
1. O *transferítor* chama a `transfer` API.
1. O *transferidar* decide se deseja `accept` ou `reject` a solicitação de transferência para o *destino de transferência* usando um `transferRequested` evento.
1. O *destino de transferência* receberá uma chamada de entrada somente se o *transferida* aceitar a solicitação de transferência.

Para transferir uma chamada atual, você pode usar a `transfer` API. `transfer` usa o opcional `transferCallOptions` , que permite definir um `disableForwardingAndUnanswered` sinalizador:

- `disableForwardingAndUnanswered = false`: Se o *destino de transferência* não responder à chamada de transferência, a transferência seguirá as configurações de encaminhamento de *destino de transferência* e não respondidas.
- `disableForwardingAndUnanswered = true`: Se o *destino de transferência* não responder à chamada de transferência, a tentativa de transferência terminará.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

A `transfer` API permite que você assine `transferStateChanged` e `transferRequested` eventos. Um `transferRequested` evento vem de uma `call` instância do; um `transferStateChanged` evento e uma transferência `state` e `error` provenientes de uma `transfer` instância.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

O *transferidar* pode aceitar ou rejeitar a solicitação de transferência iniciada pelo *transferidar* no `transferRequested` evento usando o `accept()` ou o `reject()` no `transferRequestedEventArgs` . Você pode acessar `targetParticipant` informações e `accept` `reject` métodos no `transferRequestedEventArgs` .

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

## <a name="learn-about-eventing-models"></a>Saiba mais sobre modelos de eventos

Inspecione os valores atuais e assine os eventos de atualização para futuros valores.

### <a name="properties"></a>Propriedades

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Coleções

```js
// Inspect the current collection
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
