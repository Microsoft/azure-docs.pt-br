---
title: 'Solucionar problemas de uma conexão VPN site a site do Azure que não pode conectar titleSuffix: gateway de VPN do Azure'
description: Saiba como solucionar problemas de conexão VPN site a site que repentinamente para de funcionar e não pode ser reconectada.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/22/2021
ms.author: genli
ms.openlocfilehash: cebb05b35379573fc9797e89dee3c0c2bf3de6e2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867280"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Solução de problemas: uma conexão VPN site a site do Azure não consegue se conectar e deixa de funcionar

Depois de configurar uma conexão VPN site a site entre uma rede local e uma rede virtual do Azure, a conexão VPN repentinamente deixa de funcionar e não pode ser reconectada. Este artigo apresenta etapas para a solução de problemas para ajudá-lo a resolver esse problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Para resolver o problema, primeiro tente [redefinir o gateway de VPN do Azure](./reset-gateway.md) e redefinir o túnel do dispositivo VPN local. Se o problema persistir, siga essas etapas para identificar a causa do problema.

### <a name="prerequisite-step"></a>Etapa de pré-requisito

Verifique o tipo do gateway de VPN do Azure.

1. Vá para o [Portal do Azure](https://portal.azure.com).

2. Verifique a página **Visão Geral** do gateway de VPN para as informações de tipo.
    
    ![Visão geral do gateway](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Etapa 1. Verifique se o dispositivo VPN local está validado

1. Verifique se você está usando um [dispositivo VPN e uma versão do sistema operacional validados](vpn-gateway-about-vpn-devices.md#devicetable). Se o dispositivo não for um dispositivo VPN validado, talvez seja necessário contatar o fabricante do dispositivo para verificar se há algum problema de compatibilidade.

2. Verifique se o dispositivo VPN está configurado corretamente. Para obter mais informações, consulte [Editar exemplos de configuração de dispositivo](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Etapa 2. Verifique a chave compartilhada

Compare a chave compartilhada do dispositivo VPN local e VPN de Rede Virtual do Azure para assegurar que as chaves correspondem. 

Para exibir a chave compartilhada para a conexão VPN do Azure, utilize um dos seguintes métodos:

**Azure portal**

1. Vá para a conexão site a site do gateway de VPN que você criou.

2. Na seção **configurações** , clique em **chave compartilhada**.
    
    ![Chave compartilhada](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para o modelo de implantação do Azure Resource Manager:

```azurepowershell
Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>
```

Para o modelo de implantação clássico:

```azurepowershell
Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName
```

### <a name="step-3-verify-the-vpn-peer-ips"></a>Etapa 3. Verifique os IPs pares de VPN

-   A definição do IP no objeto do **Gateway de Rede Local** no Azure deve corresponder com o IP do dispositivo local.
-   A definição de IP do gateway do Azure que está configurada no dispositivo local deve corresponder ao IP de gateway do Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Etapa 4. Verifique UDR e NSGs na sub-rede do gateway

Verifique e remova o UDR (roteamento definido pelo usuário) ou NSG (grupos de segurança de rede) na sub-rede de gateway e, em seguida, teste o resultado. Se o problema for resolvido, valide as configurações aplicadas pelo UDR ou NSG.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Etapa 5. Verifique o endereço da interface externa do dispositivo VPN local

Se o endereço IP voltado para a Internet do dispositivo VPN estiver incluído na definição de **Rede local** no Azure, você poderá experimentar desconexões esporádicas.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Etapa 6. Verifique se as sub-redes coincidem exatamente (gateways baseados em políticas do Azure)

-   Verifique se os espaços de endereço da rede virtual correspondem exatamente entre a rede virtual do Azure e as definições locais.
-   Verifique se as sub-redes correspondem exatamente entre o **Gateway de Rede Local** e as definições locais para a rede local.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Etapa 7. Verifique a investigação de integridade do gateway do Azure

1. Abra a investigação de integridade, navegando até a URL a seguir:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Clique no aviso do certificado.
3. Se você receber uma resposta, o gateway de VPN será considerado íntegro. Se você não receber uma resposta, o gateway poderá não estar íntegro ou um NSG na sub-rede do gateway estará causando o problema. O texto a seguir é uma resposta de exemplo:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Etapa 8. Verifique se o dispositivo VPN local tem o recurso PFS habilitado

O recurso PFS pode causar os problemas de desconexão. Se o dispositivo VPN estiver com PFS habilitado, desabilite o recurso. Em seguida, atualize a política IPsec do gateway de VPN.

## <a name="next-steps"></a>Próximas etapas

-   [Configurar uma conexão site a site com uma rede virtual](./tutorial-site-to-site-portal.md)
-   [Configurar uma política IPsec/IKE para conexões VPN site a site](vpn-gateway-ipsecikepolicy-rm-powershell.md)