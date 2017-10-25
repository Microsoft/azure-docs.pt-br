---
title: "Práticas recomendadas de segurança de rede do Azure | Microsoft Docs"
description: "Este artigo fornece um conjunto de práticas recomendadas de segurança de rede usando recursos internos do Azure."
services: security
documentationcenter: na
author: TomShinder
manager: swadhwa
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2017
ms.author: TomSh
ms.openlocfilehash: 659304937eebb1b2fe6faf019dfef63e1e29bcd4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-network-security-best-practices"></a>Práticas recomendadas de rede do Azure
O Microsoft Azure permite que você conecte dispositivos e máquinas virtuais a outros dispositivos de rede, colocando-os em redes virtuais do Azure. Uma Rede Virtual do Azure é uma construção de rede virtual que permite que você conecte placas de interface de rede virtual a uma rede virtual para permitir a comunicação baseada em TCP/IP entre os dispositivos habilitados para rede. As Máquinas Virtuais do Azure conectadas a uma Rede Virtual do Azure são capazes de se conectar a dispositivos na mesma Rede Virtual do Azure, em Redes Virtuais do Azure diferentes, na Internet ou até mesmo em suas próprias redes locais.

Neste artigo, veremos uma coleção de práticas recomendadas de segurança de rede do Azure. Essas práticas recomendadas derivam da nossa experiência de rede do Azure e da experiência de clientes como você.

Para cada prática recomendada, vamos explicar:

* O que é a prática recomendada
* Por que é ideal habilitar essa prática recomendada
* O que poderá acontecer se você não habilitar a prática recomendada
* Possíveis alternativas à prática recomendada
* Como você pode aprender a habilitar a prática recomendada

Este artigo Práticas recomendadas de segurança de rede do Azure baseia-se em um consenso e nos recursos da plataforma Azure e em conjuntos de recursos tal como existiam no momento em que o artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As práticas recomendadas de segurança de rede do Azure discutidas neste artigo incluem:

* Segmentar logicamente as sub-redes
* Controlar o comportamento de roteamento
* Habilitar o túnel forçado
* Usar dispositivos de rede virtual
* Implantar DMZs para zoneamento de segurança
* Evitar a exposição à Internet por meio de links WAN dedicados
* Otimizar o desempenho e o tempo de atividade
* Usar o balanceamento de carga global
* Desabilitar o acesso RDP para máquinas virtuais do Azure
* Habilitar a Central de Segurança do Azure
* Estender seu datacenter para o Azure

## <a name="logically-segment-subnets"></a>Segmentar logicamente as sub-redes
As [Redes Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-network/) são semelhantes a uma LAN em sua rede local. A ideia por trás de uma Rede Virtual do Azure é que você cria uma única rede baseada em espaço de endereço IP na qual pode colocar suas [Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/). Os espaços de endereço IP privados disponíveis estão nos intervalos de Classe A (10.0.0.0/8), de Classe B (172.16.0.0/12) e de Classe C (192.168.0.0/16).

Semelhante ao que você faria no local, convém segmentar o espaço de endereços maior em sub-redes. Você pode usar os princípios de sub-redes baseados em [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) para criar suas sub-redes.

O roteamento entre sub-redes ocorrerá automaticamente e você não precisa configurar as tabelas de roteamento de forma manual. No entanto, a configuração padrão não contempla nenhum controle de acesso à rede entre as sub-redes criadas na Rede Virtual do Azure. Para criar controles de acesso à rede entre as sub-redes, será necessário colocar algo entre as sub-redes.

Uma das coisas que você pode usar para realizar essa tarefa é um NSG ([Grupo de Segurança de Rede](../virtual-network/virtual-networks-nsg.md)). Os NSGs são dispositivos de inspeção de pacotes com monitoração de estado simples que usam a abordagem de 5 tuplas (o IP de origem, a porta de origem, o IP de destino, a porta de destino e o protocolo de camada 4) para criar regras de permissão/negação para o tráfego de rede. Você pode permitir ou negar o tráfego para e de um único endereço IP, para e de vários endereços IP ou até mesmo de sub-redes inteiras.

