---
title: Reservar endereços IPv6 públicos e intervalos de endereços em uma rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como reservar endereços IPv6 públicos e intervalos de endereços em uma rede virtual do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: ac79e1eb5c4f7448dc17804cd8aac3cba582497e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88509936"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Reservar prefixo de endereço IPv6 público
O IPv6 para a Rede Virtual do Microsoft Azure (VNet) permite hospedar aplicativos no Azure com conectividade de IPv6 e IPv4 tanto dentro de uma rede virtual quanto de e para a Internet. Além de reservar endereços IPv6 individuais, Reserve intervalos contíguos de endereços IPv6 do Azure (conhecido como prefixo IP) para seu uso. Este artigo descreve como criar endereços IP públicos IPv6 e intervalos de endereços usando o Azure PowerShell e a CLI.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>Criar um único IP público IPv6 reservado

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Você pode criar um único endereço IP público de IPv6 reservado (estático) usando Azure PowerShell com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) da seguinte maneira:

```azurepowershell
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Usando a CLI do Azure

 Você pode criar um único endereço IP público de IPv6 reservado (estático) CLI do Azure com [AZ Network Public-IP Create](/cli/azure/network/public-ip) da seguinte maneira:

```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Criar um prefixo IPv6 reservado (intervalo)

Para reservar um prefixo IPv6, adicione a família de endereços IP do IPv6 ao mesmo comando usado para criar prefixos IPv4. Os comandos a seguir criam um prefixo de tamanho/125 (8 endereços IPv6).

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Você pode criar um endereço IPv6 público usando CLI do Azure com [AZ Network Public-IP Create](/powershell/module/az.network/new-azpublicipprefix) da seguinte maneira:
```azurepowershell
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Usando a CLI do Azure

Você pode criar um endereço IPv6 público usando CLI do Azure da seguinte maneira:

```azurecli
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Alocar um endereço IP público de um prefixo IPv6 reservado

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

 Você cria um IP público IPv6 estático a partir de um prefixo reservado adicionando o `-PublicIpPrefix` argumento ao criar o IP público usando Azure PowerShell. O exemplo a seguir pressupõe que um prefixo foi criado e armazenado em uma variável do PowerShell chamada: *$MyOwnIPv 6prefix*.

```azurepowershell
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Usando a CLI do Azure

O exemplo a seguir pressupõe que um prefixo foi criado e armazenado em uma variável CLI chamada: *IPv6PrefixWestUS*.

```azurecli
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [prefixo de endereço IPv6](ipv6-public-ip-address-prefix.md).
- Saiba mais sobre [endereços IPv6](ipv6-overview.md).
