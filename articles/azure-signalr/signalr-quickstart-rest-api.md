---
title: 'Início Rápido: API REST do Serviço do Azure SignalR'
description: Saiba como usar a API REST com o Serviço do Azure SignalR seguindo exemplos. Encontre detalhes da especificação da API REST.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 2613f91e8c7f1ad3a05792a9a165f4560c09a637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94874518"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Início Rápido: Difundir mensagens em tempo real do aplicativo de console

O Azure SignalR Service fornece [API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) para dar suporte ao servidor para cenários de comunicação do cliente, como difusão. Você pode escolher qualquer linguagem de programação que pode fazer a chamada à API REST. Você pode postar mensagens para todos os clientes conectados, um cliente específico por nome ou um grupo de clientes.

Neste início rápido, você aprenderá como enviar mensagens de um aplicativo de linha de comando para aplicativos cliente conectados em C#.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido pode ser executado no macOS, Windows ou Linux.

* [SDK do .NET Core](https://www.microsoft.com/net/download/core)
* Um editor de texto ou editor de código de sua escolha.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsapi).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em <https://portal.azure.com/> com sua conta do Azure.

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsapi).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsapi).

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Durante a implantação do serviço, vamos alternar para preparar o código. Clone o [aplicativo de exemplo do GitHub](https://github.com/aspnet/AzureSignalR-samples.git), defina a cadeia de conexão do SignalR Service e execute o aplicativo localmente.

1. Abra uma janela de terminal git. Mude para uma pasta em que deseja clonar o projeto de exemplo.

1. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```
Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsapi).

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

Este exemplo é um aplicativo de console mostrando o uso do Azure SignalR Service. Ele fornece dois modos:

- Modo de servidor: use comandos simples para chamar a API REST do Azure SignalR Service.
- Modo cliente: conectar-se ao Azure SignalR Service e receber mensagens de servidor.

Também é possível encontrar como gerar um token de acesso para autenticar com o Azure SignalR Service.

### <a name="build-the-executable-file"></a>Criar o arquivo executável

Como exemplo, usamos macOS osx.10.13-x64. Você pode encontrar [referências](/dotnet/core/rid-catalog) sobre como criar em outras plataformas.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Iniciar um cliente

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Iniciar um servidor

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsapi).

## <a name="run-the-sample-without-publishing"></a>Executar o exemplo sem publicação

Você também pode executar o comando a seguir para iniciar um servidor ou cliente

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Use segredos do usuário para especificar a Cadeia de Conexão

Você pode executar `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` no diretório raiz do exemplo. Depois disso, você não precisa mais da opção `-c "<ConnectionString>"`.

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsapi).

## <a name="usage"></a>Uso

Depois que o servidor for iniciado, use o comando para enviar a mensagem:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Você pode iniciar vários clientes com nomes de clientes diferentes.

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsapi).

## <a name="integration-with-third-party-services"></a><a name="usage"> </a> Integração com serviços de terceiros

O serviço Azure SignalR permite que serviços de terceiros integrem-se ao sistema.

### <a name="definition-of-technical-specifications"></a>Definição de especificações técnicas

A tabela a seguir mostra todas as versões das APIs REST com suporte atualmente. Você também pode encontrar o arquivo de definição de cada versão específica

Versão | Estado de API | Porta | Específicas
--- | --- | --- | ---
`1.0-preview` | Disponível | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Disponível | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

A lista de APIs disponíveis para cada versão específica está disponível na lista a seguir.

API | 1.0-preview | 1.0
--- | --- | ---
[Transmitir para todos](#broadcast) | **&#x2713;** | **&#x2713;**
[Transmitir para um grupo](#broadcast-group) | **&#x2713;** | **&#x2713;**
Transmitir para alguns grupos | **&#x2713;** (Preterido) | `N / A`
[Enviar para um usuário](#send-user) | **&#x2713;** | **&#x2713;**
Enviar para alguns usuários | **&#x2713;** (Preterido) | `N / A`
[Adicionar um usuário a um grupo](#add-user-to-group) | `N / A` | **&#x2713;**
[Remover um usuário de um grupo](#remove-user-from-group) | `N / A` | **&#x2713;**
[Verificar a existência do usuário](#check-user-existence) | `N / A` | **&#x2713;**
[Remover um usuário de todos os grupos](#remove-user-from-all-groups) | `N / A` | **&#x2713;**
[Enviar para uma conexão](#send-connection) | `N / A` | **&#x2713;**
[Adicionar uma conexão a um grupo](#add-connection-to-group) | `N / A` | **&#x2713;**
[Remover uma conexão de um grupo](#remove-connection-from-group) | `N / A` | **&#x2713;**
[Fechar uma conexão de cliente](#close-connection) | `N / A` | **&#x2713;**
[Integridade do Serviço](#service-health) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Transmitir para todos

Versão | Método HTTP da API | URL de Solicitação | Corpo da solicitação
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | O mesmo que o descrito acima

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Transmitir para um grupo

Versão | Método HTTP da API | URL de Solicitação | Corpo da solicitação
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | O mesmo que o descrito acima

<a name="send-user"> </a>
### <a name="sending-to-a-user"></a>Envio para um usuário

Versão | Método HTTP da API | URL de Solicitação | Corpo da solicitação
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | O mesmo que o descrito acima

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Adicionar um usuário a um grupo

Versão | Método HTTP da API | URL de Solicitação
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Remover um usuário de um grupo

Versão | Método HTTP da API | URL de Solicitação
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="check-user-existence"> </a>
### <a name="check-user-existence-in-a-group"></a>Verificar a existência do usuário em um grupo

Versão da API | Método HTTP da API | URL de Solicitação
---|---|---
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups/<group-name>`
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>` 

Código de status de resposta | Descrição
---|---
`200` | O usuário existe
`404` | O usuário não existe

<a name="remove-user-from-all-groups"> </a>
### <a name="remove-a-user-from-all-groups"></a>Remover um usuário de todos os grupos

Versão da API | Método HTTP da API | URL de Solicitação
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups`

<a name="send-connection"> </a>
### <a name="send-message-to-a-connection"></a>Enviar uma mensagem para uma conexão

Versão da API | Método HTTP da API | URL de Solicitação | Corpo da solicitação
---|---|---|---
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`

<a name="add-connection-to-group"> </a>
### <a name="add-a-connection-to-a-group"></a>Adicionar uma conexão a um grupo

Versão da API | Método HTTP da API | URL de Solicitação
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="remove-connection-from-group"> </a>
### <a name="remove-a-connection-from-a-group"></a>Remover uma conexão de um grupo

Versão da API | Método HTTP da API | URL de Solicitação
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="close-connection"> </a>
### <a name="close-a-client-connection"></a>Fechar uma conexão de cliente

Versão da API | Método HTTP da API | URL de Solicitação
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>?reason=<close-reason>`

<a name="service-health"> </a>
### <a name="service-health"></a>Integridade do Serviço

Versão da API | Método HTTP da API | URL de Solicitação
---|---|---                             
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/health`

Código de status de resposta | Descrição
---|---
`200` | Serviço em execução
`5xx` | Erro de Serviço

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsapi).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsapi).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como usar a API REST para transmitir mensagens em tempo real do SignalR Service aos clientes. Em seguida, saiba mais sobre como desenvolver e implantar o Azure Functions com SignalR Service associação, que é criada sobre a API REST.

> [!div class="nextstepaction"]
> [Desenvolver Azure Functions usando associações de serviço do Azure SignalR](signalr-quickstart-azure-functions-csharp.md)
