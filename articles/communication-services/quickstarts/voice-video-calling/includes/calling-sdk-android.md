---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 40d9f03526e5232c0a7b33f64ff35a8501702609
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107699"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Um recurso dos Serviços de Comunicação implantado. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um `User Access Token` para habilitar o cliente de chamada. Para obter mais informações sobre [como obter um `User Access Token`](../../access-tokens.md)
- Opcional: conclua o guia de início rápido para [começar a adicionar chamadas ao seu aplicativo](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configurando

### <a name="install-the-package"></a>Instalar o pacote

> [!NOTE]
> Este documento usa a versão 1.0.0-beta. 8 do SDK de chamada.

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
Em seguida, no nível do módulo build.gradle, adicione as linhas a seguir à seção de dependências

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}

```

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir tratam de alguns dos principais recursos dos serviços de comunicação do Azure que chamam o SDK:

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| O CallClient é o ponto de entrada principal para o SDK de chamada.|
| CallAgent | O CallAgent é usado para iniciar e gerenciar chamadas. |
| CommunicationTokenCredential | O CommunicationTokenCredential é usado como a credencial de token para criar uma instância do CallAgent.|
| CommunicationIdentifier | O CommunicationIdentifier é usado como um tipo diferente de participante que poderia fazer parte de uma chamada.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>Inicialize o CallClient, crie um CallAgent e acesse o DeviceManager

Para criar uma instância `CallAgent`, você precisa chamar o método `createCallAgent` em uma instância `CallClient`. Isso retorna de modo assíncrono um objeto de instância `CallAgent`.
O método `createCallAgent` usa um `CommunicationUserCredential` como argumento, que encapsula um [token de acesso](../../access-tokens.md).
Para acessar o `DeviceManager`, uma instância callAgent precisa ser criada primeiro e, depois, você poderá usar o método `CallClient.getDeviceManager` para obter o DeviceManager.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager().get();
```
Para definir um nome de exibição para o chamador, use este método alternativo:

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
DeviceManager deviceManager = callClient.getDeviceManager().get();
```


## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Realizar uma chamada de saída e ingresse em uma chamada em grupo

Para criar e iniciar uma chamada, você precisa chamar o método `CallAgent.startCall()` e fornecer o `Identifier` dos receptores da chamada.
Para ingressar em uma chamada de grupo, você precisa chamar o método `CallAgent.join()` e fornecer o groupId. As IDs de grupo precisam estar no formato GUID ou UUID.

A criação e o início da chamada são síncronos. A instância de chamada permite que você assine todos os eventos na chamada.

### <a name="place-a-11-call-to-a-user"></a>Realize uma chamada 1:1 para um usuário
Para realizar uma chamada para outro usuário dos Serviços de Comunicação, invoque o método `call` em `callAgent` e passe um objeto com a chave `communicationUserId`.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Realizar uma chamada 1:n com usuários e PSTN
> [!WARNING]
> Atualmente, a chamada PSTN não está disponível

Para realizar uma chamada 1:n para um usuário e um número de PSTN, você precisa especificar o número de telefone do receptor da chamada.
O recurso Serviços de Comunicação precisa estar configurado para permitir a chamada PSTN:
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>Realizar uma chamada 1:1 com a câmera de vídeo
> [!WARNING]
> Atualmente, apenas um fluxo de vídeo local de saída tem suporte. Para realizar uma chamada com vídeo, você precisa enumerar as câmeras locais usando a API `getCameras` do `deviceManager`.
Depois de selecionar uma câmera desejada, use-a para construir uma instância `LocalVideoStream` e passá-la no `videoOptions` como um item na matriz `localVideoStream` para um método `call`.
Quando a chamada for conectada, ela iniciará automaticamente o envio de um fluxo de vídeo da câmera selecionada para os outros participantes.

> [!NOTE]
> Devido a questões de privacidade, o vídeo não será compartilhado com a chamada se não estiver sendo visualizado localmente.
Confira a [Visualização da câmera local](#local-camera-preview) para obter mais detalhes.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameras().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.startCall(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>Ingressar em um grupo
Para iniciar uma nova chamada em grupo ou ingressar em uma chamada em grupo em andamento, chame o método 'join' e passe um objeto com uma propriedade `groupId`. O valor deve ser um GUID.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

### <a name="accept-a-call"></a>Aceitar uma chamada
Para aceitar uma chamada, chame o método 'accept' em um objeto de chamada.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

Para aceitar uma chamada com a câmera de vídeo ligada:

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

A chamada de entrada pode ser obtida assinando o evento `onIncomingCall` no objeto `callAgent`:

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```


