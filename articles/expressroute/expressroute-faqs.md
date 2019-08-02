---
title: Perguntas frequentes - Azure ExpressRoute | Microsoft Docs
description: As Perguntas Frequentes Sobre ExpressRoute contêm informações sobre Serviços do Azure com Suporte, Custo, Dados e Conexões, SLA, Provedores e Locais, Largura de banda e Detalhes Técnicos adicionais.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: e5674ffb4325eb27af8d0673b2d6ad5ba3a6195e
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854354"
---
# <a name="expressroute-faq"></a>Perguntas Frequentes sobre ExpressRoute

## <a name="what-is-expressroute"></a>O que é ExpressRoute?

O ExpressRoute é um serviço do Azure que permite a criação de conexões privadas entre os datacenters da Microsoft e a infraestrutura no seu local ou em uma instalação de colocalização. As conexões de ExpressRoute não passam pela Internet pública e oferecem mais segurança, confiabilidade e velocidades maiores com latências menores do que conexões típicas pela Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Quais são os benefícios do uso de ExpressRoute e conexões de rede privada?

As conexões de ExpressRoute não passam pela Internet pública. Oferecem mais segurança, confiabilidade e velocidades maiores com latências consistentes e menores do que conexões típicas pela Internet. Em alguns casos, o uso de conexões do ExpressRoute para transferir dados entre dispositivos locais e o Azure pode proporcionar relações custo/benefício significativas.

### <a name="where-is-the-service-available"></a>Onde o serviço está disponível?

