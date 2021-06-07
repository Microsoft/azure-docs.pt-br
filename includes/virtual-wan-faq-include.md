---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b7f79bebce5a086b268f4fc1080c33517555fb39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102431517"
---
### <a name="is-azure-virtual-wan-in-ga"></a>A WAN Virtual do Azure está em GA?

Sim, a WAN Virtual do Azure está em GA (disponibilidade geral). No entanto, a WAN Virtual é composta por vários recursos e cenários. Há recursos ou cenários na WAN Virtual em que a Microsoft aplica a marca Versão Prévia. Nesses casos, o recurso específico ou o próprio cenário está em versão prévia. Se você não usar uma versão prévia do recurso específica, será aplicável o suporte à GA regular. Para saber mais sobre o suporte a Versões prévias, confira os [Termos de uso complementares para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>O usuário precisa ter hub e spoke com dispositivos SD-WAN/VPN para usar a WAN virtual do Azure?

A WAN virtual oferece muitas funcionalidades incorporadas em um único painel de controle, como conectividade VPN site a site/site, conectividade de Usuário/P2S, conectividade do ExpressRoute, conectividade de Rede Virtual, Interconectividade de VPN ExpressRoute, conectividade transitiva VNet a VNet, roteamento centralizado, Firewall do Azure e segurança do Gerenciador de Firewall, Monitoramento, Criptografia do ExpressRoute e muitos outros recursos. Não é necessário ter todos esses casos de uso para começar a usar a WAN Virtual. Você pode começar com apenas um caso de uso.

A arquitetura da WAN Virtual é uma arquitetura de hub e spoke com dimensionamento e desempenho internos em que as ramificações (dispositivos VPN/SD-WAN), os usuários (clientes VPN do Azure, clientes openVPN ou IKEv2), os circuitos de ExpressRoute, as Redes Virtuais servem como spokes para os hubs virtuais. Todos os hubs são conectados em malha completa em uma WAN Virtual Padrão, o que facilita para o usuário usar o backbone da Microsoft para conectividade de qualquer um para qualquer um (qualquer spoke). Para o hub e spoke com dispositivos SD-WAN/VPN, os usuários podem configurá-lo manualmente no portal da WAN Virtual do Azure ou usar o CPE do Parceiro da WAN Virtual (SD-WAN/VPN) para configurar a conectividade com o Azure.

Os parceiros de WAN Virtual fornecem automação para conectividade, que é a capacidade de exportar as informações do dispositivo para o Azure, baixar a configuração do Azure e estabelecer conectividade com o hub da WAN Virtual do Azure. Para a conectividade de VPN do usuário/ponto a site, damos suporte a [cliente de VPN do Azure](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN ou cliente do IKEv2.

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Você pode desabilitar hubs totalmente entrelaçados em uma WAN Virtual?

A WAN Virtual é fornecida em dois tipos: Básico e Standard. Na WAN Virtual básica, os hubs não são entrelaçados. Em uma WAN Virtual padrão, os hubs são entrelaçados e conectados automaticamente quando a WAN virtual é configurada pela primeira vez. O usuário não precisa fazer nada específico. O usuário também não precisa desabilitar nem habilitar a funcionalidade para obter hubs entrelaçados. A WAN Virtual oferece várias opções de roteamento para direcionar o tráfego entre qualquer spoke (VNet, VPN ou ExpressRoute). Ela proporciona a facilidade de hubs totalmente entrelaçados e também a flexibilidade de roteamento de tráfego de acordo com suas necessidades.

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Como as Zonas de Disponibilidade e resiliência são manipuladas na WAN Virtual?

A WAN Virtual é uma coleção de hubs e serviços disponibilizados dentro do Hub. O usuário pode ter tantas WANs Virtuais conforme a necessidade. Em um hub de WAN Virtual, há vários serviços como VPN, ExpressRoute etc. Cada um desses serviços (exceto o Firewall do Azure) é implantado em uma região de Zonas de Disponibilidade, se a região oferecer suporte a Zonas de Disponibilidade. Se uma região se tornar uma Zona de Disponibilidade após a implantação inicial no hub, o usuário poderá recriar os gateways, disparando uma implantação de Zona de Disponibilidade. Todos os gateways são provisionados em um hub como ativo-ativo, o que significa que há resiliência interna em um hub. Os usuários poderão se conectar a vários hubs se desejarem resiliência entre regiões.

Embora o conceito de WAN Virtual seja global, o recurso WAN Virtual real é baseado no Resource Manager e é implantado regionalmente. Se a região da WAN virtual em si tiver um problema, todos os hubs nessa WAN virtual continuarão a funcionar como estão, mas o usuário não poderá criar hubs até que a região da WAN virtual esteja disponível.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Com qual cliente a VPN de Usuário da WAN Virtual do Azure (ponto a site) é compatível?

A WAN virtual é compatível com [cliente da VPN do Azure](https://go.microsoft.com/fwlink/?linkid=2117554), cliente OpenVPN ou qualquer cliente IKEv2. A autenticação do Azure AD é compatível com o cliente VPN do Azure. É necessário um sistema operacional cliente Windows 10 versão 17763.0 ou superior.  Os clientes OpenVPN podem dar suporte à autenticação baseada em certificado. Depois que a autenticação baseada em certificado for selecionada no gateway, você verá o arquivo .ovpn a ser baixado em seu dispositivo. O IKEv2 dá suporte à autenticação de certificado e RADIUS. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Para VPN de usuário (ponto a site) – por que o pool de clientes P2S é dividido em duas rotas?

Cada gateway tem duas instâncias, a divisão acontece para que cada instância de gateway possa alocar IPs de cliente de maneira independente para clientes conectados e o tráfego da rede virtual seja roteado de volta para a instância de gateway correta para evitar o salto de instância entre gateways.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Como faço para adicionar servidores DNS a clientes P2S?

Há duas opções para adicionar servidores DNS aos clientes do P2S. O primeiro método é preferido, pois adiciona os servidores DNS personalizados ao gateway em vez do cliente.

1. Use o script do PowerShell a seguir para adicionar os servidores DNS personalizados. Substitua os valores de acordo com seu ambiente.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. Ou, se você estiver usando o cliente VPN do Azure para Windows 10, poderá modificar o arquivo XML do perfil baixado e adicionar as marcas **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** antes de importá-lo.

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Para VPN de usuário (ponto a site) – há suporte para quantos clientes?

Cada gateway de VPN P2S de usuário tem duas instâncias. Cada instância dá suporte a até um determinado número de conexões à medida que a unidade de escala é alterada. A unidade de escala 1-3 dá suporte a 500 conexões, a unidade de escala 4-6 dá suporte a mil conexões, a unidade de escala 7-12 dá suporte a 5 mil conexões e a unidade de escala 13-18 dá suporte a até 10 mil conexões.

Por exemplo, digamos que o usuário escolha uma unidade de escala. Cada unidade de escala implica em um gateway ativo-ativo implantado e cada uma das instâncias (neste caso, 2) seria compatível com até 500 conexões. Como você pode obter 500 conexões * 2 por gateway, isso não significa que você planeja mil conexões em vez de 500 para essa unidade de escala. As instâncias podem precisar de manutenção, durante a qual a conectividade das 500 adicionais poderá ser interrompida se você ultrapassar a contagem de conexões recomendada. Além disso, não se esqueça de planejar o tempo de inatividade caso você decida escalar ou reduzir verticalmente na unidade de escala ou alterar a configuração de ponto a site no gateway de VPN.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Qual é a diferença entre um gateway de rede virtual do Azure (Gateway de VPN) e um gateway de VPN da WAN Virtual do Azure?

A WAN Virtual fornece conectividade site a site em larga escala e é criada visando produtividade, escalabilidade e facilidade de uso. Quando você conecta um site a um gateway de VPN de WAN Virtual, ele é diferente de um gateway de rede virtual comum que usa um tipo de gateway 'VPN'. Da mesma forma, quando você conecta um circuito de ExpressRoute a um hub de WAN Virtual, ele usa para o gateway de ExpressRoute um recurso diferente daquele usado pelo gateway de rede virtual regular, o qual usa o tipo de gateway 'ExpressRoute'. 

A WAN Virtual dá suporte a uma taxa de transferência de agregação de até 20 Gbps para VPN e ExpressRoute. A WAN Virtual também tem automação para conectividade com um ecossistema de parceiros de dispositivo de ramificação CPE. Os dispositivos da ramificação CPE têm automação interna que provisiona automaticamente e se conecta à WAN Virtual do Azure. Esses dispositivos estão disponíveis em um ecossistema crescente de SD-WAN e parceiros de VPN. Confira a [Lista de Parceiros Preferenciais](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Como a WAN Virtual é diferente de um gateway de rede virtual do Azure existente?

A VPN do Gateway de Rede Virtual é limitada a 30 túneis. Para conexões, você deve usar a WAN Virtual para VPN em larga escala. Você pode conectar até 1.000 conexões de ramificação por região (hub virtual) com agregação de 20 Gbps por hub. Uma conexão é um túnel de ativo-ativo do dispositivo VPN local para o hub virtual. Pode haver um hub por região, o que significa que você pode conectar a mais de 1.000 branches entre os hubs.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>O que é uma unidade de escala de gateway da WAN Virtual?

Uma unidade de escala é uma unidade definida para escolher uma taxa de transferência agregada de um gateway no Hub virtual. 1 unidade de escala de VPN = 500 Mbps. 1 unidade de escala do ExpressRoute = 2 Gbps. Exemplo: 10 unidades de escala de VPN implicariam 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Quais provedores de dispositivo (parceiros de WAN Virtual) são compatíveis?

Neste momento, muitos parceiros dão suporte à experiência de WAN Virtual totalmente automatizada. Para saber mais, confira [Parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quais são as etapas de automação de parceiro de WAN Virtual?

Para conhecer as etapas de automação de parceiro, confira [Automação de parceiro de WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Eu sou obrigado a usar um dispositivo de parceiro preferido?

Não. Você pode usar qualquer dispositivo com capacidade para VPN que siga os requisitos do Azure para suporte a IPsec IKEv2/IKEv1. A WAN Virtual também tem soluções de parceiros de CPE que automatizam a conectividade com a WAN Virtual do Azure, facilitando a configuração de conexões VPN IPsec em escala.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Como os parceiros de WAN Virtual automatizam a conectividade com a WAN Virtual do Azure?

Soluções de conectividade definidas pelo software normalmente gerenciam seus dispositivos de branch usando um controlador ou um centro de provisionamento de dispositivos. O controlador pode usar as APIs do Azure para automatizar a conectividade com a WAN Virtual do Azure. A automação inclui o upload de informações de branch, o download da configuração do Azure, a configuração de túneis IPsec em hubs virtuais do Azure e a configuração automática da conectividade do dispositivo de branch para a WAN Virtual do Azure. Quando você tem centenas de ramificações, é fácil conectar-se usando parceiros CPE da WAN Virtual, pois a experiência de integração elimina a necessidade de configurar e gerenciar a conectividade IPsec em larga escala. Para saber mais, confira [Automação de parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>E se um dispositivo que estou usando não estiver na lista de parceiros da WAN Virtual? Ainda posso usá-lo para se conectar à VPN de WAN Virtual do Azure?

Sim, desde que o dispositivo seja compatível com IPsec IKEv1 ou IKEv2. Os parceiros WAN virtuais automatizam a conectividade do dispositivo aos pontos de extremidade de VPN do Azure. Isso implica na automatização de etapas como 'upload de informações de branch', 'IPsec e configuração' e 'conectividade'. Como o dispositivo não é proveniente de um ecossistema de parceiros da WAN Virtual, você precisará fazer o trabalho pesado de realizar manualmente a configuração do Azure e atualizar seu dispositivo para configurar a conectividade IPsec.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Como integrar novos parceiros que não estejam na sua lista de parceiros de lançamento?

Todas as APIs de WAN Virtual são de API aberta. Você pode examinar a documentação [automação do parceiro de WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) para avaliar a viabilidade técnica. Um parceiro ideal é aquele que tem um dispositivo que pode ser provisionado para conectividade IPsec IKEv1 ou IKEv2. Depois que a empresa tiver concluído o trabalho de automação para o dispositivo CPE com base nas diretrizes de automação fornecidas acima, você poderá entrar em contato com o azurevirtualwan@microsoft.com a ser listado aqui: [Conectividade por meio de parceiros]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners). Se você é um cliente que gostaria que uma determinada solução da empresa fosse listada como um parceiro de WAN Virtual, peça para a empresa entrar em contato com a WAN Virtual enviando um email para azurevirtualwan@microsoft.com.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Como o WAN Virtual dá suporte aos dispositivos SD-WAN?

Parceiros WAN Virtuais automatizam a conectividade IPsec com pontos de extremidade de VPN do Azure. Se o parceiro WAN Virtual é um provedor de SD-WAN, está implícito que o controlador de SD-WAN gerencia a automação e conectividade IPsec aos pontos de extremidade do Azure VPN. Se o dispositivo de SD-WAN requer seu próprio ponto de extremidade de VPN do Azure para qualquer funcionalidade SD-WAN proprietária, você pode implantar o ponto de extremidade SD-WAN em uma Rede Virtual do Azure e coexiste com o WAN Virtual do Azure.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quantos dispositivos VPN podem se conectar a um único hub?

O hub virtual é compatível com até 1.000 conexões. Cada conexão é composta por quatro links, cada um dos quais dá suporte a dois túneis que estão em uma configuração ativo/ativo. Os túneis terminam em um VPN de gateway do hub virtual do Azure. Os links representam o link do ISP físico no dispositivo VPN/branch.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>O que é uma conexão de ramificação WAN Virtual do Azure?

Uma conexão de um dispositivo VPN ou de branch com a WAN Virtual do Azure é uma conexão VPN que se conecta virtualmente com o site de VPN e com o Gateway de VPN do Azure em um hub virtual.

### <a name="what-happens-if-the-on-premises-vpn-device-only-has-1-tunnel-to-an-azure-virtual-wan-vpn-gateway"></a>O que acontece se o dispositivo VPN local tem apenas um túnel para um gateway de VPN da WAN Virtual do Azure?

Uma conexão da WAN Virtual do Azure é composta por dois túneis. Um gateway de VPN da WAN Virtual é implantado em um hub virtual no modo ativo-ativo, o que significa que há túneis separados de dispositivos locais que terminam em instâncias separadas. Essa é a recomendação para todos os usuários. No entanto, se o usuário optar por ter apenas um túnel para uma das instâncias de gateway de VPN da WAN Virtual e, se por algum motivo (manutenção, patches etc.), a instância de gateway for colocada offline, o túnel será movido para a instância ativa secundária e o usuário poderá experimentar uma reconexão. As sessões BGP não serão movidas entre instâncias.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>O dispositivo VPN local pode se conectar a vários hubs?

Sim. O fluxo de tráfego ao começar é do dispositivo local para a borda de rede da Microsoft mais próxima e, em seguida, para o hub virtual.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Há novos recursos do Gerenciador de recursos disponíveis para WAN Virtual?
  
Sim, a WAN Virtual tem novos recursos do Resource Manager. Para saber mais, confira a [Visão geral](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Posso implantar e usar minha solução de virtualização de rede favorita (em uma VNet NVA) com a WAN Virtual do Azure?

Sim, você pode conectar a VNET da sua NVA (solução de virtualização de rede) favorita à WAN Virtual do Azure.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Posso criar uma solução de virtualização de rede dentro do hub virtual?

Uma NVA (solução de virtualização de rede) não pode ser implantada dentro de um hub virtual. No entanto, você pode criá-la em uma VNet spoke que esteja conectada com o hub virtual e habilitar o roteamento apropriado para o tráfego direto de acordo com suas necessidades.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Uma VNet spoke pode ter um gateway de rede virtual?

Não. A VNet spoke não poderá ter um gateway de rede virtual se estiver conectada ao hub virtual.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Há suporte para BGP na conectividade da VPN?

Sim, há suporte para BGP. Quando você cria um site VPN, você pode fornecer os parâmetros de BGP nele. Isso implicará que todas as conexões criadas no Azure para o site serão habilitadas para BGP.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Há quaisquer informações de licenciamento ou preços para a WAN Virtual?

Sim. Confira a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>É possível construir a WAN Virtual do Azure com um modelo do Resource Manager?

Uma configuração simples de uma WAN Virtual com um hub e um vpnsite pode ser criada usando um [modelo de início rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). A WAN Virtual é basicamente um serviço controlado por REST ou pelo portal.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>VNets spoke conectadas a um hub virtual podem se comunicar umas com as outras (Trânsito do V2V)?

Sim. A WAN Virtual padrão é compatível com a conectividade transitiva VNet a VNet por meio do hub da WAN Virtual ao qual as VNets estão conectadas. Na terminologia da WAN Virtual, esses caminhos são denominados "trânsito local de VNet da WAN Virtual" para VNets conectadas a um hub da WAN Virtual dentro de uma única região e "trânsito global de VNet da WAN Virtual" para VNets conectadas por meio de vários hubs da WAN Virtual entre duas ou mais regiões.

Em alguns cenários, VNets spoke também podem ser diretamente emparelhadas entre si usando o [emparelhamento de rede virtual](../articles/virtual-network/virtual-network-peering-overview.md), além do trânsito local ou global de VNet da WAN Virtual. Nesse caso, o Emparelhamento VNet tem precedência sobre a conexão transitiva por meio do hub da WAN Virtual.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A conectividade de branch para branch é permitida na WAN Virtual?

Sim, a conectividade de branch para branch está disponível na WAN Virtual. O branch é conceitualmente aplicável ao site de VPN, a circuitos do ExpressRoute ou a usuários de VPN ponto a site/VPN do Usuário. A conectividade branch a branch é habilitada por padrão e pode ser localizada em definições da **Configuração** de WAN. Isso permite que os usuários/branches de VPN se conectem a outros branches de VPN. A conectividade de trânsito também é habilitada entre os usuários de VPN e do ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>O tráfego de branch para branch atravessa a WAN Virtual do Azure?

Sim. O tráfego de branch para branch atravessa a WAN Virtual do Azure.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>A WAN Virtual exige o ExpressRoute de cada site?

Não. A WAN Virtual não exige o ExpressRoute de cada site. Seus sites podem estar conectados a uma rede do provedor usando um circuito do ExpressRoute. Para sites conectados a um hub virtual usando o ExpressRoute, bem como VPN IPsec no mesmo hub, o hub virtual oferece conectividade de trânsito entre o usuário de VPN e do ExpressRoute.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Há um limite de taxa de transferência de rede ou de conexão ao usar a WAN Virtual do Azure?

A taxa de transferência de rede é de acordo com o serviço em um hub de WAN virtual. Embora você possa ter tantas WANs virtuais quanto desejar, cada WAN Virtual permite um hub por região. Em cada hub, a taxa de transferência agregada de VPN é de até 20 Gbps, a taxa de transferência agregada do ExpressRoute é de até 20 Gbps e a taxa de transferência agregada de VPN do Usuário/VPN ponto a site é de até 20 Gbps. O roteador no hub virtual é compatível com até 50 Gbps para fluxos de tráfego de VNet a VNet e admite um total de 2 mil cargas de trabalho de VM em todas as VNets conectadas a apenas um hub virtual.

Os sites de VPN se conectam a um hub por meio de conexões. A WAN Virtual é compatível com até mil conexões ou 2 mil túneis de IPsec por hub virtual. Quando usuários remotos se conectam ao hub virtual, eles se conectam ao gateway de VPN P2S, que é compatível com até 10 mil usuários, dependendo da unidade de escala (largura de banda) escolhida para o gateway de VPN P2S no hub virtual.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>O que é a taxa de transferência total do VPN de um túnel VPN e de uma conexão?

A taxa de transferência total do VPN de um hub é de até 20 Gbps com base na unidade de escala escolhida do gateway de VPN. Taxa de transferência é compartilhada por todas as conexões existentes. Cada túnel em uma conexão pode oferecer suporte a até 1 Gbps.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>Posso usar um NAT-T em minhas conexões VPN?

Sim, o NAT-T (NAT Traversal) é compatível. O gateway de VPN da WAN Virtual NÃO executará nenhuma funcionalidade semelhante ao NAT de/para túneis IPsec nos pacotes internos. Nessa configuração, verifique se o dispositivo local inicia o túnel IPsec.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Não vejo a configuração de 20 Gbps para o hub virtual no portal. Como fazer para configurar isso?

Navegue até o gateway de VPN dentro de um hub no portal e clique na unidade de escala para alterá-la para a configuração apropriada.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A WAN Virtual permite que o dispositivo local utilize vários ISPs em paralelo ou sempre será um único túnel VPN?

As soluções de dispositivo local podem aplicar políticas de tráfego para direcionar o tráfego de vários túneis para o hub da WAN Virtual do Azure (gateway de VPN no hub virtual).

### <a name="what-is-global-transit-architecture"></a>O que é a arquitetura de trânsito global?

Para obter informações sobre a arquitetura de trânsito global, confira [arquitetura de rede de trânsito global e WAN Virtual](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Como o tráfego é roteado no backbone do Azure?

O tráfego segue o padrão: dispositivo branch -> ISP -> Borda da rede Microsoft -> Microsoft DC (hub VNet) -> Borda da rede Microsoft -> ISP -> dispositivo branch

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Nesse modelo, o que você precisa em cada site? Apenas uma conexão com a Internet?

Sim. Uma conexão de Internet e um dispositivo físico que é compatível com IPsec, preferencialmente nossos [parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-locations-partners.md) integrados. Opcionalmente, você pode gerenciar manualmente a configuração e a conectividade com o Azure usando seu dispositivo preferido.

### <a name="how-do-i-enable-default-route-00000-for-a-connection-vpn-expressroute-or-virtual-network"></a>Como fazer para habilitar a rota padrão (0.0.0.0/0) para uma conexão (VPN, ExpressRoute ou Rede Virtual)?

Um hub virtual poderá propagar uma rota padrão aprendida para uma conexão VPN/ExpressRoute de rede virtual/site a site se o sinalizador for 'Habilitado' na conexão. Esse sinalizador fica visível quando o usuário edita uma conexão de rede virtual, uma conexão VPN ou uma conexão ExpressRoute. Por padrão, esse sinalizador é desabilitado quando um site ou um circuito ExpressRoute é conectado a um hub. Ele é habilitado por padrão quando uma conexão de rede virtual é adicionada para conectar uma VNet a um hub virtual.

A rota padrão não é originada no hub de WAN Virtual; a rota padrão é propagada se já foi aprendida pelo hub de WAN Virtual como resultado da implantação de um firewall no hub ou se a opção de túnel forçado está habilitada em outro site conectado. Uma rota padrão não se propaga entre hubs (inter-hub).

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Como o hub virtual em uma WAN virtual seleciona o melhor caminho para uma rota de vários hubs?

Se um hub virtual aprende a mesma rota de vários hubs remotos, a ordem na qual ele decide é a seguinte:

1. Correspondência de prefixo mais longa.
1. Rotas locais no interhub (o hub virtual atribui 65520–65520 para o interhub AS).
1. Rotas estáticas por BGP: Isso está no contexto da decisão que está sendo tomada pelo roteador do hub virtual. No entanto, se o tomador de decisões for o gateway de VPN em que um site anuncia rotas via BGP ou fornece prefixos de endereço estático, as rotas estáticas poderão ser preferidas em vez de rotas BGP.
1. ER (ExpressRoute) por VPN: O ER é preferencial em relação ao VPN quando o contexto é um hub local. A conectividade de trânsito entre circuitos do ExpressRoute só está disponível por meio do Alcance Global. Portanto, em cenários em que o circuito do ExpressRoute está conectado a um hub e há outro circuito do ExpressRoute conectado a um hub diferente com conexão VPN, a VPN poderá ser preferida para cenários entre hubs.
1. Comprimento do caminho AS.

### <a name="does-the-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>O hub da WAN Virtual permite a conectividade entre circuitos do ExpressRoute?

O trânsito entre ERs é sempre por meio do alcance global. Os gateways do hub virtual são implantados no controlador de domínio ou em regiões do Azure. Quando dois circuitos do ExpressRoute se conectam por meio do alcance global, não há necessidade de que o tráfego venha dos roteadores de borda para o controlador de domínio do hub virtual.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Há um conceito de peso em circuitos de ExpressRoute de WAN Virtual do Azure ou conexões VPN

Quando vários circuitos de ExpressRoute estão conectados a um hub virtual, o peso de roteamento na conexão fornece um mecanismo para que o ExpressRoute no hub virtual dê preferência a um circuito em vez de outro. Não há nenhum mecanismo para definir um peso em uma conexão VPN. O Azure sempre prefere uma conexão do ExpressRoute em vez de uma conexão VPN em um único hub.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>A WAN Virtual prefere o ExpressRoute em relação ao VPN para tráfego de saída do Azure

Sim. A WAN Virtual prefere o ExpressRoute em relação ao VPN para tráfego de saída do Azure.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Quando um hub da WAN Virtual tiver um circuito do ExpressRoute e um Site VPN conectado a ele, o que fará com que uma rota de conexão VPN seja preferencial em relação ao ExpressRoute?

Quando um circuito do ExpressRoute é conectado ao hub virtual, os roteadores de borda da Microsoft são o primeiro nó para comunicação entre o local e o Azure. Esses roteadores de borda se comunicam com os gateways do ExpressRoute da WAN Virtual que, por sua vez, aprendem as rotas do roteador do hub virtual, que controla todas as rotas entre todos os gateways da WAN Virtual. Os roteadores de borda da Microsoft processam as rotas do ExpressRoute do hub virtual com preferência mais alta em relação às rotas aprendidas localmente.

Se, por qualquer motivo, a conexão VPN se tornar o meio principal para o hub virtual aprender rotas (por exemplo, cenários de failover entre o ExpressRoute e a VPN), a menos que o Site VPN tenha um tamanho de caminho AS mais longo, o hub virtual continuará compartilhando as rotas de VPN aprendidas com o gateway do ExpressRoute. Isso faz com que os roteadores de borda da Microsoft prefiram rotas VPN em detrimento das rotas locais.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Quando dois hubs (hub 1 e 2) estão conectados e houver um circuito do ExpressRoute conectado como um laço para ambos os hubs, qual será o caminho para que uma VNet conectada ao hub 1 alcance uma VNet conectada ao Hub 2?

O comportamento atual é dar preferência ao caminho do circuito do ExpressRoute em vez da conectividade hub a hub ou VNet a VNet. No entanto, isso não é incentivado em uma configuração de WAN virtual. A equipe da WAN Virtual está trabalhando em uma correção para permitir a preferência de hub a hub em vez do caminho do ExpressRoute. A recomendação é para que vários circuitos do ExpressRoute (provedores diferentes) se conectem a um hub e usem a conectividade hub a hub fornecida pela WAN Virtual para fluxos de tráfego inter-regional.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>Os hubs podem ser criados em diferentes grupos de recursos na WAN Virtual?

Sim. Atualmente, essa opção só está disponível por meio do PowerShell. O portal da WAN Virtual exige que os hubs estejam no mesmo grupo de recursos que o recurso da WAN Virtual propriamente dito.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Há suporte para IPv6 na WAN Virtual?

O IPv6 não é compatível com o hub da WAN Virtual e os respectivos gateways. Se você tem uma VNet compatível com IPv4 e IPv6 e deseja conectá-la à WAN Virtual, atualmente, não há suporte para esse cenário.

Para o cenário de VPN de usuário ponto a site com a análise da Internet por meio do Firewall do Azure, você provavelmente terá que desligar a conectividade IPv6 no dispositivo cliente a fim de forçar o tráfego para o hub da WAN Virtual. Isso ocorre porque os dispositivos modernos usam endereços IPv6 por padrão.

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>Qual é a versão de API recomendada a ser usada por scripts que automatizam várias funcionalidades da WAN Virtual?

É necessária uma versão mínima de 05-01-2020 (1 de maio de 2020).

### <a name="are-there-any-virtual-wan-limits"></a>Há algum limite para a WAN Virtual?

Confira a seção [Limites da WAN Virtual](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits) na página Limites de serviço e assinatura.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Quais são as diferenças entre os tipos de WAN Virtual (Básico e Standard)?

Consulte [WANs virtuais Básicas e Standard](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Para obter os preços, confira a página de [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="does-virtual-wan-store-customer-data"></a>A WAN virtual armazena dados do cliente?

Não. A WAN virtual não armazena nenhum dado do cliente.

### <a name="are-there-any-managed-service-providers-that-can-manage-virtual-wan-for-users-as-a-service"></a>Há Provedores de Serviço Gerenciado que podem gerenciar a WAN Virtual para usuários como um serviço?

Sim. Para obter uma lista de soluções de MSP (provedor de serviços gerenciados) habilitadas através do Azure Marketplace, confira [Ofertas do Azure Marketplace de parceiros MSP de Rede do Azure](../articles/networking/networking-partners-msp.md#msp).

### <a name="how-does-virtual-wan-hub-routing-differ-from-azure-route-server-in-a-vnet"></a>Como o roteamento do hub de WAN Virtual difere do Servidor de Rotas do Azure em uma VNet?

O Servidor de Rotas do Azure fornece um serviço de emparelhamento BGP (Border Gateway Protocol) que pode ser usado por NVAs (Soluções de Virtualização de Rede) para aprender rotas do servidor de rotas em uma VNet de hub do tipo faça você mesmo. O roteamento de WAN Virtual fornece várias funcionalidades, incluindo roteamento de trânsito de VNet para VNet, roteamento personalizado, associação de rota personalizada e propagação, além de um serviço de hub totalmente entrelaçado sem toque, juntamente com os serviços de conectividade do ExpressRoute, VPN de Site, VPN P2S de grande escala/usuário remoto e funcionalidades de hub seguro (Firewall do Azure). Ao estabelecer um emparelhamento via protocolo BGP entre sua NVA e o Servidor de Rotas do Azure, você pode anunciar endereços IP da sua NVA para sua rede virtual. Para todas as funcionalidades de roteamento avançadas, como roteamento de trânsito, roteamento personalizado etc, você pode usar o roteamento de WAN Virtual.
