---
title: Melhores práticas para configurar a rede para as cargas de trabalho migradas para o Azure | Microsoft Docs
description: Depois de migrar para o Azure, obtenha as melhores práticas para a configuração de rede para suas cargas de trabalho migradas.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: raynew
ms.openlocfilehash: 302445038dc9767bd412e232f62fc5249a1a7f09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61296208"
---
# <a name="best-practices-to-set-up-networking-for-workloads-migrated-to-azure"></a>Melhores práticas para configurar a rede para as cargas de trabalho migradas para o Azure

Ao planejar e estruturar a migração, além da migração em si, uma das etapas mais críticas é o design e implementação da rede do Azure. Este artigo descreve as melhores práticas para a rede durante a migração para implementações de IaaS e PaaS no Azure.

> [!IMPORTANT]
> As melhores práticas e as opiniões descritas neste artigo baseiam-se na plataforma Azure e nos recursos do serviço disponíveis no momento em que o artigo foi escrito. Os recursos e as funcionalidades mudam ao longo do tempo. Nem todas as recomendações poderão ser aplicáveis à sua implantação. Portanto, selecione o que funciona para você.


## <a name="design-virtual-networks"></a>Design de redes virtuais

O Azure fornece VNets (redes virtuais):
- Os recursos do Azure se comunicam em particular, diretamente e em segurança entre si em VNets.
- É possível configurar conexões de ponto de extremidade em VNets para VMs e serviços que exigem comunicação via Internet.
- Uma VNet é um isolamento lógico da nuvem do Azure dedicada à sua assinatura.
- É possível implementar várias VNets dentro de cada assinatura e região do Azure.
- Cada VNet é isolada de outras VNets.
- VNets podem conter endereços IP públicos e privados, definidos no [RFC 1918](https://tools.ietf.org/html/rfc1918), expresso em notação CIDR. Os endereços IP públicos não são acessíveis diretamente da Internet.
- VNets podem se conectar entre si usando o Emparelhamento VNET. VNets conectadas podem estar em regiões iguais ou diferentes. Portanto, recursos em uma VNets podem se conectar a recursos em outras VNets.
- Por padrão, o Azure encaminha o tráfego entre sub-redes em uma VNet, VNets conectadas, redes locais e Internet.


Há uma série de pontos que você precisa pensar ao planejar sua topologia de VNet, incluindo como organizar os espaços de endereços IP, como implementar uma rede de hub-spoke, como segmentar as VNets em sub-redes, configurar o DNS e implementar as zonas de disponibilidade do Azure.

## <a name="best-practice-plan-ip-addressing"></a>Melhor prática: planejar o endereçamento IP

Ao criar VNets como parte de sua migração, é importante planejar seu espaço de endereços IP da VNet.

- Você deve atribuir um espaço de endereço que não seja maior do que um intervalo CIDR de /16 para cada VNet. VNets permitem o uso de endereços IP 65536 e atribuir um prefixo menor que /16 resulta na perda de endereços IP. É importante não desperdiçar endereços IP, mesmo se estiverem em intervalos privados definidos pelo RFC 1918.
- O espaço de endereço da VNet não deve sobrepor os intervalos de rede local.
- A NAT (conversão de endereços de rede) não deve ser usada.
- A sobreposição de endereços pode fazer com que as redes não possam ser conectadas e o roteamento não funcione corretamente. Se as redes forem sobrepostas, você precisará reprojetar a rede ou usar a NAT (conversão de endereços de rede).

**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) das VNets do Azure.
- [Leia](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq) as perguntas frequentes sobre a rede.
- [Saiba mais sobre](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) as limitações de rede.


## <a name="best-practice-implement-a-hub-spoke-network-topology"></a>Melhor prática: implementar uma topologia de rede hub-spoke

Uma topologia de rede hub-spoke isola as cargas de trabalho enquanto compartilha os serviços, como a identidade e a segurança.
- O hub é uma VNet do Azure que atua como um ponto central de conectividade.
- Os spokes são VNets que se conectam à VNet do hub usando o Emparelhamento VNET.
- Os serviços compartilhados são implantados no hub, enquanto as cargas de trabalho individuais são implantadas como spokes. 

Considere o seguinte:
- A implementação de uma topologia hub e spoke no Azure centraliza os serviços comuns, como conexões com redes locais, firewalls e isolamento entre VNets. A VNet de hub fornece um ponto central de conectividade para redes locais e um local para hospedar o uso dos serviços por cargas de trabalho hospedadas em VNets spoke.
- Normalmente, uma configuração de hub e spoke é usada por empresas de grande porte. Redes menores podem considerar um design mais simples para economizar nos custos e na complexidade.
- As VNets de spoke podem ser usadas para isolar as cargas de trabalho, com cada spoke gerenciado separadamente de outros spokes. Cada carga de trabalho pode incluir várias camadas e várias sub-redes conectadas por meio de balanceadores de carga do Azure.
- As VNets de hub e spoke podem ser implementadas em diferentes grupos de recursos e até mesmo em diferentes assinaturas. Ao usar redes virtuais pares em diferentes assinaturas, as assinaturas podem ser associadas ao mesmo locatário ou a um locatário diferente do AD (Azure Active Directory). Isso possibilita o gerenciamento descentralizado de cada carga de trabalho, compartilhando os serviços mantidos na rede do hub.

![Gerenciamento de alterações](./media/migrate-best-practices-networking/hub-spoke.png)
*Topologia hub e spoke*

**Saiba mais:**

- [Leia sobre](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) uma topologia hub e spoke.
- Obtenha recomendações de rede para a execução de VMs [Windows](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm) e [Linux](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/linux-vm) do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) o Emparelhamento VNET.


## <a name="best-practice-design-subnets"></a>Melhor prática: design de sub-redes

