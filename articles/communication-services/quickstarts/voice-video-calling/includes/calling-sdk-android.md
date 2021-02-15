---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 26e39b8f0429995bfa336c4971c76f90d903ff55
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628933"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Um recurso dos Serviços de Comunicação implantado. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um `User Access Token` para habilitar o cliente de chamada. Para obter mais informações sobre [como obter um `User Access Token`](../../access-tokens.md)
- Opcional: conclua o guia de início rápido para [começar a adicionar a chamada ao seu aplicativo](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configurando

### <a name="install-the-package"></a>Instalar o pacote

<!-- TODO: update with instructions on how to download, install and add package to project -->
Localize o nível de projeto build.gradle e certifique-se de adicionar `mavenCentral()` à lista de repositórios em `buildscript` e `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Em seguida, no nível do módulo Build. gradle, adicione as seguintes linhas à seção Dependencies

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.2'
    ...
}

```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e as interfaces administram alguns dos principais recursos da biblioteca de clientes de Chamada dos Serviços de Comunicação do Azure:

| Name                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| O CallClient é o ponto de entrada principal para a biblioteca de clientes de Chamada.|
| CallAgent | O CallAgent é usado para iniciar e gerenciar chamadas. |
| CommunicationUserCredential | O CommunicationUserCredential é usado como a credencial de token para criar uma instância do CallAgent.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>Inicializar o CallClient, criar um CallAgent e acessar o DeviceManager

Para criar uma `CallAgent` instância, você precisa chamar o `createCallAgent` método em uma `CallClient` instância. Isso retorna de forma assíncrona um `CallAgent` objeto de instância.
O `createCallAgent` método usa um `CommunicationUserCredential` como argumento, que encapsula um token de [acesso](../../access-tokens.md).
Para acessar o `DeviceManager` , uma instância de callAgent deve ser criada primeiro e, em seguida, você pode usar o `CallClient.getDeviceManager` método para obter o DeviceManager.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationUserCredential tokenCredential = new CommunicationUserCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = await callClient.createCallAgent((appContext, tokenCredential).get();
DeviceManage deviceManager = await callClient.getDeviceManager().get();
```
Para definir um nome de exibição para o chamador, use este método alternativo:

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationUserCredential tokenCredential = new CommunicationUserCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
CallAgent callAgent = await callClient.createCallAgent((appContext, tokenCredential, callAgentOptions).get();
DeviceManage deviceManager = await callClient.getDeviceManager().get();
```


## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Fazer uma chamada de saída e ingressar em uma chamada de grupo

Para criar e iniciar uma chamada, você precisa chamar o `CallAgent.call()` método e fornecer o `Identifier` do (s) receptor (es).
Para ingressar em uma chamada de grupo, você precisa chamar o `CallAgent.join()` método e fornecer o GroupId. As IDs de grupo devem estar no formato GUID ou UUID.

A criação e o início da chamada são síncronos. A instância de chamada permite que você assine todos os eventos na chamada.

### <a name="place-a-11-call-to-a-user"></a>Fazer uma chamada de 1:1 para um usuário
Para fazer uma chamada para outro usuário dos serviços de comunicação, invoque o `call` método em `callAgent` e passe um objeto com a `communicationUserId` chave.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUser acsUserId = new CommunicationUser(<USER_ID>);
CommunicationUser participants[] = new CommunicationUser[]{ acsUserId };
call oneToOneCall = callAgent.call(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Coloque uma chamada 1: n com usuários e PSTN
> [!WARNING]
> Atualmente, a chamada PSTN não está disponível

Para fazer uma chamada 1: n para um usuário e um número PSTN, você precisa especificar o número de telefone do receptor.
O recurso de serviços de comunicação deve ser configurado para permitir a chamada de PSTN:
```java
CommunicationUser acsUser1 = new CommunicationUser(<USER_ID>);
PhoneNumber acsUser2 = new PhoneNumber("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.call(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>Coloque uma chamada de 1:1 com câmera de vídeo
> [!WARNING]
> Atualmente, apenas um fluxo de vídeo local de saída tem suporte para fazer uma chamada com vídeo, você precisa enumerar câmeras locais usando a `deviceManager` `getCameraList` API.
Depois de selecionar uma câmera desejada, use-a para construir uma `LocalVideoStream` instância e passá-la `videoOptions` como um item na `localVideoStream` matriz para um `call` método.
Depois que a chamada se conectar, ele iniciará automaticamente o envio de um fluxo de vídeo da câmera selecionada para outros participantes.

> [!NOTE]
> Devido a questões de privacidade, o vídeo não será compartilhado para a chamada se não estiver sendo visualizado localmente.
Consulte [Visualização da câmera local](#local-camera-preview) para obter mais detalhes.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUser[] participants = new CommunicationUser[]{ new CommunicationUser("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.call(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>Ingressar em um grupo
Para iniciar uma nova chamada de grupo ou ingressar em uma chamada de grupo em andamento, você precisa chamar o método ' join ' e passar um objeto com uma `groupId` propriedade. O valor deve ser um GUID.
```java
Context appContext = this.getApplicationContext();
GroupCallContext groupCallContext = new groupCallContext("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallContext, joinCallOptions);
```

### <a name="accept-a-call"></a>Aceitar uma chamada
Para aceitar uma chamada, chame o método ' Accept ' em um objeto de chamada.

```java
Context appContext = this.getApplicationContext();
Call incomingCall = retrieveIncomingCall();
incomingCall.accept(context).get();
```

Para aceitar uma chamada com câmera de vídeo em:

```java
Context appContext = this.getApplicationContext();
Call incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
incomingCall.accept(context, acceptCallOptions).get();
```

A chamada de entrada pode ser obtida com a assinatura do `CallsUpdated` evento no `callAgent` objeto e o loop pelas chamadas adicionadas:

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    Call incomingCall;
    callAgent.addOnCallsUpdatedListener(new CallsUpdatedListener() {
        void onCallsUpdated(CallsUpdatedEvent callsUpdatedEvent) {
            // Look for incoming call
            List<Call> calls = callsUpdatedEvent.getAddedCalls();
            for (Call call : calls) {
                if (call.getState() == CallState.Incoming) {
                    incomingCall = call;
                    break;
                }
            }
        }
    });
    return incomingCall;
}
```


## <a name="push-notifications"></a>Notificações por push

### <a name="overview"></a>Visão geral
As notificações de push móvel são as notificações pop-up que você vê em dispositivos móveis. Para chamar, vamos nos concentrar em notificações por push de VoIP (protocolo de Internet de voz). Registraremos notificações por push, lidaremos com notificações por push e, em seguida, cancela o registro de notificações por push.

### <a name="prerequisites"></a>Pré-requisitos

Uma conta do firebase configurada com o FCM (mensagens de nuvem) habilitada e com o serviço de mensagens de nuvem firebase conectado a uma instância do hub de notificação do Azure. Consulte [notificações de serviços de comunicação](../../../concepts/notifications.md) para obter mais informações.
Além disso, o tutorial pressupõe que você esteja usando Android Studio versão 3,6 ou superior para compilar seu aplicativo.

Um conjunto de permissões é necessário para o aplicativo Android a fim de receber mensagens de notificações do firebase Cloud Messaging. Em seu `AndroidManifest.xml` arquivo, adicione o seguinte conjunto de permissões logo após o *<manifesto... >* ou abaixo da *</application>* marca

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Registrar notificações por push

Para se registrar para notificações por push, o aplicativo precisa chamar `registerPushNotification()` em uma instância *CallAgent* com um token de registro de dispositivo.

Para obter o token de registro do dispositivo, adicione a biblioteca de cliente do firebase ao arquivo *Build. gradle* do seu módulo de aplicativo adicionando as seguintes linhas na `dependencies` seção se ela ainda não estiver lá:

```
    // Add the client library for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

No arquivo *Build. gradle* do nível do projeto, adicione o seguinte na `dependencies` seção se ele ainda não estiver lá:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Adicione o seguinte plug-in ao início do arquivo se ele ainda não estiver lá:

```
apply plugin: 'com.google.gms.google-services'
```

Selecione *sincronizar agora* na barra de ferramentas. Adicione o seguinte trecho de código para obter o token de registro de dispositivo gerado pela biblioteca de cliente do firebase Cloud Messaging para a instância do aplicativo cliente, certifique-se de adicionar as importações abaixo ao cabeçalho da atividade principal para a instância. Eles são necessários para que o trecho de código recupere o token:

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Adicione este trecho para recuperar o token:

```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w("PushNotification", "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d("PushNotification", "Device Registration token retrieved successfully");
                    }
                });
```
Registre o token de registro de dispositivo com a biblioteca de cliente de serviços de chamada para notificações por push de chamada de entrada:

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Tratamento de notificação por push

Para receber notificações por push de chamada de entrada, chame *handlePushNotification ()* em uma instância de *CallAgent* com uma carga.

Para obter o conteúdo do firebase Cloud Messaging, comece criando um novo serviço (arquivo > novo serviço de > serviço >) que estende a classe de biblioteca de cliente *FirebaseMessagingService* firebase e substitui o `onMessageReceived` método. Esse método é o manipulador de eventos chamado quando o firebase Cloud Messaging entrega a notificação por push ao aplicativo.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
Adicione a seguinte definição de serviço ao `AndroidManifest.xml` arquivo, dentro da <application> marca:

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- Depois que a carga é recuperada, ela pode ser passada para a biblioteca de cliente *dos serviços de comunicação* para ser tratada chamando o método *handlePushNotification* em uma instância *CallAgent* . Uma `CallAgent` instância é criada chamando o `createCallAgent(...)` método na `CallClient` classe.

```java
try {
    callAgent.handlePushNotification(pushNotificationMessageDataFromFCM).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Quando o tratamento da mensagem de notificação por push é bem-sucedido e os manipuladores de todos os eventos são registrados corretamente, o aplicativo tocará.

### <a name="unregister-push-notifications"></a>Cancelar o registro de notificações por push

Os aplicativos podem cancelar o registro de notificações por push a qualquer momento. Chame o `unregisterPushNotification()` método em callAgent para cancelar o registro.

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>Gerenciamento de chamadas
Você pode acessar as propriedades da chamada e executar várias operações durante uma chamada para gerenciar as configurações relacionadas ao vídeo e ao áudio.

### <a name="call-properties"></a>Propriedades da chamada

Obtenha a ID exclusiva para esta chamada:

```java
String callId = call.getCallId();
```

Para saber mais sobre outros participantes na coleção de inspeção de chamada `remoteParticipant` na `call` instância:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

A identidade do chamador se a chamada for recebida:

```java
CommunicationIdentifier callerId = call.getCallerId();
```

Obtenha o estado da chamada: 

```java
CallState callState = call.getState();
```

Ele retorna uma cadeia de caracteres que representa o estado atual de uma chamada:
* ' Nenhum '-estado de chamada inicial
* ' Entrada '-indica que a chamada é recebida, que deve ser aceita ou rejeitada
* "Conectando"-estado de transição inicial depois que a chamada é colocada ou aceita
* ' Tocando '-para uma chamada de saída – indica que a chamada está tocando para os participantes remotos, é ' de entrada ' em seu lado
* ' EarlyMedia ' – indica um estado no qual um comunicado é reproduzido antes da chamada ser conectada
* ' Conectado '-a chamada está conectada
* ' Hold '-a chamada é colocada em espera, nenhuma mídia está fluindo entre o ponto de extremidade local e os participantes remotos
* ' Desconectando '-o estado de transição antes da chamada vai para o estado ' desconectado '
* ' Desconectado '-estado de chamada final


Para saber por que uma chamada terminou, inspecione a `callEndReason` propriedade. Ele contém código/subcódigo: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Para ver se a chamada atual é uma chamada de entrada, verifique a `isIncoming` Propriedade:

```java
boolean isIncoming = call.getIsIncoming();
```

Para ver se o microfone atual está mudo, inspecione a `muted` Propriedade:

```java
boolean muted = call.getIsMicrophoneMuted();
```

Para inspecionar fluxos de vídeo ativos, verifique a `localVideoStreams` coleção:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>Ativar mudo e mudo

Para ativar mudo ou desativar mudo do ponto de extremidade local, você pode usar as `mute` `unmute` APIs assíncronas do e do:

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>Iniciar e parar de enviar vídeo local

Para iniciar um vídeo, você precisa enumerar câmeras usando a `getCameraList` API no `deviceManager` objeto. Em seguida, crie uma nova instância do `LocalVideoStream` passando a câmera desejada e passe-a na `startVideo` API como um argumento:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
videoOptions = new VideoOptions(currentVideoStream);
Future startVideoFuture = call.startVideo(currentVideoStream);
startVideoFuture.get();
```

Depois que você começar a enviar o vídeo com êxito, uma `LocalVideoStream` instância será adicionada à `localVideoStreams` coleção na instância de chamada.

```java
currentVideoStream == call.getLocalVideoStreams().get(0);
```

Para parar o vídeo local, passe a `localVideoStream` instância disponível na `localVideoStreams` coleção:

```java
call.stopVideo(localVideoStream).get();
```

Você pode alternar para um dispositivo de câmera diferente enquanto o vídeo está sendo enviado invocando `switchSource` em uma `localVideoStream` instância:
```java
localVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>Gerenciamento de participantes remotos

Todos os participantes remotos são representados por `RemoteParticipant` tipo e estão disponíveis por meio da `remoteParticipants` coleção em uma instância de chamada.

### <a name="list-participants-in-a-call"></a>Listar os participantes em uma chamada
A `remoteParticipants` coleção retorna uma lista de participantes remotos na chamada fornecida:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>Propriedades do participante remoto
Qualquer participante remoto fornecido tem um conjunto de propriedades e coleções associadas a ele:

* Obtenha o identificador para este participante remoto.
Identity é um dos tipos ' identifier '
```java
CommunicationIdentifier participantIdentity = remoteParticipant.getIdentifier();
```

* Obter o estado deste participante remoto.
```java
ParticipantState state = remoteParticipant.getState();
```
O estado pode ser um de
* ' Idle '-estado inicial
* "Conectando"-estado de transição enquanto o participante está se conectando à chamada
* ' Conectado '-o participante está conectado à chamada
* ' Hold '-o participante está em espera
* ' EarlyMedia '-o comunicado é reproduzido antes de o participante estar conectado à chamada
* ' Desconectado '-estado final-o participante está desconectado da chamada


* Para saber por que um participante saiu da chamada, inspecione a `callEndReason` Propriedade:
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* Para verificar se esse participante remoto está mudo ou não, inspecione a `isMuted` Propriedade:
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* Para verificar se esse participante remoto está falando ou não, inspecione a `isSpeaking` Propriedade:
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* Para inspecionar todos os fluxos de vídeo que um determinado participante está enviando nesta chamada, verifique a `videoStreams` coleção:
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>Adicionar um participante a uma chamada

Para adicionar um participante a uma chamada (um usuário ou um número de telefone), você pode invocar `addParticipant` . Isso retornará de forma síncrona a instância de participante remota.

```java
const acsUser = new CommunicationUser("<acs user id>");
const acsPhone = new PhoneNumber("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone);
```

### <a name="remove-participant-from-a-call"></a>Remover participante de uma chamada
Para remover um participante de uma chamada (um usuário ou um número de telefone), você pode invocar `removeParticipant` .
Isso será resolvido de forma assíncrona depois que o participante for removido da chamada.
O participante também será removido da `remoteParticipants` coleção.
```java
RemoteParticipant remoteParticipant = call.getParticipants().get(0);
call.removeParticipant(acsUser).get();
call.removeParticipant(acsPhone).get();
```

## <a name="render-remote-participant-video-streams"></a>Renderizar fluxos de vídeo do participante remoto
Para listar fluxos de vídeo e fluxos de tela de compartilhamento de participantes remotos, inspecione as `videoStreams` coleções:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Para renderizar um `RemoteVideoStream` de um participante remoto, você precisa assinar um `OnVideoStreamsUpdated` evento.

Dentro do evento, a alteração da `isAvailable` propriedade para true indica que o participante remoto está enviando um fluxo no momento. Quando isso acontecer, crie uma nova instância de a e, `Renderer` em seguida, crie uma nova `RendererView` usando `createView` a API assíncrona e anexe `view.target` em qualquer lugar da interface do usuário do seu aplicativo.

Sempre que a disponibilidade de um fluxo remoto for alterada, você poderá optar por destruir todo o renderizador, um específico `RendererView` ou mantê-los, mas isso resultará na exibição do quadro de vídeo em branco.

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Propriedades de fluxo de vídeo remoto
O fluxo de vídeo remoto tem algumas propriedades

* `Id` -ID de um fluxo de vídeo remoto
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` -Pode ser ' vídeo ' ou ' ScreenSharing '
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` -Indica se o ponto de extremidade do participante remoto está enviando o fluxo ativamente
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>Métodos e propriedades do processador
Objeto do processador seguindo as APIs

* Crie uma `RendererView` instância que pode ser anexada posteriormente na interface do usuário do aplicativo para renderizar o fluxo de vídeo remoto.
```java
// Create a view for a video stream
renderer.createView()
```
* Descarte o renderizador e todos `RendererView` associados a esse renderizador. A ser chamado quando você tiver removido todas as exibições associadas da interface do usuário.
```java
renderer.dispose()
```

* `StreamSize` -tamanho (largura/altura) de um fluxo de vídeo remoto
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>Métodos e propriedades do RendererView
Ao criar um `RendererView` , você pode especificar `scalingMode` as `mirrored` Propriedades e que serão aplicadas a este modo de exibição: o modo de dimensionamento pode ser ' Stretch ' | ' Cortar ' | ' Ajustar ' se `mirrored` for definido como `true` , o fluxo renderizado será invertido verticalmente.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

O RendererView criado pode então ser anexado à interface do usuário do aplicativo usando o seguinte trecho:
```java
layout.addView(rendererView);
```

Mais tarde, você pode atualizar o modo de dimensionamento invocando a `updateScalingMode` API no objeto RendererView com um dos dimensionmode. Stretch | Scalingmode. cortar | Scalingmode. ajustar como um argumento.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>Gerenciamento de dispositivo

`DeviceManager` permite que você enumere dispositivos locais que podem ser usados em uma chamada para transmitir seus fluxos de áudio/vídeo. Ele também permite solicitar permissão de um usuário para acessar o microfone e a câmera usando a API do navegador nativo.

Você pode acessar `deviceManager` chamando `callClient.getDeviceManager()` método.
> [!WARNING]
> Atualmente, um `callAgent` objeto deve ser instanciado primeiro para obter acesso ao DeviceManager

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>Enumerar dispositivos locais

Para acessar dispositivos locais, você pode usar métodos de enumeração no Gerenciador de Dispositivos. A enumeração é uma ação síncrona.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>Definir microfone/orador padrão

O Gerenciador de dispositivos permite que você defina um dispositivo padrão que será usado ao iniciar uma chamada.
Se os padrões do cliente não estiverem definidos, os serviços de comunicação retornarão para OS padrões do sistema operacional.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophoneList().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakerList().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>Visualização da câmera local

Você pode usar `DeviceManager` e `Renderer` para começar a renderizar fluxos de sua câmera local. Este fluxo não será enviado para outros participantes; é um feed de visualização local. Esta é uma ação assíncrona.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>Modelo de eventos
Você pode assinar a maioria das propriedades e coleções a serem notificadas quando os valores forem alterados.

### <a name="properties"></a>Propriedades
Para assinar `property changed` eventos:

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnCallStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnCallStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Coleções
Para assinar `collection updated` eventos:

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
