---
title: O que é Retransmissão do Azure? | Microsoft Docs
description: Este artigo fornece uma visão geral do serviço de Retransmissão do Azure, que permite que você desenvolva aplicativos de nuvem que consomem serviços locais em execução na sua rede corporativa sem abrir uma conexão de firewall ou fazer mudanças intrusivas na sua rede infraestrutura.
services: service-bus-relay
author: spelluru
manager: ''
editor: ''
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: 964a472a5c0a6350090f83755747a12e89a1650e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422922"
---
# <a name="what-is-azure-relay"></a>O que é Retransmissão do Azure?
O serviço de Retransmissão do Azure permite que você exponha com segurança os serviços que são executados na sua rede corporativa para a nuvem pública. Você pode expor os serviços sem abrir uma porta no seu firewall e realizar alterações invasivas a uma infraestrutura de rede corporativa. 

O serviço de retransmissão é compatível com os seguintes cenários entre os serviços locais e aplicativos em execução na nuvem ou em outro ambiente local. 

- Comunicação unidirecional tradicional, solicitação/resposta e ponto a ponto 
- Distribuição de eventos no escopo da Internet para habilitar cenários de publicação/assinatura 
- Comunicação de soquete bidirecional e não armazenada em buffer entre os limites de rede.

A Retransmissão do Azure é diferente de tecnologias de integração no nível de rede, como VPN. O escopo de uma Retransmissão do Azure pode ser definido para um único ponto de extremidade do aplicativo em um único computador. A tecnologia VPN é muito mais intrusiva, pois depende da alteração do ambiente de rede. 

## <a name="basic-flow"></a>Fluxo básico
No padrão de transferência de dados retransmitidos, as etapas básicas envolvidas são:

1. Um serviço local conecta-se ao serviço de retransmissão por meio de uma porta de saída. 
2. Ele cria um soquete bidirecional para comunicação vinculada a um endereço específico. 
3. O cliente pode então comunicar-se com o serviço local enviando tráfego para o serviço de retransmissão direcionado para esse endereço. 
4. O serviço de retransmissão *retransmite* os dados para o serviço local por meio de um soquete bidirecional dedicado ao cliente. O cliente não precisa de uma conexão direta para o serviço local. Ele não precisa saber o local do serviço. E o serviço local não precisa de portas de entrada abertas no firewall.


## <a name="features"></a>Recursos 
A Retransmissão do Azure tem dois recursos:

- [Conexões Híbridas](#hybrid-connections) – usa os soquetes Web de padrão aberto, habilitando cenários de várias plataformas.
- Retransmissões do WCF – usa o WCF (Windows Communication Foundation) para habilitar chamadas de procedimento remotas. A Retransmissão de WCF é a oferta de retransmissão herdada oferta que muitos clientes já usam com seus modelos de programação do WCF.

## <a name="hybrid-connections"></a>Conexões híbridas

O recurso Conexões Híbridas na Retransmissão do Azure é uma evolução segura e de protocolo aberto dos recursos de Retransmissão que existiam anteriormente. Você pode usá-lo em qualquer plataforma e linguagem. O recurso Conexões Híbridas na Retransmissão do Azure é baseado em protocolos HTTP e WebSockets. Ele permite enviar solicitações e receber respostas por meio de soquetes da Web ou HTTP(S). Esse recurso é compatível com a API do WebSocket em navegadores da Web comuns. 

Para obter detalhes sobre o protocolo de Conexão híbrida, confira o [Guia de protocolo de Conexões Híbridas](relay-hybrid-connections-protocol.md). Você pode usar as Conexões Híbridas com qualquer biblioteca de soquetes da Web para qualquer tempo de execução/linguagem.

> [!NOTE]
> As Conexões Híbridas da Retransmissão do Azure substituem o antigo recurso Conexões Híbridas dos Serviços BizTalk. O recurso Conexões Híbridas nos Serviços BizTalk era baseado na Retransmissão do WCF do Barramento de Serviço do Azure. A funcionalidade Conexões Híbridas na Retransmissão do Azure complementa o recurso de Retransmissão do WCF já existente. Essas duas funcionalidades de serviço (Retransmissão do WCF e Conexões Híbridas) existem lado a lado no serviço de Retransmissão do Azure. Eles compartilham um gateway comum, mas têm implementações diferentes.

## <a name="wcf-relay"></a>Retransmissão do WCF
A Retransmissão do WCF funciona com o .NET Framework completo e o WCF. Você cria uma conexão entre o serviço local e o serviço de retransmissão usando um pacote de associações de “retransmissão” do WCF. As associações de retransmissão são mapeadas para novos elementos de ligação de transporte projetados para criar componentes de canal WCF que são integrados ao Barramento de Serviço na nuvem. Para saber mais, confira a [introdução ao WCF Relay](service-bus-relay-tutorial.md).

## <a name="hybrid-connections-vs-wcf-relay"></a>Conexões Híbridas versus Retransmissão do WCF
Tanto as Conexões Híbridas quanto a Retransmissão do WCF habilitam a conexão segura com os ativos que existem dentro de uma rede corporativa. Usar uma ou outra depende das suas necessidades específicas, conforme descrito na seguinte tabela:

|  | Retransmissão do WCF | Conexões híbridas |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **Script Java/Node.JS** | |x |
| **Protocolo aberto baseado em padrões** | |x |
| **Modelos de programação RPC** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>Arquitetura: Processamento de mensagens de solicitações de retransmissão
O diagrama a seguir mostra como as solicitações de retransmissão de entrada são manipuladas pelo serviço de Retransmissão do Azure:

![Processamento de mensagens de solicitações de retransmissão WCF](./media/relay-what-is-it/ic690645.png)

1. O cliente ouvinte envia uma solicitação de escuta para o serviço de Retransmissão do Azure. O Azure Load Balancer encaminha a solicitação para um de nós de gateway. 
2. O serviço de Retransmissão do Azure cria uma retransmissão no repositório de gateway. 
3. O cliente remetente envia uma solicitação para se conectar ao serviço de escuta. 
4. O gateway que recebe a solicitação pesquisa a retransmissão no repositório de gateway. 
5. O gateway encaminha a solicitação de conexão para o gateway correto mencionado no repositório de gateway. 
6. O gateway envia uma solicitação para o cliente ouvinte para que ele crie um canal temporário para o nó de gateway mais próximo ao cliente remetente. 
7. O cliente ouvinte criará um canal temporário para o gateway mais próximo do cliente remetente. Agora que a conexão foi estabelecida entre os clientes por meio de um gateway, os clientes podem trocar mensagens entre si. 
8. O gateway encaminha todas as mensagens do cliente de escuta para o cliente de envio. 
9. O gateway encaminha qualquer mensagem do cliente que enviou para o cliente que está escutando.  

## <a name="next-steps"></a>Próximas etapas
* [Introdução a .NET WebSockets](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução a Pedidos .NET HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Introdução a Node WebSockets](relay-hybrid-connections-node-get-started.md)
* [Introdução a Pedidos Node HTTP](relay-hybrid-connections-http-requests-node-get-started.md)
* [Perguntas frequentes sobre retransmissão](relay-faq.md)

