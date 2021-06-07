---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: b2c5237f3f7e949edbfb5486a3a17cc6e0a008a4
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178578"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

- Criar uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instalar o [Android Studio](https://developer.android.com/studio). Usaremos o Android Studio a fim de criar um aplicativo Android para o início rápido para instalar dependências.
- Criar um recurso dos Serviços de Comunicação do Azure. Para obter detalhes, confira [Criar um recurso de comunicação do Azure](../../create-communication-resource.md). Você precisará **registrar o ponto de extremidade do recurso** para este guia de início rápido.
- Crie **dois** usuários dos Serviços de Comunicação e emita-os como um token de acesso do usuário [Token de Acesso do Usuário](../../access-tokens.md). Defina o escopo como **chat** e **anote a cadeia de caracteres do token e a cadeia de caracteres da userId**. Neste guia de início rápido, criaremos um thread com um participante inicial e adicionaremos um segundo participante a ele.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-android-application"></a>Criar um aplicativo Android

1. Abra o Android Studio e selecione `Create a new project`. 
2. Na próxima janela, selecione `Empty Activity` como o modelo do projeto.
3. Ao escolher as opções, insira `ChatQuickstart` como o nome do projeto.
4. Clique em avançar e escolha o diretório em que você deseja que o projeto seja criado.

### <a name="install-the-libraries"></a>Instalar as bibliotecas

Usaremos Gradle para instalar as dependências necessárias dos Serviços de Comunicação. Na linha de comando, navegue para dentro do diretório raiz do projeto `ChatQuickstart`. Abra o arquivo build.gradle do aplicativo e adicione as seguintes dependências ao destino `ChatQuickstart`:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.8'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.8'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>Exclua os metarquivos nas opções de empacotamento na raiz build.gradle
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>(Alternativa) Para instalar bibliotecas por meio do Maven
Para importar a biblioteca para seu projeto usando o sistema de build do [Maven](https://maven.apache.org/), adicione-a à seção `dependencies` do arquivo `pom.xml` do seu aplicativo, especificando a ID de artefato e a versão que você deseja usar:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.8</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Configurar os espaços reservados

Abra e edite o arquivo `MainActivity.java`. Neste Início Rápido, adicionaremos nosso código a `MainActivity` e veremos a saída no console. Este guia de início rápido não aborda a criação de uma interface do usuário. Na parte superior do arquivo, importe as bibliotecas `Communication common` e `Communication chat`:

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Copie o seguinte código no arquivo `MainActivity`:

```java
    private String secondUserId = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.8";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "--------------Chat Quickstart App-------------";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

Nas etapas a seguir, substituiremos os espaços reservados pelo exemplo de código usando a biblioteca de chat dos Serviços de Comunicação do Azure.


### <a name="create-a-chat-client"></a>Criar um cliente de chat

Substitua o comentário `<CREATE A CHAT CLIENT>` pelo seguinte código (coloque as instruções de importação na parte superior do arquivo):

```java
import com.azure.android.communication.chat.ChatAsyncClient;
import com.azure.android.communication.chat.ChatClientBuilder;
import com.azure.android.core.credential.AccessToken;
import com.azure.android.core.http.HttpHeader;
import com.azure.android.core.http.okhttp.OkHttpAsyncClientProvider;
import com.azure.android.core.http.policy.BearerTokenAuthenticationPolicy;
import com.azure.android.core.http.policy.UserAgentPolicy;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .buildAsyncClient();

```

1. Use o `ChatClientBuilder` para configurar e criar uma instância do `ChatAsyncClient`.
2. Substitua `<resource>` pelo seu recurso dos Serviços de Comunicação.
3. Substitua `<user_access_token>` por um token de acesso válido dos Serviços de Comunicação.

## <a name="object-model"></a>Modelo de objeto
As classes e as interfaces a seguir lidam com alguns dos principais recursos do SDK de Chat dos Serviços de Comunicação do Azure para JavaScript.

| Nome                                   | Descrição                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Essa classe é necessária para a funcionalidade de chat. Crie uma instância dela com suas informações de assinatura e use-a para criar, obter e excluir conversas. |
| ChatThreadClient/ChatThreadAsyncClient | Essa classe é necessária para a funcionalidade de conversa de chat. Obtenha uma instância por meio de ChatClient e use-a para enviar/receber/atualizar/excluir mensagens, adicionar/remover/obter usuários, enviar notificações de digitação e confirmações de leitura e assinar eventos de chat. |

## <a name="start-a-chat-thread"></a>Iniciar uma conversa de chat

Usaremos nosso `ChatAsyncClient` para criar um thread com um usuário inicial.

Substitua o comentário `<CREATE A CHAT THREAD>` pelo código a seguir:

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(id))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

Substitua `<user_id>` por uma ID de usuário válida dos Serviços de Comunicação. Usaremos o `threadId` da resposta retornada ao manipulador de conclusão em etapas posteriores, portanto, substitua o `<thread_id>` na classe pelo `threadId` que obtemos dessa solicitação e execute novamente o aplicativo.

## <a name="get-a-chat-thread-client"></a>Obter um cliente de conversa de chat

Agora que criamos um thread de chat, obteremos um `ChatThreadAsyncClient` para executar operações dentro do thread. Substitua o comentário `<CREATE A CHAT THREAD CLIENT>` pelo código a seguir:

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>Enviar uma mensagem para uma conversa de chat

Verifique se substituímos `<thread_id>` por uma ID de thread válida. Enviaremos a mensagem para esse thread agora.

Substitua o comentário `<SEND A MESSAGE>` pelo código a seguir:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// A string is the response returned from sending a message, it is an id, which is the unique ID of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

Depois de obter `chatMessageId`, podemos substituir `<chat_message_id>` pelo `chatMessageId` para uso do método posteriormente no início rápido e executar novamente o aplicativo.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adicionar um usuário como participante ao thread de chat

Substitua o comentário `<ADD A USER>` pelo código a seguir:

```java
// The display name for the thread participant.
displayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```

Substitua `<second_user_id>` na classe pela ID de usuário dos Serviços de Comunicação do usuário a ser adicionado. 

## <a name="list-users-in-a-thread"></a>Listar os usuários em um thread

Substitua o comentário `<LIST USERS>` pelo seguinte código:

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatParticipant> firstPageWithResponse =
    chatThreadAsyncClient.getParticipantsFirstPageWithResponse(listParticipantsOptions, Context.NONE).get();

for (ChatParticipant participant : firstPageWithResponse.getValue()) {
    // You code to handle participant
}

listParticipantsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Insira o seguinte método auxiliar na classe:

```java
void listParticipantsNextPage(String continuationToken, int pageNumber) {
if (continuationToken != null) {
    PagedResponse<ChatParticipant> nextPageWithResponse =
        chatThreadAsyncClient.getParticipantsNextPageWithResponse(continuationToken, Context.NONE).get();
        for (ChatParticipant participant : nextPageWithResponse.getValue()) {
            // You code to handle participant
        }
            
        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="remove-user-from-a-chat-thread"></a>Remover um usuário de uma conversa de chat

Substitua `<second_user_id>` por uma ID de usuário válida. Vamos remover o segundo usuário do thread agora.

Substitua o comentário `<REMOVE A USER>` pelo seguinte código:

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>Enviar uma notificação de digitação

Substitua o comentário `<SEND A TYPING NOTIFICATION>` pelo seguinte código:

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>Enviar uma confirmação de leitura

Substitua `<chat_message_id>` por uma ID de mensagem de chat válida. Enviaremos a confirmação de leitura desta mensagem agora.

Substitua o comentário `<SEND A READ RECEIPT>` pelo seguinte código:

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>Listar confirmações de leitura

Substitua o comentário `<READ RECEIPTS>` pelo seguinte código:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatMessageReadReceipt> firstPageWithResponse =
    chatThreadAsyncClient.getReadReceiptsFirstPageWithResponse(listReadReceiptOptions, Context.NONE).get();

for (ChatMessageReadReceipt readReceipt : firstPageWithResponse.getValue()) {
    // You code to handle readReceipt
}

listReadReceiptsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Insira o seguinte método auxiliar na classe:
```java
void listReadReceiptsNextPage(String continuationToken, int pageNumber) {
    if (continuationToken != null) {
        PagedResponse<ChatMessageReadReceipt> nextPageWithResponse =
            chatThreadAsyncClient.getReadReceiptsNextPageWithResponse(continuationToken, Context.NONE).get();

        for (ChatMessageReadReceipt readReceipt : nextPageWithResponse.getValue()) {
            // You code to handle readReceipt
        }

        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="run-the-code"></a>Executar o código

No Android Studio, pressione o botão Executar para criar e executar o projeto. No console, você pode exibir a saída do código e a saída do agente do ChatClient.
