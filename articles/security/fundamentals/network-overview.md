---
title: Requisitos e conceitos de segurança de rede no Azure | Microsoft Docs
description: Este artigo fornece explicações básicas sobre os principais requisitos e conceitos de segurança de rede, além de informações sobre o que o Azure tem a oferecer em cada uma dessas áreas.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 17d48b6093d88a926569d5cfe84475fc0c306a0d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874376"
---
# <a name="azure-network-security-overview"></a>Visão geral da segurança de rede do Azure

A segurança de rede pode ser definida como o processo de proteger os recursos de acesso não autorizado ou ataque por meio da aplicação de controles para o tráfego de rede. A meta é garantir que apenas o tráfego legítimo seja permitido. O Azure inclui uma infraestrutura de rede robusta para dar suporte a seus requisitos de conectividade de aplicativo e de serviço. A conectividade de rede é possível entre os recursos localizados no Azure, entre os recursos locais e aqueles hospedados no Azure e de origem e destino à Internet e ao Azure.

Este artigo cobre algumas das opções que o Azure oferece na área de segurança de rede. Você pode aprender sobre:

* Rede do Azure
* Controle de acesso à rede
* Firewall do Azure
* Acesso remoto seguro e conectividade entre instalações
* Disponibilidade
* Resolução de nomes
* Arquitetura de rede de perímetro (DMZ)
* Proteção contra DDoS do Azure
* Porta da frente do Azure
* Gerenciador de tráfego
* Monitoramento e detecção de ameaças

## <a name="azure-networking"></a>Rede do Azure

O Azure exige que as máquinas virtuais estejam conectadas a uma Rede Virtual do Azure. Uma rede virtual é um constructo lógico criado na malha de rede física do Azure. Cada rede virtual é isolada de todas outras redes virtuais. Isso ajuda a garantir que o tráfego de rede em suas implantações não esteja acessível para outros clientes do Azure.

Saiba mais:

