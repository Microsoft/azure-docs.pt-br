---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: a8cfdc76694d52acee70cde0e3f1697cd8129d06
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97691922"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Um recurso dos Serviços de Comunicação implantado. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um `User Access Token` para habilitar o cliente de chamada. Para obter mais informações sobre [como obter um `User Access Token`](../../access-tokens.md)
- Opcional: conclua o guia de início rápido para [começar a adicionar a chamada ao seu aplicativo](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configurando

### <a name="creating-the-xcode-project"></a>Como criar o projeto do Xcode

No Xcode, crie um projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único**. Este guia de início rápido usa a [estrutura SwiftUI](https://developer.apple.com/xcode/swiftui/), portanto, você deve definir o **idioma** como **Swift** e a **interface do usuário** como **SwiftUI**. Você não vai criar testes de unidade ou testes de interface do usuário durante este guia de início rápido. Sinta-se à vontade para desmarcar **incluir testes de unidade** e também desmarcar **incluir testes de interface do usuário**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Captura de tela que mostra a janela Criar Projeto no Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instale o pacote e as dependências com o CocoaPods

1. Crie um Podfile para seu aplicativo, da seguinte maneira:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.5'
     pod 'AzureCommunication', '~> 1.0.0-beta.5'
     pod 'AzureCore', '~> 1.0.0-beta.5'
   end
   ```

2. Execute `pod install`.
3. Abra o `.xcworkspace` com o Xcode.

### <a name="request-access-to-the-microphone"></a>Solicitar acesso ao microfone

Para acessar o microfone do dispositivo, você precisa atualizar a Lista de Propriedades de Informações do aplicativo com um `NSMicrophoneUsageDescription`. Você define o valor associado para um `string` que será incluído na caixa de diálogo que o sistema usa para solicitar acesso do usuário.

Clique com o botão direito do mouse na entrada `Info.plist` da árvore do projeto e selecione **Abrir Como** > **Código-Fonte**. Adicione as linhas a seguir no nível superior da seção `<dict>` e, em seguida, salve o arquivo.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

Abra o arquivo **ContentView.swift** do seu projeto e adicione uma declaração `import` à parte superior do arquivo para importar o `AzureCommunicationCalling library`. Além disso, `AVFoundation` a importação será necessária para a solicitação de permissão de áudio no código.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir tratam de alguns dos principais recursos dos serviços de comunicação do Azure que chamam a biblioteca de cliente para iOS.


| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | O ACSCallClient é o ponto de entrada principal para a biblioteca de cliente de chamada.|
| ACSCallAgent | O ACSCallAgent é usado para iniciar e gerenciar chamadas. |
| CommunicationUserCredential | O CommunicationUserCredential é usado como a credencial de token para criar uma instância do CallAgent.| 
| CommunicationIndentifier | O CommunicationIndentifier é usado para representar a identidade do usuário que pode ser uma das seguintes: CommunicationUser/PhoneNumber/CallingApplication. |

> [!NOTE]
> Ao implementar delegados de evento, o aplicativo precisa manter uma referência forte aos objetos que exigem assinaturas de evento. Por exemplo, quando um `ACSRemoteParticipant` objeto é retornado na invocação do `call.addParticipant` método e o aplicativo define o delegado a ser escutado `ACSRemoteParticipantDelegate` , o aplicativo deve conter uma referência forte ao `ACSRemoteParticipant` objeto. Caso contrário, se esse objeto for coletado, o delegado lançará uma exceção fatal quando o SDK de chamada tentar invocar o objeto.

## <a name="initialize-the-acscallagent"></a>Inicializar o ACSCallAgent

Para criar uma `ACSCallAgent` instância do `ACSCallClient` , você precisa usar um `callClient.createCallAgent` método que retorne de forma assíncrona um `ACSCallAgent` objeto depois que ele for inicializado

Para criar o cliente de chamada, você precisa passar um `CommunicationUserCredential` objeto.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(
        initialToken: tokenString, refreshProactively: true,
        tokenRefresher: self.fetchTokenSync
    )
} catch {
    print("Failed to create CommunicationCredential object")
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Passe o objeto CommunicationUserCredential criado acima para ACSCallClient e defina o nome de exibição.

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()
options.displayName = "ACS iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions,
    completionHandler: { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Fazer uma chamada de saída

Para criar e iniciar uma chamada, você precisa chamar uma das APIs no `ACSCallAgent` e fornecer a identidade dos serviços de comunicação de um usuário que você provisionou usando a biblioteca do cliente de gerenciamento dos serviços de comunicação.

A criação e o início da chamada são síncronos. Você receberá uma chamada de instância que permite assinar todos os eventos na chamada.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Coloque uma chamada de 1:1 para um usuário ou uma chamada de 1: n com usuários e PSTN

```swift

let callees = [CommunicationUser(identifier: 'acsUserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Coloque uma chamada 1: n com usuários e PSTN
Para fazer a chamada para a PSTN, você precisa especificar o número de telefone adquirido com os serviços de comunicação
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Coloque uma chamada de 1:1 com vídeo
Para obter uma instância do Gerenciador de dispositivos, consulte [aqui](#device-management)

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Ingressar em um grupo
Para ingressar em uma chamada, você precisa chamar uma das APIs no *CallAgent*

```swift

let groupCallContext = GroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: JoinCallOptions())

```

### <a name="accept-an-incoming-call"></a>Aceitar uma chamada de entrada
Para aceitar uma chamada, chame o método ' Accept ' em um objeto de chamada.
Definir um delegado para o CallAgent 
```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions,
                          completionHandler: { (error) in
                           if error == nil {
                               print("Incoming call accepted")
                           } else {
                               print("Failed to accept incoming call")
                           }
                       })
} else {
   print("No incoming call found to accept")
}
```

## <a name="push-notification"></a>Notificação por push

Notificação por push móvel é a notificação de pop-up que você obtém no dispositivo móvel. Para chamar, iremos nos concentrar em notificações por push de VoIP (protocolo de Internet por voz). Forneceremos os recursos de registro para notificação por push, para lidar com notificações por push e para cancelar o registro de notificação por push.

### <a name="prerequisite"></a>Pré-requisito

- Etapa 1: recursos de & de assinatura do Xcode->-> adicionar funcionalidade-> "notificações por push"
- Etapa 2: recursos de & de assinatura do Xcode->-> adicionar capacidade-> "modos de segundo plano"
- Etapa 3: "modos de segundo plano"-> selecione "voz sobre IP" e "notificações remotas"

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Captura de tela mostrando como adicionar recursos no Xcode." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Registrar para notificações por push

Para se registrar para notificação por push, chame registerPushNotification () em uma instância de *CallAgent* com um token de registro de dispositivo.

O registro para notificação por push precisa ser chamado após a inicialização bem-sucedida. Quando o `callAgent` objeto for destruído, `logout` será chamado, o que cancelará o registro automático das notificações por push.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken,
                completionHandler: { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
})

```

#### <a name="push-notification-handling"></a>Tratamento de notificação por push
Para receber notificações por push de chamadas de entrada, chame *handlePushNotification ()* em uma instância de *CallAgent* com uma carga de dicionário.

```swift

let dictionaryPayload = pushPayload?.dictionaryPayload
callAgent.handlePushNotification(payload: dictionaryPayload, completionHandler: { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
})

```
#### <a name="unregister-push-notification"></a>Cancelar registro de notificação por push

Os aplicativos podem cancelar o registro de notificações por push a qualquer momento. Basta chamar o `unRegisterPushNotification` método em *CallAgent*.
> [!NOTE]
> Os aplicativos não são desregistrados automaticamente da notificação por push no logout.

```swift

callAgent.unRegisterPushNotifications(completionHandler: { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
})

```

## <a name="mid-call-operations"></a>Operações de chamada intermediária

Você pode executar várias operações durante uma chamada para gerenciar configurações relacionadas a vídeo e áudio.

### <a name="mute-and-unmute"></a>Ativar mudo e mudo

Para ativar mudo ou desativar mudo do ponto de extremidade local, você pode usar as `mute` `unmute` APIs assíncronas do e do:

```swift
call!.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

Manipulador Mudo local

```swift
call!.unmute(completionHandler:{ (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
})
```

### <a name="start-and-stop-sending-local-video"></a>Iniciar e parar de enviar vídeo local

Para começar a enviar vídeo local para outros participantes na chamada, use `startVideo` API e passe `localVideoStream` com `camera`

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    }
    else {
        print("Local video failed to start")
    }
}

```

Depois de começar a enviar o vídeo, a `ACSLocalVideoStream` instância é adicionada à `localVideoStreams` coleção em uma instância de chamada:

```swift

call.localVideoStreams[0]

```

Manipulador Para parar o vídeo local, passe o `localVideoStream` retornado da invocação de `call.startVideo` :

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Gerenciamento de participantes remotos

Todos os participantes remotos são representados pelo `ACSRemoteParticipant` tipo e estão disponíveis por meio da `remoteParticipants` coleção em uma instância de chamada:

### <a name="list-participants-in-a-call"></a>Listar os participantes em uma chamada

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Propriedades do participante remoto

```swift

// [ACSRemoteParticipantDelegate] delegate - an object you provide to receive events from this ACSRemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ACSParticipantStateIdle = 0, ACSParticipantStateEarlyMedia = 1, ACSParticipantStateConnecting = 2, ACSParticipantStateConnected = 3, ACSParticipantStateOnHold = 4, ACSParticipantStateInLobby = 5, ACSParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [ACSError] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// ACSRemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [ACSRemoteVideoStream, ACSRemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Adicionar um participante a uma chamada

Para adicionar um participante a uma chamada (um usuário ou um número de telefone), você pode invocar `addParticipant` . Isso retornará de forma síncrona uma instância de participante remota.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Remover um participante de uma chamada
Para remover um participante de uma chamada (um usuário ou um número de telefone), você pode invocar a  `removeParticipant` API. Isso será resolvido de forma assíncrona.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Renderizar fluxos de vídeo do participante remoto

Os participantes remotos podem iniciar o compartilhamento de vídeo ou tela durante uma chamada.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>Manipular fluxos de compartilhamento de tela/vídeo do participante remoto

Para listar os fluxos de participantes remotos, inspecione as `videoStreams` coleções:

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Propriedades de fluxo de vídeo remoto

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Renderizar fluxo de participante remoto

Para iniciar a renderização de fluxos de participantes remotos:

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Métodos e propriedades do processador de vídeo remoto

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>Gerenciamento de dispositivo

`DeviceManager` permite que você enumere dispositivos locais que podem ser usados em uma chamada para transmitir fluxos de áudio/vídeo. Ele também permite solicitar permissão de um usuário para acessar o microfone/câmera. Você pode acessar `deviceManager` no `callClient` objeto:

```swift

self.callClient!.getDeviceManager(
    completionHandler: { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    })
```

### <a name="enumerate-local-devices"></a>Enumerar dispositivos locais

Para acessar dispositivos locais, você pode usar métodos de enumeração no Device Manager. A enumeração é uma ação síncrona.

```swift
// enumerate local cameras
var localCameras = deviceManager.getCameraList() // [ACSVideoDeviceInfo, ACSVideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.getMicrophoneList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.getSpeakerList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Definir microfone/orador padrão

O Gerenciador de dispositivos permite que você defina um dispositivo padrão que será usado ao iniciar uma chamada. Se os padrões de pilha não estiverem definidos, os serviços de comunicação voltarão para OS padrões do sistema operacional.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.getMicrophoneList()![0]
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="local-camera-preview"></a>Visualização da câmera local

Você pode usar `ACSRenderer` o para começar a renderizar um fluxo de sua câmera local. Este fluxo não será enviado para outros participantes; é um feed de visualização local. Esta é uma ação assíncrona.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>Propriedades da visualização da câmera local

O renderizador tem um conjunto de propriedades e métodos que permitem controlar a renderização:

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>Modelo de eventos

Você pode assinar a maioria das propriedades e coleções a serem notificadas quando os valores forem alterados.

### <a name="properties"></a>Propriedades
Para assinar `property changed` eventos:

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Coleções
Para assinar `collection updated` eventos:

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