Para fornecer isolamento em uma VNet, segmente a VNet em uma ou mais sub-redes e aloque uma parte do espaço de endereço da VNet a cada sub-rede.
- Você pode criar várias sub-redes em cada VNet.
- Por padrão, o Azure roteia o tráfego entre todas as sub-redes em uma VNet.
- Suas decisões de sub-rede são baseadas em seus requisitos técnicos e organizacionais.  
- Você cria sub-redes usando a notação CIDR.
- Ao decidir sobre o intervalo de rede para sub-redes, é importante observar que o Azure mantém cinco endereços IP de cada sub-rede que não pode ser usada. Por exemplo, se você criar a menor sub-rede disponível de /29 (com oito endereços IP), o Azure manterá cinco endereços, portanto, você terá apenas três endereços utilizáveis que podem ser atribuídos aos hosts na sub-rede.
- Na maioria dos casos, é recomendável usar /28 como a menor sub-rede.

### <a name="example"></a>Exemplo

A tabela mostra um exemplo de uma VNet com um espaço de endereço de 10.245.16.0/20 segmentada em sub-redes, para uma migração planejada.

**Sub-rede** | **CIDR** | **Endereços** | **Uso**
--- | --- | --- | ---
DEV-FE-EUS2 | 10.245.16.0/22 | 1019 | VMs de camada de front-end/Web
DEV-APP-EUS2 | 10.245.20.0/22 | 1019 | Máquinas virtuais de camada de aplicativo
DEV-DB-EUS2 | 10.245.24.0/23 | 507 | VMs de banco de dados

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#segmentation) o design de sub-redes.
- [Saiba como](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure) uma empresa fictícia (Contoso) preparou sua infraestrutura de rede para migração.


## <a name="best-practice-set-up-a-dns-server"></a>Melhor prática: configurar um servidor DNS

O Azure adiciona um servidor DNS por padrão quando você implanta uma VNet. Isso permite que você rapidamente crie VNets e implante recursos. No entanto, esse servidor DNS fornece serviços apenas aos recursos na VNet. Se você quiser conectar várias VNets em conjunto ou se conectar a um servidor local das VNets, serão necessárias funcionalidades adicionais de resolução de nome. Por exemplo, pode ser necessário usar o Active Directory para resolver os nomes de DNS entre as redes virtuais. Para fazer isso, você deve implantar seu próprio servidor DNS personalizado no Azure.

- Os servidores DNS em uma VNet podem encaminhar consultas DNS para os resolvedores recursivos no Azure. Isso permite que você resolva os nomes de host dentro dessa VNet. Por exemplo, um controlador de domínio em execução no Azure pode responder a consultas DNS de seus domínios e encaminhar todas as outras consultas ao Azure.
- O encaminhamento de DNS permite que as VMs vejam tanto seus recursos locais (pelo controlador de domínio) quanto nomes de host fornecidos pelo Azure (pelo encaminhador). O acesso a resolvedores recursivos no Azure é fornecido usando o endereço IP virtual 168.63.129.16.
- O encaminhamento do DNS também habilita a resolução do DNS entre VNets e permite que os computadores locais resolvam nomes de host fornecidos pelo Azure.
    - Para resolver um nome de host da VM, a VM do servidor DNS deve residir na mesma VNet e ser configurada para encaminhar consultas de nome de host ao Azure.
    - Como o sufixo DNS é diferente em cada VNet, você pode usar regras de encaminhamento condicionais para enviar consultas DNS a fim de serem resolvidas pela VNet correta.
- Ao usar seus próprios servidores DNS, você pode especificar vários servidores DNS para cada VNet. Você também pode especificar diversos servidores DNS por adaptador de rede (para o Azure Resource Manager) ou por um serviço de nuvem (para o modelo de implantação clássico).
- Os servidores DNS especificados para um adaptador de rede ou serviço de nuvem têm precedência sobre aqueles especificados para a VNet.
- No modelo de implantação do Azure Resource Manager, você pode especificar servidores DNS para uma VNet e um adaptador de rede, mas a melhor prática é usar a configuração somente em VNets.

    ![Servidores DNS](./media/migrate-best-practices-networking/dns2.png) *Servidores DNS para VNet*

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure) a resolução de nome ao usar seu próprio servidor DNS.
- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-subscriptions) as regras e as restrições de nomenclatura de DNS.


## <a name="best-practice-set-up-availability-zones"></a>Melhor prática: Configurar zonas de disponibilidade

As zonas de disponibilidade aumentam a alta disponibilidade para proteger os aplicativos e os dados contra falhas no datacenter.

- As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure.
- Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes.
- Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas.
- A separação física das zonas de disponibilidade dentro de uma região protege os aplicativos e os dados contra falhas do datacenter.
- Os serviços com redundância de zona replicam os aplicativos e os dados entre zonas de disponibilidade para proteger dos pontos únicos de falha. Com as zonas de disponibilidade, o Azure oferece um SLA de tempo de atividade da VM de 99,99%.

    ![Zona de disponibilidade](./media/migrate-best-practices-networking/availability-zone.png) *Zona de disponibilidade*

- É possível planejar e criar alta disponibilidade na arquitetura de migração colocalizando os recursos de computação, armazenamento, rede e dados em uma zona e replicando em outras zonas. Os serviços do Azure que dão suporte a zonas de disponibilidade enquadram-se em duas categorias:
    - Serviços de zonas: Você associa um recurso a uma zona específica. Por exemplo, VMs, discos gerenciados, endereços IP.
    - Serviços com redundância de zona: O recurso replica automaticamente entre zonas. Por exemplo, armazenamento com redundância de zona, Banco de Dados SQL do Azure.
