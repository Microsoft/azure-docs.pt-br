---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/03/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72ddd0b6cd6c3e12417d3698c403f89312b531f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411758"
---
As perguntas frequentes sobre redes virtuais para redes virtuais se aplicam a conexões de Gateway de VPN. Para obter informações sobre o emparelhamento de rede virtual, consulte [emparelhamento de rede Virtual](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>O Azure cobra pelo tráfego entre VNets?

O tráfego de VNet para VNet na mesma região é gratuito para ambas as direções ao usar uma conexão de gateway de VPN. O tráfego de saída de rede virtual com rede virtual entre regiões é cobrado de acordo com as taxas de transferência de dados entre redes virtuais de saída com base nas regiões de origem. Para saber mais, veja a página [Preços do Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Se você estiver conectando VNets usando o Emparelhamento de VNet, em vez de Gateway de VPN, confira a [página de preços de Rede Virtual](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>O tráfego de Rede Virtual para Rede Virtual viaja pela Internet?

Não. O tráfego de rede virtual com rede virtual viaja pelo backbone do Microsoft Azure, não pela Internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Posso estabelecer uma conexão VNet a VNet entre os Locatários do Azure Active Directory (AAD)?

Sim, as conexões VNet a VNet, usando gateways de VPN do Azure, funcionam entre os Locatários do AAD.

### <a name="is-vnet-to-vnet-traffic-secure"></a>O tráfego de VNet para VNet é seguro?

Sim, ele é protegido por criptografia IPsec/IKE.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>É necessário um dispositivo VPN para conectar redes virtuais?

Não. A conexão de várias redes virtuais do Azure entre si não exige um dispositivo VPN, a menos que a conectividade entre locais seja necessária.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Minhas redes virtuais precisam estar na mesma região?

Não. As redes virtuais podem estar na mesma região ou em regiões diferentes do Azure (locais).

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Se as Redes Virtuais não estiverem na mesma assinatura, as assinaturas precisam ser associadas ao mesmo locatário do Active Directory?

Não.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Posso usar VNet para VNet a fim de conectar a redes virtuais em instâncias separadas do Azure? 

Não. VNet para VNet dá suporte à conexão de redes virtuais na mesma instância do Azure. Por exemplo, você não pode criar uma conexão entre a instância do Azure global e a instâncias chinesa/alemã/do governo dos EUA. Considere o uso de uma conexão VPN Site a Site para esses cenários.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Posso usar Rede Virtual para Rede Virtual com conexões multissite?

Sim. A conectividade de rede virtual pode ser usada simultaneamente com VPNs de multissite.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>A quantos sites locais e redes virtuais uma rede virtual pode se conectar?

Veja a tabela [Requisitos de gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements).

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Posso usar Rede Virtual para Rede Virtual para me conectar a máquinas virtuais ou serviços de nuvem fora de uma rede virtual?

Não. A rede virtual com rede virtual dá suporte à conexão de redes virtuais. Ele não dá suporte à conexão de máquinas virtuais ou serviços de nuvem que não estão em uma rede virtual.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Um serviço de nuvem ou um ponto de extremidade de balanceamento de carga pode abranger redes virtuais?

Não. Um serviço de nuvem ou um ponto de extremidade de balanceamento de carga não pode abranger redes virtuais, mesmo que elas estejam conectadas entre si.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Posso usar um tipo de VPN PolicyBased para conexões de Rede Virtual para Rede Virtual ou Multissite?

Não. As conexões de rede virtual para rede virtual e de vários sites exigem gateways de VPN com tipos de VPN RouteBased (anteriormente chamado de Roteamento Dinâmico).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Posso conectar uma rede virtual a um tipo de VPN RouteBased para outra rede virtual com um tipo de VPN PolicyBased?

Não, ambas as redes virtuais DEVEM usar VPNs baseados em rota (anteriormente, chamados de roteamento dinâmico).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Os túneis de VPN compartilham largura de banda?

Sim. Todos os túneis de VPN da rede virtual compartilham a largura de banda disponível no gateway de VPN do Azure e o mesmo SLA de tempo de atividade de gateway de VPN no Azure.

### <a name="are-redundant-tunnels-supported"></a>Há suporte para túneis redundantes?

Os túneis redundantes entre um par de redes virtuais terão suporte quando um gateway de rede virtual estiver configurado como ativo.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Posso ter espaços de endereço sobrepostos para configurações de Rede Virtual para Rede Virtual?

Não. Você não pode ter intervalos de endereços IP sobrepostos.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Pode haver sobreposição de espaços de endereço entre as redes virtuais conectadas e sites local locais?

Não. Você não pode ter intervalos de endereços IP sobrepostos.