O uso de NSGs para controle de acesso à rede entre sub-redes permite que você coloque recursos que pertençam à mesma zona ou função de segurança em suas próprias sub-redes. Pense, por exemplo, em um aplicativo de camada 3 simples que tenha uma camada da Web, uma camada lógica de aplicativo e uma camada de banco de dados. Você coloca máquinas virtuais que pertençam a cada uma dessas camadas em suas próprias sub-redes. Em seguida, usa os NSGs para controlar o tráfego entre as sub-redes:

* As máquinas virtuais da camada Web só podem iniciar conexões para as máquinas de lógica do aplicativo e só podem aceitar conexões da Internet
* As máquinas virtuais de lógica do aplicativo só podem iniciar conexões com a camada de banco de dados e só podem aceitar conexões da camada da Web
* As máquinas virtuais da camada de banco de dados não podem iniciar conexões com nada que esteja fora de sua própria sub-rede e só podem aceitar conexões da camada de lógica do aplicativo

Para saber mais sobre os Grupos de Segurança de Rede e sobre como você pode usá-los para segmentar logicamente suas Redes Virtuais do Azure, leia o artigo [O que é um Grupo de Segurança de Rede](../virtual-network/virtual-networks-nsg.md) (NSG).

## <a name="control-routing-behavior"></a>Controlar o comportamento de roteamento
Quando você colocar uma máquina virtual em uma Rede Virtual do Azure, perceberá que a máquina virtual pode se conectar a outra máquina virtual na mesma Rede Virtual do Azure, mesmo se as outras máquinas virtuais estiverem em sub-redes diferentes. A razão pela qual isso possível é que há uma coleção de rotas do sistema que são habilitadas por padrão e que permitem esse tipo de comunicação. Essas rotas padrão permitem que as máquinas virtuais na mesma Rede Virtual do Azure iniciem conexões entre si e com a Internet (somente para comunicações de saída para a Internet).

Embora as rotas padrão do sistema sejam úteis para muitos cenários de implantação, há ocasiões em que você deseja personalizar a configuração de roteamento para suas implantações. Essas personalizações permitirão que você configure o endereço do próximo salto para atingir destinos específicos.

É recomendável que você configure Rotas Definidas pelo Usuário ao implantar um dispositivo de segurança de rede virtual, sobre o qual falaremos em uma prática recomendada posterior.

> [!NOTE]
> As Rotas Definidas pelo Usuário não são obrigatórias e as rotas de sistema padrão funcionarão na maioria dos casos.
>
>

