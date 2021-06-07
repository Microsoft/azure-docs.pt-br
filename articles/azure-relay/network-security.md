---
title: Segurança de rede para retransmissão do Azure
description: Este artigo descreve como usar regras de firewall IP e pontos de extremidade privados com a retransmissão do Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 735a0b7dc303ad63cb301151e3e14e4488c856b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217236"
---
# <a name="network-security-for-azure-relay"></a>Segurança de rede para retransmissão do Azure 
Este artigo descreve como usar os seguintes recursos de segurança com a retransmissão do Azure: 

- Regras de firewall de IP
- Pontos de extremidade privados 

> [!NOTE]
> A retransmissão do Azure não dá suporte a pontos de extremidade de serviço de rede. 


## <a name="ip-firewall"></a>Firewall de IP 
Por padrão, os namespaces de Retransmissão são acessíveis da Internet, desde que a solicitação acompanhe autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Esse recurso é útil em cenários nos quais a Retransmissão do Azure deve ser acessível apenas de determinados sites conhecidos. As regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, se você usa a Retransmissão com o [Azure ExpressRoute](../expressroute/expressroute-faqs.md#supported-services), é possível criar uma **regra de firewall** para permitir o tráfego apenas de seus endereços IP da infraestrutura local. 

As regras de firewall IP são aplicadas no nível do namespace de retransmissão. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra de IP permitida no namespace de retransmissão é rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Para obter mais informações, consulte [como configurar o firewall IP para um namespace de retransmissão](ip-firewall-virtual-networks.md)

## <a name="private-endpoints"></a>Pontos de extremidade privados

O Serviço de Link Privado do Azure permite acessar os Serviços do Azure (por exemplo, Retransmissão do Azure, Barramento de Serviço do Azure, Hubs de Eventos do Azure, Armazenamento do Microsoft Azure e Azure Cosmos DB) e serviços de parceiros/clientes hospedados no Azure em um **ponto de extremidade privado** da sua rede virtual. Para obter mais informações, confira [O que é o Link Privado do Azure?](../private-link/private-link-overview.md)

Um **ponto de extremidade privado**  é uma interface de rede que permite que suas cargas de trabalho em execução em uma rede virtual se conectem de forma privada e segura a um serviço que tenha um **recurso de link privado** (por exemplo, um namespace de Retransmissão). O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhum ExpressRoute, nenhuma conexão VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode fornecer um nível de granularidade ao controle de acesso que permite conexões a namespaces específicos da Retransmissão do Azure.

Para obter mais informações, consulte [como configurar pontos de extremidade privados](private-link-service.md)


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como configurar o firewall IP](ip-firewall-virtual-networks.md)
- [Como configurar pontos de extremidade privados](private-link-service.md)