- Você pode implantar uma carga do Azure padrão balanceada com cargas de trabalho voltadas para a Internet ou camadas de aplicativo, para fornecer tolerância a falhas de zona.

    ![Balanceador de carga](./media/migrate-best-practices-networking/load-balancer.png) *Balanceador de carga*

**Saiba mais:**
-   [Obtenha uma visão geral](https://docs.microsoft.com/azure/availability-zones/az-overview) das zonas de disponibilidade.



## <a name="design-hybrid-cloud-networking"></a>Design da rede de nuvem híbrida

Para uma migração bem-sucedida, é essencial conectar redes corporativas locais ao Azure. Isso cria uma conexão sempre ativa conhecida como uma rede de nuvem híbrida, na qual os serviços são fornecidos da nuvem do Azure para os usuários corporativos. Há duas opções para criar esse tipo de rede:

- **VPN site a site:** você estabelece uma conexão site a site entre o dispositivo VPN local compatível e um gateway de VPN do Azure implantado em uma VNet. Qualquer recurso local autorizado pode acessar VNets. As comunicações site a site são enviadas por meio de um túnel criptografado pela Internet. 
- **Azure ExpressRoute:** você estabelece uma conexão do Azure ExpressRoute entre sua rede local e o Azure, por meio de um parceiro de ExpressRoute. Essa conexão é privada e o tráfego não passa pela Internet.

**Saiba mais:**

- [Saiba mais](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vpn) sobre a rede de nuvem híbrida.

## <a name="best-practice-implement-a-highly-available-site-to-site-vpn"></a>Melhor prática: implementar uma VPN site a site altamente disponível

Para implementar uma VPN site a site, configure um gateway de VPN no Azure.
- Um gateway de VPN é um tipo específico de gateway de VNet que é usado para enviar tráfego criptografado entre uma VNet do Azure e uma localização local na Internet pública.
- Você também pode usar um gateway de VPN para enviar tráfego criptografado entre as VNets do Azure pela rede da Microsoft.
- Cada VNet pode ter apenas um gateway de VPN.
- Você pode criar várias conexões com o mesmo gateway de VPN. Quando você cria várias conexões, todos os túneis de VPN compartilham a largura de banda de gateway disponível.
- Cada gateway de VPN do Azure consiste em duas instâncias em uma configuração ativa e em espera.
    - Para manutenção planejada ou interrupção não planejada na instância ativa, o failover ocorre e a instância em espera assume automaticamente e retoma as conexões site a site ou VNet a VNet. 
    - A mudança causa uma breve interrupção.
    - Para uma manutenção planejada, a conectividade deve ser restaurada dentro de 10 a 15 segundos.
    - Para os problemas não planejados, a recuperação da conexão será mais longa, aproximadamente de 1 a 1,5 minuto.
    - As conexões do cliente VPN P2S (ponto a site) com o gateway serão desconectadas e os usuários precisarão reconectar pelos computadores clientes.

Ao configurar uma VPN site a site, faça o seguinte:
 - Você precisa de uma VNet cujo intervalo de endereços não se sobreponha à rede local ao qual a VPN se conectará.
 - Você cria uma sub-rede de gateway na rede.
 - Você cria um gateway de VPN, especifica o tipo de gateway (VPN) e se o gateway é baseado em políticas ou baseado em rota. Uma VPN RouteBased é recomendada porque é mais apta e à prova de obsolescência.
 - Você cria um gateway de rede local e configura seu dispositivo VPN local. 
 - Você cria uma conexão de VPN site a site de failover entre o gateway da VNet e o dispositivo local. Usar VPN baseada em rota permite conexões ativo-passivo ou ativo-ativo ao Azure. Ser baseado em rota também dá suporte a conexões de ponto a site (de um único computador) e de site a site (de qualquer computador) simultaneamente.
 - Você especifica o SKU de gateway que deseja usar. Isso dependerá de seus requisitos de carga de trabalho, taxas de transferência, recursos e SLAs.
 - BGP (Border Gateway Protocol) é um recurso opcional que você pode usar com o Azure ExpressRoute e gateways de VPN baseados em rota para propagar as rotas BGP locais às suas VNets.

![VPN](./media/migrate-best-practices-networking/vpn.png)
*VPN site a site*
 
**Saiba mais:**

- [Examine](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) os dispositivos VPN locais compatíveis.
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) dos gateways de VPN.
- [Saiba mais sobre](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable) as conexões de VPN de alta disponibilidade.
- [Saiba mais sobre](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) o planejamento e o design de um gateway de VPN.
- [Examine](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) as configurações do gateway de VPN.
- [Examine](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) os SKUs de gateway.
- [Leia sobre](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview) como configurar o BGP com gateways de VPN do Azure.


### <a name="best-practice-configure-a-gateway-for-vpn-gateways"></a>Melhor prática: configurar um gateway para gateways de VPN

Ao criar um gateway de VPN no Azure, você deve usar uma sub-rede especial chamada GatewaySubnet. Ao criar essa sub-rede, observe essas melhores práticas:

- O comprimento do prefixo da sub-rede de gateway pode ter um comprimento de prefixo máximo de 29 (por exemplo, 10.119.255.248/29). A recomendação atual é que você use um comprimento de prefixo de 27 (por exemplo, 10.119.255.224/27).
- Ao definir o espaço de endereço da sub-rede de gateway, use a última parte do espaço de endereço da VNet.
- Ao usar o Azure GatewaySubnet, nunca implante todas as VMs ou outros dispositivos, como o Gateway de Aplicativo para a sub-rede de gateway.
- Não atribua um NSG (Grupo de Segurança de Rede) a essa sub-rede. Isso fará com que o gateway pare de funcionar.

