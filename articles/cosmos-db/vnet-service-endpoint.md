---
title: Proteger o acesso a uma conta do Azure Cosmos DB usando o ponto de extremidade de serviço de Rede Virtual do Azure
description: Este documento descreve sobre o controle de acesso à rede virtual e sub-rede para uma conta do Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 672c62c440708f8e949d67d545bee2179c6066b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765509"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Acessar o Azure Cosmos DB de VNets (redes virtuais)

Você pode configurar a conta do Azure Cosmos para permitir o acesso somente de uma sub-rede específica da rede virtual (VNet). Ao habilitar o [Service endpoint](../virtual-network/virtual-network-service-endpoints-overview.md) para acessar o Azure Cosmos DB na sub-rede em uma rede virtual, o tráfego dessa sub-rede é enviado ao Azure Cosmos DB com a identidade da sub-rede e da Virtual Network. Quando o ponto de extremidade do serviço do Azure Cosmos DB estiver habilitado, você poderá limitar o acesso à sub-rede adicionando-o à sua conta do Azure Cosmos.

Por padrão, uma conta do Azure Cosmos pode ser acessada de qualquer origem se a solicitação for acompanhada por um token de autorização válido. Quando você adiciona uma ou mais sub-redes dentro de VNets, somente solicitações originadas dessas sub-redes obterão uma resposta válida. Solicitações provenientes de qualquer outra fonte receberão uma resposta 403 (Proibido). 

## <a name="frequently-asked-questions"></a>Perguntas frequentes

Aqui estão algumas perguntas frequentes sobre como configurar o acesso a partir de redes virtuais:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Posso especificar o ponto de extremidade de serviço de rede virtual e a política de controle de acesso IP em uma conta do Azure Cosmos? 

Você pode habilitar o ponto de extremidade de serviço de rede virtual e uma diretiva de controle de acesso IP (também chamada de firewall) em sua conta do Azure Cosmos. Esses dois recursos são complementares e coletivamente garantem o isolamento e a segurança da sua conta do Azure Cosmos. Usar o firewall IP garante que os IPs estáticos possam acessar sua conta. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Como fazer para limitar o acesso à sub-rede dentro de uma rede virtual? 

Há duas etapas necessárias para limitar o acesso à conta do Azure Cosmos a partir de uma sub-rede. Primeiro, você permite que o tráfego da sub-rede carregue sua sub-rede e identidade de rede virtual para o Azure Cosmos DB. Isso é feito habilitando o ponto de extremidade de serviço do Azure Cosmos DB na sub-rede. Em seguida está adicionando uma regra na conta do Azure Cosmos especificando essa sub-rede como uma fonte da qual a conta pode ser acessada.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>As ACLs de rede virtual e o Firewall IP rejeitarão solicitações ou conexões? 

Quando o firewall IP ou as regras de acesso à rede virtual são adicionadas, somente solicitações de fontes permitidas obtêm respostas válidas. Outras solicitações são rejeitadas com um erro 403 (Proibido). É importante distinguir o firewall da conta do Azure Cosmos de um firewall no nível de conexão. A origem ainda pode se conectar ao serviço e as conexões em si não são rejeitadas.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Minhas solicitações começaram a ser bloqueadas quando eu ativei o endpoint de serviço para o Azure Cosmos DB na sub-rede. O que aconteceu?

Depois que o ponto de extremidade de serviço do Azure Cosmos DB é habilitado em uma sub-rede, a origem do tráfego que chega à conta muda de IP público para rede virtual e sub-rede. Se a sua conta do Azure Cosmos tiver somente firewall baseado em IP, o tráfego da sub-rede habilitada para serviço não corresponderá mais às regras de firewall de IP e, portanto, será rejeitado. Siga os passos para migrar sem problemas do firewall baseado em IP para o controle de acesso baseado em rede virtual.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>As redes virtuais emparelhadas também têm acesso à conta do Azure Cosmos? 
Apenas a rede virtual e suas sub-redes adicionadas à conta do Azure Cosmos têm acesso. Suas VNets emparelhadas não podem acessar a conta até que as sub-redes em redes virtuais separadas sejam adicionadas à conta.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Qual é o número máximo de sub-redes com permissão para acessar uma única conta do Cosmos? 
No momento, você pode ter no máximo 64 sub-redes permitidas para uma conta de Cosmos do Azure.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Posso habilitar o acesso da VPN e do Expresso Route? 
Para acessar a conta do Azure Cosmos pela rota expressa, de locais, você precisa habilitar o emparelhamento da Microsoft. Depois de colocar as regras de firewall de IP ou de acesso à rede virtual, você poderá adicionar os endereços IP públicos usados para o emparelhamento da Microsoft em seu firewall IP da conta do Azure Cosmos para permitir acesso de serviços nas instalações à conta do Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Preciso atualizar as regras do Network Security Groups (NSG)? 
As regras NSG são usadas para limitar a conectividade de e para uma sub-rede com rede virtual. Quando você adiciona o ponto de extremidade de serviço do Azure Cosmos DB à sub-rede, não há necessidade de abrir a conectividade de saída no NSG para sua conta do Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Os pontos de extremidade de serviço estão disponíveis para todas as VNets?
Não, somente as redes virtuais do Azure Resource Manager podem ter o ponto de extremidade do serviço ativado. As redes virtuais clássicas não suportam pontos de extremidade de serviço.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Posso "Aceitar conexões de datacenters públicos do Azure" quando o acesso ao ponto de extremidade de serviço está habilitado para o Banco de Dados do Azure Cosmos?  
Isso é necessário somente quando você deseja que sua conta do Azure Cosmos DB seja acessada por outros serviços de terceiros do Azure primeiro, como Azure Data Factory, Azure Search ou qualquer serviço implantado em determinada região do Azure.


## <a name="next-steps"></a>Próximas etapas

* [Como limitar o acesso de conta do Azure Cosmos às sub-redes em redes virtuais](how-to-configure-vnet-service-endpoint.md)
* [Como configurar o firewall de IP para sua conta do Azure Cosmos](how-to-configure-firewall.md)