## <a name="push-notifications"></a>Notificações por push

### <a name="overview"></a>Visão geral
As notificações por push móveis são as notificações pop-up que você vê em dispositivos móveis. Para chamadas, nos concentraremos em notificações por push de protocolo VoIP. Faremos o registro de notificações por push, a administração de notificações por push e o cancelamento do registro de notificações por push.

### <a name="prerequisites"></a>Pré-requisitos

Uma conta do Firebase configurada com o FCM (Firebase Cloud Messaging) habilitado e com o serviço do FCM conectado a uma instância do Hub de Notificações do Azure. Confira [Notificações dos Serviços de Comunicação](../../../concepts/notifications.md) para obter mais informações.
Além disso, o tutorial pressupõe que você esteja usando o Android Studio versão 3.6 ou superior para criar o seu aplicativo.

Um conjunto de permissões é necessário para que o aplicativo Android possa receber mensagens de notificações do Firebase Cloud Messaging. No arquivo `AndroidManifest.xml`, adicione o conjunto de permissões a seguir logo após *<manifest ...>* ou abaixo da marca *</application>*

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Registrar notificações por push

Para se registrar para obter notificações por push, o aplicativo precisa chamar `registerPushNotification()` em uma instância *CallAgent* com um token de registro de dispositivo.

Para obter o token de registro do dispositivo, adicione o SDK do firebase ao arquivo *Build. gradle* do seu módulo de aplicativo adicionando as seguintes linhas na `dependencies` seção se ele ainda não estiver lá:

```
    // Add the SDK for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

No arquivo *build.gradle* do nível do projeto, adicione os seguintes itens na seção `dependencies`, caso eles ainda não tenham sido adicionados:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Adicione o seguinte plug-in ao início do arquivo, caso ele ainda não tenha sido adicionado:

```
apply plugin: 'com.google.gms.google-services'
```

Selecione *Sincronizar Agora* na barra de ferramentas. Adicione o seguinte trecho de código para obter o token de registro de dispositivo gerado pelo firebase Cloud Messaging SDK para a instância do aplicativo cliente, certifique-se de adicionar as importações abaixo ao cabeçalho da atividade principal para a instância. Elas são necessárias para que o snippet recupere o token:

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Adicione esse snippet para recuperar o token:

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
Registre o token de registro de dispositivo com o SDK de serviços de chamada para notificações por push de chamada de entrada:

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Gerenciamento de notificações por push

Para receber notificações por push de chamadas de entrada, chame *handlePushNotification()* em uma instância *CallAgent* com um conteúdo.

Para obter a carga do firebase Cloud Messaging, comece criando um novo serviço (arquivo > novo serviço de > serviço de >) que estende a classe *FirebaseMessagingService* firebase SDK e substituir o `onMessageReceived` método. Esse método é o manipulador de eventos chamado quando o Firebase Cloud Messaging entrega a notificação por push ao aplicativo.

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
Adicione a seguinte definição de serviço ao arquivo `AndroidManifest.xml`, dentro da marca <application>:

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- Depois que a carga é recuperada, ela pode ser passada para o SDK *dos serviços de comunicação* a ser analisado em um objeto *IncomingCallInformation* interno que será tratado chamando o método *handlePushNotification* em uma instância *CallAgent* . Uma instância `CallAgent` é criada chamando o método `createCallAgent(...)` na classe `CallClient`.

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Quando a administração de mensagens de notificação por push for bem-sucedida e os todos os manipuladores de eventos forem registrados corretamente, o aplicativo tocará.

### <a name="unregister-push-notifications"></a>Cancelar o registro de notificações por push

Os aplicativos podem cancelar o registro de notificações por push a qualquer momento. Chame o método `unregisterPushNotification()` no callAgent para cancelar o registro.

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

Obtenha a ID exclusiva desta chamada:

```java
String callId = call.getId();
```

Para saber mais sobre outros participantes na chamada, inspecione a coleção `remoteParticipant` na instância `call`:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

A identidade do chamador se a chamada for de entrada:

```java
CommunicationIdentifier callerId = call.getCallerId();
```

Obtenha o estado da chamada: 

```java
CallState callState = call.getState();
```

Isso retorna uma cadeia de caracteres que representa o estado atual de uma chamada:
* 'None' – Estado inicial da chamada
* 'Connecting' – Estado de transição inicial após a chamada ser realizada ou aceita
* 'Ringing' – Em uma chamada de saída, indica para os participantes remotos que a chamada está tocando
* 'EarlyMedia' – Indica um estado no qual um comunicado é reproduzido antes da chamada ser conectada
* 'Connected' – A chamada está conectada
* 'LocalHold' – A chamada é colocada em espera pelo participante local, nenhuma mídia está sendo transmitida entre o ponto de extremidade local e os participantes remotos
* 'RemoteHold' – A chamada é colocada em espera por um participante remoto, nenhuma mídia está sendo transmitida entre o ponto de extremidade local e os participantes remotos
* 'Disconnecting' – Estado de transição antes que a chamada vá para o estado 'Disconnected'
* 'Disconnected' – Estado final da chamada


Para saber por que uma chamada terminou, inspecione a propriedade `callEndReason`. Ela contém o código/subcódigo: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Para ver se a chamada atual é uma chamada de entrada ou de saída, inspecione a propriedade `callDirection`:

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.Incoming for incoming call
// callDirection == CallDirection.Outgoing for outgoing call
```

