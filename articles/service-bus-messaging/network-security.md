---
title: Segurança de rede para o barramento de serviço do Azure
description: Este artigo descreve os recursos de segurança de rede, como marcas de serviço, regras de firewall de IP, pontos de extremidade de serviço e pontos de extremidade privados.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: db0dd89d1f902699c27b724609505ba681757454
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310454"
---
# <a name="network-security-for-azure-service-bus"></a>Segurança de rede para o barramento de serviço do Azure 
Este artigo descreve como usar os seguintes recursos de segurança com o barramento de serviço do Azure: 

- Marcas de serviço
- Regras de firewall de IP
- Pontos de extremidade de serviço de rede
- Pontos de extremidade privados


## <a name="service-tags"></a>Marcas de serviço
Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços, minimizando a complexidade de atualizações frequentes das regras de segurança de rede. Para obter mais informações sobre marcas de serviço, consulte [visão geral das marcas de serviço](../virtual-network/service-tags-overview.md).

Você pode usar marcas de serviço para definir os controles de acesso à rede em [grupos de segurança de rede](../virtual-network/network-security-groups-overview.md#security-rules) ou no [Firewall do Azure](../firewall/service-tags.md). Use marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, **ServiceBus**) no campo de *origem* ou *destino* apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente.

| Marca de serviço | Finalidade | É possível usar entrada ou saída? | Pode ser regional? | É possível usar com o Firewall do Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Barramento de Serviço** | O tráfego do Barramento de Serviço do Azure que usa a camada de serviço Premium. | Saída | Sim | Sim |


> [!NOTE]
> Você pode usar marcas de serviço somente para namespaces **Premium** . Se você estiver usando um namespace **padrão** , use o endereço IP que você vê ao executar o seguinte comando: `nslookup <host name for the namespace>` . Por exemplo: `nslookup contosons.servicebus.windows.net`. 

## <a name="ip-firewall"></a>Firewall de IP 
Por padrão, os namespaces de Barramento de Serviço são acessíveis pela Internet, desde que a solicitação acompanhe uma autenticação e uma autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Esse recurso é útil em cenários nos quais o Barramento de Serviço do Azure deve ser acessível apenas através determinados sites conhecidos. As regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, se você usar o barramento de serviço com [rota expressa do Azure] [rota expressa], poderá criar uma **regra de firewall** para permitir o tráfego somente de endereços IP de infraestrutura local ou endereços de um gateway de NAT corporativo. 

As regras de firewall de IP são aplicadas no nível de namespace do Barramento de Serviço. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace do Barramento de Serviço será rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Para obter mais informações, consulte [como configurar o firewall IP para um namespace do barramento de serviço](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>Pontos de extremidade de serviço de rede
A integração do barramento de serviço com [pontos de extremidade de serviço de rede virtual (VNet)](service-bus-service-endpoints.md) permite o acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede protegido em ambas as extremidades.

Após ser configurado para associar-se a pelo menos um ponto de extremidade de serviço de sub-rede da rede virtual, o respectivo namespace de Barramento de Serviço não aceitará mais tráfego de nenhum lugar, exceto das redes virtuais autorizadas. Da perspectiva da rede virtual, a associação de um namespace de Barramento de Serviço a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de sistema de mensagens.

O resultado é um relacionamento privado e isolado entre as cargas de trabalho associadas à sub-rede e o respectivo namespace de Barramento de Serviço, apesar do endereço de rede observável do ponto de extremidade de serviço de mensagens estar em um intervalo de IP público.

> [!IMPORTANT]
> As redes virtuais têm suporte apenas em namespaces do Barramento de Serviço [camada Premium](service-bus-premium-messaging.md).
> 
> Ao usar pontos de extremidade de serviço de VNet com o barramento de serviço, você não deve habilitar esses pontos de extremidade em aplicativos que misturam namespaces de barramento de serviço de camada Standard e Premium. Porque a camada Standard não oferece suporte a VNets. O ponto de extremidade é restrito somente a namespaces da camada Premium.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração de VNet 

Soluções que exigem segurança compartimentada e restrita, e onde as sub-redes da rede virtual fornecem a segmentação entre os serviços compartimentados, geralmente ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo aqueles que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades a partir da camada de rede. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, onde as mensagens são gravadas no disco na medida em que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão associadas à mesma instância de Barramento de Serviço podem comunicar-se de maneira eficiente e confiável por meio de mensagens, enquanto a integridade de limite de isolamento da respectiva rede é preservada.
 
Isso significa que as soluções na nuvem de segurança confidencial não apenas obtêm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do mercado do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução protegidos que são inerentemente mais seguros que o modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de socket protegidos por TLS.

### <a name="bind-service-bus-to-virtual-networks"></a>Associar o barramento de serviço a redes virtuais

As *regras da rede virtual* são o recurso de segurança do firewall que controla se o servidor de Barramento de Serviço do Azure aceita conexões de uma sub-rede de rede virtual específica.

Associar um namespace de Barramento de Serviço a uma rede virtual é um processo de duas etapas. Primeiro, você precisa criar um **ponto de extremidade de serviço de rede virtual** em uma sub-rede de rede virtual e habilitá-lo para **Microsoft. ServiceBus** , conforme explicado na [visão geral do ponto de extremidade de serviço](service-bus-service-endpoints.md). Após adicionar o ponto de extremidade de serviço, você associa o namespace de Barramento de Serviço ao ponto de extremidade com uma **regra da rede virtual**.

A regra da rede virtual é uma associação do namespace de Barramento de Serviço com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho associadas à sub-rede recebem acesso ao namespace de Barramento de Serviço. Os próprios Barramentos de Serviço nunca estabelecem conexões de saída, não precisam obter acesso e, portanto, nunca recebem acesso à sub-rede habilitando essa regra.

Para obter mais informações, consulte [como configurar pontos de extremidade de serviço de rede virtual para um namespace do barramento de serviço](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Pontos de extremidade privados

O Serviço de Link Privado do Azure permite acessar os Serviços do Azure (por exemplo, o Barramento de Serviço do Azure, o Armazenamento do Azure e o Azure Cosmos DB) e serviços de parceiros/clientes hospedados no Azure em um **ponto de extremidade privado** da sua rede virtual.

O ponto de extremidade privado é uma interface de rede que conecta você de forma privada e segura a um serviço com tecnologia do Link Privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Para obter mais informações, confira [O que é o Link Privado do Azure?](../private-link/private-link-overview.md)

> [!NOTE]
> Esse recurso é compatível com a camada **Premium** do Barramento de Serviço do Azure. Para saber mais sobre a camada Premium, confira [Camadas de mensagens Premium e Standard do Barramento de Serviço](service-bus-premium-messaging.md).


Para obter mais informações, consulte [como configurar pontos de extremidade privados para um namespace do barramento de serviço](private-link-service.md)


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como configurar o firewall IP para um namespace do barramento de serviço](service-bus-ip-filtering.md)
- [Como configurar pontos de extremidade de serviço de rede virtual para um namespace do barramento de serviço](service-bus-service-endpoints.md)
- [Como configurar pontos de extremidade privados para um namespace do barramento de serviço](private-link-service.md)