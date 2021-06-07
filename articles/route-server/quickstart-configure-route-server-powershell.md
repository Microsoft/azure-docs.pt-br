---
title: 'Início rápido: criar e configurar o Servidor de Rota usando o Azure PowerShell'
description: Neste início rápido, você aprenderá a criar e configurar um Servidor de Rota usando o Azure PowerShell.
services: route-server
author: duongau
ms.author: duau
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: route-server
ms.custom:
- mode-api
ms.openlocfilehash: 608ec3755fcd231d5cc89bbc28a01ce172978144
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538707"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>Início rápido: criar e configurar o Servidor de Rota usando o Azure PowerShell

Este artigo ajuda a configurar o Servidor de Rota do Azure para ser emparelhado com uma NVA (Solução de Virtualização de Rede) na rede virtual usando o PowerShell. O Servidor de Rota do Azure aprenderá e programará as rotas da NVA nas máquinas virtuais da rede virtual. O Servidor de Rota do Azure também vai anunciar as rotas de rede virtual para a NVA. Para obter mais informações, leia [Servidor de Rota do Azure](overview.md).

> [!IMPORTANT]
> No momento, o Servidor de Rota do Azure (versão prévia) está na versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Verifique se você tem os módulos mais recentes do PowerShell ou se pode usar o Azure Cloud Shell no portal.
* Examine os [limites de serviço do Servidor de Rota do Azure](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Criar um Servidor de Rota

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>Criar um grupo de recursos e uma rede virtual

Para criar um Servidor de Rota do Azure, você precisa de uma rede virtual para hospedar a implantação. Use o comando a seguir para criar um grupo de recursos e uma rede virtual. Se você já tiver uma rede virtual, pode pular para a próxima seção.

```azurepowershell-interactive
New-AzResourceGroup –Name "RouteServerRG” -Location “West US"
New-AzVirtualNetwork –ResourceGroupName "RouteServerRG" -Location "West US" -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Adicione uma sub-rede chamada *RouteServerSubnet* para implantar o Servidor de Rota do Azure. Essa é uma sub-rede dedicada somente para o Servidor de Rota do Azure. O RouteServerSubnet deve ser /27 ou um prefixo mais curto (como /26, /25), caso contrário, você receberá uma mensagem de erro ao adicionar o Servidor de Rota do Azure.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "myVirtualNetwork" - ResourceGroupName "RouteServerRG"
    Add-AzVirtualNetworkSubnetConfig –Name "RouteServerSubnet" -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. Obtenha a ID de RouteServerSubnet. Para ver a ID de recurso de todas as sub-redes na rede virtual, use este comando:

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "vnet_name" -ResourceGroupName "RouteServerRG"
    $vnet.Subnets
    ```

A ID de RouteServerSubnet é parecida com a seguinte:

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Crie o Servidor de Rota

Crie o Servidor de Rota usando este comando:

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US" -HostedSubnet "RouteServerSubnet_ID"
```

O local precisa corresponder ao local da rede virtual. O HostedSubnet é a ID de RouteServerSubnet que você obteve na seção anterior.

## <a name="create-peering-with-an-nva"></a>Crie o emparelhamento com uma NVA

Use o seguinte comando para estabelecer o emparelhamento via protocolo BGP do Servidor de Rota para o NVA:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

"nva_ip" é o IP de rede virtual atribuído à NVA. "nva_asn" é o ASN (número de sistema autônomo) configurado na NVA. O ASN pode ser qualquer número de 16 bits que não esteja no intervalo de 65515-65520. Esse intervalo de ASNs é reservado pela Microsoft.

Para configurar o emparelhamento com uma NVA diferente ou outra instância da mesma NVA para redundância, use este comando:

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>Conclua a configuração na NVA

Para concluir a configuração na NVA e habilitar as sessões do protocolo BGP, você precisa do IP e do ASN do Servidor de Rota do Azure. Você pode obter essas informações usando este comando:

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

A saída tem as seguintes informações:

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>Configurar troca de rota

Se você tiver um gateway de ExpressRoute e um gateway de VPN do Azure na mesma VNet e quiser que troquem rotas, pode habilitar a troca de rota no Servidor de Rota do Azure.

1. Habilite a troca de rota entre o Servidor de Rota do Azure e os gateways usando este comando:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Desabilite a troca de rota entre o Servidor de Rota do Azure e os gateways usando este comando:

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>Solução de problemas

Você pode exibir as rotas anunciadas e recebidas pelo Servidor de Rota do Azure usando este comando:

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais do Servidor de Rota do Azure, use estes comandos para remover o emparelhamento via BGP e, em seguida, remova o Servidor de Rota. 

1. Remova o emparelhamento via protocolo BGP entre o Servidor de Rota do Azure e uma NVA usando este comando:

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName "nva_name" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. Remova o Servidor de Rota usando este comando:

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>Próximas etapas

Depois de criar o Servidor de Rota do Azure, continue a aprender como o Servidor de Rota do Azure interage com os gateways de VPN e ExpressRoute: 

> [!div class="nextstepaction"]
> [Suporte à VPN do Azure e ao Azure ExpressRoute](expressroute-vpn-support.md)
