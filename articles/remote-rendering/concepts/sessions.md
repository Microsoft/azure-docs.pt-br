---
title: Sessões do Remote Rendering
description: Descreve o que é uma sessão de Remote Rendering
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 79f3f93338d15562dcc37857d63bc8b2d7e96b05
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530545"
---
# <a name="remote-rendering-sessions"></a>Sessões do Remote Rendering

No Azure Remote Rendering (ARR), uma *sessão* é um conceito fundamental. Este artigo explica o que é exatamente uma sessão.

## <a name="overview"></a>Visão geral

O Azure Remote Rendering funciona com o descarregamento de tarefas de renderização complexas na nuvem. Essas tarefas de renderização não podem ser atendidas apenas por qualquer servidor, pois a maioria dos servidores de nuvem não tem GPUs. Devido à quantidade de dados envolvidos e ao requisito rígido de produzir resultados em taxas de quadros interativas, a responsabilidade de controlar qual servidor controla qual solicitação de usuário também não pode ser atribuída a outro computador imediatamente, tal como seria possível em um tráfego da Web mais comum.

Isso significa que, quando você usa o Azure Remote Rendering, um servidor de nuvem com os recursos de hardware necessários deve ser reservado exclusivamente para lidar com suas solicitações de renderização. Uma *sessão* refere-se a tudo o que está envolvido na interação com este servidor. Ele começa com a solicitação inicial de reserva (*concessão*) de um computador para você usar, continua com todos os comandos para carregar e manipular modelos e termina com a liberação da concessão no servidor de nuvem.

## <a name="managing-sessions"></a>Gerenciando sessões

Há várias maneiras de gerenciar e interagir com sessões. A maneira independente da linguagem de criar, atualizar e desligar sessões é por meio da [API REST de gerenciamento de sessão](../how-tos/session-rest-api.md). No C# e no C++, essas operações são expostas por meio das classes `RemoteRenderingClient` e `RenderingSession`. Para aplicativos do Unity, há outras funções de utilitário fornecidas pelo componente `ARRServiceUnity`.

Quando você estiver *conectado* a uma sessão ativa, operações como [carregamento de modelos](models.md) e interação com a cena são expostas por meio da classe `RenderingSession`.

### <a name="managing-multiple-sessions-simultaneously"></a>Gerenciando várias sessões simultaneamente

Não é possível se *conectar* totalmente a várias sessões de um dispositivo. No entanto, você pode criar, observar e desligar quantas sessões desejar em um único aplicativo. Desde que o aplicativo não seja destinado a se conectar a uma sessão, ele também não precisa ser executado em um dispositivo como o HoloLens 2. Um exemplo de caso de uso para tal implementação seria você querer controlar sessões por meio de um mecanismo central. Por exemplo, é possível criar um aplicativo Web, em que vários tablets e dispositivos HoloLens podem fazer logon. Em seguida, o aplicativo pode exibir opções nos tablets, como o modelo CAD a ser exibido. Se um usuário fizer uma seleção, essas informações serão comunicadas a todos os dispositivos do HoloLens para criar uma experiência compartilhada.

## <a name="session-phases"></a>Fases da sessão

Cada sessão passa por várias fases.

### <a name="session-startup"></a>Inicialização da sessão

Quando você pede ao ARR para [criar uma nova sessão](../how-tos/session-rest-api.md), a primeira coisa a fazer é retornar uma sessão [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). Esse UUID permite consultar informações sobre a sessão. O UUID e algumas informações básicas sobre a sessão persistem por 30 dias, para que você possa consultar essas informações mesmo depois que a sessão for interrompida. Neste ponto, o **estado da sessão** será relatado como **Iniciando**.