**Saiba mais:**
- [Use essa ferramenta](https://gallery.technet.microsoft.com/scriptcenter/Address-prefix-calculator-a94b6eed) para determinar o espaço de endereços IP.

## <a name="best-practice-implement-azure-virtual-wan-for-branch-offices"></a>Melhor prática: implementar a WAN Virtual do Azure em filiais

Para várias conexões VPN, a WAN Virtual do Azure é um serviço de rede que fornece conectividade otimizada e automatizada entre branches por meio do Azure.
- A WAN Virtual permite que você conecte e configure dispositivos de branch para se comunicar com o Azure. Isso pode ser feito manualmente ou usando dispositivos de provedor preferencial por meio de um parceiro de WAN Virtual.
- O uso de dispositivos de provedor preferencial simplifica o uso, a conectividade e o gerenciamento de configuração.
- O painel interno de WAN do Azure fornece informações instantâneas de solução de problemas que podem ajudar você a economizar tempo e proporcionam uma maneira fácil de acompanhar a conectividade site a site em larga escala. 

**Saiba mais:**
[Saiba mais sobre](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about) a WAN Virtual do Azure.

### <a name="best-practice-implement-expressroute-for-mission-critical-connections"></a>Melhor prática: implementar o ExpressRoute para conexões críticas

O serviço Azure ExpressRoute permite que você estenda sua infraestrutura local na nuvem da Microsoft com a criação de conexões privadas entre o datacenter virtual do Azure e as redes locais.
- As conexões do ExpressRoute podem ser em uma rede qualquer (VPN de IP), uma rede Ethernet ponto a ponto ou por meio de um provedor de conectividade. Elas não passam pela Internet pública.
- As conexões do ExpressRoute oferecem maior segurança, confiabilidade e velocidades mais altas (de até 10 Gbps), além de latência consistente.
- O ExpressRoute é muito útil para os datacenters virtuais, uma vez que os clientes podem aproveitar os benefícios das regras de conformidade associadas às conexões privadas.
- Com o ExpressRoute Direct, é possível se conectar diretamente aos roteadores da Microsoft em 100 Gbps, para maiores necessidades de largura de banda.
- O ExpressRoute usa o BGP para trocar rotas entre redes locais, instâncias do Azure e endereços públicos da Microsoft.

Implantar conexões do ExpressRoute envolve normalmente a interação com um provedor de serviços do ExpressRoute. Para início rápido, é comum usar inicialmente o VPN site a site para estabelecer a conectividade entre o datacenter virtual e os recursos locais, então migrar para a conexão do ExpressRoute quando a interconexão física com o provedor de serviços estiver estabelecida.

**Saiba mais:**
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) do ExpressRoute.
- [Saiba mais sobre](https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about) o ExpressRoute Direct.

### <a name="best-practice-optimize-expressroute-routing-with-bgp-communities"></a>Melhor prática: otimizar o roteamento do ExpressRoute com as comunidades de BGP

Quando você tem vários circuitos de ExpressRoute, tem mais de um caminho para se conectar à Microsoft. Como resultado, pode ocorrer um roteamento abaixo do ideal e o tráfego pode levar um caminho mais longo para acessar a Microsoft e esta para acessar a sua rede. Quanto maior o caminho de rede, maior será a latência. A latência tem impacto direto na experiência de usuário e no desempenho do aplicativo.

#### <a name="example"></a>Exemplo

Vejamos um exemplo.

- Você tem dois escritórios nos EUA, um em Los Angeles e um em Nova York.
- Seus escritórios estão conectados em uma WAN, que pode ser sua própria rede backbone ou VPN de IP do provedor de serviços.
- Você tem dois circuitos de ExpressRoute, um no Oeste dos EUA e outro no Leste dos EUA, que também estão conectados à WAN. Obviamente, você tem dois caminhos para se conectar à rede da Microsoft.


 
**Problema** Agora imagine que você tem a implantação do Azure (por exemplo, o Serviço de Aplicativo do Azure) no Oeste dos EUA e no Leste dos EUA.
- Você deseja que os usuários em cada escritório acessem seus serviços do Azure mais próximos para uma experiência ideal.
- Assim, você deseja conectar usuários em Los Angeles ao Azure do Oeste dos EUA e usuários em Nova York ao Leste dos EUA.
- Isso funciona para usuários da Costa Leste, mas não para o oeste. O problema é o seguinte:
    - Em cada circuito do ExpressRoute, anunciamos tanto o prefixo do Azure no Leste dos EUA (23.100.0.0/16) quanto o prefixo do Azure no Oeste dos EUA (13.100.0.0/16).
    - Sem saber qual prefixo é de qual região, os prefixos não são tratados de maneira diferente.
    - Sua rede WAN pode assumir que ambos os prefixos são mais próximos do Leste dos EUA do que do Oeste dos EUA e, portanto, roteia os usuários de ambos os escritórios para o circuito do ExpressRoute no Leste dos EUA, fornecendo uma experiência abaixo do ideal aos usuários no escritório de Los Angeles.

![VPN](./media/migrate-best-practices-networking/bgp1.png)
*Conexão não otimizada de comunidades de BGP*

**Solução**

Para otimizar o roteamento dos usuários dos escritórios, você precisa saber qual é o prefixo do Azure no Oeste dos EUA e no Leste dos EUA. Você pode codificar essas informações usando valores da comunidade BGP.
- Você atribui um valor de comunidade BGP exclusivo para cada região do Azure. Por exemplo, 12076:51004 para o Leste dos EUA, 12076:51006 para o Oeste dos EUA.
- Agora que está claro qual prefixo pertence a qual região do Azure, você pode configurar um circuito de ExpressRoute preferencial.
- Como você está usando o BGP para trocar informações de roteamento, você pode usar a preferência de local do BGP para influenciar o roteamento.
- Em nosso exemplo, você atribui um valor maior de preferência de local a 13.100.0.0/16 no Oeste dos EUA que no Leste dos EUA e, da mesma forma, um valor mais alto de preferência de local a 23.100.0.0/16 no Leste dos EUA que no Oeste dos EUA. 
- Essa configuração garante que quando os caminhos para a Microsoft estiverem disponíveis, os usuários em Los Angeles se conectarão ao Azure no Oeste dos EUA usando o circuito oeste e os usuários de Nova York se conectarão ao Azure do Leste dos EUA usando o circuito leste. O roteamento é otimizado em ambos os lados.

