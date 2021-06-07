---
title: Mensagens e conexões no Serviço do Azure SignalR
description: Uma visão geral dos principais conceitos sobre mensagens e conexões no Serviço do Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: zhshang
ms.openlocfilehash: 3c4d28addac0ecfc9605678582562550a1c96b8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491938"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Mensagens e conexões no Serviço do Azure SignalR

O modelo de cobrança do Serviço do Azure SignalR é baseado no número de conexões e no número de mensagens. Este artigo explica como as mensagens e conexões são definidas e contadas para cobrança.


## <a name="message-formats"></a>Formatos de mensagem 

O serviço de Signaler do Azure dá suporte aos mesmos formatos que ASP.NET Core Signalr: [JSON](https://www.json.org/) e [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Tamanho da mensagem

O Serviço do Azure SignalR não tem nenhum limite de tamanho para mensagens.

Mensagens grandes são divididas em mensagens menores com, no máximo, 2 KB cada, que são transmitidas separadamente. Os SDKs manipulam a divisão e a montagem de mensagens. Não é necessário nenhum esforço por parte do desenvolvedor.

Mensagens grandes afetam negativamente o desempenho do sistema de mensagens. Use mensagens menores sempre que possível e teste para determinar o tamanho ideal da mensagem para cada cenário de caso de uso.

## <a name="how-messages-are-counted-for-billing"></a>Como as mensagens são contadas para cobrança

Para cobrança, somente as mensagens de saída do Serviço do Azure SignalR são contadas. Mensagens ping entre clientes e servidores são ignoradas.

Mensagens maiores do que 2 KB são contadas como várias mensagens de 2 KB cada. O gráfico de contagem de mensagens no portal do Azure é atualizado a cada 100 mensagens por hub.

Por exemplo, imagine que você tenha um servidor de aplicativos e três clientes:

O servidor de aplicativos transmite uma mensagem de 1 KB para todos os clientes conectados, a mensagem do servidor de aplicativos para o serviço é considerada uma mensagem de entrada livre. Somente as três mensagens que enviam do serviço para cada cliente são cobradas como mensagens de saída.

O cliente A envia uma mensagem de 1 KB para outro cliente B, sem passar pelo servidor de aplicativos. A mensagem do cliente a para o serviço é uma mensagem de entrada livre. A mensagem do serviço para o cliente B é cobrada como mensagem de saída.

Se você tiver três clientes e um servidor de aplicativos. Um cliente envia uma mensagem de 4 KB para permitir que o servidor difunda para todos os clientes. A contagem de mensagens cobradas é de oito: uma mensagem do serviço para o servidor de aplicativos e três mensagens do serviço para os clientes. Cada mensagem é contada como duas mensagens de 2 KB.

## <a name="how-connections-are-counted"></a>Como as conexões são contadas

Há conexões de servidor e conexões de cliente com o serviço de Signaler do Azure. Por padrão, cada servidor de aplicativos começa com cinco conexões iniciais por Hub e cada cliente tem uma conexão de cliente.

Por exemplo, suponha que você tenha dois servidores de aplicativos e que defina cinco hubs no código. A contagem de conexões do servidor será 50:2 servidores de aplicativos * 5 hubs * 5 conexões por Hub.

A contagem de conexões mostrada no portal do Azure inclui conexões de servidor, conexões de cliente, conexões de diagnóstico e conexões de rastreamento ao vivo. Os tipos de conexão são definidos na lista a seguir:

- **Conexão do servidor**: conecta o serviço de Signaler do Azure e o servidor de aplicativos.
- **Conexão de cliente**: conecta o serviço de Signalr do Azure e o aplicativo cliente.
- **Conexão de diagnóstico**: um tipo especial de conexão de cliente que pode produzir um log mais detalhado, o que pode afetar o desempenho. Esse tipo de cliente foi projetado para solução de problemas.
- **Conexão de rastreamento ao** vivo: conecta-se ao ponto de extremidade de rastreamento ao vivo e recebe rastreamentos ao vivo do serviço de signaler do Azure. 
 
Observe que uma conexão de rastreamento ao vivo não é contada como uma conexão de cliente ou como uma conexão de servidor. 

O ASP.NET SignalR calcula conexões de servidor de maneira diferente. Ele inclui um hub padrão, além dos hubs que você definir. Por padrão, cada servidor de aplicativos precisa de mais cinco conexões de servidor iniciais. A contagem de conexões inicial para o Hub padrão permanece consistente com outros hubs.

O serviço e o servidor de aplicativos continuam sincronizando o status da conexão e fazendo ajustes nas conexões do servidor para obter melhor desempenho e estabilidade do serviço.  Portanto, você pode ver o número de conexão do servidor ser alterado de tempos em tempos.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Como o tráfego de entrada/saída é contado

A mensagem enviada para o serviço é uma mensagem de entrada. Mensagem enviada fora do serviço é mensagem de saída. O tráfego é calculado em bytes.

## <a name="related-resources"></a>Recursos relacionados

- [Tipos de agregação no Azure Monitor](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr )
- [Configuração do ASP.NET Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
