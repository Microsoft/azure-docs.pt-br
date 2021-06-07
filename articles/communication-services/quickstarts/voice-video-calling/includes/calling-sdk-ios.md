---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: d36bf92a1b1bdef4e45b22b934728b3e8c46c3da
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107697"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Um recurso dos Serviços de Comunicação implantado. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um `User Access Token` para habilitar o cliente de chamada. Para obter mais informações sobre [como obter um `User Access Token`](../../access-tokens.md)
- Opcional: conclua o guia de início rápido para [começar a adicionar chamadas ao seu aplicativo](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configurando

### <a name="creating-the-xcode-project"></a>Como criar o projeto do Xcode

> [!NOTE]
> Este documento usa a versão 1.0.0-beta. 8 do SDK de chamada.

No Xcode, crie um projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único**. Este guia de início rápido usa a [estrutura SwiftUI](https://developer.apple.com/xcode/swiftui/), portanto, você deve definir a **Linguagem** como **Swift** e a **Interface do Usuário** como **SwiftUI**. Você não criará testes de unidade ou testes da interface do usuário neste guia de início rápido. Fique à vontade para desmarcar **Incluir Testes de Unidade** e **Incluir Testes da Interface do Usuário**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Captura de tela que mostra a janela Criar Projeto no Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instale o pacote e as dependências com o CocoaPods

1. Crie um Podfile para seu aplicativo, da seguinte maneira:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

2. Execute `pod install`.
3. Abra o `.xcworkspace` com o XCode.

### <a name="request-access-to-the-microphone"></a>Solicitar acesso ao microfone

Para acessar o microfone do dispositivo, você precisa atualizar a Lista de Propriedades de Informações do aplicativo com um `NSMicrophoneUsageDescription`. Você define o valor associado para um `string` que será incluído na caixa de diálogo que o sistema usa para solicitar acesso do usuário.

Clique com o botão direito do mouse na entrada `Info.plist` da árvore do projeto e selecione **Abrir Como** > **Código-Fonte**. Adicione as linhas a seguir no nível superior da seção `<dict>` e, em seguida, salve o arquivo.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

Abra o arquivo **ContentView.swift** do seu projeto e adicione uma declaração `import` à parte superior do arquivo para importar o `AzureCommunicationCalling library`. Além disso, importe `AVFoundation`. Precisaremos disso para a solicitação de permissão de áudio no código.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir tratam de alguns dos principais recursos dos serviços de comunicação do Azure que chamam o SDK para iOS.


| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | O CallClient é o ponto de entrada principal para o SDK de chamada.|
| CallAgent | O CallAgent é usado para iniciar e gerenciar chamadas. |
| CommunicationTokenCredential | O CommunicationTokenCredential é usado como a credencial de token para criar uma instância do CallAgent.| 
| CommunicationIdentifier | O CommunicationIdentifier é usado para representar a identidade do usuário, que pode ser uma das seguintes: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

> [!NOTE]
> Ao implementar delegados de evento, o aplicativo precisa manter uma referência forte aos objetos que exigem assinaturas de evento. Por exemplo, quando um objeto `RemoteParticipant` é retornado ao invocar o método `call.addParticipant` e o aplicativo define o delegado a ser escutado em `RemoteParticipantDelegate`, o aplicativo precisa manter uma referência forte ao objeto `RemoteParticipant`. Caso contrário, se esse objeto for coletado, o delegado lançará uma exceção fatal quando o SDK de chamada tentar invocar o objeto.

## <a name="initialize-the-callagent"></a>Inicializar o CallAgent

Para criar uma instância `CallAgent` por meio do `CallClient`, você precisa usar um método `callClient.createCallAgent` que retorne de modo assíncrono um objeto `CallAgent` depois que ele for inicializado

Para criar o cliente de chamada, você precisa passar um objeto `CommunicationTokenCredential`.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
var userCredential: CommunicationTokenCredential?
   do {
       userCredential = try CommunicationTokenCredential(with: CommunicationTokenRefreshOptions(initialToken: token, 
                                                                     refreshProactively: true,
                                                                     tokenRefresher: self.fetchTokenSync))
   } catch {
       return
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Passe o objeto `CommunicationTokenCredential` criado acima para `CallClient` e defina o nome de exibição.

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()!
options.displayName = " iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Realizar uma chamada de saída

Para criar e iniciar uma chamada, você precisa chamar uma das APIs no `CallAgent` e fornecer a identidade dos serviços de comunicação de um usuário que você provisionou usando o SDK de gerenciamento de serviços de comunicação.

A criação e o início da chamada são síncronos. Você receberá uma instância de chamada que permite assinar todos os eventos na chamada.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Realizar uma chamada 1:1 para um usuário ou uma chamada 1:n com usuários e PSTN

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Realizar uma chamada 1:n com usuários e PSTN
Para realizar a chamada para a PSTN, você precisa especificar o número de telefone adquirido com os Serviços de Comunicação
```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Realizar uma chamada 1:1 com vídeo
Para obter uma instância do gerenciador de dispositivos, clique [aqui](#device-management)

```swift

let camera = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUserIdentifier(identifier: 'UserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Ingressar em um grupo
Para ingressar em uma chamada, você precisa chamar uma das APIs no *CallAgent*

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-for-incoming-call"></a>Assinar a chamada de entrada
Assinar o evento de chamada de entrada

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func onIncomingCall(_ callAgent: CallAgent!, incomingcall: IncomingCall!) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func onCallEnded(_ incomingCall: IncomingCall!, args: PropertyChangedEventArgs!) {
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>Aceitar uma chamada de entrada
Para aceitar uma chamada, chame o método 'accept' em um objeto de chamada.
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

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions) { (call, error) in
               if error == nil {
                   print("Incoming call accepted")
               } else {
                   print("Failed to accept incoming call")
               }
           }
} else {
   print("No incoming call found to accept")
}
```

## <a name="push-notification"></a>Notificação por push

A notificação por push móvel é a notificação em pop-up que você obtém no dispositivo móvel. Para chamadas, nos concentraremos em notificações por push de protocolo VoIP. Forneceremos as funcionalidades para que você se registre para notificação por push, administre as notificações por push e cancele o registro da notificação por push.

### <a name="prerequisite"></a>Pré-requisito

- Etapa 1: Xcode -> Assinatura e Funcionalidades -> Adicionar Funcionalidade -> "Notificações por Push"
- Etapa 2: Xcode -> Assinatura e Funcionalidades -> Adicionar Funcionalidade -> "Modos em Segundo Plano"
- Etapa 3: "Modos em Segundo Plano"-> Selecione "Protocolo VoIP" e "Notificações remotas"

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Captura de tela que mostra como adicionar funcionalidades no Xcode." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Registrar para notificações por push

Para se registrar para obter notificações por push, chame registerPushNotification() em uma instância de *CallAgent* com um token de registro de dispositivo.

O registro para notificação por push precisa ser chamado após a inicialização bem-sucedida. Quando o objeto `callAgent` for destruído, o `logout` será chamado, o que cancelará automaticamente o registro das notificações por push.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

#### <a name="push-notification-handling"></a>Gerenciamento de notificações por push
Para receber notificações por push de chamadas de entrada, chame *handlePushNotification()* em uma instância de *CallAgent* com um conteúdo de dicionário.

```swift

let callNotification = IncomingCallInformation.from(payload: pushPayload?.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
}

```
#### <a name="unregister-push-notification"></a>Cancelar o registro de notificações por push

Os aplicativos podem cancelar o registro de notificações por push a qualquer momento. Basta chamar o método `unregisterPushNotification` no *CallAgent*.
> [!NOTE]
> Ao fazer logoff, o registro dos aplicativos não é cancelado automaticamente da notificação por push.

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="mid-call-operations"></a>Operações durante a chamada

Você pode executar várias operações durante uma chamada para gerenciar as configurações relacionadas ao vídeo e ao áudio.

### <a name="mute-and-unmute"></a>Ativar e desativar mudo

Para ativar ou desativar o mudo no ponto de extremidade local, você pode usar as APIs `mute` e `unmute` assíncronas:

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

[Assíncrono] Desativar mudo local

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>Iniciar e parar o envio do vídeo local

Para começar a enviar um vídeo local para outros participantes na chamada, use a API `startVideo` e passe `localVideoStream` com `camera`

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

Depois de começar a enviar o vídeo, a instância `LocalVideoStream` é adicionada à coleção `localVideoStreams` em uma instância de chamada:

```swift

call.localVideoStreams[0]

```

[Assíncrono] Para parar o vídeo local, passe o `localVideoStream` retornado da invocação de `call.startVideo`:

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

Todos os participantes remotos são representados pelo tipo `RemoteParticipant` e estão disponíveis por meio da coleção `remoteParticipants` em uma instância de chamada:

### <a name="list-participants-in-a-call"></a>Listar os participantes em uma chamada

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Propriedades do participante remoto

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Adicionar um participante a uma chamada

Para adicionar um participante a uma chamada (um usuário ou um número de telefone), você pode invocar `addParticipant`. Isso retornará de modo síncrono uma instância do participante remoto.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Remover um participante de uma chamada
Para remover um participante de uma chamada (um usuário ou um número de telefone), você pode invocar a API `removeParticipant`. Isso será resolvido de modo assíncrono.

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

Os participantes remotos podem iniciar o vídeo ou o compartilhamento de tela durante uma chamada.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>Administrar fluxos de vídeo/compartilhamento de tela do participante remoto

Para listar os fluxos de participantes remotos, inspecione as coleções de `videoStreams`:

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Propriedades de fluxo de vídeo remoto

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Renderizar o fluxo de participante remoto

Para iniciar a renderização de fluxos de participantes remotos:

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Métodos e propriedades do renderizador de vídeo remoto

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>Gerenciamento de dispositivo

O `DeviceManager` permite que você enumere os dispositivos locais que podem ser usados em uma chamada para transmitir os fluxos de áudio/vídeo. Ele também possibilita que você solicite permissão de um usuário para acessar o microfone/câmera. Você pode acessar o `deviceManager` no objeto `callClient`:

```swift

self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>Enumerar dispositivos locais

Para acessar dispositivos locais, você pode usar métodos de enumeração no Gerenciador de Dispositivos. A enumeração é uma ação síncrona.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Definir o microfone/alto-falante padrão

O gerenciador de dispositivos permite que você defina um dispositivo padrão que será usado ao iniciar uma chamada. Se os padrões de pilha não estiverem definidos, os Serviços de Comunicação retornarão para os padrões do SO.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.cameras!.first
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.speakers!
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="local-camera-preview"></a>Visualização da câmera local

Você pode usar o `Renderer` para começar a renderizar um fluxo da sua câmera local. Esse fluxo não será enviado para outros participantes; ele é um feed de visualização local. Essa é uma ação assíncrona.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>Propriedades da visualização da câmera local

O renderizador tem um conjunto de propriedades e métodos que permitem controlar a renderização:

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [RendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>Modelo de evento

Você pode assinar a maioria das propriedades e coleções para ser notificado quando os valores forem alterados.

### <a name="properties"></a>Propriedades
Para assinar eventos `property changed`:

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
Para assinar eventos `collection updated`:

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
