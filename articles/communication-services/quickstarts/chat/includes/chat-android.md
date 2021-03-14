---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/16/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 021abce5c6cd83257ad65f529833848d8f14f534
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750447"
---
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
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.6'
```

#### <a name="exclude-meta-files-in-packaging-options"></a>Excluir arquivos meta nas opções de empacotamento
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

Clique em 'sincronizar agora' no Android Studio.

#### <a name="alternative-to-install-libraries-through-maven"></a>(Alternativa) Para instalar bibliotecas por meio do Maven
Para importar a biblioteca para seu projeto usando o sistema de build do [Maven](https://maven.apache.org/), adicione-a à seção `dependencies` do arquivo `pom.xml` do seu aplicativo, especificando a ID de artefato e a versão que você deseja usar:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.6</version>
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
    private String second_user_id = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.6";
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
import com.azure.android.communication.chat.ChatClient;
import com.azure.android.core.http.HttpHeader;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient client = new ChatAsyncClient.Builder()
        .endpoint(endpoint)
        .credentialInterceptor(chain -> chain.proceed(chain.request()
                .newBuilder()
                .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                .build()))
        .build();
```

1. Use o `ChatAsyncClient.Builder` para configurar e criar uma instância do `ChatAsyncClient`.
2. Substitua `<resource>` pelo seu recurso dos Serviços de Comunicação.
3. Substitua `<user_access_token>` por um token de acesso válido dos Serviços de Comunicação.

## <a name="object-model"></a>Modelo de objeto
As classes e as interfaces a seguir lidam com alguns dos principais recursos da biblioteca de clientes de Chat dos Serviços de Comunicação do Azure para JavaScript.

| Name                                   | Descrição                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Essa classe é necessária para a funcionalidade de chat. Crie uma instância dela com suas informações de assinatura e use-a para criar, obter e excluir conversas. |
| ChatThreadClient/ChatThreadAsyncClient | Essa classe é necessária para a funcionalidade de conversa de chat. Obtenha uma instância por meio de ChatClient e use-a para enviar/receber/atualizar/excluir mensagens, adicionar/remover/obter usuários, enviar notificações de digitação e confirmações de leitura e assinar eventos de chat. |

## <a name="start-a-chat-thread"></a>Iniciar uma conversa de chat

Usaremos nosso `ChatClient` para criar um thread com um usuário inicial.

Substitua o comentário `<CREATE A CHAT THREAD>` pelo código a seguir:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
        .setId(id)
        .setDisplayName(displayName)
);

// The topic for the thread.
final String topic = "General";
// The model to pass to the create method.
CreateChatThreadRequest thread = new CreateChatThreadRequest()
  .setTopic(topic)
  .setParticipants(participants);

// optional, set a repeat request ID
final String repeatabilityRequestID = "";

client.createChatThread(thread, repeatabilityRequestID, new Callback<CreateChatThreadResult>() {
      public void onSuccess(CreateChatThreadResult result, okhttp3.Response response) {
              ChatThread chatThread = result.getChatThread();
              threadId = chatThread.getId();
              // take further action
              Log.i(TAG, "threadId: " + threadId);
      }

      public void onFailure(Throwable throwable, okhttp3.Response response) {
              // Handle error.
              Log.e(TAG, throwable.getMessage());
      }
});
```

Substitua `<user_id>` por uma ID de usuário válida dos Serviços de Comunicação. Usaremos o `threadId` da resposta retornada ao manipulador de conclusão em etapas posteriores, portanto, substitua o `<thread_id>` na classe pelo `threadId` que obtemos dessa solicitação e execute novamente o aplicativo.

## <a name="get-a-chat-thread-client"></a>Obter um cliente de conversa de chat

Agora que criamos um thread de chat, obteremos um `ChatThreadClient` para executar operações dentro do thread. Substitua o comentário `<CREATE A CHAT THREAD CLIENT>` pelo código a seguir:

```
ChatThreadAsyncClient threadClient =
        new ChatThreadAsyncClient.Builder()
                .endpoint(endpoint)
                .credentialInterceptor(chain -> chain.proceed(chain.request()
                    .newBuilder()
                    .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                    .build()))
                .build();
```

## <a name="send-a-message-to-a-chat-thread"></a>Enviar uma mensagem para uma conversa de chat

Verifique se substituímos `<thread_id>` por uma ID de thread válida. Enviaremos a mensagem para esse thread agora.

Substitua o comentário `<SEND A MESSAGE>` pelo código a seguir:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageRequest message = new SendChatMessageRequest()
        .setType(ChatMessageType.TEXT)
        .setContent(content)
        .setSenderDisplayName(senderDisplayName);

threadClient.sendChatMessage(threadId, message, new Callback<String>() {
        @Override
        public void onSuccess(String messageId, okhttp3.Response response) {
                // A string is the response returned from sending a message, it is an id,
                // which is the unique ID of the message.
                chatMessageId = messageId;
                // Take further action.
                Log.i(TAG, "chatMessageId: " + chatMessageId);
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Depois de obter `chatMessageId`, podemos substituir `<chat_message_id>` pelo `chatMessageId` para uso do método posteriormente no início rápido e executar novamente o aplicativo.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adicionar um usuário como participante ao thread de chat

Substitua o comentário `<ADD A USER>` pelo código a seguir:

```java
//  The list of ChatParticipant to be added to the thread.
participants = new ArrayList<>();
// The display name for the thread participant.
displayName = "a new participant";
participants.add(new ChatParticipant().setId(second_user_id).setDisplayName(secondUserDisplayName));
// The model to pass to the add method.
AddChatParticipantsRequest addParticipantsRequest = new AddChatParticipantsRequest()
  .setParticipants(participants);