Você pode aprender mais sobre as Rotas Definidas pelo Usuário e sobre como configurá-las ao ler o artigo [O que são as Rotas Definidas pelo Usuário e o Encaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Habilitar o túnel forçado
Para entender melhor o túnel forçado, convém compreender o que é "um túnel dividido".
O exemplo mais comum de túnel dividido é visto com conexões VPN. Imagine que você estabeleça uma conexão VPN do seu quarto de hotel para sua rede corporativa. Essa conexão permite que você se conecte a recursos em sua rede corporativa e acesse todas as comunicações de recursos em sua rede corporativa por meio do túnel VPN.

O que acontece quando você deseja se conectar aos recursos na Internet? Quando o túnel dividido é habilitado, as conexões são estabelecidas diretamente com a Internet e não por meio do túnel VPN. Alguns especialistas em segurança consideram isso um risco em potencial e, portanto, recomendam que o túnel dividido seja desabilitado e que todas as conexões, as destinadas à Internet e as destinadas a recursos corporativos, passem pelo túnel VPN. A vantagem disso é que as conexões com Internet são forçadas por meio de dispositivos de segurança da rede corporativa, o que não seria o caso se o cliente VPN conectado à Internet estivesse fora do túnel VPN.

Agora vamos voltar para as máquinas virtuais em uma Rede Virtual do Azure. As rotas padrão para uma Rede Virtual do Azure permitem que as máquinas virtuais iniciem o tráfego para a Internet. Isso também pode representar um risco à segurança, já que essas conexões de saída podem aumentar a superfície de ataque de uma máquina virtual e ser aproveitadas por invasores.
Por esse motivo, recomendamos que você habilite o túnel forçado em suas máquinas virtuais quando tiver conectividade entre locais entre sua Rede Virtual do Azure e sua rede local. Falaremos sobre a conectividade entre locais neste documento sobre práticas recomendadas de rede do Azure.

Se você não tiver uma conexão entre locais, aproveite os Grupos de Segurança de Rede (discutidos anteriormente) ou os dispositivos de segurança de rede virtual do Azure (discutidos em seguida) para evitar conexões de saída para Internet de suas Máquinas Virtuais do Azure.

Para saber mais sobre túneis forçados e sobre como habilitá-los, leia o artigo [Configurar o túnel forçado usando o PowerShell e o Azure Resource Manager](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Usar dispositivos de rede virtual
Embora os Grupos de Segurança de Rede e o Roteamento Definido pelo Usuário possam fornecer uma determinada medida de segurança de rede nas camadas de rede e de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), haverá situações em que você vai desejar ou precisar habilitar a segurança em níveis mais altos da pilha. Em tais situações, é recomendável que você implante dispositivos de segurança de rede virtual fornecidos por parceiros do Azure.

Os dispositivos de segurança de rede do Azure podem oferecer níveis consideravelmente aprimorados de segurança em relação ao que é fornecido pelos controles de nível de rede. Alguns dos recursos de segurança de rede fornecidos por dispositivos de segurança de rede virtual incluem:

* Firewall
* Detecção de intrusão/prevenção contra intrusões
* Gerenciamento de vulnerabilidades
* Controle de aplicativo
* Detecção de anomalias baseada em rede
* Filtragem da Web
* Antivírus
* Proteção botnet

Se você precisar de um nível mais alto de segurança de rede que pode obter com controles de acesso no nível de rede, é recomendável que você investigue e implante os dispositivos de segurança de rede virtual do Azure.

Para saber mais sobre quais dispositivos de segurança de rede virtual do Azure estão disponíveis e sobre seus recursos, visite o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procure "security" e "segurança de rede".

## <a name="deploy-dmzs-for-security-zoning"></a>Implantar DMZs para zoneamento de segurança
Uma DMZ, ou “rede de perímetro”, é um segmento de rede lógico ou físico projetado para fornecer uma camada adicional de segurança entre seus ativos e a Internet. A intenção da DMZ é colocar dispositivos de controle de acesso à rede especializados na borda da rede DMZ de modo que apenas o tráfego desejado possa passar do dispositivo de segurança de rede e para sua Rede Virtual do Azure.

As DMZs são úteis porque você pode concentrar o gerenciamento de controle de acesso à rede, o monitoramento, a geração de logs e os relatórios nos dispositivos na borda da sua Rede Virtual do Azure. Aqui, você normalmente habilitaria a prevenção de DDoS, os sistemas de Detecção de Intrusão/Prevenção contra Intrusão (IDS/IPS), as regras e políticas de firewall, a filtragem da Web, a rede antimalware e muito mais. Os dispositivos de segurança de rede ficam entre a Internet e sua Rede Virtual do Azure e tem uma interface em ambas as redes.

Embora esse seja o design básico de uma DMZ, existem muitos designs de DMZ diferentes, como back-to-back, tri-homed, multi-homed e outros.

É recomendável para todas as implantações de alta segurança que você considere a implantação de uma DMZ para aumentar o nível de segurança de rede para os recursos do Azure.

Para saber mais sobre DMZs e como implantá-las no Azure, leia o artigo [Segurança de rede e serviços do Microsoft Cloud](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitar a exposição à Internet por meio de links WAN dedicados
Muitas organizações escolheram a rota de TI Híbrida. Na TI híbrida, alguns dos ativos de informação da empresa estão no Azure, enquanto outros permanecem no local. Em muitos caso,s alguns componentes de um serviço estarão em execução no Azure, enquanto outros componentes permanecem no local.

No cenário de TI híbrida, geralmente há algum tipo de conectividade entre locais. Essa conectividade entre locais entre locais permite que a empresa conecte suas redes locais a Redes Virtuais do Azure. Há duas soluções de conectividade entre locais disponíveis:

* VPN site a site
* ExpressRoute

A [VPN site a site](../vpn-gateway/vpn-gateway-site-to-site-create.md) representa uma conexão privada virtual entre sua rede local e uma Rede Virtual do Azure. Essa conexão ocorre pela Internet e permite que você envie suas informações por um "túnel" dentro de um link criptografado entre sua rede e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. A criptografia de túnel é realizada usando o [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Embora a VPN site a site seja uma tecnologia confiável e estabelecida, o tráfego no túnel não atravessa a Internet. Além disso, a largura de banda é relativamente restrita a um máximo de aproximadamente 200 Mbps.

Se você precisar de um nível excepcional de segurança ou de desempenho para as conexões entre locais, recomendamos que você use a Azure ExpressRoute para sua conectividade entre locais. O ExpressRoute é um link WAN dedicado entre seu local ou um provedor de hospedagem do Exchange. Como essa é uma conexão de telecomunicações, seus dados não viajam pela Internet e, portanto, não são expostos aos potenciais riscos de comunicações pela Internet.

Para saber mais sobre o funcionamento da ExpressRoute do Azure e sobre como implantar, leia o artigo [Visão geral técnica da ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Otimizar o desempenho e o tempo de atividade
Confidencialidade, integridade e disponibilidade (CIA) formam a tríade do modelo de segurança mais influente de hoje. A confidencialidade está relacionada a criptografia e a privacidade, a integridade está relacionada a garantir que os dados não sejam alterados por pessoas não autorizadas e a disponibilidade está relacionada a garantir que indivíduos autorizados sejam capazes de acessar as informações que estão autorizados a acessar. A falha em qualquer uma dessas áreas representa uma possível violação de segurança.

A disponibilidade pode ser pensada como relacionada a tempo de atividade e ao desempenho. Se um serviço estiver inativo, as informações não poderão ser acessadas. Se o desempenho for tão ruim que inutilize os dados, poderemos considerar os dados como inacessíveis. Portanto, de uma perspectiva de segurança, precisamos fazer tudo o que podemos para nos certificarmos de que nossos serviços tenham o desempenho e o tempo de atividade ideais.
Um método popular e eficaz usado para melhorar a disponibilidade e o desempenho é usar o balanceamento de carga. O balanceamento de carga é um método de distribuição de tráfego de rede entre servidores que fazem parte de um serviço. Por exemplo, se você tiver servidores Web de front-end como parte de seu serviço, poderá usar o balanceamento de carga para distribuir o tráfego entre vários servidores Web de front-end.

Essa distribuição de tráfego aumenta a disponibilidade porque se um dos servidores Web ficar indisponível, o balanceador de carga interromperá o envio de tráfego para esse servidor e redirecionará o tráfego para os servidores que ainda estiverem online. O balanceamento de carga também ajuda no desempenho porque a sobrecarga do processador, da rede e da memória para atender às solicitações é distribuída por todos os servidores com carga balanceada.

É recomendável implantar o balanceamento de carga sempre que possível e conforme apropriado para seus serviços. Vamos abordar a adequação nas seções a seguir.
No nível da Rede Virtual do Azure, o Azure fornece três opções principais de balanceamento de carga:

* Balanceamento de carga baseado em HTTP
* Balanceamento de carga externo
* Balanceamento de carga interno

## <a name="http-based-load-balancing"></a>Balanceamento de carga baseado em HTTP
O balanceamento de carga baseado em HTTP baseia as decisões sobre para qual servidor serão enviadas conexões usando características do protocolo HTTP. O Azure tem um balanceador de carga de HTTP que passa pelo nome do Application Gateway.

É recomendável que você use o Gateway de Aplicativo do Azure quando:

* Os aplicativos que exijam solicitações da mesma sessão de usuário/cliente para acessar a mesma máquina virtual de back-end. Exemplos disso são os aplicativos de carrinho de compras e servidores de email na Web.
* Os aplicativos que desejam liberar os farms de servidores Web da sobrecarga da terminação SSL ao tirar proveito do recurso [descarregamento SSL](https://f5.com/glossary/ssl-offloading) do Gateway de Aplicativo.
* Aplicativos, como uma rede de distribuição de conteúdo, que exigem que várias solicitações HTTP na mesma conexão TCP de execução longa sejam roteadas ou balanceadas por carga para diferentes servidores back-end.

Para saber mais sobre como funciona o Gateway de Aplicativo do Azure e como usá-lo em suas implantações, leia o artigo [Visão geral do Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Balanceamento de carga externo
O balanceamento de carga externo ocorre quando as conexões de entrada da Internet têm a carga balanceada entre os servidores localizados em uma Rede Virtual do Azure. O balanceador de carga externo do Azure pode fornecer essa capacidade e é recomendável usá-lo quando você não exigir as sessões temporárias ou o descarregamento de SSL.

Em contraste ao balanceamento de carga baseado em HTTP, o balanceador de carga externo usa as informações nas camadas de transporte e de rede do modelo de rede OSI para tomar decisões sobre para qual servidor a conexão terá balanceamento de carga.

É recomendável que você use o balanceamento de carga externo sempre que houver [aplicativos sem monitoração de estado](http://whatis.techtarget.com/definition/stateless-app) que aceitem solicitações de entrada da Internet.

Para saber mais sobre como funciona o Balanceador Externo de Carga do Azure e sobre como você pode implantá-lo, leia o artigo [Introdução à criação de um balanceador de carga voltado para Internet no Resource Manager usando o PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Balanceamento de carga interno
O balanceamento de carga interno é semelhante ao balanceamento de carga externo e usa o mesmo mecanismo para conexões com balanceamento de carga para os servidores por trás delas. A única diferença é que o balanceador de carga nesse caso está aceitando conexões de máquinas virtuais que não estejam na Internet. Na maioria dos casos, as conexões aceitas para balanceamento de carga são iniciadas por dispositivos em uma Rede Virtual do Azure.

É recomendável que você use o balanceamento de carga interno para cenários que irão se beneficiar desse recurso, como quando for necessário balancear a carga de conexões para SQL Servers ou para servidores Web internos.

Para saber mais sobre o funcionamento do Balanceamento de Carga Interno do Azure e como você pode implantá-lo, leia o artigo [Introdução à criação de um balanceador de carga usando o PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## <a name="use-global-load-balancing"></a>Usar o balanceamento de carga global
A computação de nuvem pública possibilita a implantação de aplicativos globalmente distribuídos com componentes localizados em datacenters no mundo todo. Isso é possível no Microsoft Azure devido à presença do datacenter global do Azure. Em contraste com a tecnologias de balanceamento de carga mencionadas anteriormente, o balanceamento de carga global possibilita disponibilizar serviços mesmo quando datacenters inteiros se tornem indisponíveis.

Você pode obter esse tipo de balanceamento de carga global no Azure ao aproveitar as vantagens do [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/). O Gerenciador de Tráfego faz é possível carregar conexões de saldo de serviços com base no local do usuário.

Por exemplo, se o usuário estiver fazendo uma solicitação ao serviço da União Europeia, a conexão será direcionada para os serviços localizados em um datacenter da União Europeia. Esta parte do balanceamento de carga global do Gerenciador de Tráfego ajuda a melhorar o desempenho porque a conexão com o datacenter mais próximo é mais rápida do que conectar-se a data centers que estejam distantes.

Pelo lado da disponibilidade, o balanceamento de carga global garante que o serviço esteja disponível mesmo se um datacenter inteiro ficar indisponível.

Por exemplo, se um datacenter do Azure ficar indisponível por motivos ambientais ou devido a interrupções (tais como falhas de rede regional), as conexões ao serviço seriam redirecionadas para o datacenter online mais próximo. Esse balanceamento de carga global é realizado tirando proveito das políticas de DNS que você pode criar no Gerenciador de Tráfego.

É recomendável que você use o Gerenciador de Tráfego para desenvolver qualquer solução de nuvem que tenha um escopo amplamente distribuído entre várias regiões e que exija o nível mais alto de tempo de atividade possível.

Para saber mais sobre o Gerenciador de Tráfego e sobre como implantá-lo, leia o artigo [O que é o Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Desabilitar o acesso RDP/SSH para Máquinas Virtuais do Azure
É possível acessar as Máquinas Virtuais do Azure usando o protocolo RDP ([Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol)) e os protocolos SSH ([Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell)). Esses protocolos permitem gerenciar máquinas virtuais de locais remotos e são padrão na computação do datacenter.

O potencial problema de segurança com o uso desses protocolos pela Internet é que os invasores podem usar diversas técnicas de [força bruta](https://en.wikipedia.org/wiki/Brute-force_attack) para obter acesso às Máquinas Virtuais do Azure. Depois que os invasores obtiverem acesso, eles poderão usar sua máquina virtual como um ponto de partida para comprometer outros computadores na sua Rede Virtual do Azure ou até mesmo para atacar dispositivos de rede fora do Azure.

Por isso, recomendamos que você desabilite o acesso direito RDP e SSH para Máquinas Virtuais do Azure da Internet. Depois que o acesso direito RDP e SSH da Internet estiver desabilitado, você terá outras opções que poderá usar para acessar essas máquinas virtuais para gerenciamento remoto:

* VPN ponto a site
* VPN site a site
* ExpressRoute

A [VPN ponto a site](../vpn-gateway/vpn-gateway-point-to-site-create.md) é outro termo para uma conexão de cliente/servidor de VPN de acesso remoto. Uma VPN ponto a site permite que um único usuário se conecte a uma Rede Virtual do Azure pela Internet. Depois que a conexão ponto a site for estabelecida, o usuário será capaz de usar o RDP ou o SSH para se conectar a máquinas virtuais localizadas na Rede Virtual do Azure às quais o usuário se conectou via VPN ponto a site. Isso pressupõe que o usuário esteja autorizado a acessar essas máquinas virtuais.

A VPN ponto a site é mais segura do que conexões diretas RDP ou SSH porque o usuário precisa se autenticar duas vezes antes de se conectar a uma máquina virtual. Primeiro, o usuário precisa se autenticar (e ser autorizado) para estabelecer a conexão de VPN ponto a site; depois, o usuário precisa se autenticar (e ser autorizado) para estabelecer a sessão RDP ou SSH.

Uma [VPN site a site](../vpn-gateway/vpn-gateway-site-to-site-create.md) conecta uma rede inteira a outra rede pela Internet. Você pode usar uma VPN site a site para conectar sua rede local a uma Rede Virtual do Azure. Se você implantar uma VPN site a site, os usuários em sua rede local poderão se conectar a máquinas virtuais em sua Rede Virtual do Azure usando o protocolo RDP ou SSH pela conexão VPN site a site e não exigirão que você permita o acesso direito RDP ou SSH pela Internet.

Você também pode usar um link WAN dedicado para fornecer funcionalidade semelhante à VPN site a site. As diferenças principais são 1. o link WAN dedicado não percorre a Internet e 2. os links WAN dedicados são geralmente mais estáveis e com maior desempenho. O Azure oferece uma solução de link WAN dedicado na forma de [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Habilitar a Central de Segurança do Azure
A Central de Segurança do Azure ajuda você a impedir, detectar e responder a ameaças e oferece maior visibilidade e controle sobre a segurança dos recursos do Azure Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

A Central de Segurança do Azure ajuda a otimizar e a monitorar a segurança da rede:

* Fornecendo recomendações de segurança de rede
* Monitorando o estado de configuração de segurança da rede
* Alertando você contra ameaças baseadas em rede, nos níveis do ponto de extremidade e de rede

É altamente recomendável que você habilite a Central de Segurança do Azure para todas as implantações do Azure.

Para saber mais sobre a Central de Segurança do Azure e sobre como habilitá-la para suas implantações, leia o artigo [Introdução à Central de Segurança do Azure](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Estenda com segurança seu datacenter para o Azure
Muitas organizações de TI estão procurando se expandir para a nuvem em vez de ampliarem seus datacenters locais. Essa expansão representa uma extensão da infraestrutura de TI existente para a nuvem pública. Ao aproveitar a vantagem das opções de conectividade entre locais, é possível tratar suas Redes Virtuais do Azure como qualquer outra sub-rede na sua infraestrutura de rede local.

No entanto, há muitos problemas de planejamento e de design que precisam ser abordados primeiro. Isso é especialmente importante na área de segurança de rede. Uma das melhores maneiras de entender como abordar esse design é ver um exemplo.

A Microsoft criou o [Diagrama de arquitetura de referência de extensão do datacenter](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) e material de apoio para ajudar você a entender a aparência de uma extensão do datacenter. Isso fornece um exemplo de implementação de referência que você pode usar para planejar e projetar uma extensão segura do datacenter corporativo para a nuvem. É recomendável que você leia este documento para ter uma ideia dos principais componentes de uma solução segura.

Para saber mais sobre como estender com segurança seu data center para o Azure, veja o vídeo [Extensão do seu datacenter para o Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
