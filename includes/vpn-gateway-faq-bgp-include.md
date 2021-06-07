---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0eb509d543bc9b4a8846319126185eb1f27ec7a2
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953435"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>O BGP tem suporte em todas as SKUs de Gateway de VPN do Azure?

O BGP tem suporte em todos os SKUs de Gateway de VPN do Azure, exceto o SKU Básico.

### <a name="can-i-use-bgp-with-azure-policy-vpn-gateways"></a>Posso usar o BGP com gateways de VPN do Azure Policy?

Não, o BGP é compatível somente com gateways de VPN baseados em rota.

### <a name="what-asns-autonomous-system-numbers-can-i-use"></a>Quais ASNs (Números do Sistema Autônomo) posso usar?

Você pode usar seus ASNs públicos ou ASNs privados para redes locais e redes virtuais do Azure. Não é possível usar os intervalos reservados pelo Azure nem pela IANA.

Os seguintes ASNs são reservados pelo Azure ou pela IANA:
* ASNs reservados pelo Azure:

  * ASNs públicos: 8074, 8075, 12076
  * ASNs privados: 65515, 65517, 65518, 65519, 65520
* Os ASNs [reservados pela IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):

  * 23456, 64496-64511, 65535-65551 e 429496729

Não é possível especificar esses ASNs para seus dispositivos VPN locais ao se conectar a gateways de VPN do Azure.

### <a name="can-i-use-32-bit-4-byte-asns"></a>Posso usar ASNs de 32 bits (4 bytes)?

Sim, o Gateway de VPN agora é compatível com ASNs de 32 bits (4 bytes). Para configurar usando um ASN em formato decimal, use o PowerShell, a CLI do Azure ou o SDK do Azure.

### <a name="what-private-asns-can-i-use"></a>Quais ASNs privados posso usar?

Os intervalos utilizáveis de ASNs privados são:

* 64512-65514 e 65521-65534

Esses ASNs não são reservados pela IANA nem pelo Azure para uso, portanto, podem ser usados para atribuição ao seu gateway de VPN do Azure.

### <a name="what-address-does-vpn-gateway-use-for-bgp-peer-ip"></a>Qual endereço o Gateway de VPN usa para o IP do par no nível de protocolo BGP?

Por padrão, o Gateway de VPN alocará um endereço IP do intervalo *GatewaySubnet* para gateways de VPN em espera ativos ou dois endereços IP para gateways de VPN ativos/ativos. Esses endereços são alocados automaticamente quando você cria o gateway de VPN. É possível obter o real endereço IP BGP alocado usando o PowerShell ou localizando-o no portal do Azure. No PowerShell, use **Get-AzVirtualNetworkGateway** e procure a propriedade **bgpPeeringAddress**. No portal do Azure, na página **Configuração de Gateway**, procure na propriedade da opção **Configurar BGP ASN**.

