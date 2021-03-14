---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 192970540f21905b624ce3f6e3558baa935a4d7a
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489680"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK (Java Development Kit)](/java/azure/jdk/) versão 8 ou superior.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Um recurso e uma cadeia de conexão dos Serviços de Comunicação implantados. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um [token de acesso do usuário](../../access-tokens.md). Defina o escopo como "chat" e anote a cadeia de caracteres do token, bem como a cadeia de caracteres da userId.


## <a name="setting-up"></a>Configurando

### <a name="create-a-new-java-application"></a>Criar um aplicativo Java

Abra o terminal ou a janela de comando e navegue até o diretório no qual você deseja criar o seu aplicativo Java. Execute o comando abaixo para gerar o projeto Java no modelo maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Você observará que a meta 'gerar' criou um diretório com o mesmo nome que artifactId. Nesse diretório, o `src/main/java directory` contém o código-fonte do projeto, o diretório `src/test/java` contém a fonte de teste e o arquivo pom.xml é o Modelo de Objeto do Projeto ou [POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) do projeto.

Atualize o arquivo POM do seu aplicativo para usar o Java 8 ou superior:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-client-library"></a>Adicionar as referências de pacote à biblioteca de clientes de chat

Em seu arquivo POM, referencie o pacote `azure-communication-chat` com as APIs do Chat:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0-beta.4</version> 
</dependency>
```

Para autenticação, o cliente precisa fazer referência ao pacote `azure-communication-common`:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0</version> 
</dependency>
```

## <a name="object-model"></a>Modelo de objeto

As classes e as interfaces a seguir lidam com alguns dos principais recursos da biblioteca de clientes do Chat dos Serviços de Comunicação do Azure para Java.

| Name                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Essa classe é necessária para a funcionalidade de chat. Crie uma instância dela com suas informações de assinatura e use-a para criar, obter e excluir conversas. |
| ChatAsyncClient | Essa classe é necessária para a funcionalidade de Chat assíncrona. Crie uma instância dela com suas informações de assinatura e use-a para criar, obter e excluir conversas. |
| ChatThreadClient | Essa classe é necessária para a funcionalidade de conversa de chat. Obtenha uma instância por meio de ChatClient e use-a para enviar/receber/atualizar/excluir mensagens, adicionar/remover/obter usuários e enviar notificações de digitação e confirmações de leitura. |
| ChatThreadAsyncClient | Essa classe é necessária para a funcionalidade de Conversa de Chat assíncrona. Obtenha uma instância por meio de ChatAsyncClient e use-a para enviar/receber/atualizar/excluir mensagens, adicionar/remover/obter usuários, enviar notificações de digitação e confirmações de leitura. |

## <a name="create-a-chat-client"></a>Criar um cliente de chat
Para criar um cliente de chat, você usará o ponto de extremidade do Serviço de Comunicação e o token de acesso que foi gerado como parte das etapas de pré-requisito. Os tokens de acesso do usuário permitem que você crie aplicativos cliente que se autenticam diretamente nos Serviços de Comunicação do Azure. Depois de gerar esses tokens no servidor, transmita-os novamente para um dispositivo cliente. Você precisa usar a classe CommunicationTokenCredential da biblioteca de clientes comum para passar o token para seu cliente de chat. 

Saiba mais sobre a [Arquitetura de Chat](../../../concepts/chat/concepts.md)

Ao adicionar as instruções de importação, lembre-se de adicionar somente importações dos namespaces com.azure.communication.chat e com.azure.communication.chat.models, e não do namespace com.azure.communication.chat.implementation. No arquivo App.java que foi gerado pelo Maven, você pode usar o seguinte código para começar:

```Java
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");
        
        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```


## <a name="start-a-chat-thread"></a>Iniciar uma conversa de chat

Use o método `createChatThread` para criar uma conversa de chat.
`createChatThreadOptions` é usado para descrever a solicitação de conversa.

- Use `topic` para fornecer um tópico a esse chat; o tópico pode ser atualizado depois que a conversa de chat é criada por meio da função `UpdateThread`.
- Use `participants` para listar os participantes do thread a serem adicionados a ele. `ChatParticipant` usa o usuário que você criou no guia de início rápido do [Token de Acesso do Usuário](../../access-tokens.md).

A resposta `chatThreadClient` é usada para executar operações no thread de chat criado: adicionando participantes ao thread de chat, enviando uma mensagem, excluindo uma mensagem etc. Ela contém uma propriedade `chatThreadId`, que é a ID exclusiva da conversa de chat. A propriedade pode ser acessada pelo método público .getChatThreadId().

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(firstUser)
    .setDisplayName("Participant Display Name 1");
    
ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(secondUser)
    .setDisplayName("Participant Display Name 2");