Em seguida, o Azure Remote Rendering tenta encontrar um servidor que pode hospedar sua sessão. Há dois parâmetros para essa pesquisa. Primeiro, ele só reservará servidores em sua [região](../reference/regions.md). Isso ocorre porque a latência de rede entre regiões pode ser alta demais para garantir uma experiência razoável. O segundo fator é o *tamanho* desejado que você especificou. Em cada região, há um número limitado de servidores que podem atender à solicitação de tamanho [*Standard*](../reference/vm-sizes.md) ou [*Premium*](../reference/vm-sizes.md) . Consequentemente, se todos os servidores do tamanho solicitado estiverem atualmente em uso em sua região, a criação da sessão falhará. O motivo da falha [pode ser consultado](../how-tos/session-rest-api.md).

> [!IMPORTANT]
> Se você solicitar um tamanho de servidor *padrão* e a solicitação falhar devido a alta demanda, isso não implica que a solicitação de um servidor *Premium* também falhará. Portanto, se for uma opção para você, você pode tentar fazer fallback para um tamanho de servidor *Premium* .

Quando o serviço encontra um servidor adequado, ele precisa copiar a VM (máquina virtual) apropriada para ela, a fim de transformá-la em um host do Azure Remote Rendering. Esse processo leva vários minutos. Depois, a VM é inicializada e o **estado de sessão** faz a transição para **Pronto**.

Nesse momento, o servidor está esperando exclusivamente pela sua entrada. Esse também é o momento em que você recebe a cobrança pelo serviço.

### <a name="connecting-to-a-session"></a>Conectando-se a uma sessão

Quando a sessão estiver *pronta*, você poderá se *conectar* a ela. Enquanto estiver conectado, o dispositivo pode enviar comandos para carregar e modificar modelos. Cada host ARR apenas atende a um dispositivo de cliente por vez, portanto, quando um cliente se conecta a uma sessão, ele tem controle exclusivo sobre o conteúdo renderizado. Isso também significa que o desempenho de renderização nunca irá variar por motivos fora de seu controle.

> [!IMPORTANT]
> Embora apenas um cliente possa se *conectar* a uma sessão, as informações básicas sobre sessões, como seu estado atual, podem ser consultadas sem conexão.

Enquanto um dispositivo estiver conectado a uma sessão, as tentativas de conexão por outros dispositivos falharão. No entanto, quando o dispositivo conectado for desconectado, de forma voluntária ou devido a algum tipo de falha, a sessão aceitará outra solicitação de conexão. Todos os estados anteriores (modelos carregados e assim por diante) são descartados de forma que o próximo dispositivo de conexão comece sem restrições. Assim, as sessões podem ser reutilizadas muitas vezes, por diferentes dispositivos e será possível ocultar a sobrecarga de inicialização da sessão do usuário final na maioria dos casos.

> [!IMPORTANT]
> O servidor remoto nunca altera o estado dos dados do lado do cliente. Todas as mutações de dados (como atualizações de transformação e solicitações de carregamento) devem ser executadas pelo aplicativo cliente. Todas as ações atualizam imediatamente o estado do cliente.

### <a name="session-end"></a>Término da Sessão

Ao solicitar uma nova sessão, você especifica um *tempo máximo de concessão*, normalmente no intervalo de uma a oito horas. Essa é a duração durante a qual o host aceitará sua entrada.

Há dois motivos habituais para uma sessão terminar. Você solicita manualmente que a sessão seja interrompida ou o tempo máximo de concessão expira. Em ambos os casos, qualquer conexão ativa com o host é fechada imediatamente e o serviço é desligado nesse servidor. Em seguida, o servidor é retornado para o pool do Azure e pode ser requisitado para outras finalidades. A interrupção de uma sessão não pode ser desfeita ou cancelada. Consultar o **estado de sessão** em uma sessão interrompida retornará como **Interrompido** ou **Expirado**, dependendo se a sessão foi desligada manualmente ou se o tempo máximo de concessão foi atingido.

Uma sessão também pode ser interrompida devido a alguma falha.

Em todos os casos, você não continuará a cobrado depois que uma sessão for interrompida.

