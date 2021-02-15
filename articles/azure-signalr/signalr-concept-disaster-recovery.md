---
title: Resiliência e recuperação de desastre no Serviço do Azure SignalR
description: Uma visão geral de como configurar várias instâncias de serviço do SignalR para ter resiliência e recuperação de desastres
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: b1cb48d1ae858dbcd0df80780b4c3cee3deac75b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90976501"
---
# <a name="resiliency-and-disaster-recovery-in-azure-signalr-service"></a>Resiliência e recuperação de desastre no Serviço do Azure SignalR

Resiliência e recuperação de desastre são necessidades comuns de sistemas online. O Serviço do Azure SignalR já garante uma disponibilidade de 99,9%, mas ainda é um serviço regional.
Sua instância de serviço está sempre em execução em uma região e não fará failover para outra região quando houver uma interrupção em toda a região.

Em vez disso, nosso SDK do serviço fornece uma funcionalidade para dar suporte a várias instâncias de serviço do SignalR e alternar automaticamente para outras instâncias quando algumas delas não estão disponíveis.
Com esse recurso, você poderá se recuperar quando ocorrer um desastre, mas precisará configurar a topologia de sistema certa por conta própria. Você aprenderá a fazer isso neste documento.

## <a name="high-available-architecture-for-signalr-service"></a>Arquitetura de alta disponibilidade para o Serviço do SignalR

Para ter resiliência entre regiões para o Serviço do SignalR, você precisa configurar várias instâncias do serviço em diferentes regiões. Assim, quando uma região estiver inativa, as outras poderão ser usadas como backup.
Ao conectar várias instâncias de serviço ao servidor de aplicativos, há duas funções: a primária e a secundária.
A primária é uma instância que está recebendo o tráfego online e a secundária é uma instância de backup, mas totalmente funcional, da primária.
Em nossa implementação do SDK, negociar retornará apenas pontos de extremidade primários, de modo que durante o uso normal os clientes se conectam apenas a pontos de extremidade primários.
No entanto, quando a instância primária estiver inativa, negociar retornará pontos de extremidade secundários para que o cliente ainda possa fazer conexões.
A instância primária e o servidor de aplicativos são conectados por meio de conexões de servidor normais, mas a instância secundária e o servidor de aplicativos são conectados por meio de um tipo especial de conexão chamada conexão fraca.
A principal diferença de uma conexão fraca é que ela não aceita o roteamento de conexão de cliente, pois a instância secundária está localizada em outra região. O roteamento de um cliente para outra região não é uma opção ideal (aumenta a latência).

Uma instância de serviço pode ter funções diferentes ao se conectar a vários servidores de aplicativos.
Uma configuração típica do cenário com várias regiões é ter dois (ou mais) pares de instâncias do Serviço do SignalR e servidores de aplicativos.
Dentro de cada par, o servidor de aplicativos e o Serviço do SignalR estão localizados na mesma região, e o Serviço do SignalR está conectado ao servidor de aplicativos como uma função primária.
Entre cada par, o servidor de aplicativos e o Serviço do SignalR também estão conectados, mas o SignalR se torna secundário ao se conectar ao servidor em outra região.

Com esta topologia, mensagens de um servidor ainda podem ser entregues a todos os clientes, uma vez que todos os servidores de aplicativos e instâncias do Serviço do SignalR são interconectados.
No entanto, quando um cliente é conectado, ele sempre é roteado para o servidor de aplicativos na mesma região para alcançar a latência de rede ideal.

A seguir, há um diagrama que ilustra essa topologia:

