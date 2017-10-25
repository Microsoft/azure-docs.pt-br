---
title: Perguntas frequentes do Gateway de VPN do Azure | Microsoft Docs
description: "Perguntas frequentes sobre o Gateway de VPN. Perguntas frequentes para conexões entre locais de Rede Virtual do Microsoft Azure, conexões de configuração híbrida e gateways de VPN."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: 6ce36765-250e-444b-bfc7-5f9ec7ce0742
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2017
ms.author: cherylmc,yushwang
ms.openlocfilehash: b12eab7a430e620d0b6e872551c0252ccb5d4c14
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="vpn-gateway-faq"></a>Perguntas frequentes de gateway de VPN

## <a name="connecting"></a>Conectar-se a redes virtuais

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Posso conectar redes virtuais em diferentes regiões do Azure?

Sim. Na verdade, não há nenhuma restrição de região. Uma rede virtual pode se conectar a outra rede virtual na mesma região ou em outra região do Azure. 

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Posso conectar redes virtuais em assinaturas diferentes?

Sim.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Posso me conectar vários sites de uma única rede virtual?

Você pode se conectar a vários sites usando o Windows PowerShell e as APIs REST do Azure. Consulte a seção [Conectividade multissite e de VNet para VNet](#V2VMulti) das perguntas frequentes.

### <a name="what-are-my-cross-premises-connection-options"></a>Quais são minhas opções de conexão entre locais?

Há suporte para as seguintes conexões entre locais:

* Site a Site – conexão VPN no IPsec (IKE v1 e IKE v2). Esse tipo de conexão exige um dispositivo VPN ou RRAS. Para obter mais informações, consulte [Site a Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
* Ponto a Site – conexão VPN no SSTP (Secure Socket Tunneling Protocol). Essa conexão não exige um dispositivo VPN. Para obter mais informações, consulte [Ponto a Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* VNet a VNet - esse tipo de conexão é igual a uma configuração Site a Site. VNet a VNet é uma conexão VPN sobre IPsec (IKE v1 e IKE v2). Ela não requer um dispositivo VPN. Para obter mais informações, consulte [VNet a VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
* Multissite - essa é uma variação da configuração Site a Site que permite conectar vários sites locais a uma rede virtual. Para obter mais informações, consulte [Multissite](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).
* ExpressRoute - ExpressRoute é uma conexão direta com o Azure a partir de sua WAN, não uma conexão VPN na Internet pública. Para saber mais, veja a [Visão geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md) e as [Perguntas frequentes sobre o ExpressRoute](../expressroute/expressroute-faqs.md).

Para saber mais sobre conexões de gateway de VPN, confira [Sobre gateway de VPN](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Qual é a diferença entre uma conexão site a site e uma ponto a site?

**Site a Site** as configurações (túnel VPN IPsec/IKE) estão entre o local e o Azure. Isso significa que você pode conectar, a partir de qualquer um dos computadores localizados em suas instalações, qualquer máquina virtual ou instância de função em sua rede virtual, dependendo de como você escolhe configurar o roteamento e as permissões. É uma ótima opção para uma conexão entre locais sempre disponível e é bastante adequada para configurações híbridas. Esse tipo de conexão se baseia em um dispositivo de VPN IPsec (dispositivo de hardware ou software), que deve ser implantado na borda de sua rede. Para criar esse tipo de conexão, você deve ter um endereço IPv4 voltado para o exterior que não está atrás de um NAT.

As configurações **Ponto a Site** (VPN no SSTP) permitem conectar, a partir de um único computador e de qualquer lugar, qualquer coisa localizada em sua rede virtual. Elas usam o cliente de VPN integrado ao Windows. Como parte da configuração de ponto a site, você pode instalar um certificado e um pacote de configuração de cliente VPN, que contém as configurações que permitem que o computador se conecte a qualquer máquina virtual ou instância de função na rede virtual. É ótimo quando você deseja se conectar a uma rede virtual, mas não está localizado no local. Também é uma boa opção quando você não tem acesso a hardware de VPN ou a um endereço IPv4 voltado para o exterior, sendo que ambos são necessários para uma conexão site a site.

Você pode configurar sua rede virtual para usar as opções de site a site e ponto a site simultaneamente, desde que crie sua conexão site a site usando um tipo de VPN com base em rota para seu gateway. Tipos de VPN baseados em rota são chamados de gateways dinâmicos no modelo de implantação clássica.

## <a name="gateways"></a>Gateways da rede virtual

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>Um gateway de VPN é um gateway de rede virtual?

Um gateway de VPN é um tipo de gateway de rede virtual. Um gateway de VPN envia o tráfego criptografado entre sua rede virtual e seu local em uma conexão pública. Você também pode usar o Gateway de VPN para enviar tráfego entre redes virtuais. Quando você cria um gateway de VPN, pode usar o valor 'Vpn' de -GatewayType. Para saber mais, veja [Sobre as configurações de Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).

### <a name="what-is-a-policy-based-static-routing-gateway"></a>O que é um gateway baseado em políticas (roteamento estático)?

Os gateways baseados em política implementam VPNs baseadas em política. As VPNs baseadas em política criptografam e direcionam pacotes por meio de túneis IPsec com base em combinações de prefixos de endereço entre sua rede local e a VNet do Azure. A política (ou o seletor de tráfego) normalmente é definida como uma lista de acesso na configuração de VPN.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>O que é um gateway baseado em rotas (roteamento dinâmico)?

Gateways baseados em rota implementam VPNs baseadas em rota. As VPNs baseadas em rota usam "rotas" da tabela de roteamento ou de encaminhamento de IP para direcionar pacotes para as interfaces de túnel correspondentes. As interfaces de túnel criptografam ou descriptografam então os pacotes para dentro e para fora dos túneis. O seletor de política ou de tráfego para as VPNs baseadas em rota são configurados como qualquer para qualquer (ou curingas).

### <a name="do-i-need-a-gatewaysubnet"></a>É necessária uma 'GatewaySubnet'?

Sim. A sub-rede de gateway contém os endereços IP que usam os serviços de gateway de rede virtual. Você precisa criar uma sub-rede de gateway para a VNet para configurar um gateway de rede virtual. Todas as sub-redes de gateway devem ser nomeadas como ‘GatewaySubnet’ para funcionar adequadamente. Não dê outro nome à sua sub-rede de gateway. E não implante VMs ou qualquer outra coisa na sub-rede de gateway.

Quando você cria a sub-rede de gateway, pode especificar o número de endereços IP que contém a sub-rede. Os endereços IP na sub-rede do gateway são alocados para o serviço de gateway. Algumas configurações exigem mais endereços IP a ser alocada para os serviços de gateway que outras pessoas. Convém certificar-se de que sua sub-rede de gateway contenha endereços IP suficientes para acomodar o crescimento futuro e possíveis novas configurações de conexão adicionais. Dessa forma, embora seja possível criar uma sub-rede de gateway tão pequena quanto /29, é recomendável criar uma sub-rede de gateway de /27 ou maior (/27, /26, /25 etc.). Examine os requisitos para a configuração que deseja criar e verifique se a sub-rede de gateway que você tem atende a esses requisitos.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Posso implantar máquinas virtuais ou instâncias de função na minha sub-rede de gateway?

Não.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Posso obter o endereço IP do gateway de VPN antes de criá-lo?

Não. Você precisa criar seu gateway primeiro para obter o endereço IP. O endereço IP será alterado se você excluir e recriar o gateway de VPN.

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>Eu posso solicitar um endereço IP Público Estático para o meu gateway de VPN?

Não. Somente a atribuição de endereço IP Dinâmico é suportada. No entanto, isso não significa que o endereço IP é alterado depois que ele foi atribuído ao seu gateway de VPN. A única vez em que o endereço IP de gateway de VPN é alterado é quando o gateway é excluído e recriado. O endereço IP público do gateway de VPN não muda durante o redimensionamento, a redefinição ou a manutenção/atualização interna do gateway de VPN. 

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Como meu túnel de VPN é autenticado?

A VPN do Azure usa autenticação PSK (Chave Pré-Compartilhada). Geramos uma PSK (chave pré-compartilhada) durante a criação do túnel de VPN. Você pode alterar a PSK gerada automaticamente para a sua própria com o cmdlet do PowerShell Definir Chave Pré-Compartilhada ou a API REST.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Posso usar a API Definir chave pré-compartilhada para configurar minha VPN de gateway baseada em política (roteamento estático)?

Sim, a API Definir chave pré-compartilhada e o cmdlet do PowerShell podem ser usados para configurar VPNs baseadas em política (estáticas) do Azure e as VPNs de roteamento baseadas em rota (dinâmicas).

### <a name="can-i-use-other-authentication-options"></a>É possível usar outras opções de autenticação?

Estamos limitados ao uso de PSK (chaves pré-compartilhadas) para autenticação.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Como especificar qual tráfego passa pelo gateway de VPN?

#### <a name="resource-manager-deployment-model"></a>Modelo de implantação do Gerenciador de Recursos

* PowerShell: use "AddressPrefix" para especificar o tráfego para o gateway de rede local.
* Portal do Azure: navegue até o Gateway de rede local > Configuração > Espaço de endereço.

#### <a name="classic-deployment-model"></a>Modelo de implantação clássica

* Portal do Azure: navegue até a rede virtual clássica > Conexões VPN > Conexões VPN Site a Site > Nome do site Local > Site local > Espaço de endereço do cliente. 
* Portal clássico: adicione cada intervalo que você deseja enviar pelo gateway para sua rede virtual na página Redes, em Redes Locais. 

### <a name="can-i-configure-force-tunneling"></a>Posso configurar o Túnel Forçado?

Sim. Confira [Configurar o túnel forçado](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Posso configurar meu próprio servidor de VPN no Azure e usá-lo para me conectar à minha rede local?

Sim, você pode implantar seus próprios gateways de VPN ou servidores no Azure, por meio do Azure Marketplace, ou criar seus próprios roteadores de VPN. Você precisa configurar as rotas definidas pelo usuário em sua rede virtual para garantir que o tráfego seja roteado corretamente entre suas redes locais e as sub-redes da rede virtual.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Por que determinadas portas serão abertas no meu gateway VPN?

Elas são necessárias para a comunicação de infraestrutura do Azure. Elas são protegidas (bloqueadas) por certificados do Azure. Sem certificados apropriados, entidades externas, incluindo os clientes desses gateways, não poderão causar efeitos nesses pontos de extremidade.

Um gateway de VPN é fundamentalmente um dispositivo de hospedagem múltipla com um toque de NIC para a rede privada do cliente e uma NIC voltada para a rede pública. As entidades de infraestrutura do Azure não podem tocar em redes privadas de cliente por motivos de conformidade, devendo utilizar pontos de extremidade públicos para comunicação de infraestrutura. Os pontos de extremidade públicos são verificados periodicamente pela auditoria de segurança do Azure.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Mais informações sobre tipos de gateway, requisitos e taxa de transferência

Para saber mais, veja [Sobre as configurações de Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="s2s"></a>Conexões Site a Site e dispositivos VPN

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>O que devo considerar ao escolher um dispositivo VPN?

Validamos um conjunto de dispositivos de VPN site a site padrão em parceria com fornecedores de dispositivos. Uma lista de dispositivos de VPN compatíveis e conhecidos, instruções de configuração correspondentes ou exemplos, e especificações do dispositivo podem ser encontrados no arquivo [Sobre os dispositivos VPN](vpn-gateway-about-vpn-devices.md). Todos os dispositivos das famílias de dispositivos listadas como compatíveis e conhecidas devem funcionar com a Rede Virtual. Para ajudar a configurar seu dispositivo VPN, consulte o exemplo de configuração do dispositivo ou o link que corresponde à família de dispositivos apropriada.

### <a name="where-can-i-find-vpn-device-configuration-settings"></a>Onde posso encontrar as definições de configuração para o dispositivo VPN?

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>Como edito os exemplos de configuração do dispositivo VPN?

Para obter informações sobre a edição dos exemplos de configuração do dispositivo, consulte [Edição de exemplos](vpn-gateway-about-vpn-devices.md#editing).

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>Onde encontro os parâmetros IPsec e IKE?

Para obter os parâmetros IPsec/IKE, consulte [Parâmetros](vpn-gateway-about-vpn-devices.md#ipsec).

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Por que meu túnel VPN baseado em políticas é desativado quando o tráfego está ocioso?

Esse comportamento é esperado para gateways de VPN baseados em políticas (também conhecidos como roteamento estático). Quando o tráfego pelo túnel de fica ocioso por mais de 5 minutos, o túnel é interrompido. Assim que o tráfego começa a fluir em qualquer direção, o túnel é restabelecido imediatamente.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Posso usar VPNs de software para me conectar ao Azure?

Há suporte para servidores RRAS (Roteamento e Acesso Remoto) do Windows Server 2012 para configuração entre locais site a site.

Outras soluções VPN de software devem funcionar com nosso gateway, contanto que estejam em conformidade com implementações de IPsec padrão do setor. Contate o fornecedor do software para obter instruções de configuração e suporte.

## <a name="P2S"></a>Ponto a Site - Autenticação de certificado nativa do Azure

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="P2SRADIUS"></a>Ponto a Site - autenticação RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="V2VMulti"></a>Conexões Rede Virtual para Rede Virtual e Multissite

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Posso usar o gateway de VPN do Azure para o tráfego entre meus sites locais ou para outra rede virtual?

**Modelo de implantação do Resource Manager**<br>
Sim. Veja a seção [BGP](#bgp) para saber mais.

**Modelo de implantação clássica**<br>
O tráfego via Gateway de VPN do Azure é possível usando o modelo de implantação clássica, mas se baseia em espaços de endereço estaticamente definidos no arquivo de configuração de rede. Ainda não há suporte a BGP com Redes Virtuais do Azure e Gateways de VPN usando o modelo de implantação clássica. Sem BGP, a definição manual de espaços de endereço de trânsito é muito propensa a erros e não é recomendada.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>O Azure gera a mesma chave pré-compartilhada IPsec/IKE para todas as minhas conexões VPN para a mesma rede virtual?

Não, por padrão, o Azure gera chaves pré-compartilhadas diferentes para conexões VPN diferentes. No entanto, você pode usar a API REST Definir Chave de Gateway de VPN ou o cmdlet do PowerShell para definir o valor de chave que preferir. A chave deve ser uma cadeia de caracteres alfanumérica com comprimento entre 1 e 128 caracteres.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Obtenho mais largura de banda com mais VPNs site a site do que com uma única rede virtual?

Não, todos os túneis de VPN, incluindo VPNs site a ponto, compartilham o mesmo gateway de VPN do Azure e a largura de banda disponível.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Posso configurar vários túneis entre minha rede virtual e meu site local usando uma VPN multissite?

Sim, mas você deve configurar o BGP em ambos os túneis para o mesmo local.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Posso usar VPNs de ponto a site com minha rede virtual com vários túneis de VPN?

Sim, as VPNs P2S (ponto a site) podem ser usadas com os gateways de VPN conectando a vários sites locais e outras redes virtuais.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Posso conectar uma rede virtual com VPNs IPsec a meu circuito ExpressRoute?

Sim, isso é suportado. Para obter mais informações, consulte [Configurar conexões de VPN Site a Site e de ExpressRoute que coexistam](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="ipsecike"></a>Política do IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="bgp"></a>BGP

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="vms"></a>Conectividade entre locais e VMs

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Se minha máquina virtual estiver em uma rede virtual e eu tiver uma conexão entre locais, como devo me conectar à VM?

Você tem algumas opções. Se você tiver um protocolo RDP habilitado para a sua VM, você pode se conectar à sua máquina virtual usando o endereço IP privado. Nesse caso, você especificaria o endereço IP privado e a porta à qual deseja se conectar (normalmente 3389). Você precisará configurar a porta em sua máquina virtual para o tráfego.

Você também pode se conectar à sua máquina virtual com o endereço IP privado de outra máquina virtual localizada na mesma rede virtual. Você não poderá RDP para sua máquina virtual usando o endereço IP privado se estiver se conectando de um local fora de sua rede virtual. Por exemplo, se tiver uma rede virtual de ponto para site configurada e não estabelecer uma conexão do seu computador, você não poderá se conectar à máquina virtual com o endereço IP privado.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Se a minha máquina virtual estiver em uma rede virtual com conectividade entre locais, todo o tráfego de minha VM passará por essa conexão?

Não. Somente o tráfego com um destino IP contido em intervalos de endereços IP de rede Local virtual de rede especificado passará pelo gateway de rede virtual. O tráfego que tiver um destino IP localizado na rede virtual permanecerá na rede virtual. Outro tráfego é enviado pelo balanceador de carga para as redes públicas ou, se for usado o túnel forçado, enviado pelo gateway da VPN do Azure.

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>Como eu faço para solucionar problemas de uma conexão de RDP para uma VM

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]


## <a name="faq"></a>Perguntas Frequentes da Rede Virtual

As informações adicionais de rede virtual são exibidas em [Perguntas Frequentes sobre Rede Virtual](../virtual-network/virtual-networks-faq.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o Gateway de VPN, veja [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md).
* Para saber mais sobre definições de configuração de Gateway de VPN, veja [Sobre definições de configuração do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).