> [!WARNING]
> O fato de se conectar a uma sessão não afetará a cobrança, independentemente do tempo de conexão. O que você paga pelo serviço depende da *duração da sessão*, o que significa que o tempo que um servidor está reservado exclusivamente para você e os recursos de hardware solicitados (o [tamanho alocado](../reference/vm-sizes.md)). Se você iniciar uma sessão, conecte-se por cinco minutos e, em seguida, não interrompa a sessão, de modo que ela continue em execução até que sua concessão expire, assim, você será cobrado pelo tempo total de concessão da sessão. Por outro lado, o *tempo máximo de concessão* é principalmente uma rede de segurança. Não importa se você solicitar uma sessão com um tempo de concessão de oito horas e, em seguida, usá-la somente por cinco minutos, se você interromper manualmente a sessão posteriormente.

#### <a name="extend-a-sessions-lease-time"></a>Estender o tempo de concessão de uma sessão

Você pode [estender o tempo de concessão](../how-tos/session-rest-api.md) de uma sessão ativa, caso você precise dela por mais tempo.

## <a name="example-code"></a>Código de exemplo

O código a seguir mostra uma implementação simples de início de sessão, aguardando o estado *pronto*, conectando-se e, em seguida, desconectando-se e desligando novamente.

```cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

SessionConfiguration sessionConfig = new SessionConfiguration();
// fill out sessionConfig details...

RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

RenderingSessionCreationOptions rendererOptions = new RenderingSessionCreationOptions();
// fill out rendererOptions...

CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(rendererOptions);

RenderingSession session = result.Session;
RenderingSessionProperties sessionProperties;
while (true)
{
    var propertiesResult = await session.GetPropertiesAsync();
    sessionProperties = propertiesResult.SessionProperties;
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
    // REST calls must not be issued too frequently, otherwise the server returns failure code 429 ("too many requests"). So we insert the recommended delay of 10s
    await Task.Delay(TimeSpan.FromSeconds(10));
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
ConnectionStatus connectStatus = await session.ConnectAsync(new RendererInitOptions());

// Connected!

while (...)
{
    // per frame update

    session.Connection.Update();
}

// Disconnect
session.Disconnect();

// stop the session
await session.StopAsync();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

Várias instâncias `RemoteRenderingClient` e `RenderingSession` podem ser mantidas, manipuladas e consultadas no código. Porém, apenas um único dispositivo pode se conectar a uma `RenderingSession` de cada vez.

O tempo de vida de uma máquina virtual não está vinculado à instância `RemoteRenderingClient` ou à instância `RenderingSession`. O `RenderingSession.StopAsync` deve ser chamado para interromper uma sessão.

A ID de sessão persistente pode ser consultada por meio do `RenderingSession.SessionUuid()` e armazenada em cache localmente. Com essa ID, um aplicativo pode chamar o `RemoteRenderingClient.OpenRenderingSessionAsync` para se associar a essa sessão.

Quando o `RenderingSession.IsConnected` é true, o `RenderingSession.Connection` retorna uma instância do `RenderingConnection`, que contém as funções para [modelos de carga](models.md), manipular [entidades](entities.md) e [informações de consulta](../overview/features/spatial-queries.md) sobre a cena renderizada.

## <a name="api-documentation"></a>Documentação da API

* [Classe C# RenderingSession](/dotnet/api/microsoft.azure.remoterendering.renderingsession)
* [C# RemoteRenderingClient. CreateNewRenderingSessionAsync ()](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient.createnewrenderingsessionasync)
* [C# RemoteRenderingClient. OpenRenderingSessionAsync ()](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient.openrenderingsessionasync)
* [Classe C++ RenderingSession](/cpp/api/remote-rendering/renderingsession)
* [C++ RemoteRenderingClient:: CreateNewRenderingSessionAsync](/cpp/api/remote-rendering/remoterenderingclient#createnewrenderingsessionasync)
* [C++ RemoteRenderingClient:: OpenRenderingSession](/cpp/api/remote-rendering/remoterenderingclient#openrenderingsession)

## <a name="next-steps"></a>Próximas etapas

* [Entidades](entities.md)
* [Modelos](models.md)