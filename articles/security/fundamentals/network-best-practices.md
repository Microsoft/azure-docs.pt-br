---
title: Melhores práticas de segurança de rede – Microsoft Azure
description: Este artigo fornece um conjunto de práticas recomendadas de segurança de rede usando recursos internos do Azure.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 4793216a12b17c4e4ea03f62d5a0ba512febc232
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735719"
---
# <a name="azure-best-practices-for-network-security"></a>Melhores práticas para segurança de rede do Azure
Este artigo descreve uma coleção de melhores práticas do Azure para aprimorar a segurança de rede. Essas práticas recomendadas derivam da nossa experiência de rede do Azure e da experiência de clientes como você.

Para cada prática recomendada, este artigo explica:

* O que é a prática recomendada
* Por que é ideal habilitar essa prática recomendada
* O que poderá acontecer se você não habilitar a prática recomendada
* Possíveis alternativas à prática recomendada
* Como você pode aprender a habilitar a prática recomendada

Essas melhores práticas são baseadas em um consenso e nos recursos da plataforma Azure e em conjuntos de recursos tal como existiam no momento em que o artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="use-strong-network-controls"></a>Usar controles de rede fortes
Você pode conectar [máquinas virtuais (VMs) do Azure](https://azure.microsoft.com/services/virtual-machines/) e dispositivos a outros dispositivos em rede, colocando-os em [redes virtuais do Azure](../../virtual-network/index.yml). Ou seja, você pode conectar cartões de interface de rede virtual a uma rede virtual para permitir comunicações baseadas em TCP / IP entre dispositivos habilitados para rede. Máquinas virtuais conectadas a uma rede virtual do Azure podem se conectar a dispositivos na mesma rede virtual, em diferentes redes virtuais, na Internet ou em suas próprias redes locais.

Conforme você planeja sua rede e a segurança da rede, recomendamos centralizar:

- Gerenciamento das principais funções de rede como ExpressRoute, provisionamento de rede e sub-rede virtual e endereçamento IP.
- Governança de elementos de segurança de rede, por exemplo, funções de solução de virtualização de rede, como ExpressRoute, provisionamento de rede e sub-rede virtual e endereçamento IP.

Se você usar um conjunto comum de ferramentas de gerenciamento para monitorar sua rede e a segurança da rede, terá visibilidade clara em ambos. Uma estratégia de segurança simples e unificada reduz os erros, pois aumenta a compreensão humana e a confiabilidade na automação.

## <a name="logically-segment-subnets"></a>Segmentar logicamente as sub-redes
As redes virtuais do Azure são semelhantes a LANs em sua rede local. A ideia por trás de uma rede virtual do Azure é que você crie uma única rede baseada em espaço de endereço IP privado na qual é possível colocar todas as suas máquinas virtuais do Azure. Os espaços de endereço IP privados disponíveis estão nos intervalos de Classe A (10.0.0.0/8), de Classe B (172.16.0.0/12) e de Classe C (192.168.0.0/16).

As práticas recomendadas para segmentar logicamente sub-redes incluem:

**Melhor prática**: não atribua regras de permissão com intervalos amplos (por exemplo, permita 0.0.0.0 até 255.255.255.255).  
**Detalhe**: verifique se os procedimentos de solução de problemas desencorajam ou proíbem a configuração desses tipos de regras. As regras de permissão levam a uma falsa sensação de segurança e são encontradas e exploradas com frequência por equipes vermelhas.

**Melhor prática**: Segmentar o espaço de endereço maior em sub-redes.   
**Detalhe**: Use princípios de sub-redes baseados no [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) para criar suas sub-redes.

**Melhor prática**: Criar controles de acesso à rede entre sub-redes. O roteamento entre sub-redes acontece automaticamente, e você não precisa configurar manualmente as tabelas de roteamento. Por padrão, não há controles de acesso de rede entre sub-redes criadas na rede virtual do Azure.   
**Detalhe**: use um [grupo de segurança de rede](../../virtual-network/virtual-network-vnet-plan-design-arm.md) para proteger contra tráfego não solicitado em sub-redes do Azure. Os grupos de segurança de rede são dispositivos de inspeção de pacotes simples e monitorados que usam a abordagem quíntupla (IP de origem, porta de origem, IP de destino, porta de destino e protocolo de quatro camadas) para criar regras de permissão/negação para tráfego de rede. Você permite ou nega o tráfego de e para um único endereço IP, de e para vários endereços IP ou para e de sub-redes inteiras.

Quando você usa grupos de segurança de rede para controle de acesso de rede entre sub-redes, é possível colocar recursos que pertencem à mesma zona ou função de segurança nas sub-redes deles.

**Melhor prática**: evite redes e sub-redes virtuais pequenas para garantir simplicidade e flexibilidade.   
**Detalhe**: a maioria das organizações adiciona mais recursos do que o planejado inicialmente e a realocação de endereços é muito trabalhoso. O uso de sub-redes pequenas adiciona um valor de segurança limitado, e o mapeamento de um grupo de segurança de rede para cada sub-rede adiciona sobrecarga. Defina sub-redes em larga escala para garantir que você tenha flexibilidade para o crescimento.

**Melhor prática**: simplifique o gerenciamento de regras do grupo de segurança de rede definindo [Grupos de segurança de aplicativo](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Detalhe**: defina um Grupo de segurança de aplicativo para listas de endereços IP que podem ser alterados no futuro ou usados em vários grupos de segurança de rede. Nomeie Grupos de segurança de aplicativo claramente para que outras pessoas entendam o conteúdo e a finalidade.

## <a name="adopt-a-zero-trust-approach"></a>Adotar uma abordagem de Confiança Zero
As redes baseadas em perímetro funcionam com a suposição de que todos os sistemas em uma rede são confiáveis. Mas os funcionários de hoje acessam os recursos de sua organização de qualquer lugar em vários dispositivos e aplicativos, o que torna os controles de segurança de perímetro irrelevantes. As políticas de controle de acesso que se concentram apenas em quem pode acessar um recurso não são suficientes. Para dominar o equilíbrio entre segurança e produtividade, os administradores de segurança também precisam considerar *como* um recurso está sendo acessado.

As redes precisam evoluir de defesas tradicionais, pois podem estar vulneráveis a violações: um invasor pode comprometer um único ponto de extremidade dentro do limite confiável e, em seguida, expandir rapidamente uma base em toda a rede. As redes com [Confiança Zero](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) eliminam o conceito de confiança com base no local de rede em um perímetro. Em vez disso, as arquiteturas de Confiança Zero usam declarações de confiança do dispositivo e usuário para ter acesso a dados e recursos organizacionais. Para novas iniciativas, adote abordagens de Confiança Zero que validam a confiança no momento do acesso.

As melhores práticas são:

**Melhor prática**: conceda acesso condicional a recursos com base em dispositivo, identidade, garantia, local de rede e muito mais.  
**Detalhe**: o [Acesso condicional do Azure AD](../../active-directory/conditional-access/overview.md) permite aplicar os controles de acesso certos implementando decisões de controle de acesso automatizadas baseadas nas condições necessárias. Para saber mais, confira [Administrar o acesso ao gerenciamento do Azure com acesso condicional](../../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).

**Melhor prática**: habilitar o acesso à porta somente após a aprovação do fluxo de trabalho.  
**Detalhe**: você pode usar o [acesso VM just-in-time na Central de Segurança do Azure](../../security-center/security-center-just-in-time.md) para bloquear o tráfego de entrada nas VMs do Azure, reduzindo a exposição a ataques, enquanto fornece fácil acesso para conectar às VMs quando necessário.

**Melhor prática**: conceder permissões temporárias para executar tarefas privilegiadas, para impedir que usuários mal-intencionados ou não autorizados obtenham acesso após a expiração das permissões. O acesso é concedido somente quando os usuários precisam dele.  
**Detalhe**: use o acesso just-in-time no Azure AD Privileged Identity Management ou em uma solução de terceiros para conceder permissões para executar tarefas privilegiadas.

Confiança Zero é a próxima evolução na segurança de rede. O estado dos ataques cibernéticos orienta as organizações a ter uma mentalidade de "assumir a violação", mas essa abordagem não deve ser limitada. As redes de Confiança Zero protegem dados e recursos corporativos, garantindo que as organizações possam criar um local de trabalho moderno usando tecnologias que capacitam os funcionários a serem produtivos a qualquer momento, em qualquer lugar.

## <a name="control-routing-behavior"></a>Controlar o comportamento de roteamento
Quando você coloca uma máquina virtual em uma rede virtual do Azure, a VM pode se conectar a qualquer outra VM na mesma rede virtual, mesmo se as outras VMs estiverem em sub-redes diferentes. Isso é possível porque uma coleção de rotas do sistema ativadas por padrão permite esse tipo de comunicação. Essas rotas padrão permitem que as VMs na mesma rede virtual iniciem conexões umas com as outras e com a Internet (somente para comunicações de saída com a Internet).

Embora as rotas do sistema padrão sejam úteis para muitos cenários de implantação, há momentos em que você deseja personalizar a configuração de roteamento para suas implantações. Você pode configurar o endereço do próximo salto para alcançar destinos específicos.

Recomendamos que você configure [rotas definidas pelo usuário](../../virtual-network/virtual-networks-udr-overview.md) ao implantar um dispositivo de segurança para uma rede virtual. Podemos falar sobre isso em uma seção posterior intitulada [proteger seus recursos essenciais do serviço do Azure para apenas suas redes virtuais](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Rotas definidas pelo usuário não são obrigatórias e as rotas de sistema padrão funcionam normalmente.
>
>

## <a name="use-virtual-network-appliances"></a>Usar dispositivos de rede virtual
Os grupos de segurança de rede e o roteamento definido pelo usuário podem fornecer uma certa medida de segurança na rede e transportar camadas do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model). Mas, em algumas situações, você quer ou precisa ativar a segurança em níveis altos da pilha. Em tais situações, é recomendável que você implante dispositivos de segurança de rede virtual fornecidos por parceiros do Azure.

Os dispositivos de segurança de rede do Azure podem oferecer uma segurança melhor do que os controles no nível da rede. Os recursos de segurança de rede dos dispositivos de segurança de rede virtual incluem:


* Firewall
* Prevenção de intrusão/detecção de intrusões
* Gerenciamento de vulnerabilidades
* Controle de aplicativo
* Detecção de anomalias baseada em rede
* Filtragem da Web
* Antivírus
* Proteção botnet

Para encontrar dispositivos de segurança de rede virtual do Azure disponíveis, vá para [Azure Marketplace](https://azure.microsoft.com/marketplace/) e pesquise por "segurança" e "segurança de rede".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Implantação de redes de perímetro para zonas de segurança
Uma [rede de perímetro](/azure/architecture/vdc/networking-virtual-datacenter) (também conhecida como DMZ) é um segmento de rede físico ou lógico que fornece uma camada adicional de segurança entre seus ativos e a Internet. Os dispositivos especializados de controle de acesso à rede na extremidade de uma rede de perímetro permitem apenas o tráfego desejado na sua rede virtual.

As redes de perímetro são úteis porque você pode concentrar seu gerenciamento, monitoramento, registro e geração de relatórios de controle de acesso de rede nos dispositivos na borda da rede virtual do Azure. Uma rede de perímetro é onde você normalmente ativa a prevenção de DDoS (negação de serviço distribuída), sistemas de detecção de invasão/prevenção de invasão (IDS/IPS), regras e políticas de firewall, filtragem da Web, antimalware de rede e muito mais. Os dispositivos de segurança de rede ficam entre a Internet e sua rede virtual do Azure e têm uma interface nas duas redes.

Embora esse seja o design básico de uma rede de perímetro, há muitos designs diferentes, como back-to-back, tri-homed e multihomed.

Com base no conceito Confiança Zero mencionado antes, recomendamos considerar o uso de uma rede de perímetro para todas as implantações de segurança alta para melhorar o nível de segurança da rede e o controle de acesso de seus recursos do Azure. Você pode usar o Azure ou uma solução de terceiros para fornecer uma camada de segurança adicional entre seus ativos e a internet:

- Controles nativos do Azure. O [Firewall do Azure](../../firewall/overview.md) e o [firewall do aplicativo Web no Gateway de Aplicativo](../../application-gateway/features.md#web-application-firewall) oferecem segurança básica com um firewall totalmente monitorado como um serviço, alta disponibilidade integrada, escalabilidade de nuvem irrestrita, filtragem de FQDN, suporte para conjuntos de regras principais OWASP, além de configuração e instalação simples.
- Ofertas de terceiros. Pesquise o [Azure Marketplace](https://azuremarketplace.microsoft.com/) para o NGFW (firewall de próxima geração) e outras ofertas de terceiros que fornecem ferramentas de segurança conhecidas e níveis de segurança de rede significativamente aprimorados. A configuração pode ser mais complexa, mas uma oferta de terceiros pode permitir que você use funcionalidades e habilidades existentes.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitar a exposição à Internet por meio de links WAN dedicados
Muitas organizações escolheram a rota de TI híbrida. Com a TI híbrida, alguns dos ativos de informação da empresa estão no Azure e outros permanecem no local. Em muitos casos, alguns componentes de um serviço estão sendo executados no Azure, enquanto outros componentes permanecem no local.

Em um cenário de TI híbrida, geralmente há algum tipo de conectividade entre locais. A conectividade entre instalações permite que a empresa conecte suas redes locais às redes virtuais do Azure. Duas soluções de conectividade entre locais estão disponíveis:

* [VPN site a site](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). É uma tecnologia estabelecida e confiável, mas a conexão ocorre pela Internet. A largura de banda é limitada a um máximo de aproximadamente 1,25 Gbps. O VPN site a site é uma opção desejável em alguns cenários.
* **Azure ExpressRoute**. é recomendável usar o [ExpressRoute](../../expressroute/expressroute-introduction.md) para a conectividade entre locais. O ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de conexão privada facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como Azure, Microsoft 365 e Dynamics 365. O ExpressRoute é um link WAN dedicado entre seu local ou um provedor de hospedagem do Microsoft Exchange. Como essa é uma conexão de telecomunicação, seus dados não trafegam pela Internet. Portanto, não estão expostos aos possíveis riscos das comunicações pela Internet.

O local da conexão do ExpressRoute pode afetar a capacidade do firewall, a escalabilidade, a confiabilidade e a visibilidade do tráfego de rede. Você precisará identificar onde terminar o ExpressRoute em redes existentes (locais). Você pode:

- Terminar fora do firewall (o paradigma de rede de perímetro) se precisar de visibilidade no tráfego, se precisar continuar uma prática existente de isolar datacenters ou se estiver colocando apenas recursos de extranet no Azure.
- Terminar dentro do firewall (o paradigma de extensão de rede). Essa é a recomendação padrão. Em todos os outros casos, recomendamos tratar o Azure como um enésimo datacenter.

## <a name="optimize-uptime-and-performance"></a>Otimizar o desempenho e o tempo de atividade
Se um serviço estiver inativo, as informações não poderão ser acessadas. Se o desempenho é tão ruim que os dados não podem ser usados, você pode considerar os dados como inacessíveis. Do ponto de vista de segurança, você precisa fazer o que puder para garantir que seus serviços tenham tempo de atividade e desempenho ideais.

Um método popular e eficaz para melhorar a disponibilidade e o desempenho é o balanceamento de carga. O balanceamento de carga é um método de distribuição de tráfego de rede entre servidores que fazem parte de um serviço. Por exemplo, se você tiver servidores Web de front-end como parte de seu serviço, poderá usar o balanceamento de carga para distribuir o tráfego entre vários servidores Web de front-end.

Essa distribuição de tráfego aumenta a disponibilidade porque, se um dos servidores Web ficar indisponível, o balanceador de carga interromperá o tráfego de envio para esse servidor e redirecionará o tráfego para os servidores que ainda estiverem online. O balanceamento de carga também ajuda no desempenho porque a sobrecarga do processador, da rede e da memória para atender às solicitações é distribuída por todos os servidores com carga balanceada.

É recomendável implantar o balanceamento de carga sempre que possível e conforme apropriado para seus serviços. A seguir estão os cenários no nível da rede virtual do Azure e no nível global, juntamente com as opções de balanceamento de carga para cada um.

**Cenário**: você tem um aplicativo que:

- Requer solicitações da mesma sessão de usuário / cliente para alcançar a mesma máquina virtual de back-end. Exemplos disso são aplicativos de carrinho de compras e servidores de webmail.
- Aceita apenas uma conexão segura, portanto, a comunicação não criptografada com o servidor não é uma opção aceitável.
- Exige que várias solicitações HTTP na mesma conexão TCP de longa duração sejam roteadas ou carregadas com balanceamento para diferentes servidores de back-end.

**Opção de balanceamento de carga**: use o [Gateway de Aplicativo do Azure](../../application-gateway/overview.md), um balanceador de carga de tráfego da Web HTTP. O Gateway de Aplicativo suporta criptografia TSL de ponta a ponta e [terminação TSL](../../application-gateway/overview.md) no gateway. Os servidores da Web podem então ser aliviados da sobrecarga de criptografia e descriptografia e do fluxo de tráfego não criptografado para os servidores de back-end.

**Cenário**: você precisa balancear a carga das conexões de entrada da Internet entre seus servidores localizados em uma rede virtual do Azure. Os cenários são quando você:

- Tiver aplicativos sem monitoração de estado que aceitam solicitações de entrada da internet.
- Não precisa de sessões temporárias ou descarregamento TSL. As sessões temporárias é um método usado com balanceamento de carga do aplicativo, para alcançar a afinidade do servidor.

**Opção de balanceamento de carga**: use o portal do Azure para [criar um balanceador de carga externo](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) que distribua solicitações de entrada em várias VMs para fornecer um nível mais alto de disponibilidade.

**Cenário**: é necessário balancear a carga de conexões de VMs que não estão na Internet. Na maioria dos casos, as conexões aceitas para balanceamento de carga são iniciadas por dispositivos em uma rede virtual do Azure, como instâncias do SQL Server ou servidores da Web internos.   
**Opção de balanceamento de carga**: use o portal do Azure para [criar um balanceador de carga interno](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) que distribua solicitações de entrada em várias VMs para fornecer um nível mais alto de disponibilidade.

**Cenário**: você precisa de balanceamento de carga global porque:

- Tenha uma solução em nuvem amplamente distribuída em várias regiões e que exija o mais alto nível de disponibilidade (disponibilidade) possível.
- É necessário o mais alto nível de tempo de atividade possível para garantir que seu serviço esteja disponível mesmo se um datacenter inteiro ficar indisponível.

**Opção de balanceamento de carga**: Use o Gerenciador de Tráfego do Azure. O Gerenciador de Tráfego possibilita o balanceamento de carga das conexões para seus serviços com base na localização do usuário.

Por exemplo, se o usuário fizer uma solicitação ao seu serviço a partir da UE, a conexão será direcionada para os serviços localizados em um datacenter da UE. Esta parte do balanceamento de carga global do Gerenciador de Tráfego ajuda a melhorar o desempenho porque a conexão com o datacenter mais próximo é mais rápida do que conectar-se a data centers que estejam distantes.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Desativar o acesso RDP / SSH às máquinas virtuais
É possível alcançar máquinas virtuais do Azure usando protocolo [RDP](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (Remote Desktop Protocol) e [SSH](https://en.wikipedia.org/wiki/Secure_Shell) (Secure Shell). Esses protocolos permitem o gerenciamento de VMs de locais remotos e são padrão na computação de data center.

O problema de segurança em potencial ao usar esses protocolos pela Internet é que os invasores podem usar as técnicas de [força bruta](https://en.wikipedia.org/wiki/Brute-force_attack) para obter acesso às máquinas virtuais do Azure. Depois que os invasores tiverem acesso, eles poderão usar sua VM como ponto de partida para comprometer outras máquinas em sua rede virtual ou até mesmo atacar dispositivos em rede fora do Azure.

Recomendamos que você desabilite o acesso RDP e SSH direto às máquinas virtuais do Azure a partir da Internet. Depois que o acesso RDP e SSH direto da Internet for desativado, você terá outras opções que podem ser usadas para acessar essas VMs para gerenciamento remoto.

**Cenário**: permite que um único usuário se conecte a uma rede virtual do Azure pela Internet.   
**Opção**: A [VPN ponto a site](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) é outro termo para uma conexão de cliente/servidor de VPN de acesso remoto. Depois que a conexão ponto a ponto é estabelecida, o usuário pode usar RDP ou SSH para se conectar a qualquer VM localizada na rede virtual do Azure à qual o usuário se conectou por meio de VPN ponto a ponto. Isso pressupõe que o usuário está autorizado a alcançar essas VMs.

A VPN ponto-a-ponto é mais segura do que as conexões RDP ou SSH diretas porque o usuário precisa autenticar duas vezes antes de se conectar a uma VM. Primeiro, o usuário precisa autenticar (e ser autorizado) para estabelecer a conexão VPN ponto-a-ponto. Segundo, o usuário precisa autenticar (e ser autorizado) para estabelecer a sessão RDP ou SSH.

**Cenário**: permite que os usuários em sua rede local se conectem a VMs em sua rede virtual do Azure.   
**Opção**: Uma [VPN site a site](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) conecta uma rede inteira a outra rede pela Internet. Você pode usar uma VPN site a site para conectar sua rede local a uma rede virtual do Azure. Os usuários em sua rede local se conectam usando o protocolo RDP ou SSH pela conexão VPN site a site. Você não precisa permitir acesso RDP ou SSH direto pela Internet.

**Cenário**: use um link WAN dedicado para fornecer funcionalidade semelhante à VPN site a site.   
**Opção**: Use [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Ele fornece uma funcionalidade semelhante à VPN site a site. As principais diferenças são:

- O link WAN dedicado não atravessa a Internet.
- Os links de WAN dedicados geralmente são mais estáveis e apresentam melhor desempenho.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Proteja seus recursos de serviço críticos do Azure somente para suas redes virtuais
Use o link privado do Azure para acessar os serviços de PaaS do Azure (por exemplo, armazenamento do Azure e banco de dados SQL) em um ponto de extremidade privado em sua rede virtual. Os pontos de extremidade privados permitem que você proteja seus recursos críticos de serviço do Azure apenas para suas redes virtuais. O tráfego da sua rede virtual para o serviço do Azure sempre permanece na rede de backbone do Microsoft Azure. Expor sua rede virtual à Internet pública não é mais necessário consumir os serviços de PaaS do Azure. 

O link privado do Azure fornece os seguintes benefícios:
- **Segurança aprimorada para seus recursos de serviço do Azure**: com o link privado do Azure, os recursos de serviço do Azure podem ser protegidos para sua rede virtual usando o ponto de extremidade privado. Proteger os recursos de serviço para um ponto de extremidade privado na rede virtual fornece segurança aprimorada removendo completamente o acesso público à Internet aos recursos e permitindo o tráfego somente de ponto de extremidade privado em sua rede virtual.
- **Acesse os recursos de serviço do Azure de forma privada na plataforma Azure**: Conecte sua rede virtual aos serviços no Azure usando pontos de extremidade privados. Não há necessidade de um endereço IP público. A plataforma de Link Privado manipulará a conectividade entre o consumidor e os serviços na rede de backbone do Azure.
- **Acesso de redes locais e emparelhadas**: serviços de acesso em execução no Azure local por meio de emparelhamento privado do ExpressRoute, túneis de VPN e redes virtuais emparelhadas usando pontos de extremidade privados. Não é necessário configurar o emparelhamento da Microsoft no ExpressRoute nem atravessar a Internet para acessar o serviço. O Link Privado fornece uma forma segura de migrar cargas de trabalho para o Azure.
- **Proteção contra vazamento de dados**: um ponto de extremidade privado é mapeado para uma instância de um recurso de PaaS em vez de todo o serviço. Os consumidores só podem se conectar ao recurso específico. O acesso a qualquer outro recurso no serviço é bloqueado. Esse mecanismo fornece proteção contra riscos de vazamento de dados.
- **Alcance Global**: Conecte-se de maneira privada aos serviços executados em outras regiões. A rede virtual do consumidor pode estar na região A e pode se conectar aos serviços na região B.
- **Simples de configurar e gerenciar**: você não precisa mais de endereços IP públicos reservados em suas redes virtuais para proteger os recursos do Azure por meio de um firewall de IP. Não há dispositivos NAT ou de gateway necessários para configurar os pontos de extremidade privados. Pontos de extremidade privados são configurados por meio de um fluxo de trabalho simples. No lado do serviço, você também pode gerenciar as solicitações de conexão em seu recurso de serviço do Azure com facilidade. O link privado do Azure funciona para consumidores e serviços que pertencem a locatários Azure Active Directory diferentes também. 
    
Para saber mais sobre pontos de extremidade privados e os serviços e regiões do Azure que os pontos de extremidade privados estão disponíveis para o, consulte o [link privado do Azure](../../private-link/private-link-overview.md).


## <a name="next-steps"></a>Próximas etapas
Confira [Melhores práticas e padrões de segurança do Azure](best-practices-and-patterns.md) para ver mais melhores práticas segurança para usar ao projetar, implantar e gerenciar soluções de nuvem usando o Azure.