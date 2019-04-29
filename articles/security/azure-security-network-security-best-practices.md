---
title: Práticas recomendadas de segurança de rede do Azure | Microsoft Docs
description: Este artigo fornece um conjunto de práticas recomendadas de segurança de rede usando recursos internos do Azure.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/17/2018
ms.author: TomSh
ms.openlocfilehash: b644a175814fb28563a2524e27f52d0285415d66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610954"
---
# <a name="azure-network-security-best-practices"></a>Práticas recomendadas de rede do Azure
Você pode conectar [máquinas virtuais (VMs) do Azure](https://azure.microsoft.com/services/virtual-machines/) e dispositivos a outros dispositivos em rede, colocando-os em [redes virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-network/). Ou seja, você pode conectar cartões de interface de rede virtual a uma rede virtual para permitir comunicações baseadas em TCP / IP entre dispositivos habilitados para rede. Máquinas virtuais conectadas a uma rede virtual do Azure podem se conectar a dispositivos na mesma rede virtual, em diferentes redes virtuais, na Internet ou em suas próprias redes locais.

Este artigo descreve uma coleção de práticas recomendadas de segurança de rede do Azure. Essas práticas recomendadas derivam da nossa experiência de rede do Azure e da experiência de clientes como você.

Para cada prática recomendada, este artigo explica:

* O que é a prática recomendada
* Por que é ideal habilitar essa prática recomendada
* O que poderá acontecer se você não habilitar a prática recomendada
* Possíveis alternativas à prática recomendada
* Como você pode aprender a habilitar a prática recomendada

Este artigo Práticas recomendadas de segurança de rede do Azure baseia-se em um consenso e nos recursos da plataforma Azure e em conjuntos de recursos tal como existiam no momento em que o artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As seções a seguir descrevem as práticas recomendadas para segurança de rede.

## <a name="logically-segment-subnets"></a>Segmentar logicamente as sub-redes
Redes virtuais do Azure são semelhantes a uma rede local na sua rede local. A ideia por trás de uma rede virtual do Azure é que você crie uma única rede baseada em espaço de endereço IP particular na qual é possível colocar todas as suas máquinas virtuais do Azure. Os espaços de endereço IP privados disponíveis estão nos intervalos de Classe A (10.0.0.0/8), de Classe B (172.16.0.0/12) e de Classe C (192.168.0.0/16).

As práticas recomendadas para segmentar logicamente sub-redes incluem:

**Melhor prática**: Segmentar o espaço de endereço maior em sub-redes.   
**Detalhe**: Use princípios de sub-redes baseados no [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) para criar suas sub-redes.

**Melhor prática**: Criar controles de acesso à rede entre sub-redes. O roteamento entre sub-redes acontece automaticamente e você não precisa configurar manualmente as tabelas de roteamento. Por padrão, não há controles de acesso à rede entre as sub-redes que você cria na rede virtual do Azure.   
**Detalhe**: Use um NSG [(grupo de segurança de rede)](../virtual-network/virtual-networks-nsg.md). Os NSGs são dispositivos de inspeção de pacotes simples e monitorados que usam a abordagem 5-tupla (o IP de origem, porta de origem, IP de destino, porta de destino e protocolo da camada 4) para criar regras de permissão / negação para tráfego de rede. Você permite ou nega o tráfego de e para um único endereço IP, de e para vários endereços IP ou para e de sub-redes inteiras.

Quando você usa NSGs para controle de acesso à rede entre sub-redes, pode colocar recursos que pertencem à mesma zona ou função de segurança em suas próprias sub-redes.

## <a name="control-routing-behavior"></a>Controlar o comportamento de roteamento
Quando você coloca uma máquina virtual em uma rede virtual do Azure, a VM pode se conectar a qualquer outra VM na mesma rede virtual, mesmo se as outras VMs estiverem em sub-redes diferentes. Isso é possível porque uma coleção de rotas do sistema ativadas por padrão permite esse tipo de comunicação. Essas rotas padrão permitem que as VMs na mesma rede virtual iniciem conexões umas com as outras e com a Internet (somente para comunicações de saída com a Internet).

Embora as rotas do sistema padrão sejam úteis para muitos cenários de implantação, há momentos em que você deseja personalizar a configuração de roteamento para suas implantações. Você pode configurar o endereço do próximo salto para alcançar destinos específicos.

Recomendamos que você configure [rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md) ao implantar um dispositivo de segurança para uma rede virtual. Podemos falar sobre isso em uma seção posterior intitulada [proteger seus recursos essenciais do serviço do Azure para apenas suas redes virtuais](azure-security-network-security-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Rotas definidas pelo usuário não são obrigatórias e as rotas de sistema padrão funcionam normalmente.
>
>

## <a name="enable-forced-tunneling"></a>Habilitar o túnel forçado
Para entender melhor o túnel forçado, convém compreender o que é "um túnel dividido". O exemplo mais comum de encapsulamento dividido é visto com conexões de rede virtual privada (VPN). Imagine que você estabeleça uma conexão VPN do seu quarto de hotel para sua rede corporativa. Essa conexão permite acessar recursos corporativos. Todas as comunicações para sua rede corporativa passam pelo túnel VPN.

O que acontece quando você quer se conectar a recursos na internet? Quando o tunelamento dividido está habilitado, essas conexões vão diretamente para a Internet e não através do túnel VPN. Alguns especialistas em segurança consideram isso um risco em potencial. Eles recomendam desativar o tunelamento dividido e garantir que todas as conexões, aquelas destinadas à Internet e aquelas destinadas a recursos corporativos, passem pelo túnel da VPN. A vantagem de desativar o tunelamento dividido é que as conexões com a Internet são forçadas pelos dispositivos de segurança da rede corporativa. Esse não seria o caso do cliente VPN conectado à Internet fora do túnel VPN.

Agora vamos trazer isso de volta para as VMs em uma rede virtual do Azure. As rotas padrão para uma rede virtual do Azure permitem que as VMs iniciem o tráfego para a Internet. Isso também pode representar um risco de segurança, porque essas conexões de saída podem aumentar a superfície de ataque de uma VM e ser usadas por invasores. Por esse motivo, recomendamos que você [ative o encapsulamento forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) em suas VMs quando tiver conectividade entre locais entre sua rede virtual do Azure e sua rede local. Falamos sobre conectividade entre locais mais adiante nas práticas recomendadas de rede.

Se você não tiver uma conexão entre locais, aproveite os NSGs (discutidos anteriormente) ou os dispositivos de segurança de rede virtual do Azure (discutidos a seguir) para impedir conexões de saída com a Internet de suas máquinas virtuais do Azure.

## <a name="use-virtual-network-appliances"></a>Usar dispositivos de rede virtual
Os NSGs e o roteamento definido pelo usuário podem fornecer uma certa medida de segurança de rede na rede e transportar as camadas do [modelo do OSI ](https://en.wikipedia.org/wiki/OSI_model). Mas, em algumas situações, você quer ou precisa ativar a segurança em níveis altos da pilha. Em tais situações, é recomendável que você implante dispositivos de segurança de rede virtual fornecidos por parceiros do Azure.

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
Uma [rede de perímetro](https://docs.microsoft.com/azure/best-practices-network-security) (também conhecida como DMZ) é um segmento de rede físico ou lógico que fornece uma camada adicional de segurança entre seus ativos e a Internet. Os dispositivos especializados de controle de acesso à rede na extremidade de uma rede de perímetro permitem apenas o tráfego desejado na sua rede virtual.

As redes de perímetro são úteis porque você pode concentrar seu gerenciamento, monitoramento, registro e geração de relatórios de controle de acesso de rede nos dispositivos na borda da rede virtual do Azure. Aqui, você normalmente ativa a prevenção de negação de serviço distribuída (DDoS), sistemas de detecção de invasão / prevenção de invasão (IDS / IPS), regras e políticas de firewall, filtragem da Web, antimalware de rede e muito mais. Os dispositivos de segurança de rede ficam entre a Internet e sua rede virtual do Azure e têm uma interface nas duas redes.

Embora esse seja o design básico de uma rede de perímetro, há muitos designs diferentes, como back-to-back, tri-homed e multi-homed.

Recomendamos para todas as implantações de alta segurança que você considere usar uma rede de perímetro para aprimorar o nível de segurança da rede para seus recursos do Azure.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitar a exposição à Internet por meio de links WAN dedicados
Muitas organizações escolheram a rota de TI híbrida. Na TI híbrida, alguns dos ativos de informação da empresa estão no Azure, enquanto outros permanecem no local. Em muitos casos, alguns componentes de um serviço estão sendo executados no Azure, enquanto outros componentes permanecem no local.

No cenário de TI híbrida, geralmente há algum tipo de conectividade entre locais. A conectividade entre instalações permite que a empresa conecte suas redes locais às redes virtuais do Azure. Duas soluções de conectividade entre locais estão disponíveis:

* **VPN site a site**: é uma tecnologia estabelecida e confiável, mas a conexão ocorre pela Internet. A largura de banda é limitada a um máximo de cerca de 200 Mbps. A VPN site a site é uma opção desejável em alguns cenários e é discutida mais adiante na seção [Desativar o acesso RDP / SSH às máquinas virtuais](#disable-rdpssh-access-to-virtual-machines).
* **Azure ExpressRoute**: é recomendável usar o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para a conectividade entre locais. O ExpressRoute é um link WAN dedicado entre seu local ou um provedor de hospedagem do Exchange. Como essa é uma conexão de telecomunicação, seus dados não trafegam pela Internet e, portanto, não estão expostos aos possíveis riscos das comunicações pela Internet.

## <a name="optimize-uptime-and-performance"></a>Otimizar o desempenho e o tempo de atividade
Se um serviço estiver inativo, as informações não poderão ser acessadas. Se o desempenho é tão ruim que os dados não podem ser usados, você pode considerar os dados como inacessíveis. Do ponto de vista de segurança, você precisa fazer o que puder para garantir que seus serviços tenham tempo de atividade e desempenho ideais.

Um método popular e eficaz para melhorar a disponibilidade e o desempenho é o balanceamento de carga. O balanceamento de carga é um método de distribuição de tráfego de rede entre servidores que fazem parte de um serviço. Por exemplo, se você tiver servidores Web de front-end como parte de seu serviço, poderá usar o balanceamento de carga para distribuir o tráfego entre vários servidores Web de front-end.

Essa distribuição de tráfego aumenta a disponibilidade porque, se um dos servidores Web ficar indisponível, o balanceador de carga interromperá o tráfego de envio para esse servidor e redirecionará o tráfego para os servidores que ainda estiverem online. O balanceamento de carga também ajuda no desempenho porque a sobrecarga do processador, da rede e da memória para atender às solicitações é distribuída por todos os servidores com carga balanceada.

É recomendável implantar o balanceamento de carga sempre que possível e conforme apropriado para seus serviços. A seguir estão os cenários no nível da rede virtual do Azure e no nível global, juntamente com as opções de balanceamento de carga para cada um.

**Cenário**: você tem um aplicativo que:

- Requer solicitações da mesma sessão de usuário / cliente para alcançar a mesma máquina virtual de back-end. Exemplos disso são aplicativos de carrinho de compras e servidores de webmail.
- Aceita apenas uma conexão segura, portanto, a comunicação não criptografada com o servidor não é uma opção aceitável.
- Exige que várias solicitações HTTP na mesma conexão TCP de longa duração sejam roteadas ou carregadas com balanceamento para diferentes servidores de back-end.

**Opção de balanceamento de carga**: use o [Gateway de Aplicativo do Azure](../application-gateway/application-gateway-introduction.md), um balanceador de carga de tráfego da Web HTTP. O Application Gateway suporta criptografia SSL de ponta a ponta e [terminação SSL](../application-gateway/application-gateway-introduction.md) no gateway. Os servidores da Web podem então ser aliviados da sobrecarga de criptografia e descriptografia e do fluxo de tráfego não criptografado para os servidores de back-end.

**Cenário**: você precisa balancear a carga das conexões de entrada da Internet entre seus servidores localizados em uma rede virtual do Azure. Os cenários são quando você:

- Tiver aplicativos sem monitoração de estado que aceitam solicitações de entrada da internet.
- Não requerem sessões Autoadesivas ou descarregamento SSL. As sessões temporárias é um método usado com balanceamento de carga do aplicativo, para alcançar a afinidade do servidor.

**Opção de balanceamento de carga**: use o portal do Azure para [criar um balanceador de carga externo](../load-balancer/quickstart-create-basic-load-balancer-portal.md) que distribua solicitações de entrada em várias VMs para fornecer um nível mais alto de disponibilidade.

**Cenário**: é necessário balancear a carga de conexões de VMs que não estão na Internet. Na maioria dos casos, as conexões aceitas para balanceamento de carga são iniciadas por dispositivos em uma rede virtual do Azure, como instâncias do SQL Server ou servidores da Web internos.   
**Opção de balanceamento de carga**: use o portal do Azure para [criar um balanceador de carga interno](../load-balancer/quickstart-create-basic-load-balancer-powershell.md) que distribua solicitações de entrada em várias VMs para fornecer um nível mais alto de disponibilidade.

**Cenário**: você precisa de balanceamento de carga global porque:

- Tenha uma solução em nuvem amplamente distribuída em várias regiões e que exija o mais alto nível de disponibilidade (disponibilidade) possível.
- É necessário o mais alto nível de tempo de atividade possível para garantir que seu serviço esteja disponível mesmo se um datacenter inteiro ficar indisponível.

**Opção de balanceamento de carga**: Use o Gerenciador de Tráfego do Azure. O Gerenciador de Tráfego possibilita o balanceamento de carga das conexões para seus serviços com base na localização do usuário.

Por exemplo, se o usuário fizer uma solicitação ao seu serviço a partir da UE, a conexão será direcionada para os serviços localizados em um datacenter da UE. Esta parte do balanceamento de carga global do Gerenciador de Tráfego ajuda a melhorar o desempenho porque a conexão com o datacenter mais próximo é mais rápida do que conectar-se a data centers que estejam distantes.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Desativar o acesso RDP / SSH às máquinas virtuais
É possível acessar as máquinas virtuais do Azure usando [Protocolo RDP (Remote Desktop Protocol)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) e o protocolo [Secure Shell ](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Esses protocolos permitem o gerenciamento de VMs de locais remotos e são padrão na computação de data center.

O problema de segurança em potencial ao usar esses protocolos pela Internet é que os invasores podem usar as técnicas de [força bruta](https://en.wikipedia.org/wiki/Brute-force_attack) para obter acesso às máquinas virtuais do Azure. Depois que os invasores tiverem acesso, eles poderão usar sua VM como ponto de partida para comprometer outras máquinas em sua rede virtual ou até mesmo atacar dispositivos em rede fora do Azure.

Recomendamos que você desabilite o acesso RDP e SSH direto às máquinas virtuais do Azure a partir da Internet. Depois que o acesso RDP e SSH direto da Internet for desativado, você terá outras opções que podem ser usadas para acessar essas VMs para gerenciamento remoto.

**Cenário**: permite que um único usuário se conecte a uma rede virtual do Azure pela Internet.   
**Opção**: A [VPN ponto a site](../vpn-gateway/vpn-gateway-point-to-site-create.md) é outro termo para uma conexão de cliente/servidor de VPN de acesso remoto. Depois que a conexão ponto a ponto é estabelecida, o usuário pode usar RDP ou SSH para se conectar a qualquer VM localizada na rede virtual do Azure à qual o usuário se conectou por meio de VPN ponto a ponto. Isso pressupõe que o usuário está autorizado a alcançar essas VMs.

A VPN ponto-a-ponto é mais segura do que as conexões RDP ou SSH diretas porque o usuário precisa autenticar duas vezes antes de se conectar a uma VM. Primeiro, o usuário precisa autenticar (e ser autorizado) para estabelecer a conexão VPN ponto-a-ponto. Segundo, o usuário precisa autenticar (e ser autorizado) para estabelecer a sessão RDP ou SSH.

**Cenário**: permite que os usuários em sua rede local se conectem a VMs em sua rede virtual do Azure.   
**Opção**: Uma [VPN site a site](../vpn-gateway/vpn-gateway-site-to-site-create.md) conecta uma rede inteira a outra rede pela Internet. Você pode usar uma VPN site a site para conectar sua rede local a uma rede virtual do Azure. Os usuários em sua rede local se conectam usando o protocolo RDP ou SSH pela conexão VPN site a site. Você não precisa permitir acesso RDP ou SSH direto pela Internet.

**Cenário**: use um link WAN dedicado para fornecer funcionalidade semelhante à VPN site a site.   
**Opção**: Use [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Ele fornece uma funcionalidade semelhante à VPN site a site. As principais diferenças são:

- O link dedicado da WAN não atravessa a Internet.
- Os links de WAN dedicados geralmente são mais estáveis e apresentam melhor desempenho.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Proteja seus recursos de serviço críticos do Azure somente para suas redes virtuais
Use pontos de extremidade de serviço de rede virtual para estender o espaço de endereço privado de sua rede virtual e a identidade de sua rede virtual para os serviços do Azure, por meio de uma conexão direta. Os pontos de extremidade permitem que você possa garantir os recursos essenciais do serviço do Azure somente para suas redes virtuais. O tráfego da sua rede virtual para o serviço do Azure sempre permanece na rede de backbone do Microsoft Azure.

Os pontos de extremidade de serviço fornecem os seguintes benefícios:

- **Segurança aprimorada para os recursos do serviço do Azure**: Com os pontos de extremidade de serviço, os recursos do serviço do Azure podem ser garantidos para sua rede virtual. A proteção de recursos de serviço a uma rede virtual fornece segurança aprimorada, removendo totalmente o acesso público à Internet a recursos e permitindo o tráfego somente de sua rede virtual.
- **Roteamento de tráfego ideal para o serviço do Azure de sua rede virtual**: Todas as rotas em sua rede virtual que forçam o tráfego de Internet para dispositivos locais e/ou virtuais, conhecido como túnel forçado, também forçam o tráfego do serviço do Azure para seguir a mesma rota que o tráfego de Internet. Os pontos de extremidade de serviço oferecem o roteamento ideal para o tráfego do Azure.

  Os pontos de extremidade sempre levam o tráfego de serviço diretamente de sua rede virtual para o serviço na rede do backbone do Azure. Manter o tráfego na rede de backbone do Azure permite continuar a auditar e monitorar o tráfego de saída da Internet de suas redes virtuais, por meio de encapsulamento forçado, sem afetar o tráfego de serviço. Saiba mais sobre [rotas definidas pelo usuário e encapsulamento forçado](../virtual-network/virtual-networks-udr-overview.md).

- **Simples de configurar com menos sobrecarga de gerenciamento**: Você não precisa mais de endereços IP públicos reservados nas suas redes virtuais para garantir recursos do Azure pelo firewall do IP. Não é obrigatório nenhum dispositivo NAT ou de gateway para configurar os pontos de extremidade de serviço. Pontos de extremidade de serviço são configurados por meio de um clique simples em uma sub-rede. Não há sobrecarga adicional para manter os terminais.

Para saber mais sobre os pontos de extremidade de serviços e os serviços e regiões do Azure aos quais os pontos de extremidade de serviço estão disponíveis, consulte [Pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-step"></a>Próxima etapa
Veja [Melhores práticas e padrões de segurança do Azure](security-best-practices-and-patterns.md) para obter melhores práticas segurança complementares a serem usadas ao projetar, implantar e gerenciar as soluções de nuvem, usando o Azure.