![O diagrama mostra duas regiões cada uma com um servidor de aplicativos e um serviço de sinalização, em que cada servidor é associado ao serviço Signalr em sua região como primário e com o serviço na outra região como secundário.](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Configurar servidores de aplicativos com várias instâncias do Serviço do SignalR

Quando tiver servidores de aplicativos e o Serviço do SignalR criados em cada região, você poderá configurar seus servidores de aplicativos para se conectarem a todas as instâncias do Serviço do SignalR.

Há duas maneiras de fazer isso:

### <a name="through-config"></a>Usando a configuração

Você já deve saber como definir a cadeia de conexão do serviço Signalr por meio de variáveis de ambiente/configurações de aplicativo/Web. cofig, em uma entrada de configuração denominada `Azure:SignalR:ConnectionString` .
Se você tiver vários pontos de extremidade, você poderá defini-los em várias entradas de configuração, cada uma no seguinte formato:

```
Azure:SignalR:ConnectionString:<name>:<role>
```

Aqui, `<name>` é o nome do ponto de extremidade e `<role>` é sua função (primária ou secundária).
O nome é opcional, mas será útil se você quiser personalizar ainda mais o comportamento de roteamento entre vários pontos de extremidade.

### <a name="through-code"></a>Usando código

Se você preferir armazenar as cadeias de conexão em outro lugar, você também poderá lê-las em seu código e usá-las como parâmetros ao chamar `AddAzureSignalR()` (em ASP.NET Core) ou `MapAzureSignalR()` (em ASP.net).

Segue o código de exemplo:

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

Você pode configurar várias instâncias primárias ou secundárias. Se houver várias instâncias primárias e/ou secundárias, Negotiate retornará um ponto de extremidade na seguinte ordem:

1. Se houver pelo menos uma instância primária online, retorne uma instância online aleatória.
2. Se todas as instâncias primárias estiverem inativas, retorne uma instância online secundária aleatória.

## <a name="failover-sequence-and-best-practice"></a>Sequência de failover e melhor prática

Agora, você tem a topologia de sistema certa configurada. Sempre que uma instância do Serviço do SignalR estiver inativa, o tráfego online será roteado para outras instâncias.
Veja o que acontece quando uma instância primária está inativa (e se recupera após algum tempo):

1. A instância de serviço primária está inativa, todas as conexões nesta instância serão descartadas.
2. Todos os servidores conectados a esta instância a marcarão como offline e negociar deixará de retornar esse ponto de extremidade e começará a retornar o ponto de extremidade secundário.
3. Todas as conexões de cliente nessa instância também serão fechadas e os clientes serão reconectados. Uma vez que os servidores de aplicativos agora retornam o ponto de extremidade secundário, os clientes se conectarão à instância secundária.
4. Agora, a instância secundária recebe todo o tráfego online. Todas as mensagens do servidor para os clientes ainda podem ser entregues, pois o ponto de extremidade secundário está conectado a todos os servidores de aplicativos. No entanto, as mensagens do cliente para o servidor são roteadas apenas para o servidor de aplicativos na mesma região.
5. Após a instância primária ser recuperada e voltar a ficar online, o servidor de aplicativos restabelecerá conexões com ela e a marcará como online. Agora, negociar voltará a retornar o ponto de extremidade primário e, assim, os clientes serão conectados de volta ao primário. No entanto, clientes existentes não serão descartados e continuarão sendo roteados para o secundário até que se desconectem.

Os diagramas abaixo ilustram como o failover é feito no Serviço do SignalR:

Fig.1 Antes do failover ![Antes do failover](media/signalr-concept-disaster-recovery/before-failover.png)

Fig.2 Após o failover ![Após o failover](media/signalr-concept-disaster-recovery/after-failover.png)

Fig.3 Pouco tempo após a recuperação do primário ![Pouco tempo após a recuperação do primário](media/signalr-concept-disaster-recovery/after-recover.png)

Você pode ver que, no funcionamento normal, somente o servidor de aplicativos e o Serviço do SignalR primários têm tráfego online (em azul).
Após o failover, o servidor de aplicativos e o Serviço do SignalR secundários também se tornam ativos.
Após o Serviço do SignalR primário voltar a ficar online, novos clientes se conectam a ele. No entanto, clientes existentes ainda se conectam ao serviço secundário, de modo que as duas instâncias têm tráfego.
Após todos os clientes existentes se desconectarem, o sistema voltará ao normal (Fig.1).

Há dois padrões principais para implementar uma arquitetura entre regiões de alta disponibilidade:

1. O primeiro é ter um par de servidor de aplicativos e instância do Serviço do SignalR que recebe todo o tráfego online e ter outro par como backup (chamado de ativo/passivo, ilustrado na Fig.1). 
2. O outro padrão é ter dois pares (ou mais) de servidores de aplicativos e instâncias do Serviço do SignalR, cada um deles recebendo parte do tráfego online e servindo como backup para os outros pares (chamado de ativo/ativo, semelhante à Fig.3).

O Serviço do SignalR pode dar suporte aos dois padrões, a principal diferença é como você implementa os servidores de aplicativos.
Se os servidores de aplicativos forem do tipo ativo/passivo, o Serviço do SignalR também será ativo/passivo (pois o servidor de aplicativos primário retorna apenas sua instância primária do Serviço do SignalR).
Se os servidores de aplicativos forem do tipo ativo/ativo, o Serviço do SignalR também será ativo/ativo (pois todos os servidores de aplicativos retornarão suas próprias instâncias primárias do SignalR, para que todos eles possam receber tráfego).

Observe que, independentemente dos padrões que você optar por usar, você precisará conectar cada instância do serviço Signalr a um servidor de aplicativos como primário.

Além disso, devido à natureza da conexão do SignalR (trata-se de uma conexão longa), os clientes enfrentarão quedas de conexão quando houver um desastre e o failover ocorrer.
Você precisará lidar com tais casos no lado do cliente para torná-los transparentes para seus clientes finais. Por exemplo, reconecte-se após uma conexão ser encerrada.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar seu aplicativo para ter resiliência para o Serviço do SignalR. Para obter mais detalhes sobre a conexão de cliente/servidor e o roteamento de conexão no Serviço do SignalR, você pode ler [este artigo](signalr-concept-internals.md) sobre recursos internos do Serviço do SignalR.

Para cenários de dimensionamento, como fragmentação, que usam várias instâncias em conjunto para lidar com um grande número de conexões, leia [como dimensionar várias instâncias](signalr-howto-scale-multi-instances.md).
