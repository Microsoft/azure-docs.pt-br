---
title: "Requisitos e conceitos de segurança de rede no Azure | Microsoft Docs"
description: " Este artigo facilita o entendimento do que o Microsoft Azure tem a oferecer na área de segurança de rede. Fornecemos explicações básicas sobre os principais requisitos e conceitos de segurança de rede, além de informações sobre o que o Azure tem a oferecer em cada uma dessas áreas. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: terrylan
ms.openlocfilehash: 27243856d0c6b70c7515b6bde66b99ef6160eb36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-network-security-overview"></a>Azure Network Security Overview (Visão geral da segurança de rede do Azure)
O Microsoft Azure inclui uma infraestrutura de rede robusta para dar suporte a seus requisitos de conectividade de aplicativo e de serviço. A conectividade de rede é possível entre os recursos localizados no Azure, entre os recursos locais e aqueles hospedados no Azure e de origem e destino à Internet e ao Azure.

O objetivo deste artigo é facilitar o entendimento do que o Microsoft Azure tem a oferecer na área de segurança de rede. Aqui, fornecemos explicações básicas sobre os principais requisitos e conceitos de segurança de rede. Também oferecemos informações sobre o que o Azure tem a oferecer em cada uma dessas áreas, bem como links para ajudá-lo a obter uma compreensão mais aprofundada das áreas interessantes.

Este artigo de Visão geral da segurança de rede do Azure tem como foco as seguintes áreas:

* Rede do Azure
* Controle de acesso à rede
* Acesso remoto seguro e conectividade entre instalações
* Disponibilidade
* Resolução de nomes
* Arquitetura do DMZ
* Monitoramento e detecção de ameaças


## <a name="azure-networking"></a>Rede do Azure
As máquinas virtuais precisam de conectividade de rede. Para dar suporte a esse requisito, o Azure exige que as máquinas virtuais sejam conectadas a uma Rede Virtual do Azure. Uma Rede Virtual do Azure é um constructo lógico criado na malha de rede física do Azure. Cada Rede Virtual do Azure lógica é isolada das todas as outras Redes Virtuais do Azure. Isso ajuda a garantir que o tráfego de rede em suas implantações não está acessível para outros clientes do Microsoft Azure.

Saiba mais:

* [Visão geral da Rede Virtual](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>Controle de acesso à rede
O controle de acesso à rede é o ato de limitar a conectividade de entrada ou saída de sub-redes ou dispositivos específicos em uma Rede Virtual do Azure. O objetivo do controle de acesso à rede é limitar o acesso a suas máquinas virtuais e serviços para dispositivos e usuários aprovados. Os controles de acesso baseiam-se em decisões de permissão ou negação para conexões de entrada ou saída da máquina virtual ou do serviço.

O Azure dá suporte a vários tipos de controle de acesso à rede como:

* Controle de camada de rede
* Controle de rota e túnel forçado
* Dispositivos de segurança de rede virtual

### <a name="network-layer-control"></a>Controle de camada de rede
Qualquer implantação segura requer alguma medida de controle de acesso à rede. O objetivo do controle de acesso à rede é restringir a comunicação de máquina virtual para os sistemas necessários e que outras tentativas de comunicação sejam bloqueadas.

Caso você precise de um controle de acesso no nível de rede básico (baseado no endereço IP e nos protocolos TCP ou UDP), é possível usar os Grupos de Segurança de Rede. Um NSG (Grupo de Segurança de Rede) é um firewall básico de filtragem de pacotes com estado e permite o controle do acesso baseado em uma sequência de [5 tuplas](https://www.techopedia.com/definition/28190/5-tuple). Os NSGs não fornecem inspeção da camada de aplicativo nem controles de acesso autenticado.

Saiba mais:

* [Grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Controle de rota e túnel forçado
A capacidade de controlar o comportamento de roteamento em suas Redes Virtuais do Azure é uma funcionalidade crítica de controle de acesso e segurança de rede. Se o roteamento estiver configurado incorretamente, os aplicativos e serviços hospedados na máquina virtual poderão se conectar a dispositivos não autorizados, incluindo sistemas de propriedade e operados por invasores potenciais.

A rede do Azure dá suporte à capacidade de personalizar o comportamento de roteamento do tráfego de rede nas Redes Virtuais do Azure. Isso permite alterar as entradas de tabela de roteamento padrão na Rede Virtual do Azure. O controle do comportamento de roteamento ajuda a garantir que todo o tráfego de determinado dispositivo ou grupo de dispositivos entra ou sai da rede virtual por meio de um local específico.

Por exemplo, você pode ter um dispositivo de segurança de rede virtual em sua Rede Virtual do Azure. Você quer ter certeza de que todo o tráfego de entrada e saída da Rede Virtual do Azure passa por esse dispositivo de segurança virtual. É possível fazer isso configurando as [Rotas Definidas pelo Usuário](../virtual-network/virtual-networks-udr-overview.md) no Azure.

[túnel forçado](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode ser usado para garantir que seus serviços não tenham permissão para iniciar uma conexão com dispositivos na Internet. Observe que isso é diferente de aceitar conexões de entrada e responder a elas em seguida. Os servidores Web front-end precisam responder às solicitações dos hosts da Internet e, portanto, o tráfego originado da Internet tem permissão de entrada nesses servidores Web, que, por sua vez, têm permissão para responder.

O que você não quer permitir é que um servidor Web front-end inicie uma solicitação de saída. Essas solicitações podem representar um risco de segurança, pois essas conexões podem ser usadas para o download de malware. Mesmo se você quiser que esses servidores front-end iniciem solicitações de saída para a Internet, será conveniente forçá-los a passar pelos proxies Web locais, para que você possa aproveitar a filtragem de URL e o log.

Em vez disso, é adequado usar o túnel forçado para evitar essa situação. Ao habilitar o túnel forçado, todas as conexões com a Internet serão forçadas pelo gateway local. É possível configurar o túnel forçado utilizando as Rotas Definidas pelo Usuário.

Saiba mais:

* [O que são Rotas Definidas pelo Usuário e Encaminhamento de IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Dispositivos de segurança de rede virtual
Embora os Grupos de Segurança de Rede, as Rotas Definidas pelo Usuário e o túnel forçado forneçam um nível de segurança nas camadas de rede e de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), poderão haver ocasiões em que você desejará habilitar a segurança em níveis superiores à rede.

Por exemplo, seus requisitos de segurança podem incluir:

* Autenticação e autorização antes da permissão do acesso ao seu aplicativo
* Detecção de intrusões e resposta a intrusões
* Inspeção da camada do aplicativo para os protocolos de nível alto
* Filtragem de URL
* Antimalware e antivírus no nível de rede
* Proteção contra bots
* Controle de acesso de aplicativo
* Proteção de DDoS adicional (acima da proteção de DDoS fornecida pela própria malha do Azure)

É possível acessar esses recursos avançados de segurança de rede por meio de uma solução de parceiros do Azure. Você pode encontrar as soluções mais atuais de segurança de rede de parceiros do Azure visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e pesquisando por “segurança” e “segurança de rede”.

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Acesso remoto seguro e conectividade entre instalações
A instalação, a configuração e o gerenciamento de seus recursos do Azure precisam ser feitos remotamente. Além disso, talvez você queira implantar soluções de [TI híbrida](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) que têm componentes locais e na nuvem pública do Azure. Esses cenários exigem o acesso remoto seguro.

A rede do Azure dá suporte aos seguintes cenários de acesso remoto:

* Conectar estações de trabalho individuais a uma Rede Virtual do Azure
* Conectar a rede local a uma Rede Virtual do Azure com uma VPN
* Conectar a rede local a uma Rede Virtual do Azure com uma conexão WAN dedicada
* Conectar Redes Virtuais do Azure entre si

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Conectar estações de trabalho individuais a uma Rede Virtual do Azure
Pode haver ocasiões em que você desejará permitir que os desenvolvedores individuais ou a equipe de operações gerenciem as máquinas virtuais e os serviços no Azure. Por exemplo, você precisa ter acesso a uma máquina virtual em uma Rede Virtual do Azure e sua política de segurança não deve permitir o acesso remoto RDP ou SSH a máquinas virtuais individuais. Nesse caso, você pode usar uma conexão VPN ponto a site.

A conexão VPN ponto a site usa o protocolo [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) para permitir a configuração de uma conexão privada e segura entre o usuário e a Rede Virtual do Azure. Assim que a conexão VPN for estabelecida, o usuário poderá usar o RDP ou SSH pela conexão VPN em qualquer máquina virtual na Rede Virtual do Azure (supondo que o usuário possa se autenticar e tenha autorização).

Saiba mais:

* [Configurar uma conexão Ponto a Site com uma rede virtual usando o PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Conectar a rede local a uma Rede Virtual do Azure com uma VPN
Talvez você queira conectar toda a sua rede corporativa, ou partes dela, a uma Rede Virtual do Azure. Isso é comum em cenários de TI híbrida, em que as empresas [estendem seu data center local para o Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Em muitos casos, as empresas hospedarão algumas partes de um serviço no Azure e outras localmente, por exemplo, quando uma solução inclui servidores Web front-end no Azure e bancos de dados back-end localmente. Esses tipos de conexões “entre instalações” também tornam o gerenciamento dos recursos localizados no Azure mais seguros e possibilitam cenários como a extensão dos controladores de domínio do Active Directory para o Azure.

Uma maneira de fazer isso é usar uma [VPN site a site](https://www.techopedia.com/definition/30747/site-to-site-vpn). A diferença entre uma VPN site a site e uma VPN ponto a site é que essa última conecta um único dispositivo a uma Rede Virtual do Azure, ao passo que uma VPN site a site conecta uma rede inteira (como sua rede local) a uma Rede Virtual do Azure. As VPNs site a site em uma Rede Virtual do Azure usam o protocolo VPN de modo de túnel IPsec altamente seguro.

Saiba mais:

* [Criar uma rede virtual do Resource Manager com uma conexão VPN site a site usando o Portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Planejamento e design para o Gateway de VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Conectar a rede local a uma Rede Virtual do Azure com uma conexão WAN dedicada
As conexões VPN ponto a site e site a site são eficazes para habilitar a conectividade entre instalações. No entanto, algumas organizações consideram que elas trazem as seguintes desvantagens:

* As conexões VPN movem os dados pela Internet – isso expõe essas conexões a problemas potenciais de segurança envolvidos na movimentação de dados em uma rede pública. Além disso, a confiabilidade e a disponibilidade de conexões com a Internet não podem ser garantidas.
* As conexões VPN a Redes Virtuais do Azure podem ser consideradas restritas por largura de banda para alguns aplicativos e algumas finalidades, pois seu limite máximo é em torno de 200 Mbps.

As organizações que precisam do nível mais alto de segurança e disponibilidade para suas conexões entre instalações normalmente usam conexões WAN dedicadas para se conectarem a sites remotos. O Azure fornece a capacidade de usar uma conexão WAN dedicada que pode ser usada para conectar sua rede local a uma Rede Virtual do Azure. Isso é habilitado por meio da Azure ExpressRoute.

Saiba mais:

* [Visão Geral Técnica do ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Conectar Redes Virtuais do Azure entre si
É possível usar várias Redes Virtuais do Azure para suas implantações. Há muitas razões pelas quais você poderia fazer isso. Uma das razões poderia ser simplificar o gerenciamento; outra razão poderia ser por motivos de segurança. Independentemente da motivação ou da lógica para colocar os recursos em diferentes Redes Virtuais do Azure, pode haver ocasiões em que você pode desejar que os recursos em cada uma das redes conectem-se entre si.

Uma opção seria para que os serviços em uma Rede Virtual do Azure se conectem aos serviços em outra Rede Virtual do Azure “executando um loop de volta” pela Internet. A conexão seria iniciada em uma Rede Virtual do Azure, passaria pela Internet e voltaria para a Rede Virtual do Azure de destino. Essa opção expõe a conexão a problemas de segurança inerentes a qualquer comunicação baseada na Internet.

Uma opção melhor seria criar uma VPN site a site da Rede Virtual do Azure à Rede Virtual do Azure. Essa VPN site a site da Rede Virtual do Azure à Rede Virtual do Azure usa o mesmo protocolo de [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx) que a conexão VPN site a site entre instalações mencionada acima.

A vantagem de usar uma VPN site a site da Rede Virtual do Azure à Rede Virtual do Azure é que a conexão VPN é estabelecida pela malha da rede do Azure, e não pela conexão pela Internet. Isso fornece uma camada extra de segurança em comparação com as VPNs site a site que se conectam pela Internet.

Saiba mais:

* [Configurar uma conexão de rede virtual com rede virtual usando o PowerShell e o Azure Resource Manager](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Disponibilidade
A disponibilidade é um componente fundamental de qualquer programa de segurança. Se seus usuários e sistemas não conseguem acessar o que precisam pela rede, o serviço pode ser considerado comprometido. O Azure tem as tecnologias de rede que dão suporte aos seguintes mecanismos de alta disponibilidade:

* Balanceamento de carga baseado em HTTP
* Balanceamento de carga no nível de rede
* Balanceamento de carga global

Balanceamento de carga é um mecanismo criado para distribuir as conexões igualmente entre vários dispositivos. Os objetivos do balanceamento de carga são:

* Aumentar a disponibilidade – ao balancear a carga de conexões entre vários dispositivos, um ou mais dos dispositivos podem se tornar indisponíveis e os serviços em execução nos dispositivos online restantes podem continuar fornecendo o conteúdo do serviço
* Aumentar o desempenho – ao balancear a carga de conexões entre vários dispositivos, um único dispositivo não precisa receber o impacto do processador. Em vez disso, as demandas de processamento e memória para o fornecimento de conteúdo são distribuídas entre vários dispositivos.

### <a name="http-based-load-balancing"></a>Balanceamento de carga baseado em HTTP
Com frequência, as organizações que executam serviços baseados na Web desejam ter um balanceador de carga baseado em HTTP na frente desses serviços Web, a fim de ajudar a garantir níveis adequados de desempenho e de alta disponibilidade. Ao contrário dos balanceadores de carga tradicionais baseados na rede, as decisões de balanceamento de carga tomadas pelos balanceadores de carga baseados em HTTP apoiam-se nas características do protocolo HTTP, não dos protocolos de camada de transporte e de rede.

Para fornecer o balanceamento de carga baseado em HTTP para seus serviços baseados na Web, o Azure fornece o Azure Application Gateway. O Gateway de Aplicativo do Azure dá suporte aos seguintes:

* Balanceamento de carga baseado em HTTP – as decisões de balanceamento de carga são tomadas de acordo com as características especiais do protocolo HTTP
* Afinidade de sessão baseada em cookie – essa funcionalidade garante que as conexões estabelecidas com um dos servidores por trás do balanceador de carga permanecem intactas entre o cliente e o servidor. Isso assegura a estabilidade das transações.
* Descarregamento de SSL – quando uma conexão de cliente é estabelecida com o balanceador de carga, a sessão entre o cliente e o balanceador de carga é criptografada usando o protocolo HTTPS (SSL/). No entanto, para aumentar o desempenho, você tem a opção de fazer com que a conexão entre o balanceador de carga e o servidor Web por trás do balanceador de carga use o protocolo HTTP (não criptografado). Isso é chamado de “descarregamento de SSL”, pois os servidores Web por trás do balanceador de carga não enfrentam a sobrecarga de processador envolvida com a criptografia e, portanto, devem ter a capacidade de atender a solicitações mais rapidamente.
* Roteamento de conteúdo baseado em URL – esse recurso possibilita que o balanceador de carga tome decisões em relação ao local em que serão encaminhadas as conexões de acordo com a URL de destino. Isso oferece muito mais flexibilidade do que as soluções que tomam decisões de balanceamento de carga de acordo com os endereços IP.

Saiba mais:

* [Visão geral do Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Balanceamento de carga no nível de rede
Ao contrário do balanceamento de carga baseado em HTTP, o balanceamento de carga no nível de rede toma decisões de balanceamento de carga de acordo com o endereço IP e os números de porta (TCP ou UDP).
Você pode obter os benefícios do uso do balanceamento de carga no nível de rede no Azure usando o Azure Load Balancer. Entre algumas das principais características do Azure Load Balancer estão:

* Balanceamento de carga no nível de rede de acordo com o endereço IP e números de porta
* Suporte para qualquer protocolo de camada de aplicativo
* Balanceia a carga para máquinas virtuais e instâncias de função dos serviços de nuvem do Azure
* Pode ser usado para aplicativos e máquinas virtuais para a Internet (balanceamento de carga externo) e não voltadas à Internet (balanceamento de carga interno)
* Monitoramento do ponto de extremidade, que é usado para determinar se um dos serviços por trás do balanceador de carga ficou indisponível

Saiba mais:

* [Balanceador de carga para a Internet entre várias Máquinas Virtuais ou serviços](../load-balancer/load-balancer-internet-overview.md)
* [Visão geral do Balanceador de Carga Interno](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Balanceamento de carga global
Algumas organizações estarão interessadas no nível mais alto de disponibilidade possível. Uma maneira de atingir esse objetivo é hospedar os aplicativos em data centers distribuídos globalmente. Quando um aplicativo é hospedado em data centers localizados em todo o mundo, é possível que toda uma região geopolítica se torne indisponível, mas ainda ter o aplicativo em execução.

Além das vantagens de disponibilidade que você obtém ao hospedar aplicativos em data centers distribuídos globalmente, você também pode obter vantagens de desempenho. Pode-se obtê-las com o uso de um mecanismo que direciona as solicitações pelo serviço ao data center mais próximo do dispositivo que está fazendo a solicitação.

O balanceamento de carga global pode proporcionar essas duas vantagens. No Azure, você pode obter as vantagens do balanceamento de carga global usando o Gerenciador de Tráfego do Azure.

Saiba mais:

* [O que é o Gerenciador de Tráfego?](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>Resolução de nomes
Resolução de nomes é uma função crítica para todos os serviços hospedados no Azure. De uma perspectiva de segurança, o comprometimento da função de resolução de nomes pode fazer com que um invasor redirecione as solicitações de seus sites para o site do invasor. Uma resolução de nomes segura é um requisito de todos os serviços hospedados na nuvem.

Há dois tipos de resolução de nomes que precisam ser abordados:

* Resolução de nomes interna – usada pelos serviços nas Redes Virtuais do Azure, nas redes locais ou em ambas. Os nomes usados para a resolução de nomes interna não são acessíveis pela Internet. Para uma segurança ideal, é importante que seu esquema de resolução de nomes interna não seja acessível a usuários externos.
* Resolução de nome externa – usada por pessoas e dispositivos fora das redes locais e das Redes Virtuais do Azure. Esses são os nomes visíveis para a Internet e que são usados para direcionar a conexão para os serviços baseados na nuvem.

Para a resolução de nomes interna, você tem duas opções:

* Um servidor DNS da Rede Virtual do Azure – ao criar uma nova Rede Virtual do Azure, um servidor DNS é criado para você. Esse servidor DNS pode resolver os nomes dos computadores localizados nessa Rede Virtual do Azure. O servidor DNS não é configurável e é gerenciado pelo gerenciador de malha do Azure, tornando-o uma solução de resolução de nomes segura.
* Traga seu próprio servidor DNS – você tem a opção de colocar um servidor DNS de sua escolha em sua Rede Virtual do Azure. Esse servidor DNS pode ser um servidor DNS integrado ao Active Directory ou uma solução de servidor DNS dedicada fornecida por um parceiro do Azure, que pode ser obtida no Azure Marketplace.

Saiba mais:

* [Visão geral da Rede Virtual](../virtual-network/virtual-networks-overview.md)
* [Gerenciar servidores DNS usados por uma rede virtual (VNet)](../virtual-network/virtual-network-manage-network.md#dns-servers)

Para a resolução DNS externa, você tem duas opções:

* Hospedar seu próprio servidor DNS externo localmente
* Hospedar seu próprio servidor DNS externo com um provedor de serviços

Muitas organizações de grande porte hospedarão seus próprios servidores DNS localmente. Elas podem fazer isso porque têm competências sobre rede e a presença global para tomar essa decisão.

Na maioria dos casos, é melhor hospedar seus serviços de resolução de nomes DNS em um provedor de serviços. Esses provedores de serviço têm as competências referentes à rede e a presença global para garantir uma disponibilidade muito alta para os serviços de resolução de nomes. A disponibilidade é essencial para os serviços DNS, pois, se os serviços de resolução de nomes falharem, ninguém poderá ter acesso aos seus serviços para a Internet.

O Azure oferece uma solução de DNS externo de alta disponibilidade e alto desempenho na forma do DNS do Azure. Essa solução de resolução de nomes externa aproveita a infraestrutura mundial do DNS do Azure. Ela permite hospedar seu domínio no Azure usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. Como parte do Azure, ela também herda os fortes controles de segurança internos da plataforma.

Saiba mais:

* [Visão geral do DNS do Azure](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>Arquitetura do DMZ
Muitas empresas usam DMZs para segmentar suas redes a fim de criar uma zona de buffer entre a Internet e seus serviços. A parte de DMZ da rede é considerada uma zona de baixa segurança e nenhum ativo de alto valor é colocado nesse segmento da rede. Normalmente, você verá dispositivos de segurança de rede que têm uma interface de rede no segmento de DMZ e outra interface de rede conectada a uma rede com máquinas virtuais e serviços que aceitam conexões de entrada da Internet.

Há diversas variações de design de DMZ, e a decisão de implantar um DMZ, bem como o tipo de DMZ que será usado caso você decida usar um, baseia-se em seus requisitos de segurança de rede.

Saiba mais:

* [Segurança de rede e Serviços de Nuvem da Microsoft](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>Monitoramento e detecção de ameaças

O Azure fornece recursos para ajudar você nessa área-chave com detecção antecipada, monitoramento e a capacidade de coletar e analisar o tráfego de rede.

### <a name="azure-network-watcher"></a>Observador de Rede do Azure
O Observador de Rede do Azure inclui vários recursos que ajudam na solução de problemas, além de fornecerem um novo conjunto de ferramentas que auxiliam na identificação de problemas de segurança.

A [Exibição do Grupo de Segurança](/network-watcher/network-watcher-security-group-view-overview.md) ajuda com a conformidade de segurança e auditoria de Máquinas Virtuais e pode ser usada para a realização de auditorias programáticas comparando as políticas de linhas de base definida pela organização com as regras efetivas de cada uma das VMs. Isso pode ajudar a identificar qualquer descompasso de configuração.

A [captura de pacote](/network-watcher/network-watcher-packet-capture-overview.md) permite que você capture o tráfego de rede que entra e sai da máquina virtual. Além de ajudar permitindo que você colete estatísticas de rede e com a solução de problemas de aplicativo, a captura de pacote pode ser extraordinária na investigação das intrusões de rede. Você também pode usar essa funcionalidade em conjunto com o Azure Functions para iniciar capturas de rede em resposta a alertas específicos do Azure.

Para saber mais sobre o Observador de Rede do Azure e como começar a testar algumas das funcionalidades nos laboratórios, leia a [visão geral do monitoramento do Observador de Rede do Azure](/network-watcher/network-watcher-monitoring-overview.md)

>[!NOTE]
O Observador de Rede do Azure ainda está no modo preview público, de modo que ele pode não ter o mesmo nível de disponibilidade e confiabilidade que os serviços que estão na versão de disponibilidade geral. Determinados recursos podem não ter suporte, podem ter restrição e podem não estar disponíveis em todos os locais do Azure. Para ver as notificações mais recentes sobre disponibilidade e status desse serviço, verifique a [página de atualizações do Azure](https://azure.microsoft.com/updates/?product=network-watcher)

### <a name="azure-security-center"></a>Central de Segurança do Azure
A Central de Segurança ajuda a prevenir, detectar e responder a ameaças e oferece maior visibilidade e controle sobre a segurança de seus recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com um grande conjunto de soluções de segurança.

A Central de Segurança do Azure ajuda a otimizar e a monitorar a segurança da rede:

* Fornecendo recomendações de segurança de rede
* Monitorando o estado de configuração de segurança da rede
* Alertando você contra ameaças baseadas em rede, nos níveis do ponto de extremidade e de rede

Saiba mais:

* [Introdução à Central de Segurança do Azure](../security-center/security-center-intro.md)


### <a name="logging"></a>Registro em log
O log em um nível de rede é uma função essencial em qualquer cenário de segurança de rede. No Azure, é possível registrar as informações obtidas dos Grupos de Segurança de Rede para obter informações de log no nível de rede. Com o log do NSG, você obtém informações dos seguintes:

* [Logs de atividade](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) – usados para exibir todas as operações enviadas às assinaturas do Azure. Esses logs são habilitados por padrão e podem ser usados no portal do Azure. Eles eram anteriormente conhecidos como "Logs de auditoria" ou "Logs operacionais".
* Logs de eventos – esses logs fornecem informações sobre quais regras do NSG foram aplicadas.
* Logs do contador – permitem saber quantas vezes cada regra NSG foi aplicada para negar ou permitir o tráfego.

Você também pode usar o [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), uma ferramenta de visualização de dados avançada, para exibir e analisar esses logs.

Saiba mais:

* [Análise de logs para NSGs (grupos de segurança de rede)](../virtual-network/virtual-network-nsg-manage-log.md)