participants.add(firstThreadParticipant);
participants.add(secondThreadParticipant);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setParticipants(participants);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
```

## <a name="send-a-message-to-a-chat-thread"></a>Enviar uma mensagem para uma conversa de chat

Use o método `sendMessage` para enviar uma mensagem à conversa que você acabou de criar, identificada por chatThreadId.
`sendChatMessageOptions` é usado para descrever a solicitação de mensagem de chat.

- Use `content` para fornecer o conteúdo da mensagem de chat.
- Use `type` para especificar o tipo de conteúdo de mensagem de chat, TEXTO ou HTML.
- Use `senderDisplayName` para especificar o nome de exibição do remetente.

A resposta `sendChatMessageResult` contém um `id`, que é a ID exclusiva da mensagem.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```


## <a name="get-a-chat-thread-client"></a>Obter um cliente de conversa de chat

O método `getChatThreadClient` retorna um cliente de conversa para uma conversa que já existe. Ele pode ser usado para executar operações no thread criado: adicionar participantes, enviar mensagens etc. `chatThreadId` é a ID exclusiva do thread de chat existente.

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Receber mensagens de chat de uma conversa de chat

Você pode recuperar mensagens de chat sondando o método `listMessages` no cliente da conversa de chat em intervalos especificados.

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages` retorna a versão mais recente da mensagem, incluindo as edições ou as exclusões que ocorreram na mensagem usando .editMessage() e .deleteMessage(). Para mensagens excluídas, `chatMessage.getDeletedOn()` retorna um valor datetime que indica quando a mensagem foi excluída. Para as mensagens editadas, `chatMessage.getEditedOn()` retorna um datetime indicando quando a mensagem foi editada. A hora original da criação da mensagem pode ser acessada usando `chatMessage.getCreatedOn()` e pode ser usada para ordenar as mensagens.

`listMessages` retorna diferentes tipos de mensagens que podem ser identificadas por `chatMessage.getType()`. Esses tipos são:

- `text`: mensagem de chat regular enviada por um participante do thread.

- `html`: mensagem de chat em HTML enviada por um participante da conversa.

- `topicUpdated`: mensagem do sistema que indica que o tópico foi atualizado.

- `participantAdded`: mensagem do sistema que indica que um ou mais participantes foram adicionados ao thread do chat.

- `participantRemoved`: mensagem do sistema que indica que um participante foi removido do thread de chat.

Para obter mais detalhes, confira [Tipos de mensagem](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Adicionar um usuário como participante ao thread de chat

Depois que uma conversa de chat é criada, você pode adicionar e remover usuários nela. Ao adicionar usuários, você permite a eles acesso para enviar mensagens ao thread de chat e adicionar/remover outros participantes. Você precisará começar obtendo um novo token de acesso e uma identidade para esse usuário. Para chamar o método addParticipants, verifique se você adquiriu um novo token de acesso e uma identidade para esse usuário. O usuário precisará desse token de acesso para inicializar o cliente de chat.

Use o método `addParticipants` para adicionar participantes do thread identificado por threadId.

- Use `listParticipants` para listar os participantes a serem adicionados ao thread de chat.
- `communicationIdentifier`, obrigatório, é o CommunicationIdentifier que você criou por CommunicationIdentityClient no início rápido do [Token de Acesso do Usuário](../../access-tokens.md).
- `display_name`, opcional, é o nome de exibição do participante do thread.
- `share_history_time`, opcional, é a hora a partir da qual o histórico de chats é compartilhado com o participante. Para compartilhar o histórico desde o início da conversa de chat, defina essa propriedade como qualquer data igual ou inferior à hora de criação da conversa. Para não compartilhar nenhum histórico anterior ao momento em que o participante foi adicionado, defina-a como a data atual. Para compartilhar o histórico parcial, defina-o como a data necessária.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Display Name 2");

participants.add(firstThreadParticipant);
participants.add(secondThreadParticipant);

AddChatParticipantsOptions addChatParticipantsOptions = new AddChatParticipantsOptions()
    .setParticipants(participants);
chatThreadClient.addParticipants(addChatParticipantsOptions);
```

## <a name="remove-participant-from-a-chat-thread"></a>Remover um participante de uma conversa de chat

Assim como adicionar um participante a uma conversa, você pode remover participantes de uma conversa de chat. Para isso, você precisa acompanhar as identidades dos participantes que você adicionou.

Use `removeParticipant`, em que `identifier` é o CommunicationIdentifier que você criou.

```Java
chatThreadClient.removeParticipant(identity);
```

## <a name="run-the-code"></a>Executar o código

Navegue até o diretório que contém o arquivo *pom.xml* e compile o projeto usando o comando `mvn` a seguir.

```console
mvn compile
```

Em seguida, compile o pacote.

```console
mvn package
```

Execute o comando `mvn` a seguir para executar o aplicativo.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```