![VPN](./media/migrate-best-practices-networking/bgp2.png)
*Conexão otimizada de comunidades de BGP*


**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing) otimizar o roteamento

## <a name="securing-vnets"></a>Proteger VNets

A responsabilidade de proteger VNets é compartilhada entre você e a Microsoft. A Microsoft fornece muitos recursos de rede, bem como serviços que ajudam a proteger recursos. Ao planejar a segurança de VNets, há um número de melhores práticas que você deve seguir, incluindo a implementação de uma rede de perímetro, usando grupos de segurança e filtragem, protegendo o acesso aos recursos e endereços IP e implementando a proteção contra ataques.

**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices) das melhores práticas para segurança de rede.
- [Saiba como](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#security) criar redes seguras.

## <a name="best-practice-implement-an-azure-perimeter-network"></a>Melhor prática: implementar uma rede de perímetro do Azure

Embora a Microsoft faça investimentos consideráveis para proteger a infraestrutura de nuvem, você também deve proteger os serviços de nuvem e os grupos de recursos. Uma abordagem multicamada para a segurança oferece a melhor defesa. Colocar uma rede de perímetro em vigor é uma parte importante dessa estratégia de defesa.

- Uma rede de perímetro protege recursos da rede interna de uma rede não confiável. 
- É a camada mais externa que é exposta à Internet. Ela geralmente se encontra entre a Internet e a infraestrutura empresarial, geralmente com alguma forma de proteção em ambos os lados. 
- Em uma topologia de rede empresarial típica, a infraestrutura principal é reforçada intensamente nos perímetros, com várias camadas de dispositivos de segurança. O limite de cada camada consiste em dispositivos e pontos de imposição de políticas.
- Cada camada pode incluir uma combinação de soluções de segurança de rede que incluem firewalls, prevenção de DoS (ataque de negação de serviço), IDS/IPS (sistemas de detecção de intrusão/sistemas de proteção) e dispositivos VPN.
- A imposição de políticas na rede de perímetro pode usar políticas de firewall, ACLs (listas de controle de acesso) ou roteamento específico.
- Assim que o tráfego de entrada chega da Internet, ele é interceptado e tratado por uma combinação de solução de defesa para bloquear ataques e tráfego perigoso, permitindo solicitações legítimas na rede.
- O tráfego de entrada pode ser roteado diretamente aos recursos na rede de perímetro. O recurso de rede de perímetro, em seguida, pode se comunicar com outros recursos mais dentro da rede, movendo o tráfego na rede após a validação.

A figura a seguir mostra um exemplo de uma rede de perímetro de sub-rede individual em uma rede corporativa, com dois limites de segurança.

![VPN](./media/migrate-best-practices-networking/perimeter.png)
*Implantação de rede de perímetro*

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) a implantação de uma rede de perímetro entre o Azure e seu datacenter local.


## <a name="best-practice-filter-vnet-traffic-with-nsgs"></a>Melhor prática: filtrar o tráfego de VNet com NSGs

NSGs (Grupos de Segurança de Rede) contêm várias regras de segurança de entrada e saída que filtram o tráfego direcionado para e de recursos. A filtragem pode ser pelo endereço IP de destino e origem, pela porta e pelo protocolo. 
- Os NSGs contêm regras de segurança que permitem ou negam o tráfego de rede de entrada (ou de saída) em relação a vários tipos de recursos do Azure. Para cada regra, você pode especificar origem e destino, porta e protocolo.
- Regras de NSG são avaliadas por prioridade usando as informações de cinco tuplas (origem, porta de origem, destino, porta de destino e protocolo) para permitir ou negar o tráfego.
- Um registro de fluxo é criado para as conexões existentes. A comunicação é permitida ou negada com base no estado de conexão do registro de fluxo.
- Um registro de fluxo permite que um NSG esteja com estado. Por exemplo, se você especificar uma regra de segurança de saída para algum endereço pela porta 80, não será necessária uma regra de segurança de entrada para o tráfego de saída. Você precisa especificar uma regra de segurança de entrada se a comunicação for iniciada externamente.
- O oposto também é verdadeiro. Se o tráfego de entrada é permitido por uma porta, não é necessário especificar uma regra de segurança de saída para responder ao tráfego pela porta.
- As conexões existentes não são interrompidas quando você remove uma regra de segurança que habilitou o fluxo. Os fluxos de tráfego são interrompidos quando as conexões são interrompidas e nenhum tráfego está fluindo em qualquer direção por pelo menos alguns minutos.
- Ao criar NSGs, crie poucas possível, mas quantas forem necessárias.

### <a name="best-practice-secure-northsouth-and-eastwest-traffic"></a>Melhor prática: proteger o tráfego do norte/sul e leste/oeste

Ao proteger VNets, é importante considerar os vetores de ataque.
- Usar somente NSGs de sub-rede simplifica seu ambiente, mas protege apenas o tráfego em sua sub-rede. Isso é conhecido como o tráfego do norte/sul.
- O tráfego entre VMs na mesma sub-rede é conhecido como tráfego leste/oeste.
- É importante aproveitar as duas formas de proteção, pois, se um hacker obtiver acesso de fora, eles serão interrompidos durante a tentativa de atacar computadores na mesma sub-rede.