Para ver se o microfone atual está no mudo, inspecione a propriedade `muted`:

```java
boolean muted = call.getIsMicrophoneMuted();
```

Para ver se a chamada atual está sendo registrada, inspecione a propriedade `isRecordingActive`:

```java
boolean recordinggActive = call.getIsRecordingActive();
```

Para inspecionar os fluxos de vídeo ativos, verifique a coleção `localVideoStreams`:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>Ativar e desativar mudo

Para ativar ou desativar o mudo no ponto de extremidade local, você pode usar as APIs `mute` e `unmute` assíncronas:

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>Iniciar e parar o envio do vídeo local

Para iniciar um vídeo, você precisa enumerar as câmeras usando a API `getCameraList` no objeto `deviceManager`. Em seguida, crie uma instância `LocalVideoStream` passando a câmera desejada e passe-a na API `startVideo` como um argumento:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);
Future startVideoFuture = call.startVideo(currentLocalVideoStream);
startVideoFuture.get();
```

Depois que você começar a enviar o vídeo, uma instância `LocalVideoStream` será adicionada à coleção `localVideoStreams` na instância de chamada.

```java
currentLocalVideoStream == call.getLocalVideoStreams().get(0);
```

Para parar o vídeo local, passe a instância `LocalVideoStream` disponível na coleção `localVideoStreams`:

```java
call.stopVideo(currentLocalVideoStream).get();
```

Você poderá alternar para um dispositivo de câmera diferente enquanto o vídeo estiver sendo enviado invocando `switchSource` em uma instância `LocalVideoStream`:
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>Gerenciamento de participantes remotos

Todos os participantes remotos são representados pelo tipo `RemoteParticipant` e estão disponíveis por meio da coleção `remoteParticipants` em uma instância de chamada.

### <a name="list-participants-in-a-call"></a>Listar os participantes em uma chamada
A coleção `remoteParticipants` retorna uma lista de participantes remotos na chamada fornecida:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>Propriedades do participante remoto
Qualquer participante remoto tem um conjunto de propriedades e coleções associadas a ele:

* Obtenha o identificador desse participante remoto.
A identidade é um dos tipos de 'Identifier'
```java
CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
```

* Obtém o estado deste participante remoto.
```java
ParticipantState state = remoteParticipant.getState();
```
O estado pode ser um dos seguintes
* 'Idle' – Estado inicial
* 'EarlyMedia' – O comunicado é reproduzido antes de o participante estar conectado à chamada
* 'Ringing' – A chamada do participante está tocando
* 'Connecting' – Estado de transição enquanto o participante está se conectando à chamada
* 'Connected' – O participante está conectado à chamada
* 'Hold' – O participante está em espera
* 'InLobby' – O participante está aguardando no lobby ser admitido. Atualmente, ele usado somente no cenário de interoperabilidade do Teams
* 'Disconnected' – O estado final, em que o participante está desconectado da chamada


* Para saber por que um participante saiu da chamada, inspecione a propriedade `callEndReason`:
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* Para verificar se esse participante remoto está mudo ou não, inspecione a propriedade `isMuted`:
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* Para verificar se esse participante remoto está falando ou não, inspecione a propriedade `isSpeaking`:
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* Para inspecionar todos os fluxos de vídeo que um determinado participante está enviando nesta chamada, verifique a coleção `videoStreams`:
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>Adicionar um participante a uma chamada

Para adicionar um participante a uma chamada (um usuário ou um número de telefone), você pode invocar `addParticipant`. Isso retornará de modo síncrono a instância do participante remoto.

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>Remover um participante de uma chamada
Para remover um participante de uma chamada (um usuário ou um número de telefone), você pode invocar `removeParticipant`.
Isso será resolvido de modo assíncrono quando o participante for removido da chamada.
O participante também será removido da coleção `remoteParticipants`.
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

## <a name="render-remote-participant-video-streams"></a>Renderizar fluxos de vídeo do participante remoto
Para listar os fluxos de vídeo e os fluxos de tela de compartilhamento de participantes remotos, inspecione as coleções `videoStreams`:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Para renderizar um `RemoteVideoStream` de um participante remoto, você precisa assinar um evento `OnVideoStreamsUpdated`.

Dentro do evento, a alteração da propriedade `isAvailable` para true indica que o participante remoto está enviando um fluxo no momento. Quando isso acontecer, crie uma instância de um `Renderer`, crie um `RendererView` usando a API `createView` assíncrona e anexe `view.target` em qualquer lugar da interface do usuário do seu aplicativo.

Sempre que a disponibilidade de um fluxo remoto for alterada, você poderá optar por destruir todo o renderizador, um `RendererView` específico ou mantê-los, mas isso resultará na exibição do quadro de vídeo em branco.

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

* `Id` – ID de um fluxo de vídeo remoto
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` – Pode ser 'Video' ou 'ScreenSharing'
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` – Indica se o ponto de extremidade do participante remoto está enviando o fluxo ativamente
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>Métodos e propriedades do renderizador
Objeto do processador que acompanha as APIs

* Crie uma instância `RendererView` que pode ser anexada posteriormente na interface do usuário do aplicativo para renderizar o fluxo de vídeo remoto.
```java
// Create a view for a video stream
renderer.createView()
```
* Descarte o renderizador e todos os `RendererView` associados a esse renderizador. Será chamado quando você tiver removido todas as exibições associadas da interface do usuário.
```java
renderer.dispose()
```

* `StreamSize` – Tamanho (largura/altura) de um fluxo de vídeo remoto
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>Métodos e propriedades do RendererView
Ao criar um `RendererView`, você pode especificar as propriedades `scalingMode` e `mirrored` que serão aplicadas a essa exibição: o modo de escala pode ser 'Stretch', 'Crop' ou 'Fit'. Se `mirrored` for configurado como `true`, o fluxo renderizado será invertido verticalmente.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

O RendererView criado pode então ser anexado à interface do usuário do aplicativo usando o seguinte snippet:
```java
layout.addView(rendererView);
```

Mais tarde, você pode atualizar o modo de escala invocando a API `updateScalingMode` no objeto RendererView com um argumento ScalingMode.Stretch, ScalingMode.Crop ou ScalingMode.Fit.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>Gerenciamento de dispositivo

O `DeviceManager` permite que você enumere dispositivos locais que podem ser usados em uma chamada para transmitir os seus fluxos de áudio/vídeo. Ele também permite solicitar permissão de um usuário para acessar o microfone e a câmera usando a API do navegador nativo.

Você pode acessar o `deviceManager` chamando o método `callClient.getDeviceManager()`.
> [!WARNING]
> Atualmente, um objeto `callAgent` precisa criar uma instância primeiro para obter acesso ao DeviceManager

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>Enumerar dispositivos locais

Para acessar dispositivos locais, você pode usar métodos de enumeração no Gerenciador de Dispositivos. A enumeração é uma ação síncrona.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>Definir o microfone/alto-falante padrão

O gerenciador de dispositivos permite que você defina um dispositivo padrão que será usado ao iniciar uma chamada.
Se os padrões do cliente não estiverem definidos, os Serviços de Comunicação retornarão para os padrões do SO.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophones().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakers().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>Visualização da câmera local

Você pode usar `DeviceManager` e `Renderer` para começar a renderizar fluxos da sua câmera local. Esse fluxo não será enviado para outros participantes; ele é um feed de visualização local. Essa é uma ação assíncrona.

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

## <a name="eventing-model"></a>Modelo de evento
Você pode assinar a maioria das propriedades e coleções para ser notificado quando os valores forem alterados.

### <a name="properties"></a>Propriedades
Para assinar eventos `property changed`:

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
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Coleções
Para assinar eventos `collection updated`:

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
