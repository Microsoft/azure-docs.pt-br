---
title: 'Criar e definir políticas de IPsec personalizadas para ponto a site: PowerShell'
titleSuffix: Azure VPN Gateway
description: Este artigo ajuda você a criar e definir políticas de IPSec personalizadas para configurações de P2S de gateway de VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/09/2020
ms.author: cherylmc
ms.openlocfilehash: a77a870304e20c179e22b4a4ffe404315894d8a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743685"
---
# <a name="create-and-set-custom-ipsec-policies-for-point-to-site-preview"></a>Criar e definir políticas de IPsec personalizadas para ponto a site (versão prévia)

Se o seu ambiente exigir uma política IPsec personalizada para criptografia, você poderá facilmente configurar um objeto de política com as configurações necessárias. Este artigo ajuda você a criar um objeto de política personalizado e, em seguida, defini-lo usando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

Verifique se o seu ambiente atende aos seguintes pré-requisitos:

* Você tem uma VPN ponto a site em funcionamento já configurada. Se não tiver, configure uma usando as etapas do artigo **criar uma VPN ponto a site**  usando o [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)ou o [portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="1-set-variables"></a><a name="signin"></a>1. definir variáveis

Declare as variáveis que você quer usar. Use o exemplo a seguir, substituindo os valores para seu próprio quando necessário. Se você fechar sua sessão do PowerShell/Cloud Shell a qualquer momento durante o exercício, basta copiar e colar os valores novamente para redeclarar as variáveis.

  ```azurepowershell-interactive
  $RG = "TestRG"
  $GWName = "VNet1GW"
  ```

## <a name="2-create-policy-object"></a><a name="create"></a>2. criar objeto de política

Crie um objeto de política IPsec personalizado. Você pode ajustar os valores para atender aos critérios necessários.

```azurepowershell-interactive
$vpnclientipsecpolicy = New-AzVpnClientIpsecPolicy -IpsecEncryption AES256 -IpsecIntegrity SHA256 -SALifeTime 86471 -SADataSize 429496 -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup2 -PfsGroup PFS2
```

## <a name="3-update-gateway-and-set-policy"></a><a name="update"></a>3. atualizar o gateway e definir a política

Nesta etapa, atualize seu gateway de VPN P2S existente e defina a política de IPsec.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway -VpnClientIpsecPolicy $vpnclientipsecpolicy
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre configurações de P2S, consulte [sobre VPN ponto a site](point-to-site-about.md).
