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
ms.openlocfilehash: 4c8bd66dde54ff90ea2191fba58f10c87c45cf68
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958089"
---
## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, é preciso:
- Criar uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instalar o [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- Criar um recurso dos Serviços de Comunicação do Azure. Para obter detalhes, confira [Criar um recurso de comunicação do Azure](../../create-communication-resource.md). Você precisará registrar o **ponto de extremidade** do recurso para este guia de início rápido.
- Um [token de acesso do usuário](../../access-tokens.md). Defina o escopo como "chat" e anote a cadeia de caracteres do token, bem como a cadeia de caracteres da userId.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `ChatQuickstart`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: **Program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Altere o seu diretório para a pasta de aplicativo recém-criada e use o comando `dotnet build` para compilar o seu aplicativo.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalar o pacote

Instalar o SDK de Chat de Comunicação do Azure para .NET

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0
```

## <a name="object-model"></a>Modelo de objeto

As classes a seguir lidam com alguns dos principais recursos do SDK de Chat dos Serviços de Comunicação do Azure para C#.

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Essa classe é necessária para a funcionalidade de chat. Crie uma instância dela com suas informações de assinatura e use-a para criar, obter e excluir conversas. |
| ChatThreadClient | Essa classe é necessária para a funcionalidade de conversa de chat. Obtenha uma instância por meio de ChatClient e use-a para enviar/receber/atualizar/excluir mensagens, adicionar/remover/obter participantes e enviar notificações de digitação e confirmações de leitura. |

## <a name="create-a-chat-client"></a>Criar um cliente de chat

Para criar um cliente de chat, você usará o ponto de extremidade dos Serviços de Comunicação e o token de acesso que foi gerado como parte das etapas de pré-requisito. Você precisa usar a classe `CommunicationIdentityClient` do SDK de Identidade para criar um usuário e emitir um token a ser passado para o cliente de chat.

Saiba mais sobre os [tokens de acesso do usuário](../../access-tokens.md).

Este guia de início rápido não abrange a criação de uma camada de serviço para gerenciar tokens no seu aplicativo de chat, embora isso seja recomendado. Saiba mais sobre a [Arquitetura de Chat](../../../concepts/chat/concepts.md)

Copie os seguintes snippets de código e cole-os no arquivo de origem: **Program.cs**
```csharp
using Azure;
using Azure.Communication;
using Azure.Communication.Chat;
using System;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>Iniciar uma conversa de chat

Use o método `createChatThread` no chatClient para criar uma conversa de chat.
- Use `topic` para fornecer um tópico a esse chat; o tópico pode ser atualizado depois que a conversa de chat é criada por meio da função `UpdateTopic`.
- Use a propriedade `participants` para passar uma lista de objetos `ChatParticipant` a serem adicionados à conversa de chat. O objeto `ChatParticipant` é inicializado com um objeto `CommunicationIdentifier`. `CommunicationIdentifier` poderia ser do tipo `CommunicationUserIdentifier`, `MicrosoftTeamsUserIdentifier` ou `PhoneNumberIdentifier`. Por exemplo, para obter um objeto `CommunicationIdentifier`, será necessário passar uma ID de Acesso que você criou seguindo a instrução para [Criar um usuário](../../access-tokens.md#create-an-identity)

O objeto de resposta do método `createChatThread` contém os detalhes de `chatThread`. Para interagir com as operações do thread de chat, por exemplo, adicionar participantes, enviar uma mensagem, excluir uma mensagem etc., uma instância de cliente de `chatThreadClient` precisa criar uma instância usando o método `GetChatThreadClient` no cliente `ChatClient`.

```csharp
var chatParticipant = new ChatParticipant(identifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Obter um cliente de conversa de chat
O método `GetChatThreadClient` retorna um cliente de conversa para uma conversa que já existe. Ele pode ser usado para executar operações na conversa criada: adicionar membros, enviar mensagens etc. threadId é a ID exclusiva da conversa de chat existente.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: threadId);
```

## <a name="list-all-chat-threads"></a>Listar todas as conversas de chat
Use `GetChatThreads` para recuperar todas as conversas de chat das quais o usuário faz parte.

```csharp
AsyncPageable<ChatThreadItem> chatThreadItems = chatClient.GetChatThreadsAsync();
await foreach (ChatThreadItem chatThreadItem in chatThreadItems)
{
    Console.WriteLine($"{ chatThreadItem.Id}");
}
```

## <a name="send-a-message-to-a-chat-thread"></a>Enviar uma mensagem para uma conversa de chat

Use `SendMessage` para enviar uma mensagem para uma conversa.

- Use `content` para fornecer o conteúdo da mensagem; ele é obrigatório.
- Use `type` para o tipo de conteúdo da mensagem, como "Text" ou "HTML". Se não for especificado, a opção "Text" será a usada.
- Use `senderDisplayName` para especificar o nome de exibição do remetente. Se não for especificado, uma cadeia de caracteres vazia será a usada.

```csharp
SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content:"hello world", type: ChatMessageType.Text);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Receber mensagens de chat de uma conversa de chat

Você pode recuperar mensagens de chat sondando o método `GetMessages` no cliente da conversa de chat em intervalos especificados.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Content.Message}");
}
```

`GetMessages` usa um parâmetro `DateTimeOffset` opcional. Se esse deslocamento for especificado, você receberá mensagens que foram recebidas, atualizadas ou excluídas depois dele. Observe que as mensagens recebidas antes da hora de deslocamento, mas editadas ou removidas depois dela, também serão retornadas.

`GetMessages` retorna a última versão da mensagem, incluindo as edições ou as exclusões que ocorreram na mensagem usando `UpdateMessage` e `DeleteMessage`. Para mensagens excluídas, `chatMessage.DeletedOn` retorna um valor datetime que indica quando a mensagem foi excluída. Para as mensagens editadas, `chatMessage.EditedOn` retorna um datetime indicando quando a mensagem foi editada. A hora original da criação da mensagem pode ser acessada usando `chatMessage.CreatedOn` e pode ser usada para ordenar as mensagens.

`GetMessages` retorna diferentes tipos de mensagens que podem ser identificadas por `chatMessage.Type`. Esses tipos são:

- `Text`: mensagem de chat normal enviada por um membro da conversa.

- `Html`: uma mensagem de texto formatada. Observe que os usuários dos Serviços de Comunicação não podem enviar mensagens RichText no momento. Há suporte para esse tipo de mensagem nas mensagens enviadas de usuários do Teams para os usuários dos Serviços de Comunicação em cenários de interoperabilidade do Teams.

- `TopicUpdated`: mensagem do sistema que indica que o tópico foi atualizado. (readonly)

- `ParticipantAdded`: mensagem do sistema que indica que um ou mais participantes foram adicionados à conversa do chat.(readonly)

- `ParticipantRemoved`: mensagem do sistema que indica que um participante foi removido do thread de chat.

Para obter mais detalhes, confira [Tipos de mensagem](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adicionar um usuário como participante ao thread de chat

Depois que uma conversa é criada, você pode adicionar e remover usuários dela. Quando você adiciona usuários, você concede a eles acesso para poderem enviar mensagens para a conversa e adicionar/remover outro participante. Para chamar `AddParticipants`, verifique se você adquiriu um novo token de acesso e uma identidade para esse usuário. O usuário precisará desse token de acesso para inicializar o cliente de chat.

Use `AddParticipants` para adicionar um ou mais participantes à conversa de chat. A seguir, estão os atributos com suporte para participantes de cada conversa:
- `communicationUser`, obrigatório, é a identidade do participante da conversa.
- `displayName`, opcional, é o nome de exibição do participante do thread.
- `shareHistoryTime`, opcional, é o horário no qual o histórico do chat é compartilhado com o participante.

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants: participants);
```

## <a name="get-thread-participants"></a>Obter participantes da conversa

Use `GetParticipants` para recuperar os participantes da conversa de chat.

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-read-receipt"></a>Enviar confirmação de leitura

Use `SendReadReceipt` para notificar outros participantes de que a mensagem foi lida pelo usuário.

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId: messageId);
```

## <a name="run-the-code"></a>Executar o código

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```console
dotnet run
```