* [Visão geral da rede virtual](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Controle de acesso à rede

O controle de acesso à rede é o ato de limitar a conectividade de entrada ou saída de sub-redes ou dispositivos específicos em uma rede virtual. O objetivo do controle de acesso à rede é limitar o acesso a suas máquinas virtuais e serviços para dispositivos e usuários aprovados. Os controles de acesso se baseiam em decisões de permissão ou negação para conexões de entrada ou saída da máquina virtual ou do serviço.

O Azure dá suporte a vários tipos de controle de acesso à rede, como:

* Controle de camada de rede
* Controle de rota e túnel forçado
* Dispositivos de segurança de rede virtual

### <a name="network-layer-control"></a>Controle de camada de rede

Qualquer implantação segura requer alguma medida de controle de acesso à rede. O objetivo do controle de acesso à rede é restringir a comunicação de máquina virtual para os sistemas necessários. Outras tentativas de comunicação são bloqueadas.

> [!NOTE]
> Os Firewalls de Armazenamento são abordados no artigo [Visão geral de segurança do armazenamento do Azure](../../storage/blobs/security-recommendations.md)

#### <a name="network-security-rules-nsgs"></a>regras de segurança de rede (NSGs)

Caso você precise de um controle de acesso no nível de rede básico (baseado no endereço IP e nos protocolos TCP ou UDP), será possível usar os Grupos de Segurança de Rede (NSGs). NSG é um firewall básico de filtragem de pacotes com estado e permite o controle do acesso baseado em uma sequência de [5 tuplas](https://www.techopedia.com/definition/28190/5-tuple). Os NSGs incluem a funcionalidade para simplificar o gerenciamento e reduzir as chances de erros de configuração:

* **Regras de segurança aumentadas** simplificam a definição de regra do NSG e permitem que você crie regras complexas em vez de ter de criar várias regras simples para atingir o mesmo resultado.
* **Marcas de serviço** são rótulos criados pela Microsoft que representam um grupo de endereços IP. Eles atualizar dinamicamente para incluir os intervalos de IP que atendem as condições que definem a inclusão no rótulo. Por exemplo, se você quiser criar uma regra que se aplica a todo o armazenamento do Azure na região Leste você pode usar Storage.EastUS
* **Grupos de segurança de aplicativo** permitem implantar recursos em grupos de aplicativos e controlar o acesso a esses recursos, criando regras que usam esses grupos de aplicativos. Por exemplo, se você tiver servidores Web implantados para o grupo de aplicativos 'Webservers' você pode criar uma regra que se aplica a um NSG permitindo 443 de tráfego da Internet para todos os sistemas no grupo de aplicativos 'Webservers'.

Os NSGs não fornecem inspeção da camada de aplicativo nem controles de acesso autenticado.

Saiba mais:

* [Grupos de segurança de rede](../../virtual-network/network-security-groups-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>Acesso ASC just in time à VM

A [central de segurança do Azure](../../security-center/security-center-introduction.md) pode gerenciar o NSGs em VMs e bloquear o acesso à VM até que um usuário com o controle de acesso baseado em função do Azure permissões de [RBAC do Azure](../../role-based-access-control/overview.md) solicite acesso. Quando o usuário com êxito é ASC autorizado faz modificações NSGs a fim de permitir o acesso a portas selecionadas durante o tempo especificado. Quando o tempo expirar os NSGs são restaurados para seu protegido estado anterior.

Saiba mais:

* [Central de Segurança do Azure do Acesso Just-In-Time](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Pontos de extremidade de serviço

Pontos de extremidade de serviço são outra maneira de aplicar o controle sobre seu tráfego. Você pode limitar a comunicação com serviços com suporte para apenas suas redes virtuais ao longo de uma conexão direta. O tráfego de sua rede virtual para o serviço do Azure específico permanece na rede de backbone do Microsoft Azure.  

Saiba mais:

* [Pontos de extremidade de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Controle de rota e túnel forçado

A capacidade de controlar o comportamento de roteamento em suas redes virtuais é crítica. Se o roteamento estiver configurado incorretamente, os aplicativos e serviços hospedados na máquina virtual poderão se conectar a dispositivos não autorizados, incluindo sistemas de propriedade e operados por invasores potenciais.

A rede do Azure dá suporte à capacidade de personalizar o comportamento de roteamento do tráfego de rede nas redes virtuais. Isso permite alterar as entradas de tabela de roteamento padrão na sua rede virtual. O controle do comportamento de roteamento ajuda a garantir que todo o tráfego de determinado dispositivo ou grupo de dispositivos entra ou sai da rede virtual por meio de um local específico.

Por exemplo, você pode ter um dispositivo de segurança de rede virtual em sua rede virtual. Você quer ter certeza de que todo o tráfego de entrada e saída da rede virtual passa por esse dispositivo de segurança virtual. É possível fazer isso configurando as [Rotas Definidas pelo Usuário](../../virtual-network/virtual-networks-udr-overview.md) (UDRs) no Azure.

[Túnel forçado](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode ser usado para garantir que seus serviços não tenham permissão para iniciar uma conexão com dispositivos na Internet. Observe que isso é diferente de aceitar conexões de entrada e responder a elas em seguida. Os servidores Web front-end precisam responder às solicitações dos hosts da Internet e, portanto, o tráfego originado da Internet tem permissão de entrada nesses servidores Web, que, por sua vez, têm permissão para responder.

O que você não quer permitir é que um servidor Web de front-end inicie uma solicitação de saída. Essas solicitações podem representar um risco de segurança, pois essas conexões podem ser usadas para o download de malware. Mesmo se você quiser que esses servidores front-end iniciem solicitações de saída para a Internet, será conveniente forçá-los a passar pelos proxies Web locais. Isso permite que você aproveite a filtragem de URL e o log.

Em vez disso, é adequado usar o túnel forçado para evitar essa situação. Ao habilitar o túnel forçado, todas as conexões com a Internet serão forçadas pelo gateway local. É possível configurar o túnel forçado aproveitando as vantagens de UDRs.

Saiba mais:

* [O que são Rotas Definidas pelo Usuário e Encaminhamento de IP](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Dispositivos de segurança de rede virtual

Embora os NSGs, as UDRs e o túnel forçado forneçam um nível de segurança nas camadas de rede e de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), você poderá também habilitar a segurança em níveis superiores à rede.

Por exemplo, seus requisitos de segurança podem incluir:

* Autenticação e autorização antes da permissão do acesso ao seu aplicativo
* Detecção de intrusões e resposta a intrusões
* Inspeção da camada do aplicativo para os protocolos de nível alto
* Filtragem de URL
* Antimalware e antivírus no nível de rede
* Proteção contra bots
* Controle de acesso de aplicativo
* Proteção de DDoS adicional (acima da proteção de DDoS fornecida pela própria malha do Azure)

É possível acessar esses recursos avançados de segurança de rede por meio de uma solução de parceiros do Azure. Você pode encontrar as soluções de segurança de rede do parceiro do Azure mais atuais visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/)e procurando por "segurança" e "segurança de rede".

## <a name="azure-firewall"></a>Firewall do Azure

Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall totalmente com estado como serviço, com alta disponibilidade interna e escalabilidade de nuvem sem restrições. Alguns recursos incluem:

* Alta disponibilidade
* Escalabilidade de nuvem
* Regras de filtragem de FQDN de aplicativo
* Regras de filtragem de tráfego de rede

Saiba mais:

* [Visão geral do Firewall do Azure](../../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Acesso remoto seguro e conectividade entre instalações

A instalação, a configuração e o gerenciamento de seus recursos do Azure precisam ser feitos remotamente. Além disso, talvez você queira implantar soluções de [TI híbrida](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) que têm componentes locais e na nuvem pública do Azure. Esses cenários exigem o acesso remoto seguro.

A rede do Azure dá suporte aos seguintes cenários de acesso remoto:

* Conectar estações de trabalho individuais a uma rede virtual
* Conectar a rede local a uma rede virtual com uma VPN
* Conectar a rede local a uma rede virtual com uma conexão WAN dedicada
* Conectar redes virtuais entre si

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Conectar estações de trabalho individuais a uma rede virtual

Você poderá permitir que os desenvolvedores individuais ou a equipe de operações gerenciem as máquinas virtuais e os serviços no Azure. Por exemplo, digamos que você precisa ter acesso a uma máquina virtual em uma rede virtual. Mas a política de segurança não permite acesso remoto RDP ou SSH a máquinas virtuais individuais. Nesse caso, você pode usar uma [conexão VPN de ponto a ponto](../../vpn-gateway/point-to-site-about.md).

A conexão VPN ponto-a-site permite que você configure uma conexão privada e segura entre o usuário e a rede virtual. Quando a conexão VPN é estabelecida, o usuário poderá utilizar RDP ou SSH através da conexão VPN em qualquer máquina virtual em uma rede virtual. (Isso pressupõe que o usuário pode autenticar e está autorizado.) A VPN ponto a site dá suporte a:

* SSTP (Secure Socket Tunneling Protocol), que é um protocolo VPN baseado em SSL proprietário. Uma solução de VPN SSL pode penetrar em firewalls, pois a maioria dos firewalls abre a porta TCP 443, que o TLS/SSL usa. SSTP só tem suporte em dispositivos com Windows. O Azure oferece suporte a todas as versões do Windows com SSTP (Windows 7 e posterior).

* VPN IKEv2, uma solução de VPN IPsec baseada em padrões. VPN IKEv2 pode ser usada para se conectar de dispositivos Mac (OSX versões 10.11 e acima).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Saiba mais:

* [Configurar uma conexão ponto a site com uma rede virtual usando o PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Conectar a rede local a uma rede virtual com uma VPN

Você conectar toda a sua rede corporativa, ou partes dela, a uma rede virtual. Isso é comum em cenários de TI híbrida, em que as organizações [estendem seu data center local para o Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Em muitos casos, as organizações hospedam partes de um serviço no Azure e partes no local. Por exemplo, eles poderão fazer isso quando uma solução incluir servidores Web front-end no Azure e bancos de dados back-end no local. Esses tipos de conexões "entre instalações" também tornam o gerenciamento dos recursos localizados no Azure mais seguro e possibilitam cenários como a extensão dos controladores de domínio do Active Directory para o Azure.

Uma maneira de fazer isso é usar uma [VPN site a site](https://www.techopedia.com/definition/30747/site-to-site-vpn). A diferença entre uma VPN site a site e VPN ponto a site é que a última conecta um único dispositivo a uma rede virtual. Uma VPN site a site conecta a uma rede inteira (por exemplo, sua rede local) a uma rede virtual. As VPNs site a site em uma rede virtual usam o protocolo VPN de modo de túnel IPsec altamente seguro.

Saiba mais:

* [Criar uma VNet do Resource Manager com uma conexão VPN site a site usando o portal do Azure](../../vpn-gateway/tutorial-site-to-site-portal.md)
* [Sobre o Gateway de VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Conectar a rede local a uma rede virtual com uma conexão WAN dedicada

As conexões VPN ponto a site e site a site são eficazes para habilitar a conectividade entre instalações. No entanto, algumas organizações consideram que elas trazem as seguintes desvantagens:

* As conexões VPN movem dados pela Internet. Isso expõe essas conexões a problemas potenciais de segurança envolvidos na movimentação de dados em uma rede pública. Além disso, a confiabilidade e a disponibilidade de conexões com a Internet não podem ser garantidas.
* As conexões VPN a redes virtuais podem não ter a largura de banda para alguns aplicativos e algumas finalidades, pois seu limite máximo é em torno de 200 Mbps.

As organizações que precisam do nível mais alto de segurança e disponibilidade para suas conexões entre instalações normalmente usam conexões WAN dedicadas para se conectarem a sites remotos. O Azure fornece a capacidade de usar uma conexão WAN dedicada que pode ser usada para conectar sua rede local a uma rede virtual. O Azure ExpressRoute, o Express route direct e o alcance global da rota Express permitem isso.

Saiba mais:

* [Visão Geral Técnica do ExpressRoute](../../expressroute/expressroute-introduction.md)
* [ExpressRoute direto](../../expressroute/expressroute-erdirect-about.md)
* [Express alcance global de rota](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Conectar redes virtuais entre si

É possível usar várias redes virtuais para suas implantações. Há muitas razões pelas quais você poderia fazer isso. Talvez você queira simplificar o gerenciamento ou aumentar a segurança. Independentemente da motivação para colocar os recursos em diferentes redes virtuais, poderá haver ocasiões em que você desejará que os recursos em cada uma das redes conectem-se entre si.

Uma opção é que os serviços em uma rede virtual se conectem aos serviços em outra rede virtual "executando um loopback" pela Internet. A conexão é iniciada em uma rede virtual, passa pela Internet e volta para a rede virtual de destino. Essa opção expõe a conexão a problemas de segurança inerentes a qualquer comunicação baseada na Internet.

Uma opção melhor pode ser criar uma VPN site a site que estabelece a conexão entre duas redes virtuais. Esse método usa o mesmo protocolo de [modo de túnel IPSec](/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) que a conexão VPN site a site entre locais mencionada acima.

A vantagem dessa abordagem é que a conexão VPN é estabelecida pela malha da rede do Azure, e não pela conexão pela Internet. Isso fornece uma camada extra de segurança em comparação com as VPNs site a site que se conectam pela Internet.

Saiba mais:

* [Configurar uma conexão de VNet para VNet usando o Azure Resource Manager e o PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

É outra maneira de se conectar suas redes virtuais [emparelhamento VNET](../../virtual-network/virtual-network-peering-overview.md). Esse recurso permite que você se conecte duas redes do Azure para que a comunicação entre eles ocorra em infraestrutura de backbone da Microsoft sem ele nunca ficar pela Internet. Emparelhamento VNET pode conectar duas redes virtuais na mesma região ou duas redes virtuais entre regiões do Azure. NSGs podem ser usados para limitar a conectividade entre sistemas ou sub-redes diferentes.

## <a name="availability"></a>Disponibilidade

A disponibilidade é um componente fundamental de qualquer programa de segurança. Se seus usuários e sistemas não conseguem acessar o que precisam pela rede, o serviço pode ser considerado comprometido. O Azure tem as tecnologias de rede que dão suporte aos seguintes mecanismos de alta disponibilidade:

* Balanceamento de carga baseado em HTTP
* Balanceamento de carga no nível de rede
* Balanceamento de carga global

Balanceamento de carga é um mecanismo criado para distribuir as conexões igualmente entre vários dispositivos. Os objetivos do balanceamento de carga são:

* Para aumentar a disponibilidade. Quando você balanceia a carga de conexões em vários dispositivos, um ou mais dos dispositivos podem se tornar indisponíveis sem comprometer o serviço. Os serviços em execução nos dispositivos restantes online podem continuar a servir o conteúdo do serviço.
* Para aumentar o desempenho. Quando você balanceia a carga de conexões entre vários dispositivos, um único dispositivo não precisa lidar com todo o processamento. Em vez disso, as demandas de processamento e memória para o fornecimento de conteúdo são distribuídas entre vários dispositivos.

### <a name="http-based-load-balancing"></a>Balanceamento de carga baseado em HTTP

Com frequência, as organizações que executam serviços baseados na Web desejam ter um balanceador de carga baseado em HTTP na frente desses serviços Web. Isso ajuda a garantir níveis adequados de desempenho e de alta disponibilidade. Balanceadores de carga tradicionais baseados em rede dependem de protocolos de camada de transporte e de rede. Balanceadores de carga baseados em HTTP, por outro lado, tomam decisões com base em características do protocolo HTTP.

O Gateway de Aplicativo do Azure fornece balanceamento de carga baseado em HTTP para seus serviços baseados na Web. O Gateway de Aplicativo dá suporte a:

* Afinidade de sessão baseada em cookie. Essa funcionalidade garante que as conexões estabelecidas com um dos servidores por trás do balanceador de carga permanecem intactas entre o cliente e o servidor. Isso assegura a estabilidade das transações.
* Descarregamento de TLS. Quando um cliente se conecta com o balanceador de carga, essa sessão é criptografada usando o protocolo HTTPS (TLS). No entanto, para aumentar o desempenho, você pode usar o protocolo HTTP (não criptografado) para a conexão entre o balanceador de carga e o servidor Web por trás do balanceador de carga. Isso é chamado de "descarregamento de TLS", porque os servidores Web por trás do balanceador de carga não experimentam a sobrecarga do processador envolvida na criptografia. Os servidores Web podem, portanto, atender às solicitações mais rapidamente.
* Roteamento de conteúdo baseado em URL. Esse recurso possibilita que o balanceador de carga tome decisões sobre o local para onde serão encaminhadas as conexões de acordo com a URL de destino. Isso oferece muito mais flexibilidade do que as soluções que tomam decisões de balanceamento de carga de acordo com os endereços IP.

Saiba mais:

* [Visão geral do Gateway de Aplicativo](../../application-gateway/overview.md)

### <a name="network-level-load-balancing"></a>Balanceamento de carga no nível de rede

Ao contrário do balanceamento de carga baseado em HTTP, o balanceamento de carga no nível de rede toma decisões de acordo com o endereço IP e os números de porta (TCP ou UDP).
Você pode obter os benefícios do uso do balanceamento de carga no nível de rede no Azure usando o Azure Load Balancer. Entre algumas das principais características do Load Balancer estão:

* Balanceamento de carga no nível de rede de acordo com o endereço IP e números de porta.
* Suporte para qualquer protocolo de camada de aplicativo.
* Balanceia a carga para máquinas virtuais e instâncias de função dos serviços de nuvem do Azure.
* Pode ser usado para aplicativos e máquinas virtuais para a Internet (balanceamento de carga externo) e não voltadas à Internet (balanceamento de carga interno).
* Monitoramento do ponto de extremidade, que é usado para determinar se um dos serviços por trás do balanceador de carga ficou indisponível.

Saiba mais:

* [Balanceador de carga para a Internet entre várias máquinas virtuais ou serviços](../../load-balancer/load-balancer-overview.md)
* [Visão geral do balanceador de carga interno](../../load-balancer/load-balancer-overview.md)

### <a name="global-load-balancing"></a>Balanceamento de carga global

Algumas organizações querem o nível mais alto de disponibilidade possível. Uma maneira de atingir esse objetivo é hospedar os aplicativos em data centers distribuídos globalmente. Quando um aplicativo é hospedado em data centers localizados em todo o mundo, é possível que toda uma região geopolítica se torne indisponível, mas ainda tenha o aplicativo em execução.

Essa estratégia de balanceamento de carga também pode gerar benefícios de desempenho. Você pode direcionar as solicitações do serviço para o data center mais próximo do dispositivo que está fazendo a solicitação.

No Azure, você pode obter as vantagens do balanceamento de carga global usando o Gerenciador de Tráfego do Azure.

Saiba mais:

* [O que é o Gerenciador de Tráfego?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Resolução de nomes

Resolução de nomes é uma função crítica para todos os serviços hospedados no Azure. De uma perspectiva de segurança, o comprometimento da função de resolução de nomes pode fazer com que um invasor redirecione as solicitações de seus sites para o site do invasor. Uma resolução de nomes segura é um requisito de todos os serviços hospedados na nuvem.

Há dois tipos de resolução de nomes que precisam ser abordados:

* Resolução de nomes interna. Isso é usado pelos serviços nas redes virtuais, redes locais ou ambas. Os nomes usados para a resolução de nomes interna não são acessíveis pela Internet. Para ter a segurança ideal, é importante que seu esquema de resolução de nomes interna não seja acessível a usuários externos.
* Resolução de nomes externa. Isso é usado por pessoas e dispositivos fora de suas redes locais e virtuais. Esses são os nomes visíveis para a Internet e que são usados para direcionar a conexão para os serviços baseados na nuvem.

Para a resolução de nomes interna, você tem duas opções:

* Um servidor DNS de rede virtual. Quando você cria uma nova rede virtual, um servidor DNS é criado para você. Esse servidor DNS pode resolver os nomes dos computadores localizados nessa rede virtual. O servidor DNS não é configurável, é gerenciado pelo gerenciador de malha do Azure e pode, por esse motivo, ajudá-lo a garantir a solução de resolução de nomes.
* Traga seu próprio servidor DNS. Você tem a opção de colocar um servidor DNS de sua escolha em sua rede virtual. Esse servidor DNS pode ser um servidor DNS integrado ao Active Directory ou uma solução de servidor DNS dedicada fornecida por um parceiro do Azure, que pode ser obtida no Azure Marketplace.

Saiba mais:

* [Visão geral da rede virtual](../../virtual-network/virtual-networks-overview.md)
* [Gerenciar servidores DNS usados por uma rede virtual](../../virtual-network/manage-virtual-network.md#change-dns-servers)

Para a resolução de nomes externa, você tem duas opções:

* Hospedar seu próprio servidor DNS externo no local.
* Hospedar seu próprio servidor DNS externo com um provedor de serviços.

Muitas organizações de grande porte hospedam seus próprios servidores DNS no local. Elas podem fazer isso porque têm competências sobre rede e a presença global para tomar essa decisão.

Na maioria dos casos, é melhor hospedar seus serviços de resolução de nomes DNS em um provedor de serviços. Esses provedores de serviço têm as competências referentes à rede e a presença global para garantir uma disponibilidade muito alta para os serviços de resolução de nomes. A disponibilidade é essencial para os serviços DNS, pois, se os serviços de resolução de nomes falharem, ninguém poderá ter acesso aos seus serviços para a Internet.

O Azure oferece uma solução de DNS externo de alta disponibilidade e alto desempenho na forma do DNS do Azure. Essa solução de resolução de nomes externa aproveita a infraestrutura mundial do DNS do Azure. Ela permite hospedar seu domínio no Azure usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. Como parte do Azure, ela também herda os fortes controles de segurança internos da plataforma.

Saiba mais:

* [Visão geral do DNS do Azure](../../dns/dns-overview.md)
* [Zonas privadas do DNS do Azure](../../dns/private-dns-overview.md) permitem que você configure os nomes DNS privados para recursos do Azure, em vez de nomes atribuídos automaticamente sem a necessidade de adicionar uma solução DNS personalizada.

## <a name="perimeter-network-architecture"></a>Arquitetura de rede de perímetro

Muitas organizações grandes usam redes de perímetro para segmentar suas redes e criar uma zona de buffer entre a Internet e seus serviços. A parte de perímetro da rede é considerada uma zona de baixa segurança e nenhum ativo de alto valor é colocado nesse segmento da rede. Normalmente, você verá dispositivos de segurança de rede que têm um adaptador de rede no segmento de rede de perímetro. Outra interface de rede está conectada a uma rede que tem máquinas virtuais e serviços que aceitam conexões de entrada da Internet.

Você pode criar redes de perímetro de várias maneiras diferentes. A decisão de implantar uma rede de perímetro e o tipo de perímetro da rede a ser usado se você decidir usar um depende de seus requisitos de segurança de rede.

Saiba mais:

* [Segurança de rede e Serviços de Nuvem da Microsoft](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Proteção contra DDoS do Azure

Ataques de DDoS (negação de serviço distribuído) são uma das maiores preocupações de disponibilidade e de segurança enfrentadas pelos clientes que estão migrando seus aplicativos para a nuvem. Um ataque de DDoS tenta esgotar os recursos de um aplicativo, fazendo com que o aplicativo fique indisponível para usuários legítimos. Ataques de DDoS podem ser direcionadas a qualquer ponto de extremidade publicamente acessível pela Internet.
A Microsoft fornece proteção contra DDoS conhecido como **Básico** como parte da plataforma Azure. Isso é fornecido sem custo adicional e sempre inclui monitoramento ativo e em tempo real mitigação de ataques comuns de nível de rede. Além das proteções incluídas com a proteção contra DDoS **Básico** você pode permitir que a opção **Standard**. Os recursos da Proteção contra DDoS do Azure Standard:

* **Integração de plataforma nativa:** nativamente integrado ao Azure. Inclui a configuração por meio do Portal do Azure. A Proteção contra DDoS Standard compreende seus recursos e a respectiva configuração.
* **Proteção imediata:** a configuração simplificada protege de maneira imediata todos os recursos em uma rede virtual assim que a Proteção contra DDoS Standard é habilitada. Nenhuma definição ou intervenção do usuário é necessária. A Proteção contra DDoS Standard atenua o ataque de maneira instantânea e automática, assim que ele é detectado.
* **Monitoramento de tráfego Always On:** seus padrões de tráfego do aplicativo são monitorados 24 horas por dia, 7 dias por semana, procurando indicadores de ataques de DDoS. A mitigação é realizada quando as políticas de proteção são excedidas.
* **Relatórios de Mitigação de Ataque** Os Relatórios de Mitigação de Ataques usam dados de fluxo de rede agregados para fornecer informações detalhadas sobre ataques direcionados a seus recursos.
* **Logs de Fluxo de Mitigação de Ataque** Os Logs de Fluxo de Mitigação de Ataque permitem que você revise o tráfego perdido, o tráfego encaminhado e outros dados de ataque quase em tempo real durante um ataque DDoS ativo.
* **Ajuste adaptativo:** A criação de perfil de tráfego inteligente aprende o tráfego do seu aplicativo ao longo do tempo e seleciona e atualiza o perfil que é o mais adequado para o seu serviço. O perfil se ajusta conforme o tráfego é alterado ao longo do tempo. Proteção das camadas 3 a 7: fornece Proteção contra DDoS para toda a pilha quando usado com um Firewall do Aplicativo Web.
* **Escala de mitigação ampla:** mais de 60 tipos de ataques diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques de DDoS conhecidos.
* **Métricas de ataque:** métricas resumidas de cada ataque são acessíveis por meio do Azure Monitor.
* **Alerta de ataque:** Os alertas podem ser configurados no início e na interrupção de um ataque e na duração do ataque, usando métricas de ataque internas. Os alertas integram-se ao seu software operacional, como Microsoft Azure logs de monitor, Splunk, armazenamento do Azure, email e o portal do Azure.
* **Garantia de custo:** créditos de serviço de expansão de aplicativo para ataques de DDoS documentados.
* **DDoS Os clientes do padrão de proteção DDoS** de resposta rápida agora têm acesso à equipe de resposta rápida durante um ataque ativo. A DRR pode ajudar na investigação de ataques, atenuações personalizadas durante uma análise de ataque e pós-ataque.


Saiba mais:

* [visão geral da proteção contra DDoS](../../ddos-protection/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Porta da frente do Azure

O serviço de porta frontal do Azure permite definir, gerenciar e monitorar o roteamento global do tráfego da Web. Ele otimiza o roteamento do seu tráfego para melhor desempenho e alta disponibilidade. O Azure Front Door permite que você crie regras WAF (firewall do aplicativo Web) personalizadas para obter controle de acesso a fim de proteger sua carga de trabalho HTTP/HTTPS contra exploração com base em endereços IP do cliente, o código do país e parâmetros http. Além disso, a porta frontal também permite que você crie regras de limitação de taxa para a batalha de tráfego de bot mal-intencionado, inclui descarregamento de TLS e solicitação por HTTP/HTTPS, processamento de camada de aplicativo.

A própria plataforma Front Door é protegida pelo Azure DDoS Protection Basic. Para aumentar a proteção, a Proteção contra DDoS do Azure Standard pode ser habilitada em suas VNETs e proteger recursos contra ataques de camada de rede (TCP/UDP) por meio do ajuste automático e atenuação. A Front Door é um proxy reverso da camada 7, que permite apenas que o tráfego da web passe para os servidores back-end e bloqueie outros tipos de tráfego por padrão.

Saiba mais:

* Para obter mais informações sobre o conjunto completo de front-Azure da porta recursos que você pode examinar a [visão geral do Azure da frente](../../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Gerenciador de Tráfego do Azure

O Gerenciador de tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que permite que você distribua o tráfego de maneira ideal para serviços em todas as regiões globais do Azure, fornecendo alta disponibilidade e capacidade de resposta. O Gerenciador de Tráfego usa o DNS para direcionar as solicitações do cliente ao ponto de extremidade de serviço mais apropriado com base em um método de roteamento de tráfego e a integridade dos pontos de extremidade. Um ponto de extremidade é qualquer serviço para a Internet hospedado dentro ou fora do Azure. O gerenciador de tráfego monitora os terminais e não direciona o tráfego para nenhum terminal que esteja indisponível.

Saiba mais:

* [Visão geral do Azure do Gerenciador de tráfego](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Monitoramento e detecção de ameaças

O Azure fornece recursos para ajudá-lo nessa área-chave com detecção antecipada, monitoramento e coleta e análise do tráfego de rede.

### <a name="azure-network-watcher"></a>Observador de Rede do Azure

O Observador de Rede do Azure pode ajudá-lo a solucionar problemas e fornece um conjunto de ferramentas totalmente novo para ajudá-lo na identificação de problemas de segurança.

A [exibição do grupo de segurança](../../network-watcher/network-watcher-security-group-view-overview.md) ajuda na auditoria e na conformidade de segurança das máquinas virtuais. Use esse recurso para executar auditorias programáticas, comparando as políticas de linha de base definidas por sua organização para regras eficientes de cada uma das suas VMs. Isso pode ajudar a identificar qualquer descompasso de configuração.

A [captura de pacote](../../network-watcher/network-watcher-packet-capture-overview.md) permite que você capture o tráfego de rede que entra e sai da máquina virtual. Você pode coletar estatísticas de rede e solucionar problemas de aplicativos, o que pode ser valioso na investigação de invasões de rede. Você também pode usar esse recurso em conjunto com o Azure Functions para iniciar capturas de rede em resposta a alertas específicos do Azure.

Para saber mais sobre o Observador de Rede e como começar a testar algumas das funcionalidades nos laboratórios, consulte a [Visão geral do monitoramento do Observador de Rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> Para obter as notificações mais atualizadas sobre a disponibilidade e o status desse serviço, verifique a [página atualizações do Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Central de Segurança do Azure

A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças e oferece maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com um grande conjunto de soluções de segurança.

A Central de Segurança ajuda a otimizar e a monitorar a segurança da rede:

* Fornecendo recomendações de segurança de rede.
* Monitorando o estado de configuração de segurança da rede.
* Alertando você contra ameaças baseadas em rede, nos níveis do ponto de extremidade e de rede.

Saiba mais:

* [Introdução à Central de Segurança do Azure](../../security-center/security-center-introduction.md)

### <a name="virtual-network-tap"></a>Rede Virtual TAP

O TAP (Ponto de Acesso do Terminal) de rede virtual do Azure permite que você transmita o tráfego de rede por streaming continuamente da máquina virtual para uma ferramenta de coleta de pacotes ou de análise de rede. O coletor ou ferramenta de análise é fornecido por um parceiro de dispositivo virtual de rede. Você pode usar o mesmo recurso de TAP de rede virtual para o tráfego agregado por meio de vários adaptadores de rede nas mesmas assinaturas ou em outras.

Saiba mais:

* [TAP de rede virtual](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Registrando em log

O log em um nível de rede é uma função essencial em qualquer cenário de segurança de rede. No Azure, é possível registrar as informações obtidas dos NSGs para obter informações de log no nível de rede. Com o log do NSG, você obtém informações dos seguintes:

* [Logs de atividade](../../azure-monitor/platform/platform-logs-overview.md). Use esses logs para exibir todas as operações enviadas às assinaturas do Azure. Esses logs são habilitados por padrão e podem ser usados no portal do Azure. Eles eram anteriormente conhecidos como logs de auditoria ou operacionais.
* Logs de eventos. Esses logs fornecem informações sobre quais regras do NSG foram aplicadas.
* Logs de contador. Esses logs permitem saber quantas vezes cada regra NSG foi aplicada para negar ou permitir o tráfego.

Você também pode usar o [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), uma ferramenta de visualização de dados avançada, para exibir e analisar esses logs.
Saiba mais:

* [Logs de Azure Monitor para NSGs (grupos de segurança de rede)](../../virtual-network/virtual-network-nsg-manage-log.md)