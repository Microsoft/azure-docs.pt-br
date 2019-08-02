---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c0ad6ea5a687d066c78533b45a7f531561661bf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171789"
---
Estas perguntas frequentes se aplicam a conexões P2S que usam o modelo de implantação clássico.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Quais sistemas operacionais de cliente posso usar com ponto a site?

Há suporte para os seguintes sistemas operacionais de cliente:

* Windows 7 (32 bits e 64 bits)
* Windows Server 2008 R2 (somente 64 bits)
* Windows 8 (32 bits e 64 bits)
* Windows 8.1 (32 bits e 64 bits)
* Windows Server 2012 (somente 64 bits)
* Windows Server 2012 R2 (somente 64 bits)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>Posso usar qualquer cliente de VPN de software com suporte para SSTP para ponto a site?

Não. O suporte é limitado somente às versões do sistema operacional Windows listadas.

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>Quantos pontos de extremidade de cliente VPN posso ter em minha configuração ponto a site?

Até 128 clientes VPN podem se conectar a uma rede virtual ao mesmo tempo.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Posso usar minha própria CA de raiz de PKI interna para a conectividade ponto a site?

Sim. Anteriormente, somente os certificados raiz autoassinados podiam ser usados. É possível carregar até 20 certificados raiz.

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>Posso atravessar proxies e firewalls usando o recurso de ponto a site?

Sim. Usamos o protocolo SSTP (Secure Socket Tunneling Protocol) para túnel através de firewalls. Esse túnel aparecerá como uma conexão HTTPS.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se eu reiniciar um computador cliente configurado para ponto a site, a VPN se reconectará automaticamente?

Por padrão, o computador cliente não restabelecerá a conexão VPN automaticamente.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>A opção de ponto para site é compatível com a reconexão automática e DDNS nos clientes de VPN?

Não. A reconexão automática e o DDNS atualmente não são compatíveis em VPNs ponto a site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>Posso ter configurações site a site e ponto a site que na mesma rede virtual?

Sim. Essas duas soluções funcionarão se você tiver um tipo VPN baseado em rota para o gateway. Para o modelo de implantação clássica, você precisará de um gateway dinâmico. Não damos suporte a ponto a site para o roteamento estático de gateways VPN ou gateways que usam o cmdlet **-VpnType PolicyBased**.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Posso configurar um cliente de ponto a site para se conectar a várias redes virtuais ao mesmo tempo?

Sim. No entanto, as redes virtuais não podem ter prefixos IP sobrepostos e os espaços de endereço de ponto a site não devem causar sobreposição entre as redes virtuais.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Quanta taxa de transferência posso esperar por meio de conexões site a site ou ponto a site?

É difícil manter a taxa de transferência exata dos túneis de VPN. IPsec e SSTP são protocolos VPN de criptografia pesada. A taxa de transferência também é limitada pela latência e pela largura de banda entre seus locais e na Internet.