Se os roteadores VPN locais usarem endereços IP **APIPA** (169.254.x.x) como os endereços IP BGP, você precisará especificar um **endereço IP BGP APIPA do Azure** adicional no gateway de VPN do Azure. O Gateway de VPN do Azure selecionará o endereço APIPA a ser usado com o par no nível de protocolo BGP APIPA local especificado no gateway de rede local ou o endereço IP privado para o par no nível de protocolo BGP local não pertencente ao APIPA. Para obter mais informações, confira [Configurar o BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quais são os requisitos para obter endereços IP do par no nível de protocolo BGP em meu dispositivo VPN?

O seu endereço do par no nível de protocolo BGP local não poderá ser igual ao endereço IP público do dispositivo VPN nem do espaço de endereços da rede virtual do gateway de VPN. Use um endereço IP diferente no dispositivo VPN para o IP de par no nível de protocolo BGP. Ele poderá ser um endereço atribuído à interface de loopback no dispositivo (um endereço IP regular ou um endereço APIPA). Caso o dispositivo use um endereço APIPA para o BGP, você deverá especificar o endereço IP BGP APIPA em seu gateway de VPN do Azure, conforme descrito em como [Configurar o BGP](../articles/vpn-gateway/bgp-howto.md). Especifique esse endereço no gateway de rede local e correspondente que representa o local.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>O que devo especificar como prefixos de endereço para o gateway de rede local ao usar o BGP?

> [!IMPORTANT]
>
>Essa é uma alteração do requisito documentado anteriormente. Caso use o BGP para uma conexão, deixe o campo **Espaço de endereços IP** vazio para o recurso de gateway de rede local correspondente. O Gateway de VPN do Azure adicionará de modo interno uma rota de host ao IP do par no nível de protocolo BGP local pelo túnel IPsec. Não adicione a rota /32 ao campo **Espaço de endereço**. Isso será redundante. Caso use um endereço APIPA como o IP BGP do dispositivo VPN local, ela não poderá ser adicionada a esse campo. Caso adicione outros prefixos ao campo **Espaço de endereços IP**, eles serão adicionados como rotas estáticas ao gateway de VPN do Azure, além das rotas obtidas por meio do BGP.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-virtual-networks"></a>Posso usar o mesmo ASN para redes de VPN locais e redes virtuais do Azure?

Não, você deverá atribuir ASNs diferentes entre redes locais e redes virtuais do Azure caso os esteja conectando juntamente com o BGP. Os gateways de VPN do Azure têm um ASN padrão de 65515 atribuído, mesmo que o BGP esteja habilitado ou não para uma conectividade entre locais. É possível substituir esse padrão atribuindo um ASN diferente ao criar um gateway de VPN ou você pode alterar o ASN depois de criar o gateway. Será necessário atribuir ASNs locais aos gateways de rede locais e correspondentes do Azure.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Quais prefixos de endereço os gateways de VPN do Azure anunciarão para mim?

Os gateways anunciarão as seguintes rotas para seus dispositivos BGP locais:

* Prefixos de endereço da rede virtual.
* Prefixos de endereço para cada gateway de rede local conectado ao gateway de VPN do Azure.
* As rotas obtidas de outras sessões de emparelhamento via protocolo BGP conectadas ao gateway de VPN do Azure, exceto a rota padrão ou rotas sobrepostas com um prefixo de rede virtual.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Quantos prefixos posso anunciar para o Gateway de VPN do Azure?

O Gateway de VPN do Azure é compatível com até 4.000 prefixos. A sessão BGP é descartada se o número de prefixos exceder o limite.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Posso anunciar a rota padrão (0.0.0.0/0) para gateways de VPN do Azure?

Sim. Observe que isso forçará todo o tráfego de saída da rede virtual em direção ao site local. Além disso, impedirá que as VMs da rede virtual aceitem uma comunicação pública da Internet de modo direto, como RDP ou SSH da Internet para as VMs.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Posso anunciar prefixos exatos como meus prefixos de rede virtual?

Não, ação de anunciar os mesmos prefixos como um de seus prefixos de endereço da rede virtual será bloqueada ou filtrada pelo Azure. No entanto, será possível anunciar um prefixo que seja um superconjunto do que você tem em sua rede virtual. 

Por exemplo, caso a rede virtual tenha usado o espaço de endereços 10.0.0.0/16, você poderá anunciar o 10.0.0.0/8. No entanto, não será possível anunciar o 10.0.0.0/16 nem o 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-connections-between-virtual-networks"></a>Posso usar o BGP com minhas conexões entre redes virtuais?

Sim, você poderá usar o BGP para conexões entre locais e conexões entre redes virtuais.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Posso combinar o BGP a conexões não BGP para meu gateways de VPN do Azure?

Sim, você pode combinar conexões BGP e não BGP para o mesmo gateway de VPN do Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>O Gateway de VPN do Azure é compatível com o roteamento de trânsito do BGP?

Sim, o roteamento de trânsito do BGP é compatível. Porém, os gateways de VPN do Azure não anunciam rotas padrão para outros pares no nível de protocolo BGP. Para habilitar o roteamento de trânsito entre vários gateways de VPN do Azure, você deverá habilitar o BGP em todas as conexões intermediárias entre redes virtuais. Para obter mais informações, confira [Sobre o BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-an-azure-vpn-gateway-and-my-on-premises-network"></a>Posso ter mais de um túnel entre um gateway de VPN do Azure e minha rede local?

Sim, você poderá estabelecer mais de um túnel VPN S2S (site a site) entre um gateway de VPN do Azure e sua rede local. Observe que todos esses túneis serão contados em relação ao número total de túneis de seus gateways de VPN do Azure. Além disso, você deverá habilitar o BGP em dois túneis.

Por exemplo, caso você tenha dois túneis redundantes entre o gateway de VPN do Azure e uma de suas redes locais, eles consumirão dois túneis da cota total de seu gateway de VPN do Azure.

### <a name="can-i-have-multiple-tunnels-between-two-azure-virtual-networks-with-bgp"></a>Posso ter vários túneis entre duas redes virtuais do Azure com o BGP?

Sim, mas pelo menos um dos gateways de rede virtual deve estar na configuração ativo-ativo.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-azure-expressroute-and-s2s-vpn-coexistence-configuration"></a>Posso usar o BGP para uma VPN S2S em uma configuração de coexistência do Azure ExpressRoute e da VPN S2S?

Sim.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>O que devo adicionar a meu dispositivo VPN local para a sessão de emparelhamento de BGP?

Adicione uma rota de host do endereço IP do par no nível de protocolo BGP do Azure ao dispositivo VPN. Essa rota apontará para o túnel VPN S2S IPsec. Por exemplo, caso o IP do par de VPN do Azure seja 10.12.255.30, você deverá adicionar uma rota de host ao 10.12.255.30 com uma interface do próximo salto da interface de túnel IPsec correspondente em seu dispositivo VPN.

### <a name="does-the-virtual-network-gateway-support-bfd-for-s2s-connections-with-bgp"></a>O gateway de rede virtual é compatível com o BFD para conexões site a site com o BGP?

Não. O BFD (Detecção de Encaminhamento Bidirecional) é um protocolo que pode ser usado com o BGP para detectar tempos de inatividade próximos de modo mais rápido do que usando "manutenções de atividade" padrão do BGP. O BFD usa temporizadores de subsegundos projetados para funcionar em ambientes de LAN, mas não em uma Internet pública nem em conexões de Rede de Longa Distância.

Para conexões por meio de Internet pública, ter determinados pacotes atrasados ou mesmo ignorados não é incomum. Portanto, introduzir esses temporizadores agressivos poderá adicionar instabilidade. Essa instabilidade poderá fazer com que as rotas sejam amortecidas pelo BGP. Como alternativa, será possível configurar seu dispositivo local com temporizadores menores do que o padrão, um intervalo de "manutenção de atividade" de 60 segundos e um temporizador de retenção de 180 segundos. Isso resultará em um tempo de convergência mais rápido.

### <a name="do-azure-vpn-gateways-initiate-bgp-peering-sessions-or-connections"></a>Os gateways de VPN do Azure iniciam conexões ou sessões de emparelhamento via protocolo BGP?

O gateway iniciará sessões de emparelhamento via protocolo BGP com os endereços IP do par no nível de protocolo BGP local especificados nos recursos de gateway de rede local usando os endereços IP privados nos gateways de VPN. Isso ocorre independentemente de os endereços IP do BGP local estarem no intervalo APIPA ou serem endereços IP privados regulares. Se os dispositivos VPN locais usarem endereços APIPA como IP do BGP, você precisará configurar seu alto-falante BGP para iniciar as conexões.