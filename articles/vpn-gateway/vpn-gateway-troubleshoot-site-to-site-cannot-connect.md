---
title: "Solução de problemas de conexão VPN site a site do Azure | Microsoft Docs"
description: "Saiba como solucionar problemas de conexão VPN site a site que repentinamente para de funcionar e não pode ser reconectada."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: genli
ms.openlocfilehash: 55cfba5e9730b123bba20dfdc5d10c1157352a35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Solução de problemas: uma conexão VPN site a site do Azure não consegue se conectar e deixa de funcionar

Depois de configurar uma conexão VPN site a site entre uma rede local e uma rede virtual do Azure, a conexão VPN repentinamente deixa de funcionar e não pode ser reconectada. Este artigo apresenta etapas para a solução de problemas para ajudá-lo a resolver esse problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Para resolver o problema, primeiro tente [redefinir o gateway de VPN do Azure](vpn-gateway-resetgw-classic.md) e redefinir o túnel do dispositivo VPN local. Se o problema persistir, siga essas etapas para identificar a causa do problema.

### <a name="prerequisite-step"></a>Etapa de pré-requisito

Verifique o tipo do gateway de VPN do Azure.

1. Vá para o [Portal do Azure](https://portal.azure.com).

2. Verifique a página **Visão Geral** do gateway de VPN para as informações de tipo.
    
    ![Visão geral do gateway](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Etapa 1. Verifique se o dispositivo VPN local está validado

1. Verifique se você está usando um [dispositivo VPN e uma versão do sistema operacional validados](vpn-gateway-about-vpn-devices.md#devicetable). Se o dispositivo não for um dispositivo VPN validado, talvez seja necessário contatar o fabricante do dispositivo para verificar se há algum problema de compatibilidade.

2. Verifique se o dispositivo VPN está configurado corretamente. Para obter mais informações, consulte [Editar exemplos de configuração de dispositivo](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Etapa 2. Verifique a chave compartilhada

Compare a chave compartilhada do dispositivo VPN local e VPN de Rede Virtual do Azure para assegurar que as chaves correspondem. 

Para exibir a chave compartilhada para a conexão VPN do Azure, utilize um dos seguintes métodos:

**Portal do Azure**

1. Vá para a conexão site a site do gateway de VPN que você criou.

2. Na seção **Configurações**, clique em **Chave Compartilhada**.
    
    ![Chave compartilhada](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**PowerShell do Azure**

Para o modelo de implantação do Azure Resource Manager:

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Para o modelo de implantação clássico:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Etapa 3. Verifique os IPs pares de VPN

-   A definição do IP no objeto do **Gateway de Rede Local** no Azure deve corresponder com o IP do dispositivo local.
-   A definição de IP do gateway do Azure que está configurada no dispositivo local deve corresponder ao IP de gateway do Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Etapa 4. Verifique UDR e NSGs na sub-rede do gateway

Verifique e remova o UDR (roteamento definido pelo usuário) ou NSG (grupos de segurança de rede) na sub-rede de gateway e, em seguida, teste o resultado. Se o problema for resolvido, valide as configurações aplicadas pelo UDR ou NSG.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Etapa 5. Verifique o endereço da interface externa do dispositivo VPN local

- Se o endereço IP voltado para a Internet do dispositivo VPN estiver incluído na definição de **Rede local** no Azure, você poderá experimentar desconexões esporádicas.
- A interface externa do dispositivo deve estar diretamente na Internet. Não deve haver nenhuma conversão de endereços de rede ou firewall entre a Internet e o dispositivo.
- Para configurar o clustering de firewall para ter um IP virtual, será necessário interromper o cluster e expor o dispositivo VPN diretamente a uma interface pública com a qual o gateway pode se conectar.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Etapa 6. Verifique se as sub-redes coincidem exatamente (gateways baseados em políticas do Azure)

-   Verifique se as sub-redes correspondem exatamente entre a rede virtual do Azure e as definições locais para a rede virtual do Azure.
-   Verifique se as sub-redes correspondem exatamente entre o **Gateway de Rede Local** e as definições locais para a rede local.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Etapa 7. Verifique a investigação de integridade do gateway do Azure

1. Vá para a [investigação de integridade](https://&lt;YourVirtualNetworkGatewayIP&gt;:8081/healthprobe).

2. Clique no aviso do certificado.
3. Se você receber uma resposta, o gateway de VPN será considerado íntegro. Se você não receber uma resposta, o gateway poderá não estar íntegro ou um NSG na sub-rede do gateway estará causando o problema. O texto a seguir é uma resposta de exemplo:

    &lt;?xml version="1.0"?>  <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Instância primária: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string&gt;

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Etapa 8. Verifique se o dispositivo VPN local tem o recurso PFS habilitado

O recurso PFS pode causar os problemas de desconexão. Se o dispositivo VPN estiver com PFS habilitado, desabilite o recurso. Em seguida, atualize a política IPsec do gateway de VPN.

## <a name="next-steps"></a>Próximas etapas

-   [Configurar uma conexão site a site para uma rede virtual](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Configurar uma política IPsec/IKE para conexões VPN site a site](vpn-gateway-ipsecikepolicy-rm-powershell.md)
