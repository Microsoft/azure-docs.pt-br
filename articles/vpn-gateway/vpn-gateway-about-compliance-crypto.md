---
title: 'Gateway de VPN do Azure: requisitos de criptografia'
description: Saiba como configurar gateways de VPN do Azure para atender aos requisitos de criptografia para túneis de VPN S2S entre locais e conexões de VNet para VNet do Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 12/02/2020
ms.author: yushwang
ms.openlocfilehash: 47d14c5ee7f6c4816bf15351e9cb28a2aaa72b4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546838"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Sobre os requisitos criptográficos e os gateways de VPN do Azure

Este artigo discute como é possível configurar os gateways de VPN do Azure para satisfazer seus requisitos criptográficos para ambos os túneis de VPN S2S entre instalações e as conexões Rede Virtual a Rede Virtual no Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Sobre IKEv1 e IKEv2 para conexões VPN do Azure

Tradicionalmente, permitimos conexões IKEv1 somente para SKUs básicas e conexões IKEv2 permitidas para todas as SKUs de gateway de VPN diferentes de SKUs básicas. Os SKUs básicos permitem apenas uma conexão e, juntamente com outras limitações, como desempenho, os clientes que usam dispositivos herdados que dão suporte apenas a protocolos IKEv1 têm experiência limitada. Para aprimorar a experiência de clientes que usam protocolos IKEv1, agora estamos permitindo conexões IKEv1 para todas as SKUs de gateway de VPN, exceto SKU básico. Para obter mais informações, consulte [SKUs de gateway de VPN](./vpn-gateway-about-vpn-gateway-settings.md#gwsku).

![Conexões IKEv1 e IKEv2 do gateway de VPN do Azure](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Quando as conexões IKEv1 e IKEv2 são aplicadas ao mesmo gateway de VPN, o trânsito entre essas duas conexões é habilitado automaticamente.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Sobre os parâmetros de política IKE e IPsec para os gateways de VPN do Azure

O padrão de protocolo IKE e IPsec suporta uma ampla gama de algoritmos criptográficos em várias combinações. Se você não solicitar uma combinação específica de parâmetros e algoritmos de criptografia, os gateways de VPN do Azure usarão um conjunto de propostas padrão. Os conjuntos de políticas padrão foram escolhidos para maximizar a interoperabilidade com uma ampla gama de dispositivos de VPN de terceiros em configurações padrão. Como resultado, as políticas e o número de propostas não podem abranger todas as combinações possíveis de intensidades de chave e algoritmos criptográficos disponíveis.

### <a name="default-policy"></a>Política padrão

A política padrão definida para o gateway de VPN do Azure está listada no artigo: [sobre dispositivos VPN e parâmetros de IPSec/IKE para conexões de gateway de VPN site a site](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Requisitos criptográficos

Para comunicações que exigem algoritmos ou parâmetros criptográficos específicos, normalmente devido a requisitos de conformidade ou de segurança, agora você pode configurar seus gateways de VPN do Azure para usar uma política de IPsec/IKE personalizada com algoritmos de criptografia e forças de chave específicos, em vez dos conjuntos de políticas padrão do Azure.

Por exemplo, as políticas de modo principal IKEv2 para gateways de VPN do Azure utilizam apenas Diffie-Hellman grupo 2 (1024 bits), enquanto talvez seja necessário especificar grupos mais fortes a serem usados no IKE, como o grupo 14 (2048 bits), o grupo 24 (grupo de MODP bits de 2048 bits) ou ECP (grupos de curva elíptica) 256 ou 384 bit (grupo 19 e grupo 20 Requisitos semelhantes aplicam-se também às políticas de modo rápido do IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Política de IPsec/IKE personalizada com gateways de VPN do Azure

Os gateways de VPN agora suportam política de IPsec/IKE personalizada por conexão. Para uma conexão Site a Site ou Rede Virtual a Rede Virtual, você pode escolher uma combinação específica de algoritmos criptográficos para IPsec e IKE com a intensidade de chave desejada, como mostrado neste exemplo:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Você pode criar uma política de IPsec/IKE e aplicar a uma conexão nova ou existente.

### <a name="workflow"></a>Fluxo de trabalho

1. Crie redes virtuais, gateways de VPN ou gateways de rede locais para a sua topologia de conectividade, conforme descrito em outros documentos de instruções
2. Criar uma política de IPsec/IKE
3. Você pode aplicar a política ao criar uma conexão VNet-to-VNet ou S2S
4. Se a conexão já foi criada, você poderá aplicar ou atualizar a política para uma conexão existente

## <a name="ipsecike-policy-faq"></a>FAQ sobre a política de IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Próximas etapas

Consulte [Configurar a política de IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter as instruções passo a passo sobre a configuração da política de IPsec/IKE personalizada em uma conexão.

Consulte também [Conectar vários dispositivos de VPN baseados em políticas](vpn-gateway-connect-multiple-policybased-rm-ps.md) para saber mais sobre a opção UsePolicyBasedTrafficSelectors.
