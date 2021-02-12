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
ms.openlocfilehash: a76c6467dac69fd3d21aa659c52227046c166938
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816684"
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

Instale a biblioteca de clientes do Chat de Comunicação do Azure para .NET

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.3
``` 

## <a name="object-model"></a>Modelo de objeto

As classes a seguir lidam com alguns dos principais recursos da biblioteca de clientes do Chat dos Serviços de Comunicação do Azure para C#.

| Name                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Essa classe é necessária para a funcionalidade de chat. Crie uma instância dela com suas informações de assinatura e use-a para criar, obter e excluir conversas. |
| ChatThreadClient | Essa classe é necessária para a funcionalidade de conversa de chat. Obtenha uma instância por meio de ChatClient e use-a para enviar/receber/atualizar/excluir mensagens, adicionar/remover/obter usuários e enviar notificações de digitação e confirmações de leitura. |

## <a name="create-a-chat-client"></a>Criar um cliente de chat

Para criar um cliente de chat, você usará o ponto de extremidade dos Serviços de Comunicação e o token de acesso que foi gerado como parte das etapas de pré-requisito. Você precisa usar a classe `CommunicationIdentityClient` da biblioteca de clientes `Administration` para criar um usuário e emitir um token a ser passado para o cliente de chat. Saiba mais sobre os [tokens de acesso do usuário](../../access-tokens.md).

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>Iniciar uma conversa de chat

Use o método `createChatThread` para criar uma conversa de chat.
- Use `topic` para fornecer um tópico a esse chat; o tópico pode ser atualizado depois que a conversa de chat é criada por meio da função `UpdateThread`.
- Use a propriedade `members` para passar uma lista de objetos `ChatThreadMember` a serem adicionados à conversa de chat. O objeto `ChatThreadMember` é inicializado com um objeto `CommunicationUser`. Para obter um objeto `CommunicationUser`, será necessário passar uma ID de Acesso que você criou seguindo a instrução para [Criar um usuário](../../access-tokens.md#create-an-identity)

A resposta `chatThreadClient` é usada para executar operações na conversa de chat criada: adicionando membros à conversa de chat, enviando uma mensagem, excluindo uma mensagem etc. Ela contém o atributo `Id`, que é a ID exclusiva da conversa de chat. 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Obter um cliente de conversa de chat
O método `GetChatThreadClient` retorna um cliente de conversa para uma conversa que já existe. Ele pode ser usado para executar operações na conversa criada: adicionar membros, enviar mensagens etc. threadId é a ID exclusiva da conversa de chat existente.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Enviar uma mensagem para uma conversa de chat

Use o método `SendMessage` para enviar uma mensagem a uma conversa identificada por threadId.

- Use `content` para fornecer o conteúdo da mensagem de chat; ele é obrigatório.
- Use `priority` para especificar o nível de prioridade da mensagem, como 'Normal' ou 'Alto'; se não for especificado, será usado 'Normal'.
- Use `senderDisplayName` para especificar o nome de exibição do remetente; se não for especificado, será usado um nome vazio.

`SendChatMessageResult` é a resposta retornada do envio de uma mensagem; ela contém uma ID, que é a ID exclusiva da mensagem.

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Receber mensagens de chat de uma conversa de chat

Você pode recuperar mensagens de chat sondando o método `GetMessages` no cliente da conversa de chat em intervalos especificados.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

`GetMessages` usa um parâmetro `DateTimeOffset` opcional. Se esse deslocamento for especificado, você receberá mensagens que foram recebidas, atualizadas ou excluídas depois dele. Observe que as mensagens recebidas antes da hora de deslocamento, mas editadas ou removidas depois dela, também serão retornadas.

`GetMessages` retorna a última versão da mensagem, incluindo as edições ou as exclusões que ocorreram na mensagem usando `UpdateMessage` e `DeleteMessage`. Para mensagens excluídas, `chatMessage.DeletedOn` retorna um valor datetime que indica quando a mensagem foi excluída. Para as mensagens editadas, `chatMessage.EditedOn` retorna um datetime indicando quando a mensagem foi editada. A hora original da criação da mensagem pode ser acessada usando `chatMessage.CreatedOn` e pode ser usada para ordenar as mensagens.

`GetMessages` retorna diferentes tipos de mensagens que podem ser identificadas por `chatMessage.Type`. Esses tipos são:

- `Text`: mensagem de chat normal enviada por um membro da conversa.

- `ThreadActivity/TopicUpdate`: mensagem do sistema que indica que o tópico foi atualizado.

- `ThreadActivity/AddMember`: mensagem do sistema que indica que um ou mais membros foram adicionados à conversa de chat.

- `ThreadActivity/DeleteMember`: mensagem do sistema que indica que um membro foi removido da conversa de chat.

Para obter mais detalhes, confira [Tipos de mensagem](../../../concepts/chat/concepts.md#message-types).

## <a name="update-a-message"></a>Atualizar uma mensagem

Você pode atualizar uma mensagem que já foi enviada invocando `UpdateMessage` em `ChatThreadClient`.

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>Excluir uma mensagem

Você pode excluir uma mensagem invocando `DeleteMessage` em `ChatThreadClient`.

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Adicionar um usuário como membro à conversa de chat

Depois que uma conversa é criada, você pode adicionar e remover usuários dela. Quando você adiciona usuários, você concede a eles acesso para poderem enviar mensagens para a conversa e adicionar/remover outros membros. Para chamar `AddMembers`, verifique se você adquiriu um novo token de acesso e uma identidade para esse usuário. O usuário precisará desse token de acesso para inicializar o cliente de chat.

Use o método `AddMembers` para adicionar membros de conversa à conversa identificada por threadId.

 - Use `members` para listar os membros a serem adicionados à conversa de chat;
 - `User`, obrigatório, é a identidade que você obtém para esse novo usuário.
 - `DisplayName`, opcional, é o nome de exibição do membro da conversa.
 - `ShareHistoryTime`, opcional, é a hora a partir da qual o histórico de chat é compartilhado com o membro. Para compartilhar o histórico desde o início da conversa de chat, defina-a como DateTime.MinValue. Para não compartilhar nenhum histórico anterior ao momento em que o membro foi adicionado, defina-o como a hora atual. Para compartilhar o histórico parcial, defina-o como um ponto no tempo entre a criação da conversa e a hora atual.

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>Remover um usuário de uma conversa de chat

De modo semelhante à adição de um usuário a uma conversa, você pode remover usuários de uma conversa de chat. Para fazer isso, você precisa acompanhar a identidade (CommunicationUser) dos membros que você adicionou.

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>Executar o código

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```console
dotnet run
```
