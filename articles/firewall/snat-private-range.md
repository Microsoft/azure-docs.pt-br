---
title: Intervalos de endereços IP privados de SNAT do firewall do Azure
description: Você pode configurar intervalos de endereços IP para SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: c425afc314435c38d15d53ab0c38dcd48e35a40b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508921"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Intervalos de endereços IP privados de SNAT do firewall do Azure

O Firewall do Azure fornece SNAT automático para todo o tráfego de saída para endereços IP públicos. Por padrão, o Firewall do Azure não SNAT com regras de rede quando o endereço IP de destino está em um intervalo de endereços IP privado por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). As regras de aplicativo sempre são aplicadas usando um [proxy transparente](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) , seja qual for o endereço IP de destino.

Essa lógica funciona bem quando você roteia o tráfego diretamente para a Internet. No entanto, se você tiver habilitado o [túnel forçado](forced-tunneling.md), o tráfego de entrada na Internet será no modo SNAT a um dos endereços IP privados do firewall no AzureFirewallSubnet, ocultando a origem do seu firewall local.

Se a sua organização usa um intervalo de endereços IP públicos para redes privadas, o Firewall do Azure realiza SNAT do tráfego para um dos endereços IP privados do firewall no AzureFirewallSubnet. No entanto, você pode configurar o Firewall do Azure para **não** SNAT de seu intervalo de endereços IP públicos. Por exemplo, para especificar um endereço IP individual, você pode especificá-lo da seguinte maneira: `192.168.1.10` . Para especificar um intervalo de endereços IP, você pode especificá-lo da seguinte maneira: `192.168.1.0/24` .

- Para configurar o Firewall do Azure para **nunca** SNAT, independentemente do endereço IP de destino, use **0.0.0.0/0** como seu intervalo de endereços IP privados. Com essa configuração, o Firewall do Azure nunca pode rotear o tráfego diretamente para a Internet. 

- Para configurar o firewall para **sempre** SNAT, independentemente do endereço de destino, use **255.255.255.255/32** como seu intervalo de endereços IP privado.

> [!IMPORTANT]
> O intervalo de endereços privado que você especificar aplica-se apenas às regras de rede. Atualmente, as regras de aplicativo sempre SNAT.

> [!IMPORTANT]
> Se você quiser especificar seus próprios intervalos de endereços IP privados e manter os intervalos de endereços padrão IANA RFC 1918, verifique se sua lista personalizada ainda inclui o intervalo IANA RFC 1918. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Configurar intervalos de endereços IP privados SNAT-Azure PowerShell

Você pode usar Azure PowerShell para especificar intervalos de endereços IP privados para o firewall.

### <a name="new-firewall"></a>Novo firewall

Para um novo firewall, o cmdlet Azure PowerShell é:

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> A implantação do firewall do Azure usando `New-AzFirewall` o requer um endereço IP público e de VNet existente. Consulte [implantar e configurar o Firewall do Azure usando o Azure PowerShell](deploy-ps.md) para obter um guia de implantação completo.

> [!NOTE]
> O IANAPrivateRanges é expandido para os padrões atuais no firewall do Azure, enquanto os outros intervalos são adicionados a ele. Para manter o padrão IANAPrivateRanges em sua especificação de intervalo particular, ele deve permanecer em sua `PrivateRange` especificação, conforme mostrado nos exemplos a seguir.

Para obter mais informações, consulte [New-AzFirewall](/powershell/module/az.network/new-azfirewall).

### <a name="existing-firewall"></a>Firewall existente

Para configurar um firewall existente, use os seguintes cmdlets Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>Configurar intervalos de endereços IP privados SNAT-CLI do Azure

Você pode usar CLI do Azure para especificar intervalos de endereços IP privados para o firewall.

### <a name="new-firewall"></a>Novo firewall

Para um novo firewall, o comando CLI do Azure é:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> A implantação do firewall do Azure usando CLI do Azure comando `az network firewall create` requer etapas de configuração adicionais para criar endereços IP públicos e a configuração de IP. Consulte [implantar e configurar o Firewall do Azure usando o CLI do Azure](deploy-cli.md) para obter um guia de implantação completo.

> [!NOTE]
> O IANAPrivateRanges é expandido para os padrões atuais no firewall do Azure, enquanto os outros intervalos são adicionados a ele. Para manter o padrão IANAPrivateRanges em sua especificação de intervalo particular, ele deve permanecer em sua `PrivateRange` especificação, conforme mostrado nos exemplos a seguir.

### <a name="existing-firewall"></a>Firewall existente

Para configurar um firewall existente, o comando CLI do Azure é:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>Configurar intervalos de endereços IP privados SNAT-modelo ARM

Para configurar o SNAT durante a Implantação de modelo do ARM, você pode adicionar o seguinte à `additionalProperties` Propriedade:

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Configurar intervalos de endereços IP privados SNAT-portal do Azure

Você pode usar o portal do Azure para especificar intervalos de endereços IP privados para o firewall.

1. Selecione seu grupo de recursos e, em seguida, selecione o firewall.
2. Na página **visão geral** , **intervalos de IP privado**, selecione o valor padrão **IANA RFC 1918**.

   A página **Editar prefixos de IP privado** é aberta:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Editar prefixos de IP privado":::

1. Por padrão, o **IANAPrivateRanges** está configurado.
2. Edite os intervalos de endereços IP privados para seu ambiente e, em seguida, selecione **salvar**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [túnel forçado do firewall do Azure](forced-tunneling.md).