Consulte esta página para obter a disponibilidade e localização do serviço: [Localizações e parceiros do ExpressRoute](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Como posso usar o ExpressRoute para conexão à Microsoft se eu não tenho parcerias com uma das operadoras parceiras do ExpressRoute?

Você pode selecionar uma operadora regional e conexões Ethernet terrestres para um dos locais com suporte do provedor Exchange. Em seguida, você pode emparelhar com a Microsoft no local do provedor. Verifique a última seção de [Locais e Parceiros da ExpressRoute](expressroute-locations.md) para ver se o seu provedor de serviço está presente em qualquer um dos locais do Exchange. Em seguida, você pode solicitar um circuito de ExpressRoute por meio do provedor de serviço para se conectar ao Azure.

### <a name="how-much-does-expressroute-cost"></a>Quanto custa o ExpressRoute?

Consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações a respeito.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Se eu pago por um circuito de ExpressRoute de uma determinada largura de banda, a conexão VPN que adquiro do meu provedor de serviços de rede precisa ser a mesma velocidade?

Nº Você pode comprar uma conexão VPN de qualquer velocidade de seu provedor de serviços. No entanto, sua conexão com o Azure é limitada à largura de banda do circuito de ExpressRoute que você comprar.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Se eu pago por um circuito de ExpressRoute de uma determinada largura de banda, tenho a capacidade de chegar até maiores velocidades, se necessário?

Sim. Circuitos de ExpressRoute são configurados para permitir que você dispare até duas vezes o limite de largura de banda que você adquiriu, sem nenhum custo adicional. Verifique com seu provedor de serviços para ver se eles dão suporte a essa funcionalidade. Isso não é por um período de tempo prolongado e não é garantido. 

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Posso usar a mesma conexão de rede privada com rede virtual e outros serviços do Azure simultaneamente?

Sim. Um circuito de ExpressRoute, uma vez instalado, permite que você acesse os serviços em uma rede virtual e outros serviços do Azure simultaneamente. Você se conecta a redes virtuais pelo caminho de peering privado e a outros serviços pelo caminho de peering da Microsoft.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>o ExpressRoute oferece um SLA (contrato de nível de serviço)?

Para obter informações, veja a página [SLA do ExpressRoute](https://azure.microsoft.com/support/legal/sla/).

## <a name="supported-services"></a>Serviços com suporte

O ExpressRoute dá suporte a [três domínios de roteamento](expressroute-circuit-peerings.md) para vários tipos de serviço.

### <a name="private-peering"></a>Emparelhamento privado

* Redes virtuais, incluindo todas as máquinas virtuais e serviços de nuvem

### <a name="public-peering"></a>Emparelhamento público

>[!NOTE]
>Emparelhamento público foi desabilitado em novos circuitos do ExpressRoute. Os serviços do Azure estão disponíveis no emparelhamento da Microsoft.
>

* Power BI
* Dynamics 365 for Finance and Operations (anteriormente conhecido como Dynamics AX Online)
* Há suporte para a maioria dos serviços do Azure. Verifique diretamente o serviço que você deseja utilizar para confirmar o suporte.<br><br>
  **NÃO há suporte para os serviços a seguir**:
    * CDN
    * Porta da frente do Azure
    * Autenticação Multifator
    * Gerenciador de Tráfego

### <a name="microsoft-peering"></a>Emparelhamento da Microsoft

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Dynamics 365 
* Power BI-disponível por meio de uma comunidade regional do Azure, consulte [aqui](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) para saber como descobrir a região do seu locatário de Power bi. 
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (comunidade de Serviços Globais do Azure)
* Há suporte para a maioria dos serviços do Azure. Verifique diretamente o serviço que você deseja utilizar para confirmar o suporte.<br><br>**NÃO há suporte para os serviços a seguir**:
    * CDN
    * Porta da frente do Azure
    * Autenticação Multifator
    * Gerenciador de Tráfego

## <a name="data-and-connections"></a>Dados e conexões

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Há limites para a quantidade de dados que posso transferir usando ExpressRoute?

Não podemos definir um limite para a quantidade de transferência de dados. Consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações sobre as taxas de largura de banda.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Quais velocidades de conexão têm suporte pelo ExpressRoute?

Ofertas de largura de banda com suporte:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps e 10 Gbps

### <a name="which-service-providers-are-available"></a>Que provedores de serviços estão disponíveis?

Consulte [Locais e Parceiros da ExpressRoute](expressroute-locations.md) para a lista de locais e provedores de serviços.

## <a name="technical-details"></a>Detalhes técnicos

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Quais são os requisitos técnicos para conectar meu espaço local ao Azure?

Consulte a [Página de pré-requisitos para ExpressRoute](expressroute-prerequisites.md) para requisitos.

### <a name="are-connections-to-expressroute-redundant"></a>As conexões a ExpressRoute são redundantes?

Sim. Cada circuito do ExpressRoute tem um par redundante de conexões cruzadas configurado para fornecer alta disponibilidade.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Eu perderei a conectividade se um dos meus links de ExpressRoute falhar?

Você não perderá conectividade se uma das conexões cruzadas falhar. Uma conexão redundante estará disponível para suportar a carga da rede e fornecer alta disponibilidade do seu circuito ExpressRoute. Além disso, você pode criar circuitos em um local de emparelhamento diferente para obter resiliência a nível de circuito.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Como fazer implementar a redundância no emparelhamento privado?

Vários circuitos de ExpressRoute de diferentes locais de emparelhamento podem ser conectados à mesma rede virtual para fornecer alta disponibilidade no caso de um único circuito ficar indisponível. Em seguida, você pode [atribuir pesos mais altos](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) à conexão local para favorecer um circuito específico. É altamente recomendável que os clientes configurem pelo menos dois circuitos de ExpressRoute para evitar pontos únicos de falha. 

Consulte [aqui](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) para obter alta disponibilidade e [aqui](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) para design para recuperação de desastres.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Como faço para implementar a redundância no emparelhamento da Microsoft?

É altamente recomendável quando os clientes estão usando o emparelhamento da Microsoft para acessar os serviços públicos do Azure, como o armazenamento do Azure ou o SQL do Azure, bem como clientes que estão usando o emparelhamento da Microsoft para o Office 365 que implementam vários circuitos em um emparelhamento diferente locais para evitar pontos únicos de falha. Os clientes podem anunciar o mesmo prefixo em ambos os circuitos e usar [como caminho prependente](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) ou anunciar prefixos diferentes para determinar o caminho do local.

Consulte [aqui](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) para design para alta disponibilidade.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Como posso garantir a alta disponibilidade em uma rede virtual conectada ao ExpressRoute?

Você pode obter alta disponibilidade conectando circuitos ExpressRoute em diferentes locais de emparelhamento (por exemplo, Singapura, Singapura2) à sua rede virtual. Se um circuito do ExpressRoute falhar, a conectividade falhará para outro circuito do ExpressRoute. Por padrão, o tráfego que sai da rede virtual é roteado com base no roteamento ECMP (Equal Cost Multi-path). Você pode usar o Peso de conexão para decidir entre um circuito ou outro. Para obter mais informações, consulte [otimizando o roteamento do ExpressRoute](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-or-public-peering-is-preferred-on-the-expressroute-path"></a>Como fazer garantir que meu tráfego destinado a serviços públicos do Azure, como o armazenamento do Azure e o SQL do Azure na Microsoft ou o emparelhamento público, seja preferencial no caminho do ExpressRoute?

Você deve implementar o atributo de *preferência local* no (s) roteador (es) para garantir que o caminho do local para o Azure seja sempre preferido em seus circuitos do ExpressRoute.

Veja mais detalhes [aqui](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-of-microsoft-and-public-peerings) na seleção de caminho BGP e configurações de roteador comuns. 

### <a name="onep2plink"></a>Se eu não estiver colocalizado em uma troca de nuvem e meu provedor de serviços oferecer conexão ponto a ponto, preciso solicitar duas conexões físicas entre minha rede local e a Microsoft?

Se seu provedor de serviços puder estabelecer dois circuitos virtuais de Ethernet por meio da conexão física, você só precisa de uma conexão física. A conexão física (por exemplo, uma fibra óptica) será encerrada em um dispositivo de camada 1 (L1) (confira a imagem). Os dois circuitos virtuais de Ethernet estão marcados com IDs de VLAN diferentes, uma para o circuito primário e outra para o secundário. As IDs de VLAN estão no cabeçalho de Ethernet 802.1Q exterior. O cabeçalho de Ethernet 802.1Q interior (não mostrado) é mapeado para um [domínio de roteamento de ExpressRoute](expressroute-circuit-peerings.md)específico.

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Posso estender uma das minhas VLANs ao Azure usando a ExpressRoute?

Nº Não há suporte para extensões de conectividade de camada 2 ao Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Posso ter mais de um circuito de ExpressRoute em minha assinatura?

Sim. Você pode ter mais de um circuito de ExpressRoute em sua assinatura. O limite padrão é definido como 10. Você pode contatar Suporte da Microsoft para aumentar o limite, se necessário.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Posso ter circuitos de ExpressRoute de diferentes provedores de serviços?

Sim. Você pode ter circuitos de ExpressRoute de muitos provedores de serviços. Cada circuito de ExpressRoute é associado apenas a um provedor de serviços. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Eu vejo dois locais de emparelhamento da ExpressRoute no mesmo metrô, por exemplo, Singapura e Singapura2. Qual local de emparelhamento devo escolher criar o meu circuito do ExpressRoute?
Se o seu provedor de serviços oferecer o ExpressRoute em ambos os sites, você pode trabalhar com seu provedor de e escolher qualquer um dos sites para configurar o ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Posso ter vários circuitos do ExpressRoute no mesmo metro? Posso vinculá-los à mesma rede virtual?

Sim. Você pode ter vários circuitos do ExpressRoute com os mesmos provedores de serviço ou com provedores diferentes. Se o metro tiver vários locais de emparelhamento do ExpressRoute e os circuitos forem criados em diferentes locais de emparelhamento, você poderá vinculá-los à mesma rede virtual. Se os circuitos forem criados no mesmo local de emparelhamento, você poderá vincular até 4 circuitos para a mesma rede virtual.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Como conectar minhas redes virtuais a um circuito do ExpressRoute

As etapas básicas são:

* Estabeleça um circuito de ExpressRoute e fazer com que o provedor de serviços o habilite.
* Você ou o provedor deve configurar os emparelhamentos via protocolo BGP.
* Vincule uma Rede Virtual ao circuito de ExpressRoute.

Para obter mais informações, consulte [Fluxos de trabalho do ExpressRoute para provisionamento e estados do circuito](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Existem limites de conectividade para meu circuito de ExpressRoute?

Sim. O artigo [Locais e Parceiros do ExpressRoute](expressroute-locations.md) fornece uma visão geral dos limites de conectividade para um circuito de ExpressRoute. A conectividade de um circuito de ExpressRoute é limitada a uma única região geopolítica. A conectividade pode ser expandida para várias regiões geopolíticas habilitando o recurso premium do ExpressRoute.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Posso vincular mais de uma Rede Virtual a um circuito de ExpressRoute?

Sim. Você pode ter até 10 conexões de redes virtuais em um circuito de ExpressRoute padrão, e até 100 em um [circuito de ExpressRoute premium](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Tenho várias assinaturas do Azure que contêm redes virtuais. Posso conectar redes virtuais pertencentes a assinaturas separadas a um circuito do ExpressRoute?

Sim. Você pode vincular até 10 redes virtuais na mesma assinatura que o circuito ou assinaturas diferentes usando um único circuito do ExpressRoute. Esse limite pode ser aumentado habilitando o recurso premium da ExpressRoute.

Para obter mais informações, consulte [Compartilhando um circuito de ExpressRoute entre várias assinaturas](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Tenho várias assinaturas do Azure associadas a locatários diferentes do Azure Active Directory ou registros do Contrato Enterprise. Posso conectar redes virtuais que estão em registros e locatários separados a um único circuito do ExpressRoute não no mesmo locatário ou registro?

Sim. Autorizações do ExpressRoute podem abranger os limites de assinatura, locatário e registro sem a necessidade de configuração adicional. 

Para obter mais informações, consulte [Compartilhando um circuito de ExpressRoute entre várias assinaturas](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>As redes virtuais estão conectadas ao mesmo circuito e isoladas umas das outras?

Nº Segunda uma perspectiva de roteamento, todas as redes virtuais vinculadas ao mesmo circuito de ExpressRoute fazem parte do mesmo domínio de roteamento e não estão isoladas entre si. Se você precisar de isolamento de rota, você precisará criar um circuito de ExpressRoute separado.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Posso conectar uma Rede Virtual a mais de um circuito de ExpressRoute?

Sim. Você pode vincular uma única rede virtual com até quatro circuitos de ExpressRoute nos mesmos ou em locais de emparelhamento diferentes. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Poderei acessar a Internet por meio de minhas redes virtuais conectadas a circuitos de ExpressRoute?

Sim. Se você não anunciou rotas padrão (0.0.0.0/0) ou prefixos de rotas de Internet ao longo da sessão BGP, você pode se conectar à Internet por meio de uma rede virtual vinculada a um circuito de ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Posso bloquear a conectividade à Internet em minhas redes virtuais conectadas a circuitos de ExpressRoute?

Sim. Você pode anunciar rotas padrão (0.0.0.0/0) para bloquear toda a conectividade com a Internet para máquinas virtuais implantadas em uma rede virtual e rotear todo o tráfego de saída através do circuito de ExpressRoute.

Se você anunciar rotas padrão, forçamos o tráfego para serviços oferecidos pelo peering da Microsoft (como armazenamento do Azure e SQL DB) de volta às suas instalações. Você precisará configurar seus roteadores para retornar o tráfego para o Azure por meio do caminho de peering da Microsoft ou pela Internet. Se você tiver ativado um ponto de extremidade de serviço para o serviço, o tráfego para o serviço não será forçado para suas instalações. O tráfego permanece dentro da rede de backbone do Azure. Para saber mais sobre os pontos de extremidade do serviço, consulte [Pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>As redes virtuais vinculadas ao mesmo circuito de ExpressRoute podem conversar entre si?

Sim. Máquinas virtuais implantadas em redes virtuais conectadas ao mesmo circuito de ExpressRoute podem comunicar-se umas com as outras.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Posso usar a conectividade site a site para redes virtuais em conjunto com ExpressRoute?

Sim. O ExpressRoute pode coexistir com VPN dos tipos site a site. Consulte [Configure as conexões coexistentes de ExpressRoute e site a site](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Por que há um endereço IP público associado ao gateway de ExpressRoute em uma rede virtual?

O endereço IP público é usado apenas para gerenciamento interno e não constitui uma exposição de segurança de sua rede virtual.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Há limites no número de rotas que posso anunciar?

Sim. Aceitamos até 4.000 prefixos de rota para emparelhamento privado e 200 para emparelhamento da Microsoft. Você poderá aumentar esse limite para 10.000 rotas para emparelhamento privado se habilitar o recurso premium do ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Há restrições de intervalos de endereços IP que posso anunciar durante a sessão BGP?

Não aceitamos prefixos privados (RFC1918) para a sessão BGP de emparelhamento Microsoft. Aceitamos qualquer tamanho de prefixo (até/32) tanto na Microsoft quanto no emparelhamento privado.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>O que acontece se eu exceder os limites de BGP?

As sessões BGP serão interrompidas. Elas serão redefinidas quando a contagem de prefixos voltar a ficar abaixo do limite.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>O que é o tempo de espera de BGP do ExpressRoute? Pode ser ajustado?

O tempo de espera é de 180. As mensagens keep-alive são enviadas a cada 60 segundos. Essas são configurações fixas no lado do Microsoft que não podem ser alteradas. É possível configurar temporizadores diferentes e os parâmetros da sessão BGP serão negociados adequadamente.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Posso alterar a largura de banda de um circuito de ExpressRoute?

Sim, você pode tentar aumentar a largura de banda do circuito de ExpressRoute no portal do Azure ou usando o PowerShell. Se houver capacidade disponível na porta física na qual o circuito foi criado, a alteração terá êxito. 

Se sua alteração falhar, isso significa que não há capacidade suficiente na porta atual e você precisa criar um novo circuito de ExpressRoute com largura de banda maior, ou que não há capacidade adicional nesse local, e nesse caso não é possível aumentar a largura de banda. 

Você também terá de fazer o acompanhamento junto ao seu provedor de conectividade para garantir que eles atualizem os gargalos em suas redes, para dar suporte ao aumento de largura de banda. No entanto, não é possível reduzir a largura de banda de seu circuito de ExpressRoute. Você precisa criar um novo circuito de ExpressRoute com menos largura de banda e excluir o circuito antigo.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Como posso alterar a largura de banda de um circuito de ExpressRoute?

Você pode atualizar a largura de banda do circuito ExpressRoute usando a API REST ou o cmdlet do PowerShell.

## <a name="expressroute-premium"></a>ExpressRoute premium

### <a name="what-is-expressroute-premium"></a>O que é o ExpressRoute Premium?

ExpressRoute premium é uma coleção dos seguintes recursos:

* Limite maior na tabela de roteamento, de 4000 rotas para 10.000 rotas, para emparelhamento privado.
* Maior número de conexões de redes virtuais e do Alcance Global do ExpressRoute que podem ser habilitadas em um circuito de ExpressRoute (o padrão é 10). Para saber mais, veja a tabela [Limites do ExpressRoute](#limits).
* Conectividade com o Office 365 e o Dynamics 365.
* Conectividade global através da rede de núcleo da Microsoft. Agora, você pode conectar uma VNet em uma região geopolítica a um circuito de ExpressRoute em outra região.<br>
    **Exemplos:**

    *  É possível conectar uma VNet criada na Europa Ocidental a um circuito de ExpressRoute criado no Vale do Silício. 
    *  No emparelhamento da Microsoft, os prefixos de outras regiões geopolíticas são anunciados, de modo que você pode se conectar, por exemplo, SQL Azure no Oeste da Europa de um circuito no Vale do Silício.


### <a name="limits"></a>Quantas conexões de redes virtuais e o Alcance Global do ExpressRoute pode habilitar em um circuito de ExpressRoute se eu tiver habilitado o ExpressRoute premium?

As tabelas a seguir mostram os limites do ExpressRoute e o número de conexões de redes virtuais e do Alcance Global do ExpressRoute por circuito de ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Como habilito o ExpressRoute premium?

Os recursos do ExpressRoute premium podem ser ativados quando o recurso está habilitado, e podem ser desativados atualizando o estado do circuito. Você pode habilitar o ExpressRoute Premium no momento da criação do circuito ou pode chamar a API REST/o cmdlet do PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Como desabilito o ExpressRoute premium?

Você pode desabilitar o ExpressRoute Premium chamando a API REST ou o cmdlet do PowerShell. Você deve certificar-se de que dimensionou suas necessidades de conectividade para atender os limites padrão antes de desabilitar o ExpressRoute Premium. Se sua utilização expandir além dos limites padrão, a solicitação para desabilitar o ExpressRoute premium falhará.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Posso escolher os recursos que desejo do conjunto de recursos premium?

Nº Você não pode escolher os recursos. Habilitamos todos os recursos quando você ativa o ExpressRoute premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Quanto custa o ExpressRoute premium?

Consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para informações sobre custo.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Eu pago algo pelo ExpressRoute premium além dos encargos padrão do ExpressRoute?

Sim. Os encargos do ExpressRoute premium somam-se aos encargos de circuito de ExpressRoute e aos encargos demandados pelo provedor de conectividade.

## <a name="expressroute-local"></a>Local do ExpressRoute
### <a name="what-is-expressroute-local"></a>O que é o ExpressRoute local?
O ExpressRoute local é um SKU do circuito do ExpressRoute disponível no [ExpressRoute Direct](expressroute-erdirect-about.md). Um dos principais recursos do local é que um circuito local em um local de emparelhamento do ExpressRoute fornece acesso apenas a uma ou duas regiões do Azure no ou perto do mesmo metro. Por outro lado, um circuito padrão fornece acesso a todas as regiões do Azure em uma área geopolítica e um circuito Premium para todas as regiões do Azure globalmente. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Quais são os benefícios do ExpressRoute local?
Embora você precise pagar a transferência de dados de saída para o circuito de ExpressRoute Standard ou Premium, não pague a transferência de dados de saída separadamente para o circuito local do ExpressRoute. Em outras palavras, o preço do ExpressRoute local inclui taxas de transferência de dados. O ExpressRoute local é uma solução mais econômica se você tiver uma grande quantidade de dados a serem transferidos e você pode trazer seus dados por uma conexão privada para um local de emparelhamento do ExpressRoute perto das regiões do Azure desejadas. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Quais recursos estão disponíveis e o que não está no ExpressRoute local?
Em comparação com um circuito de ExpressRoute padrão, um circuito local tem o mesmo conjunto de recursos, exceto:
* Escopo de acesso às regiões do Azure, conforme descrito acima
* O ExpressRoute Alcance Global não está disponível no local

O ExpressRoute local também tem os mesmos limites de recursos (por exemplo, o número de VNets por circuito) que o padrão. 

### <a name="how-to-configure-expressroute-local"></a>Como configurar o ExpressRoute local? 
O ExpressRoute local está disponível somente no ExpressRoute Direct. Primeiro, você precisará configurar sua porta do ExpressRoute Direct. Depois que a porta direta for criada, você poderá criar um circuito local seguindo as instruções [aqui](expressroute-howto-erdirect.md).

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Onde o ExpressRoute local está disponível e a quais regiões do Azure cada local de emparelhamento está mapeado?
O ExpressRoute local está disponível nos locais de emparelhamento onde uma ou duas regiões do Azure são fechadas. Ele não está disponível em um local de emparelhamento onde não há nenhuma região do Azure nesse estado ou província ou país. Consulte os mapeamentos exatos na [página locais](expressroute-locations-providers.md).  

## <a name="expressroute-for-office-365"></a>ExpressRoute para Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Como faço para criar um circuito da Rota Expressa para se conectar aos serviços do Office 365?

1. Examine a [página de pré-requisitos do ExpressRoute](expressroute-prerequisites.md) para se certificar de que você atende aos requisitos.
2. Para garantir que suas necessidades de conectividade são atendidas, examine a lista de provedores de serviços e locais no artigo [Locais e parceiros do ExpressRoute](expressroute-locations.md).
3. Planeje seus requisitos de capacidade, revisando [Planejamento de rede e ajuste de desempenho para o Office 365](https://aka.ms/tune/)
4. Siga as etapas relacionadas nos fluxos de trabalho para configurar a conectividade [Fluxos de trabalho de ExpressRoute para provisionamento do circuito e estados do circuito](expressroute-workflows.md).

> [!IMPORTANT]
> Certifique-se de ter ativado o complemento premium do ExpressRoute ao configurar a conectividade com os serviços do Office 365.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>Meus circuitos do ExpressRoute podem dar suporte à conectividade com os serviços do Office 365 e o Dynamics 365?

Sim. Seus circuitos do ExpressRoute existentes podem ser configurados para dar suporte a conectividade com os serviços do Office 365. Certifique-se de que você tem capacidade suficiente para se conectar aos serviços do Office 365 e que você habilitou o complemento premium. [Planejamento da rede e ajuste de desempenho para o Office 365](https://aka.ms/tune/) ajuda você a planejar suas necessidades de conectividade. Veja também [Criar e modificar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Quais serviços do Office 365 podem ser acessados por uma conexão de ExpressRoute?

Confira a página [Intervalos de endereço IP e URLs do Office 365](https://aka.ms/o365endpoints) para obter uma lista atualizada dos serviços com suporte do ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Quanto custa o ExpressRoute para serviços do Office 365?

Serviços do Office 365 exigem o complemento premium para serem habilitados. Consulte a [página de detalhes de preço](https://azure.microsoft.com/pricing/details/expressroute/) para custos.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Em que regiões há suporte para ExpressRoute para Office 365?

Consulte [Parceiros e locais do ExpressRoute](expressroute-locations.md) para obter mais informações.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Posso acessar o Office 365 pela Internet mesmo se o ExpressRoute foi configurado para minha organização?

Sim. Pontos de extremidade do serviço do Office 365 estão acessíveis pela Internet, embora o ExpressRoute tenha sido configurado para sua rede. Por favor verifique com a equipe de rede de sua organização se a rede local está configurada para conectar os serviços do Office 365 por meio de ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Como posso planejar para uma alta disponibilidade de tráfego de rede no Office 365 no Azure ExpressRoute?
Consulte a recomendação para [alta disponibilidade e failover com o Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Posso acessar os serviços da Comunidade Governamental dos EUA (GCC) do Office 365 por um circuito ExpressRoute do Governo dos EUA para o Azure?

Sim. Os pontos de extremidade de serviço GCC do Office 365 são acessíveis por meio do ExpressRoute do Governo dos EUA para o Azure. No entanto, primeiro você precisa abrir um tíquete de suporte no Portal do Azure para fornecer os prefixos que pretende anunciar à Microsoft. Sua conectividade com os serviços GCC do Office 365 será estabelecida depois que o tíquete de suporte for resolvido. 

## <a name="route-filters-for-microsoft-peering"></a>Filtros de rota para emparelhamento da Microsoft

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Eu estou ativando o emparelhamento da Microsoft pela primeira vez, quais rotas haverá?

Você não verá nenhuma rota. Você precisa anexar um filtro de rota para o circuito para iniciar os anúncios de prefixo. Para obter instruções, consulte [Configurar os filtros de rota para emparelhamento da Microsoft](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Eu ativei o emparelhamento da Microsoft e agora estou tentando selecionar Exchange Online, mas aparece um erro que não estou autorizado a fazê-lo.

Ao usar filtros de rota, qualquer cliente pode ativar emparelhamento da Microsoft. No entanto, para o consumo de serviços do Office 365, você ainda precisa obter autorização do Office 365.

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>É necessário obter autorização para ativar Dynamics 365 sobre emparelhamento da Microsoft?

Não, você não precisa autorização para Dynamics 365. Você pode criar uma regra e selecionar a comunidade Dynamics 365 sem autorização.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Eu habilitei o peering da Microsoft antes de 1º de agosto de 2017, como posso aproveitar os filtros de rota?

O circuito existente continuará divulgando os prefixos para Office 365 e Dynamics 365. Se quiser adicionar anúncios de prefixos públicos do Azure através do mesmo emparelhamento da Microsoft, é possível criar um filtro de rota, selecionar os serviços que precisa anunciar (incluindo os serviços do Office 365 de que precisa e Dynamics 365) e anexar o filtro ao emparelhamento da Microsoft. Para obter instruções, consulte [Configurar os filtros de rota para emparelhamento da Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Tenho o emparelhamento da Microsoft em um local, agora estou tentando ativá-lo em outro local e não estou vendo os prefixos.

* O emparelhamento da Microsoft de circuitos de ExpressRoute configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados através do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos.

* O emparelhamento da Microsoft de circuitos de ExpressRoute configurados em ou após 1º de agosto de 2017 não terá nenhum prefixo anunciado até que um filtro de rota seja anexado ao circuito. Você não verá nenhum prefixo por padrão.

## <a name="expressRouteDirect"></a>ExpressRoute direto

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="globalreach"></a>Alcance Global

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
