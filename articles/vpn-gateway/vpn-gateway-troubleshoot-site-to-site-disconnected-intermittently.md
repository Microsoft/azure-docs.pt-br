---
title: Solucionar problemas de desconexão de VPN site a site do Azure intermitentemente
description: Saiba como solucionar o problema em que a conexão VPN Site a Site é desconectada regularmente.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: genli
ms.openlocfilehash: 582077c46f5fc7176b457cf0d392af48fbe7d40b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369332"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Solução de problemas: desconexão intermitente da VPN Site a Site do Azure

Você poderá observar o problema em que uma conexão VPN Site a Site nova ou existente do Microsoft Azure não é estável ou se desconecta regularmente. Este artigo fornece etapas de solução de problemas para ajudá-lo a identificar e resolver a causa do problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

### <a name="prerequisite-step"></a>Etapa de pré-requisito

Verifique o tipo de gateway de rede virtualdo  Azure:

1. Vá para [portal do Azure](https://portal.azure.com).
2. Verifique a página **Visão Geral** do gateway de rede virtual para os tipos de informações.
    
    ![Visão geral do gateway](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Etapa 1 Verificar se o dispositivo VPN local é validado

1. Verifique se você está usando um [dispositivo VPN e uma versão do sistema operacional validados](vpn-gateway-about-vpn-devices.md#devicetable). Se o dispositivo VPN não for validado, talvez você precise contatar o fabricante do dispositivo para ver se há algum problema de compatibilidade.
2. Verifique se o dispositivo VPN está configurado corretamente. Para obter mais informações, consulte [Editando amostras de configuração de dispositivo](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Etapa 2 Verificar as configurações de Associação de Segurança (para gateways de rede virtual do Azure baseados em políticas)

1. Verifique se a rede virtual, as sub-redes e os intervalos na definição de **Gateway de rede local** no Microsoft Azure são os mesmos da configuração do dispositivo VPN local.
2. Verifique se as configurações de Associação de Segurança correspondem.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Etapa 3 Verificar as Rotas Definidas pelo Usuário ou os Grupos de Segurança de Rede na Sub-rede do Gateway

Uma rota definida pelo usuário na sub-rede do gateway pode estar restringindo parte do tráfego e permitindo outra parte dele. Isso faz com que a conexão VPN pareça não confiável para uma parte do tráfego e boa para outra parte dele. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Etapa 4 Verificar a configuração “um Túnel VPN por Par de Sub-rede” (para gateways de rede virtual baseados em políticas)

Verifique se o dispositivo VPN local está definido para ter **um túnel VPN por par de sub-rede** para gateways de rede virtual baseados em políticas.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Etapa 5 Verificar a Limitação de Associação de Segurança (para gateways de rede virtual baseados em políticas)

O Gateway de rede virtual baseado em política tem um limite de 200 pares de sub-rede de Associação de Segurança. Se o número de sub-redes da rede virtual do Azure multiplicado pelo número de sub-redes locais for maior que 200, você observará a desconexão esporádica de sub-redes.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Etapa 6 Verificar o endereço da interface externa do dispositivo VPN local

- Se o endereço IP para a Internet do dispositivo VPN estiver incluído na definição de **Gateway de rede local** no Azure, você poderá observar desconexões esporádicas.
- A interface externa do dispositivo deve estar diretamente na Internet. Não deve haver nenhum NAT (Conversão de Endereços de Rede) ou firewall entre a Internet e o dispositivo.
-  Se você configurar o Clustering de Firewall para ter um IP virtual, deverá interromper o cluster e expor o dispositivo VPN diretamente para uma interface pública com a qual o gateway pode se comunicar.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Etapa 7 Verificar se o dispositivo VPN local tem o PFS habilitado

O recurso **PFS** pode causar problemas de desconexão. Se o dispositivo VPN tiver o **PFS** habilitado, desabilite o recurso. Em seguida, [atualize a política IPsec do gateway de rede virtual](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Próximas etapas

- [Configurar uma conexão Site a Site para uma rede virtual](./tutorial-site-to-site-portal.md)
- [Configurar a política de IPsec/IKE para conexões VPN site a site](vpn-gateway-ipsecikepolicy-rm-powershell.md)
