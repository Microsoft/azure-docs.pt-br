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
ms.openlocfilehash: 30dcda4d7bb95ac59add104452415a0ddfc3c016
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178445"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, é preciso:

- Criar uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Instalar [Xcode](https://developer.apple.com/xcode/) e [Cocoapods](https://cocoapods.org/), usaremos o Xcode para criar um aplicativo iOS para o início rápido e Cocoapods para instalar dependências.
- Criar um recurso dos Serviços de Comunicação do Azure. Para obter detalhes, confira [Criar um recurso de comunicação do Azure](../../create-communication-resource.md). Você precisará **registrar o ponto de extremidade do recurso** para este guia de início rápido.
- Crie **dois** usuários do ACS e emita-os como um [token de acesso do usuário](../../access-tokens.md) token de acesso do usuário. Defina o escopo como **chat** e **anote a cadeia de caracteres do token, bem como a cadeia de caracteres da userId**. Neste guia de início rápido, criaremos um thread com um participante inicial e adicionaremos um segundo participante a ele.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-ios-application"></a>Criar um aplicativo iOS

Abra o Xcode e selecione `Create a new Xcode project`.

Na próxima janela, selecione `iOS` como a plataforma e `App` para o modelo.

Ao escolher as opções, insira `ChatQuickstart` como o nome do projeto. Selecione `Storyboard` como a interface, `UIKit App Delegate` como o ciclo de vida e `Swift` como o idioma.

Clique em avançar e escolha o diretório em que você deseja que o projeto seja criado.

### <a name="install-the-libraries"></a>Instalar as bibliotecas

Usaremos Cocoapods para instalar as dependências necessárias dos Serviços de Comunicação.

Na linha de comando, navegue para dentro do diretório raiz do projeto iOS `ChatQuickstart`.

Crie um Podfile: `pod init`

Abra o Podfile e adicione as seguintes dependências ao destino `ChatQuickstart`:
```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

Instale as dependências, o que também criará um workspace do Xcode: `pod install`

**Depois de executar a instalação do pod, reabra o projeto no Xcode selecionando o `.xcworkspace` recém-criado.**

### <a name="setup-the-placeholders"></a>Configurar os espaços reservados

Abra o workspace `ChatQuickstart.xcworkspace` no Xcode e depois abra `ViewController.swift`.

Neste início rápido, adicionaremos nosso código a `viewController` e veremos a saída no console do Xcode. Este guia de início rápido não aborda a criação de uma interface do usuário no iOS. 

Na parte superior de `viewController.swift`, importe as bibliotecas `AzureCommunication` e `AzureCommunicatonChat`:

```
import AzureCommunication
import AzureCommunicationChat
```

Copie o seguinte código ao método `viewDidLoad()` de `ViewController`:

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>
                
                // <CREATE A CHAT THREAD CLIENT>
                
                // <SEND A MESSAGE>
                
                // <ADD A USER>
                
                // <LIST USERS>
                
                // <REMOVE A USER>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

Usaremos um semáforo para sincronizar nosso código para fins de demonstração. Nas etapas a seguir, substituiremos os espaços reservados pelo exemplo de código usando a biblioteca de chat dos Serviços de Comunicação do Azure.


### <a name="create-a-chat-client"></a>Criar um cliente de chat

Substitua o comentário `<CREATE A CHAT CLIENT>` pelo código a seguir:

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
    let credential =
    try CommunicationTokenCredential(
        token: "<ACCESS_TOKEN>"
    )
    let options = AzureCommunicationChatClientOptions()

    let chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: options
    )
```

Substitua `<ACS_RESOURCE_ENDPOINT>` pelo ponto de extremidade do Recurso do ACS.
Substitua `<ACCESS_TOKEN>` por um token de acesso ACS válido.

Este guia de início rápido não abrange a criação de uma camada de serviço para gerenciar tokens no seu aplicativo de chat, embora isso seja recomendado. Confira a documentação a seguir para obter mais detalhes sobre a [Arquitetura de Chat](../../../concepts/chat/concepts.md)

Saiba mais sobre os [tokens de acesso do usuário](../../access-tokens.md).

## <a name="object-model"></a>Modelo de objeto 
As classes e as interfaces a seguir lidam com alguns dos principais recursos do SDK de Chat dos Serviços de Comunicação do Azure para JavaScript.

| Nome                                   | Descrição                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Essa classe é necessária para a funcionalidade de chat. Crie uma instância dela com suas informações de assinatura e use-a para criar, obter e excluir conversas. |
| ChatThreadClient | Essa classe é necessária para a funcionalidade de conversa de chat. Obtenha uma instância por meio de ChatClient e use-a para enviar/receber/atualizar/excluir mensagens, adicionar/remover/obter usuários, enviar notificações de digitação e confirmações de leitura e assinar eventos de chat. |

## <a name="start-a-chat-thread"></a>Iniciar uma conversa de chat

Agora usaremos nosso `ChatClient` para criar um thread com um usuário inicial.

Substitua o comentário `<CREATE A CHAT THREAD>` pelo código a seguir:

```
let request = CreateThreadRequest(
    topic: "Quickstart",
    participants: [
        Participant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.thread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Substitua `<USER_ID>` por uma ID de usuário válida dos Serviços de Comunicação.

Estamos usando um semáforo aqui para aguardar o manipulador de conclusão antes de continuar. Usaremos `threadId` com base na resposta retornada ao manipulador de conclusão em etapas posteriores.

## <a name="get-a-chat-thread-client"></a>Obter um cliente de conversa de chat

Agora que criamos um thread de chat, obteremos um `ChatThreadClient` para executar operações dentro do thread.

Substitua o comentário `<CREATE A CHAT THREAD CLIENT>` pelo código a seguir:

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>Enviar uma mensagem para uma conversa de chat

Substitua o comentário `<SEND A MESSAGE>` pelo código a seguir:

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
)

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

Primeiro, construímos o `SendChatMessageRequest` que contém o nome de exibição do conteúdo e dos remetentes (também pode conter o tempo de histórico de compartilhamento). A resposta retornada ao manipulador de conclusão contém a ID da mensagem que foi enviada.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Adicionar um usuário como participante ao thread de chat

Substitua o comentário `<ADD A USER>` pelo código a seguir:

```
let user = Participant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.errors != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

Substitua `<USER_ID>` pela ID de usuário do ACS do usuário a ser adicionado.

Ao adicionar um participante a um thread, a resposta que retornou a conclusão pode conter erros. Esses erros representam a falha em adicionar participantes específicos.

## <a name="list-users-in-a-thread"></a>Listar os usuários em um thread

Substitua o comentário `<LIST USERS>` pelo seguinte código:

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print(user.identifier)
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```


## <a name="remove-user-from-a-chat-thread"></a>Remover um usuário de uma conversa de chat

Substitua o comentário `<REMOVE A USER>` pelo seguinte código:

```
chatThreadClient
    .remove(
        participant: CommunicationUserIdentifier("<USER_ID>")
    ) { result, _ in
        switch result {
        case .success:
            print("Removed user from the thread.")
        case .failure:
            print("Failed to remove user from the thread.")
        }
    }
```

Substitua `<USER ID>` pela ID de usuário dos Serviços de Comunicação do participante que está sendo removido.

## <a name="run-the-code"></a>Executar o código

No Xcode, pressione o botão Executar para criar e executar o projeto. No console, você pode exibir a saída do código e a saída do agente do ChatClient.