### <a name="use-service-tags-on-nsgs"></a>Usar marcas de serviço em NSGs

Uma marca de serviço representa um grupo de prefixos de endereço IP. Usar uma marca de serviço ajuda a minimizar a complexidade ao criar regras de NSG.
- Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras.
- A Microsoft gerencia os prefixos de endereço associados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.
- Você não pode criar sua própria marca de serviço ou especificar quais endereços IP estão incluídos em uma marca.

Marcas de serviço transferem o trabalho manual de atribuição de uma regra aos grupos de serviços do Azure. Por exemplo, se você quiser permitir que uma sub-rede de VNet que contém servidores Web acesse um Banco de Dados SQL do Azure, você poderá criar uma regra de saída para a porta 1433 e usar a marca de serviço **Sql**.
- Essa marca **Sql** indica os prefixos de endereço dos serviços do Banco de Dados SQL do Azure e do SQL Data Warehouse do Azure.
- Se você especificar **Sql** como valor, o tráfego será permitido ou negado para o Sql.
- Se você só deseja permitir o acesso ao **Sql** em uma região específica, é possível especificar a região. Por exemplo, se você quiser permitir o acesso somente para o Banco de Dados SQL do Azure na região Leste dos EUA, poderá especificar **Sql.EastUS** como uma marca de serviço.
- A marca representa o serviço, mas não as instâncias específicas do serviço. Por exemplo, a marca representa o serviço do Banco de Dados SQL do Azure, mas não um banco de dados ou servidor SQL específico.
- Todos os prefixos de endereço representados por essa marca também são representados pela marca da **Internet**.


**Saiba mais:**

- [Leia mais sobre](https://docs.microsoft.com/azure/virtual-network/security-overview) os NSGs.
- [Examine](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) as marcas de serviço disponíveis para NSGs.


## <a name="best-practice-use-application-security-groups"></a>Melhor prática: usar grupos de segurança do aplicativo

Grupos de segurança do aplicativo habilitam você a configurar a segurança de rede como uma extensão natural de uma estrutura de aplicativo.

- Você pode agrupar VMs e definir políticas de segurança de rede com base em grupos de segurança do aplicativo.
- Grupos de segurança do aplicativo habilitam você a reutilizar a política de segurança em escala, sem manutenção manual de endereços IP explícitos.
- Os grupos de segurança do aplicativo lidam com a complexidade de endereços IP explícitos e vários conjuntos de regras, permitindo que você se concentre na sua lógica de negócios. 

### <a name="example"></a>Exemplo

![Grupo de segurança do aplicativo](./media/migrate-best-practices-networking/asg.png)
*Exemplo de grupo de segurança do aplicativo*

**Interface de rede** | **Grupo de segurança do aplicativo**
--- | ---
NIC1 | AsgWeb
NIC2 | AsgWeb
NIC3 | AsgLogic
NIC4 | AsgDb 

- Em nosso exemplo, cada adaptador de rede pertence a apenas um grupo de segurança de aplicativo, mas, na verdade, uma interface pode pertencer a vários grupos, de acordo com os limites do Azure.
- Nenhum dos adaptadores de rede tem um NSG associado. O NSG1 está associado a ambas as sub-redes e contém as seguintes regras.

    **Nome da regra** | **Finalidade** | **Detalhes**
    --- | --- | ---   
    Allow-HTTP-Inbound-Internet | Permitir o tráfego da Internet para os servidores Web. O tráfego de entrada da Internet é negado pela regra de segurança padrão DenyAllInbound, então nenhuma regra adicional é necessária para os grupos de segurança do aplicativo AsgLogic ou AsgDb. | Prioridade: 100<br/><br/> Fonte: Internet<br/><br/> Porta de origem: *<br/><br/> Destino: AsgWeb<br/><br/> Porta de destino: 80<br/><br/> Protocolo: TCP<br/><br/> Acesso: permitir.
    Deny-Database-All | A regra de segurança padrão AllowVNetInBound permite toda a comunicação entre recursos na mesma VNet. Essa regra é necessária para negar o tráfego de todos os recursos. | Prioridade: 120<br/><br/> Fonte: *<br/><br/> Porta de origem: *<br/><br/> Destino: AsgDb<br/><br/> Porta de destino: 1433<br/><br/> Protocolo: Todos<br/><br/> Acesso: negar.
    Allow-Database-BusinessLogic | Permitir o tráfego do grupo de segurança do aplicativo AsgLogic para o grupo de segurança do aplicativo AsgDb. A prioridade para essa regra é mais alta do que a da regra Deny-Database-All e será processada antes da regra, então o tráfego do grupo de segurança do aplicativo AsgLogic é permitido enquanto todos os outros tráfegos são bloqueados. | Prioridade: 110<br/><br/> Origem: AsgLogic<br/><br/> Porta de origem: *<br/><br/> Destino: AsgDb<br/><br/> Porta de destino: 1433<br/><br/> Protocolo: TCP<br/><br/> Acesso: permitir.

- As regras que especificam um grupo de segurança de aplicativo como origem ou destino são aplicadas apenas aos adaptadores de rede que são membros do grupo de segurança de aplicativo. Se o adaptador de rede não for membro de um grupo de segurança de aplicativo, a regra não será aplicada ao adaptador de rede, mesmo que o grupo de segurança de rede esteja associado à sub-rede.

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) grupos de segurança do aplicativo.


### <a name="best-practice-secure-access-to-paas-using-vnet-service-endpoints"></a>Melhor prática: acesso seguro a PaaS usando os pontos de extremidade de serviço de rede virtual da VNet

