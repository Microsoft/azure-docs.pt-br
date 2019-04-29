---
title: Desenvolver e configurar aplicativos de serviço do Azure Functions SignalR
description: Obter detalhes sobre como desenvolver e configurar aplicativos em tempo real sem servidor usando o Azure Functions e o serviço do Azure SignalR
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809008"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Desenvolvimento de funções do Azure e a configuração com o serviço do Azure SignalR

Aplicativos de funções do Azure podem aproveitar o [associações de serviço do Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) para adicionar recursos em tempo real. Aplicativos cliente usar SDKs de cliente disponíveis em vários idiomas para se conectar ao serviço do Azure SignalR e receber mensagens em tempo real.

Este artigo descreve os conceitos de desenvolvimento e a configuração de um aplicativo de função do Azure que esteja integrado com o SignalR Service.

## <a name="signalr-service-configuration"></a>Configuração do SignalR Service

Azure SignalR Service pode ser configurado em modos diferentes. Quando usado com o Azure Functions, o serviço deve ser configurado no *Serverless* modo.

No portal do Azure, localize o *configurações* página do seu recurso do SignalR Service. Defina as *modo de serviço* para *Serverless*.

![Modo de Serviço do SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Desenvolvimento de funções do Azure

Um aplicativo em tempo real sem servidor, criado com o Azure Functions e o serviço do Azure SignalR normalmente requer duas funções do Azure:

* Token de acesso de uma função de "negotiate" que o cliente chama para obter um SignalR Service válido e URL de ponto de extremidade de serviço
* Uma ou mais funções que enviam mensagens ou gerenciar a associação de grupo

### <a name="negotiate-function"></a>negociar a função

Um aplicativo cliente requer um token de acesso válido para se conectar ao serviço do Azure SignalR. Um token de acesso pode ser anônimos ou autenticados para uma ID de determinado usuário. Aplicativos sem servidor do SignalR Service exigem que um ponto de extremidade HTTP denominado "negotiate" para obter um token e outras informações de conexão, como a URL de ponto de extremidade do SignalR Service.

Use um HTTP disparou a função do Azure e o *SignalRConnectionInfo* associação para gerar o objeto de informações de conexão de entrada. A função deve ter uma rota HTTP que termina em `/negotiate`.

Para obter mais informações sobre como criar a função negotiate, consulte o [ *SignalRConnectionInfo* referência de associação de entrada](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Para saber mais sobre como criar um token autenticado, consulte [usando a autenticação do serviço de aplicativo](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Envio de mensagens e gerenciar a associação de grupo

Use o *SignalR* associação de saída para enviar mensagens para os clientes conectados ao serviço do Azure SignalR. Você pode transmitir mensagens para todos os clientes, ou você pode enviá-los a um subconjunto de clientes que são autenticados com uma ID de usuário específico ou que foram adicionados a um grupo específico.

Os usuários podem ser adicionados a um ou mais grupos. Você também pode usar o *SignalR* associação para adicionar ou remover usuários / para grupos de saída.

Para obter mais informações, consulte o [ *SignalR* referência de associação de saída](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>Hubs de SignalR

O SignalR tem um conceito de "hubs". Cada conexão de cliente e cada mensagem enviada a partir do Azure Functions está no escopo para um hub específico. Você pode usar os hubs como uma forma de separar suas conexões e as mensagens em espaços para nome lógicos.

## <a name="client-development"></a>Desenvolvimento de cliente

Aplicativos de cliente do SignalR podem aproveitar o SDK em um dos vários idiomas para conectar facilmente e receber mensagens de serviço do Azure SignalR de cliente do SignalR.

### <a name="configuring-a-client-connection"></a>Configurando uma conexão de cliente

Para se conectar ao SignalR Service, um cliente deve concluir uma negociação bem-sucedida de conexão que consiste em uma dessas etapas:

1. Fazer uma solicitação para o *negociar* ponto de extremidade HTTP discutida acima para obter informações de conexão válida
1. Se conectar ao SignalR Service usando a URL do ponto de extremidade de serviço e token de acesso é obtido de *negociar* ponto de extremidade

SDKs de cliente SignalR já contêm a lógica necessária para executar o handshake de negociação. Passar a URL do ponto de extremidade negotiate, menos o `negotiate` segmento, o SDK `HubConnectionBuilder`. Aqui está um exemplo no JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Por convenção, o SDK automaticamente anexa `/negotiate` para a URL e o utiliza para começar a negociação.

> [!NOTE]
> Se você estiver usando o SDK JavaScript/TypeScript em um navegador, você precisará [habilitar recursos entre origens (CORS) compartilhamento](#enabling-cors) em seu aplicativo de funções.

Para obter mais informações sobre como usar o SDK do cliente do SignalR, consulte a documentação para seu idioma:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Enviar mensagens de um cliente para o serviço

Embora o SDK do SignalR permite que aplicativos cliente invocar a lógica de back-end em um hub SignalR, essa funcionalidade ainda não é suportada ao usar o SignalR Service com o Azure Functions. Use HTTP solicitações para invocar funções do Azure.

## <a name="azure-functions-configuration"></a>Configuração de funções do Azure

Aplicativos do Azure Function que se integram com o serviço do Azure SignalR podem ser implantados como qualquer aplicativo típico de função do Azure, usando técnicas como [implantação contínua](../azure-functions/functions-continuous-deployment.md), [zip implantação](../azure-functions/deployment-zip-push.md)e [execução de pacote](../azure-functions/run-functions-from-deployment-package.md).

No entanto, há algumas considerações especiais para aplicativos que usam as associações de SignalR Service. Se o cliente é executado em um navegador, o CORS deve ser habilitado. E se o aplicativo exigir autenticação, você pode integrar o ponto de extremidade negotiate com autenticação do serviço de aplicativo.

### <a name="enabling-cors"></a>Habilitando CORS

O cliente JavaScript/TypeScript faz solicitações HTTP para a função negotiate para iniciar a negociação de conexão. Quando o aplicativo cliente está hospedado em um domínio diferente daquele que o aplicativo de funções do Azure, de recursos entre origens (CORS) compartilhamento devem estar habilitado no aplicativo de funções ou o navegador bloqueará as solicitações.

#### <a name="localhost"></a>Localhost

Ao executar o aplicativo de funções em seu computador local, você pode adicionar um `Host` seção para *Settings* para habilitar o CORS. No `Host` seção, adicione duas propriedades:

* `CORS` -Insira a URL base que é a origem do aplicativo cliente
* `CORSCredentials` -Defina-a como `true` para permitir solicitações de "withCredentials"

Exemplo:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="azure"></a>Azure

Para habilitar o CORS em um aplicativo de funções do Azure, vá para a tela de configuração de CORS sob o *recursos da plataforma* guia de seu aplicativo de funções no portal do Azure.

CORS com Access-Control-Allow-Credentials deve estar habilitado para o cliente SignalR chamar a função de negotiate. Marque a caixa de seleção para habilitá-lo.

No *origens permitidas* seção, adicione uma entrada com a URL de base de origem do seu aplicativo web.

![Configurar o CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>Usando a autenticação do serviço de aplicativo

O Azure Functions tem a autenticação integrada, que dão suporte a provedores populares, como o Facebook, Twitter, Account da Microsoft, Google e Azure Active Directory. Esse recurso pode ser integrado com o *SignalRConnectionInfo* associação para criar conexões com o serviço do Azure SignalR que foram autenticadas em um ID de usuário. Seu aplicativo pode enviar mensagens usando o *SignalR* de saída de associação que são destinadas a esse ID de usuário.

No portal do Azure, em seu aplicativo de funções *recursos da plataforma* guia, abra o *autenticação/autorização* janela de configurações. Siga a documentação para [autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md) para configurar a autenticação usando um provedor de identidade de sua escolha.

Uma vez configurado, as solicitações HTTP autenticadas incluirá `x-ms-client-principal-name` e `x-ms-client-principal-id` cabeçalhos que contém o nome de usuário da identidade autenticada e a ID de usuário, respectivamente.

Você pode usar esses cabeçalhos no seu *SignalRConnectionInfo* configuração de associação para criar conexões autenticadas. Aqui está um exemplo C# negociar a função que usa o `x-ms-client-principal-id` cabeçalho.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

Em seguida, você pode enviar mensagens para esse usuário definindo o `UserId` propriedade de uma mensagem do SignalR.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Para obter informações sobre outros idiomas, consulte o [associações de serviço do Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) para fazer referência a funções do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a desenvolver e configurar aplicativos sem servidor do SignalR Service usando o Azure Functions. Tente criar um aplicativo por conta própria usando um dos tutoriais ou inícios rápidos sobre o [página de visão geral do SignalR Service](index.yml).