---
title: "Sobre os requisitos criptográficos e os gateways de VPN do Azure| Microsoft Docs"
description: "Este artigo discute os requisitos criptográficos e os gateways de VPN do Azure"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: yushwang
ms.openlocfilehash: d2f3da47f1d4eebe1b81964790ff6612dd78155d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Sobre os requisitos criptográficos e os gateways de VPN do Azure

Este artigo discute como é possível configurar os gateways de VPN do Azure para satisfazer seus requisitos criptográficos para ambos os túneis de VPN S2S entre instalações e as conexões Rede Virtual a Rede Virtual no Azure. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Sobre os parâmetros de política IKE e IPsec para os gateways de VPN do Azure
O padrão de protocolo IKE e IPsec suporta uma ampla gama de algoritmos criptográficos em várias combinações. Se os clientes não solicitarem uma combinação específica de parâmetros e algoritmos criptográficos, os gateways de VPN do Azure utilizarão um conjunto de propostas padrão. Os conjuntos de políticas padrão foram escolhidos para maximizar a interoperabilidade com uma ampla gama de dispositivos de VPN de terceiros em configurações padrão. Como resultado, as políticas e o número de propostas não podem abranger todas as combinações possíveis de intensidades de chave e algoritmos criptográficos disponíveis.

O conjunto de políticas padrão para o gateway de VPN do Azure está listado no documento: [Sobre dispositivos de VPN e parâmetros de IPsec/IKE para conexões de Gateway de VPN Site a Site](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Requisitos criptográficos
Para comunicações que exigem parâmetros ou algoritmos criptográficos específicos, geralmente, devido a requisitos de segurança ou conformidade, os clientes agora podem configurar seus gateways de VPN do Azure para utilizar uma política de IPsec/IKE personalizada com algoritmos criptográficos específicos e intensidades de chave, em vez dos conjuntos de políticas padrão do Azure.

Por exemplo, as políticas de modo principal do IKEv2 para os gateways de VPN do Azure utilizam apenas o Grupo Diffie-Hellman 2 (1024 bits), enquanto os clientes podem precisar especificar grupos mais fortes a serem utilizados no IKE, como o Grupo 14 (2048 bits), o Grupo 24 (Grupo MODP de 2048 bits) ou o ECP (grupos de curvas elípticas) 256 ou 384 bits (Grupo 19 e Grupo 20, respectivamente). Requisitos semelhantes aplicam-se também às políticas de modo rápido do IPsec.

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