threadClient.addChatParticipants(threadId, addParticipantsRequest, new Callback<AddChatParticipantsResult>() {
        @Override
        public void onSuccess(AddChatParticipantsResult result, okhttp3.Response response) {
                // Take further action.
                Log.i(TAG, "add chat participants success");
        }
        
        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Substitua `<second_user_id>` na classe pela ID de usuário dos Serviços de Comunicação do usuário a ser adicionado. 

## <a name="list-users-in-a-thread"></a>Listar os usuários em um thread

Substitua o comentário `<LIST USERS>` pelo seguinte código:

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

threadClient.listChatParticipantsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection,
            okhttp3.Response response) {
                // pageCollection enables enumerating list of chat participants.
                pageCollection.getFirstPage(new Callback<Page<ChatParticipant>>() {
                    @Override
                    public void onSuccess(Page<ChatParticipant> firstPage, okhttp3.Response response) {
                        for (ChatParticipant participant : firstPage.getItems()) {
                            // Take further action.
                            Log.i(TAG, "participant: " + participant.getDisplayName());
                        }
                        listChatParticipantsNext(firstPage.getNextPageId(), pageCollection);
                    }

                    @Override
                    public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                    }
                });

                @Override
                public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                }
                });
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Insira o seguinte método auxiliar na classe:

```java
void listChatParticipantsNext(String nextLink, AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatParticipant>>() {
            @Override
            public void onSuccess(Page<ChatParticipant> nextPage, Response response) {
                for (ChatParticipant participant : nextPage.getItems()) {
                    // Take further action.
                    Log.i(TAG, "participant: " + participant.getDisplayName());
                }
                if (nextPage.getPageId() != null) {
                    listChatParticipantsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}
```


## <a name="remove-user-from-a-chat-thread"></a>Remover um usuário de uma conversa de chat

Substitua `<second_user_id>` por uma ID de usuário válida. Vamos remover o segundo usuário do thread agora.

Substitua o comentário `<REMOVE A USER>` pelo seguinte código:

```java
threadClient.removeChatParticipant(threadId, second_user_id, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, okhttp3.Response response) {
        // Take further action.
        Log.i(TAG, "remove a user successfully");
    }

    @Override
    public void onFailure(Throwable throwable, okhttp3.Response response) {
        // Handle error.
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-typing-notification"></a>Enviar uma notificação de digitação

Substitua o comentário `<SEND A TYPING NOTIFICATION>` pelo seguinte código:

```java
threadClient.sendTypingNotification(threadId, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a typing notification successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-read-receipt"></a>Enviar uma confirmação de leitura

Substitua `<chat_message_id>` por uma ID de mensagem de chat válida. Enviaremos a confirmação de leitura desta mensagem agora.

Substitua o comentário `<SEND A READ RECEIPT>` pelo seguinte código:

```java
SendReadReceiptRequest readReceipt = new SendReadReceiptRequest()
    .setChatMessageId(chatMessageId);
threadClient.sendChatReadReceipt(threadId, readReceipt, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a read receipt successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="list-read-receipts"></a>Listar confirmações de leitura

Substitua o comentário `<READ RECEIPTS>` pelo seguinte código:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
threadClient.listChatReadReceiptsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection,
                              Response response) {
            // pageCollection enables enumerating list of chat participants.
            pageCollection.getFirstPage(new Callback<Page<ChatMessageReadReceipt>>() {
                @Override
                public void onSuccess(Page<ChatMessageReadReceipt> firstPage, Response response) {
                    for (ChatMessageReadReceipt receipt : firstPage.getItems()) {
                        Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                    }
                    listChatReadReceiptsNext(firstPage.getNextPageId(), pageCollection);
                }

                @Override
                public void onFailure(Throwable throwable, Response response) {
                    Log.e(TAG, throwable.getMessage());
                }
            });
        }

        @Override
        public void onFailure(Throwable throwable, Response response) {
            Log.e(TAG, throwable.getMessage());
        }
});
```

Insira o seguinte método auxiliar na classe:
```java
void listChatReadReceiptsNext(String nextLink, AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatMessageReadReceipt>>() {
            @Override
            public void onSuccess(Page<ChatMessageReadReceipt> nextPage, Response response) {
                for (ChatMessageReadReceipt receipt : nextPage.getItems()) {
                    Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                }
                if (nextPage.getPageId() != null) {
                    listChatReadReceiptsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}

```


## <a name="run-the-code"></a>Executar o código

No Android Studio, pressione o botão Executar para criar e executar o projeto. No console, você pode exibir a saída do código e a saída do agente do ChatClient.