Os pontos de extremidade de serviço da VNet estendem o espaço de endereço IP da VNet e a identidade para serviços do Azure por meio de uma conexão direta.

- Os pontos de extremidade permitem que você possa proteger recursos críticos do serviço do Azure somente para suas VNets. O tráfego de sua rede virtual para o serviço do Azure sempre permanece na rede de backbone do Microsoft Azure.
- O espaço de endereço privado da VNet pode estar sobreposto e, portanto, não pode ser usado para identificar de modo exclusivo o tráfego originado de uma VNet.
- Depois que os pontos de extremidade de serviço estão habilitados na sua VNet, você pode proteger os recursos de serviço do Azure adicionando uma regra de VNet aos recursos de serviço. Isso proporciona melhor segurança, removendo totalmente o acesso público à Internet dos recursos e permitindo o tráfego somente da sua VNet.

![Pontos de extremidade de serviço](./media/migrate-best-practices-networking/endpoint.png)
*Pontos de extremidade de serviço*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) pontos de extremidade de serviço da VNet.


## <a name="best-practice-control-public-ip-addresses"></a>Melhor prática: controlar endereços IP públicos

Os endereços IP públicos no Azure podem ser associados a VMs, balanceadores de carga, gateways de aplicativo e gateways de VPN.

- Os endereços IP públicos permitem que os recursos da Internet se comuniquem internamente com os recursos do Azure e que os recursos do Azure se comuniquem externamente com a Internet.
- Os endereços IP públicos são criados com um SKU básico ou padrão, com algumas diferenças entre eles. SKUs padrão podem ser atribuídos a qualquer serviço, mas são geralmente configurado nas VMs, balanceadores de carga e gateways de aplicativo.
- É importante observar que um endereço IP público básico não tem um NSG configurado automaticamente. Você precisa configurar o seu e atribuir regras para controlar o acesso. Endereços IP de SKU padrão têm um NSG e regras atribuídas por padrão.
- Como melhor prática, as VMs não devem ser configuradas com um endereço IP público.
    - Se você precisar de uma porta aberta, ela deverá ser apenas para serviços Web, como porta 80 ou 443.
    - Portas de gerenciamento remoto padrão como o SSH (22) e o RDP (3389) devem ser definidas para negar, juntamente com todas as outras portas, usando NSGs.
- Uma melhor prática é colocar as VMs atrás de um gateway de aplicativo ou do Azure Load Balancer. Em seguida, se for necessário acesso às portas de gerenciamento remoto, você poderá usar o acesso de VM Just-In-Time na Central de Segurança do Azure.

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) endereços IP públicos no Azure.
- [Leia mais](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) sobre o acesso de VM Just-In-Time na Central de Segurança do Azure.


## <a name="leverage-azure-security-features-for-networking"></a>Aproveite os recursos de segurança do Azure para a rede

O Azure tem recursos de segurança de plataforma que são fáceis de usar e fornecem contramedidas avançadas para ataques de rede comuns. Isso inclui o Firewall do Azure, o firewall do aplicativo Web e o Observador de Rede.

## <a name="best-practice-deploy-azure-firewall"></a>Melhor prática: implantar o Firewall do Azure

O Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos da VNet. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita.

![Pontos de extremidade de serviço](./media/migrate-best-practices-networking/firewall.png)
*Firewall do Azure*

- No Firewall do Azure, é possível criar, impor e registrar centralmente políticas de conectividade de rede e de aplicativo em assinaturas e VNets.
- O Firewall do Azure usa um endereço IP público estático para seus recursos de VNet, permitindo que firewalls externos identifiquem o tráfego originário de sua VNet.
- O Firewall do Azure é totalmente integrado ao Azure Monitor para registro em log e análise.
- Como melhor prática ao criar regras de Firewall do Azure, use as marcas FQDN para criar regras.
    - Uma marca FQDN representa um grupo de FQDNs associados aos serviços Microsoft conhecidos.
    - Você pode usar uma marca FQDN para permitir o tráfego de rede de saída necessário pelo firewall.
- Por exemplo, para permitir manualmente a passagem do tráfego de rede do Windows Update pelo seu firewall, você precisará criar várias regras de aplicativo. Usando as marcas FQDN, você cria uma regra de aplicativo e inclui a marca do Windows Update. Com essa regra em vigor, o tráfego de rede para pontos de extremidade do Microsoft Windows Update pode fluir pelo firewall.

**Saiba mais:**

