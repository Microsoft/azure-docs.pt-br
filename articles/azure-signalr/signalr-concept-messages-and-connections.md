---
title: Mensagens e conexões no Serviço do Azure SignalR
description: Uma visão geral dos principais conceitos sobre mensagens e conexões no Serviço do Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: e82ce8f5c97aed7e2cb832d8e808ff84691f7c9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401186"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Mensagens e conexões no Serviço do Azure SignalR

O modelo de cobrança do Serviço do Azure SignalR é baseado no número de conexões e no número de mensagens. Este artigo explica como as mensagens e conexões são definidas e contadas para cobrança.


## <a name="message-formats"></a>Formatos de mensagem 

O Serviço do Azure SignalR dá suporte aos mesmos formatos que o ASP.NET Core SignalR: [JSON](https://www.json.org/) e [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Tamanho da mensagem

O Serviço do Azure SignalR não tem nenhum limite de tamanho para mensagens.

Mensagens grandes são divididas em mensagens menores com, no máximo, 2 KB cada, que são transmitidas separadamente. Os SDKs manipulam a divisão e a montagem de mensagens. Não é necessário nenhum esforço por parte do desenvolvedor.

Mensagens grandes afetam negativamente o desempenho do sistema de mensagens. Use mensagens menores sempre que possível e teste para determinar o tamanho ideal da mensagem para cada cenário de caso de uso.

## <a name="how-messages-are-counted-for-billing"></a>Como as mensagens são contadas para cobrança

Para cobrança, somente as mensagens de saída do Serviço do Azure SignalR são contadas. Mensagens ping entre clientes e servidores são ignoradas.

Mensagens maiores do que 2 KB são contadas como várias mensagens de 2 KB cada. O gráfico de contagem de mensagens no portal do Azure é atualizado a cada 100 mensagens por hub.

Por exemplo, imagine que você tem três clientes e um servidor de aplicativos. Um cliente envia uma mensagem de 4 KB para permitir que o servidor difunda para todos os clientes. A contagem de mensagens é oito: uma mensagem do serviço para o servidor de aplicativos e três mensagens do serviço para os clientes. Cada mensagem é contada como duas mensagens de 2 KB.

A contagem de mensagens mostrada no portal do Azure permanecerá 0 até que ela se acumule e ultrapasse 100.

## <a name="how-connections-are-counted"></a>Como as conexões são contadas

Existem conexões de servidor e conexões de cliente. Por padrão, cada servidor de aplicativos tem cinco conexões por hub com o Serviço do Azure SignalR e cada cliente tem uma conexão de cliente com o Serviço do Azure SignalR.

A contagem de conexões mostrada no portal do Azure inclui conexões de servidor e de cliente.

Por exemplo, suponha que você tem dois servidores de aplicativos e que você define cinco hubs no código. A contagem de conexão do servidor será 50: 2 servidores de aplicativos * 5 hubs * 5 conexões por hub.

O ASP.NET SignalR calcula conexões de servidor de maneira diferente. Ele inclui um hub padrão, além dos hubs que você definir. Por padrão, cada servidor de aplicativos precisa de mais cinco conexões de servidor. A contagem de conexões do hub padrão se mantém consistente com a dos outros hubs.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Como o tráfego de entrada/saída é contado

A diferença entre o tráfego de entrada e de saída é baseada na perspectiva do Serviço do Azure SignalR. O tráfego é calculado em bytes. Assim como a contagem de mensagens, o tráfego também tem uma taxa de amostragem. O gráfico de entrada/saída no portal do Azure é atualizado a cada 100 KB por hub.

## <a name="related-resources"></a>Recursos relacionados

- [Tipos de agregação no Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Configuração do ASP.NET Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)