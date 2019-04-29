---
title: Componentes internos do Serviço do Azure SignalR
description: Uma visão geral dos componentes internos do Serviço do Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: cbcdfccfdca1dbed3b766b3f50295b1d355b3478
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401739"
---
# <a name="azure-signalr-service-internals"></a>Componentes internos do Serviço do Azure SignalR

O Serviço do Azure SignalR é criado com base na estrutura do ASP.NET Core SignalR. Ele também dá suporte ao SignalR do ASP.NET como uma versão prévia do recurso.

> Para dar suporte ao ASP.NET Core SignalR, o Serviço do Azure SignalR reimplementa o protocolo de dados do ASP.NET Core SignalR sobre a estrutura do ASP.NET Core

Você pode migrar facilmente um aplicativo local do ASP.NET Core SignalR para trabalhar com o Serviço do SignalR, alterando algumas linhas de código.

O diagrama a seguir descreve a arquitetura típica quando você usa o Serviço do SignalR com o servidor de aplicativos.

As diferenças de aplicativo auto-hospedado do ASP.NET Core SignalR também são abordadas.

![Arquitetura](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Conexões do servidor

O servidor de aplicativos do ASP.NET Core SignalR auto-hospedado escuta os clientes e conecta-se a eles diretamente.

Com o Serviço do SignalR, o servidor de aplicativos não está mais aceitando conexões de cliente persistentes, mas em vez disso:

1. Um ponto de extremidade `negotiate` é exposto pelo SDK do Serviço do Azure SignalR para cada hub.
1. Esse ponto de extremidade responderá às solicitações de negociação do cliente e redirecionará os clientes ao Serviço do SignalR.
1. Eventualmente, os clientes serão conectados ao Serviço do SignalR.

Para saber mais, confira [Aplicativos cliente](#client-connections).

Depois que o servidor de aplicativos é iniciado, 
- Para o ASP.NET Core SignalR, o SDK do Serviço do Azure SignalR abre 5 conexões WebSocket por hub do Serviço do SignalR. 
- Para o ASP.NET Core SignalR, o SDK do Serviço do Azure SignalR abre 5 conexões WebSocket por hub do Serviço do SignalR e uma por conexão de WebSocket do aplicativo.

Cinco conexões WebSocket é o valor padrão que pode ser alterado na [configuração](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount).

Mensagens de e para os clientes serão multiplexadas nessas conexões.

Essas conexões permanecerão conectadas para o Serviço do SignalR o tempo todo. Se uma conexão de servidor está desconectada devido a um problema de rede,
- todos os clientes que são atendidos por esta conexão do servidor são desconectados (para obter mais informações sobre isso, confira [Transmissão de dados entre o cliente e servidor](#data-transmit-between-client-and-server));
- a conexão do servidor começa a reconectar-se automaticamente.

## <a name="client-connections"></a>Conexões de cliente

Quando você usa o Serviço do SignalR, os clientes se conectam ao Serviço do SignalR em vez de ao servidor de aplicativos.
Há duas etapas para estabelecer conexões persistentes entre o cliente e o Serviço do SignalR.

1. O cliente envia uma solicitação de negociação para o servidor de aplicativos. Com o SDK do Serviço do Azure SignalR, o servidor de aplicativos retorna uma resposta de redirecionamento com a URL do Serviço do SignalR e o token de acesso.

- Para o ASP.NET Core SignalR, uma resposta de redirecionamento típica se parece com:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- Para o SignalR do ASP.NET, uma resposta de redirecionamento típica se parece com:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Depois de receber a resposta de redirecionamento, o cliente usa a nova URL e o token de acesso para iniciar o processo normal de conexão ao Serviço do SignalR.

Saiba mais sobre os [protocolos de transporte](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md) do ASP.NET Core SignalR.

## <a name="data-transmit-between-client-and-server"></a>Transmissão de dados entre o cliente e servidor

Quando um cliente estiver conectado ao Serviço do SignalR, o tempo de execução do serviço encontrará uma conexão de servidor para atender a esse cliente
- Esta etapa ocorre apenas uma vez e é um mapeamento de um para um entre as conexões de cliente e de servidor.
- O mapeamento é mantido no Serviço do SignalR até que o cliente ou o servidor desconecte.

Neste ponto, o servidor de aplicativos recebe um evento com informações do novo cliente. Uma conexão lógica para o cliente é criada no servidor de aplicativos. O canal de dados é estabelecido do cliente para o servidor de aplicativos, por meio do Serviço do SignalR.

O Serviço do SignalR transmite dados do cliente para o servidor de aplicativos de emparelhamento. Então, dados do servidor de aplicativos serão enviados para os clientes mapeados.

Como você pode ver, o Serviço do Azure SignalR é essencialmente uma camada lógica de transporte entre clientes e servidor de aplicativos. Todas as conexões persistentes são descarregadas para o Serviço do SignalR.
O servidor de aplicativos só precisa lidar com a lógica de negócios na classe de hub, sem se preocupar com as conexões de cliente.