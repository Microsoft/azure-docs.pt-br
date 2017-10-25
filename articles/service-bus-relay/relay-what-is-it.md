---
title: "O que é a Retransmissão do Azure e por que usá-la - Visão Geral| Microsoft Docs"
description: "Visão geral da Retransmissão do Azure"
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: 77ee85db0bcc701514a1a98da9405a79d658d49d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-relay"></a>O que é Retransmissão do Azure?

O serviço de Retransmissão do Azure facilita os aplicativos híbridos habilitando a exposição segura de serviços que residem em uma rede empresarial corporativa para a nuvem pública sem precisar abrir uma conexão de firewall nem exigir mudanças intrusivas em uma infraestrutura de rede corporativa. A retransmissão dá suporte a vários protocolos de transporte e padrões de serviços Web diferentes.

O serviço de retransmissão dá suporte a tráfego unidirecional tradicional, de solicitação/resposta e ponto a ponto. Ele também oferece suporte a distribuição de eventos no escopo da Internet para habilitar cenários de publicação/assinatura, e a comunicação de soquete bidirecional para maior eficiência ponto a ponto. 

No padrão de transferência de dados retransmitidos, um serviço local conecta-se ao serviço de retransmissão por meio de uma porta de saída e cria um soquete bidirecional para comunicação vinculado a um endereço específico de reunião. Então o cliente pode se comunicar com o serviço local enviando tráfego para o serviço de retransmissão direcionado ao endereço de reunião. O serviço de retransmissão então "retransmite" dados para o serviço local por meio de um soquete bidirecional dedicado para cada cliente. O cliente não precisa de uma conexão direta com o serviço local, não precisa saber onde reside o serviço, e o serviço local não precisa de qualquer porta de entrada aberta no firewall.

Os principais elementos de capacidade fornecidos pela Retransmissão são comunicação bidirecional sem buffer entre limites de rede com limitação estilo TCP, descoberta de ponto de extremidade, status de conectividade e segurança de ponto de extremidade sobreposta. As capacidades de Retransmissão diferem de tecnologias de integração em nível de rede, como VPN, no sentido de que o escopo dessa retransmissão pode ser definido para um único ponto de extremidade do aplicativo em um único computador, enquanto a tecnologia VPN é muito mais intrusiva, pois depende de alterar o ambiente de rede.

A Retransmissão do Azure tem dois recursos:

1. [Conexões Híbridas](#hybrid-connections) – usa os soquetes Web de padrão aberto, habilitando cenários de várias plataformas.
2. [Retransmissões de WCF](#wcf-relays) – usa o WCF (Windows Communication Foundation) para habilitar chamadas de procedimento remotas. A Retransmissão de WCF é a oferta de retransmissão herdada oferta que muitos clientes já usam com seus modelos de programação do WCF.

Conexões Híbridas e Retransmissões de WCF habilitam conexão segura para ativos que existem dentro de uma rede corporativa. O uso de um ou do outro depende de suas necessidades específicas, conforme descrito na seguinte tabela:

|  | Retransmissão de WCF | Conexões Híbridas |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/NodeJS** | |x |
| **Protocolo Aberto Baseado em Padrões** | |x |
| **Vários modelos de programação de RPC** | |x |

## <a name="hybrid-connections"></a>Conexões Híbridas

A funcionalidade de [Conexões Híbridas de Retransmissão do Azure](relay-hybrid-connections-protocol.md) é uma evolução segura de protocolo aberto dos recursos existentes de Retransmissão que pode ser implementada em qualquer plataforma e em qualquer linguagem que tenha uma funcionalidade básica de WebSocket, que inclui explicitamente a API WebSocket em navegadores da Web comuns. A capacidade de Conexões Híbridas baseia-se em HTTP e WebSockets.

### <a name="service-history"></a>Histórico de serviço

As Conexões Híbridas suplantam primeiro recurso, também denominado "Serviços BizTalk", que foi criado na Retransmissão de WCF do Barramento de Serviço do Azure. A nova capacidade Conexões Híbridas complementa o recurso Retransmissão de WCF existente e essas duas capacidades de serviços existirão lado a lado no serviço de Retransmissão do Azure. Eles compartilham um gateway comum, mas têm implementações diferentes.

## <a name="wcf-relays"></a>Retransmissões de WCF

A Retransmissão de WCF funciona para o todo o .NET Framework (NETFX) e o WCF. Você inicia a conexão entre o serviço local e o serviço de retransmissão usando um conjunto de associações de "retransmissão" WCF. Nos bastidores, as associações de retransmissão são mapeadas para novos elementos de ligação de transporte projetados para criar componentes de canal WCF que são integrados ao Barramento de Serviço na nuvem.

## <a name="architecture-processing-of-incoming-relay-requests"></a>Arquitetura: processamento de mensagens de solicitações de retransmissão
Quando um cliente envia uma solicitação ao Serviço [Azure Relay](/azure/service-bus-relay/), o balanceador de carga do Azure direciona para qualquer um dos nós do gateway. Se a solicitação for uma solicitação de escuta, o nó do gateway cria uma nova retransmissão. Se a solicitação for uma solicitação de conexão com uma retransmissão específica, o nó do gateway encaminha a solicitação de conexão para o nó do gateway que tem a retransmissão. O nó do gateway que tem a retransmissão envia uma solicitação de encontro ao cliente de escuta, pedindo ao ouvinte para criar um canal temporário para o nó do gateway que recebeu a solicitação de conexão.

Quando a conexão de retransmissão é estabelecida, os clientes podem trocar mensagens por meio do nó do gateway usado para o encontro.

![Processamento de mensagens de solicitações de retransmissão WCF](./media/relay-what-is-it/ic690645.png)

## <a name="next-steps"></a>Próximas etapas

* [Perguntas frequentes sobre retransmissão](relay-faq.md)
* [Criar um namespace](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)