- [Obtenha uma visão gera](https://docs.microsoft.com/azure/firewall/overview) do Firewall do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/firewall/fqdn-tags) as marcas FQDN.


## <a name="best-practice-deploy-azure-web-application-firewall-waf"></a>Melhor prática: Implantar o WAF (Firewall do Aplicativo Web) do Azure

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. As explorações incluem ataques de injeção de SQL e ataques de scripts entre sites. Pode ser difícil impedir esses ataques no código do aplicativo e pode exigir manutenção rigorosa, aplicação de patches e monitoramento em várias camadas da topologia do aplicativo. Um firewall do aplicativo Web centralizado ajuda a simplificar bastante o gerenciamento de segurança e ajuda os administradores do aplicativo contra ameaças ou invasões. Um firewall do aplicativo Web pode reagir a uma ameaça de segurança mais rapidamente, corrigindo uma vulnerabilidade conhecida em uma localização central, em vez de proteger cada um dos aplicativos Web individuais. Os gateways de aplicativos existentes podem ser facilmente convertidos em um gateway de aplicativo com firewall de aplicativo Web.

O WAF (firewall do aplicativo Web) do Azure é um recurso do gateway de aplicativo do Azure.
- O WAF fornece proteção centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns.
- O WAF protege sem modificação do código de back-end.
- Ele pode proteger vários aplicativos Web ao mesmo tempo por trás de um gateway de aplicativo
- O WAF é integrado à Central de Segurança do Azure.
- Você pode personalizar as regras WAF e grupos de regras para atender aos seus requisitos de aplicativo.
- Como melhor prática, você deve usar um WAF na frente em qualquer aplicativo voltado para a Web, incluindo os aplicativos em VMs do Azure ou como um Serviço de Aplicativo do Azure.

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/application-gateway/waf-overview) o WAF.
- [Examine](https://docs.microsoft.com/azure/application-gateway/application-gateway-waf-configuration) as limitações e as exclusões do WAF.


## <a name="best-practice-implement-azure-network-watcher"></a>Melhor prática: implementar o Observador de Rede do Azure

O Observador de Rede do Azure fornece ferramentas para monitorar recursos e comunicações em uma VNet do Azure. Por exemplo, você pode monitorar as comunicações entre uma VM e um ponto de extremidade, como outra VM ou FQDN, exibir recursos e as relações de recursos em uma VNet ou diagnosticar problemas de tráfego de rede.

![Observador de Rede](./media/migrate-best-practices-networking/network-watcher.png)
*Observador de Rede*

- Com o Observador de Rede, monitore e realize o diagnóstico de problemas de rede sem fazer logon em suas VMs.
- É possível disparar a captura de pacote por meio da configuração de alertas e obter acesso a informações de desempenho em tempo real no nível de pacote. Ao ver um problema, você poderá investigar os detalhes.
- Como melhor prática, você deve usar o Observador de Rede para examinar os logs de fluxo de NSG.
    - Os logs de fluxo de NSG no Observador de Rede permitem exibir informações sobre o tráfego IP de entrada e saída por meio de um NSG.
    - Os logs de fluxo são gravados no formato JSON.
    - Os logs de fluxo mostram os fluxos de entrada e de saída por regra, a NIC (adaptador de rede) à qual o fluxo se aplica, as informações de cinco tuplas sobre o fluxo (IP de origem/destino, porta de origem/destino e protocolo) e se o tráfego foi permitido ou negado.

**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/network-watcher) do Observador de Rede.
- [Saiba mais](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) sobre o os logs de fluxo de NSG.

## <a name="use-partner-tools-in-the-azure-marketplace"></a>Usar ferramentas de parceiro no Azure Marketplace

Para topologias de rede mais complexas, você pode usar os produtos de segurança de parceiros da Microsoft, particularmente em NVAs (soluções de virtualização de rede).

- Uma NVA é uma VM que executa uma função de rede, como um firewall, otimização WAN ou outra função de rede.
- NVAs reforçam as funções de rede e segurança de VNet. Elas podem ser implantadas para firewalls altamente disponíveis, prevenção contra intrusões, detecção de intrusão, WAFs (firewalls do aplicativo Web), otimização de WAN, roteamento, balanceamento de carga, VPN, gerenciamento de certificados, Active Directory e autenticação multifator.
- A NVA está disponível em diversos fornecedores no [Azure Marketplace](https://azuremarketplace.microsoft.com/). 
 

## <a name="best-practice-implement-firewalls-and-nvas-in-hub-networks"></a>Melhor prática: implementar firewalls e NVAs em redes de hub

No hub, a rede de perímetro (com acesso à Internet) normalmente é gerenciada por meio de um Firewall do Azure, um farm de firewalls ou WAFs (firewalls do aplicativo Web). Considere as comparações a seguir.

**Tipo de firewall** | **Detalhes**
--- | ---
WAFs | Aplicativos Web são comuns e tendem a sofrer de diversas vulnerabilidades e explorações em potencial.<br/><br/> WAFs são projetados para detectar ataques contra aplicativos Web (HTTP/HTTPS), mais especificamente que um firewall genérico.<br/><br/> Em comparação à tecnologia de firewall tradicional, WAFs têm um conjunto de recursos específicos para proteger os servidores Web internos contra ameaças.
Firewall do Azure | Como em farms de firewall da NVA, o Firewall do Azure usam um mecanismo de administração comum, com um conjunto de regras de segurança para proteger as cargas de trabalho hospedadas em redes de spoke e controlar o acesso a redes locais.<br/><br/> O Firewall do Azure tem escalabilidade interna.
Firewalls de NVA | Como no Firewall do Azure, os farms de firewall da NVA têm um mecanismo de administração comum, com um conjunto de regras de segurança para proteger as cargas de trabalho hospedadas em redes de spoke e controlar o acesso a redes locais.<br/><br/> Os firewalls de NVA podem ser dimensionados manualmente por trás de um balanceador de carga.<br/><br/> Embora um firewall de NVA tenha software menos especializado comparado a um WAF, ele tem um escopo de aplicação mais amplo para filtrar e inspecionar qualquer tipo de tráfego de entrada e saída.<br/><br/> Se você quiser usar NVAs, poderá encontrá-las no Azure Marketplace.

É recomendável usar um conjunto de Firewall do Azure (ou NVAs) para tráfego originado na Internet e outro para o tráfego originado localmente.
- Usar apenas um conjunto de firewalls para ambos é um risco à segurança, uma vez que ele não oferece nenhuma segurança de perímetro entre os dois conjuntos de tráfego de rede.
- Usar camadas de firewall separadas reduz a complexidade da verificação das regras de segurança e deixa claro quais regras correspondem a quais solicitações de rede de entrada.

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) o uso de NVAs em uma VNet do Azure.

## <a name="next-steps"></a>Próximas etapas 

Examine outras melhores práticas:

- [Melhores práticas](migrate-best-practices-security-management.md) de segurança e gerenciamento após a migração.
- [Melhores práticas](migrate-best-practices-costs.md) de gerenciamento de custos após a migração.
