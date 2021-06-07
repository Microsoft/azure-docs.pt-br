---
title: Configurar o tráfego do gateway de VPN para o emparelhamento de rede virtual
description: Configure o tráfego de gateway para o emparelhamento de rede virtual, para conectar diretamente duas redes virtuais do Azure em uma para fins de conectividade.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 73a7d76de34d29b2d51c54569b234cd8221b08f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872172"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Configurar o tráfego do gateway de VPN para o emparelhamento de rede virtual

Este artigo ajuda você a configurar o tráfego de gateway para o emparelhamento de rede virtual. O [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md) conecta diretamente duas redes virtuais do Azure, mesclando as duas redes virtuais em uma para fins de conectividade. O [trânsito de gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) é uma propriedade de emparelhamento que permite que uma rede virtual use o gateway de VPN na rede virtual emparelhada para conectividade entre locais ou VNet para vnet. O diagrama a seguir mostra como o tráfego de gateway funciona com o emparelhamento de rede virtual.

![Diagrama de trânsito do gateway](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

No diagrama, o trânsito de gateway permite que as redes virtuais emparelhadas usem o gateway de VPN do Azure no Hub-RM. A conectividade disponível no gateway de VPN, incluindo as conexões S2S, P2S e VNET a VNET, aplica-se a todas as três redes virtuais. A opção de trânsito está disponível para emparelhamento entre os mesmos ou modelos de implantação diferentes. Se você estiver configurando o trânsito entre diferentes modelos de implantação, a rede virtual do Hub e o gateway de rede virtual devem estar no modelo de implantação do Gerenciador de recursos, não no modelo de implantação clássico.
>

Na arquitetura de rede de hub e spoke, um trânsito de gateway permite que redes virtuais de spoke compartilhem o gateway de VPN no hub, em vez de implantar gateways de VPN em cada rede virtual de spoke. As rotas para as redes virtuais conectadas pelo gateway ou das redes locais serão propagadas às tabelas de roteamento para as redes virtuais emparelhadas usando o trânsito de gateway. Você pode desabilitar a propagação automática de rota no gateway de VPN. Crie uma tabela de roteamento com a opção "**Desabilitar propagação de rotas BGP**" e associe a tabela de roteamento às sub-redes para evitar a distribuição de rota para essas sub-redes. Para obter mais informações, consulte [Tabela de roteamento de rede virtual](../virtual-network/manage-route-table.md).

Há dois cenários neste artigo:

* **Mesmo modelo de implantação**: ambas as redes virtuais são criadas no modelo de implantação do Gerenciador de recursos.
* **Modelos de implantação diferentes**: a rede virtual do spoke é criada no modelo de implantação clássico e a rede virtual do Hub e o gateway estão no modelo de implantação do Gerenciador de recursos.

>[!NOTE]
> Se você fizer uma alteração na topologia de rede e tiver clientes VPN do Windows, o pacote de cliente VPN para clientes do Windows deve ser baixado e instalado novamente para que as alterações sejam aplicadas ao cliente.
>

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem as seguintes redes virtuais e permissões:

### <a name="virtual-networks"></a><a name="vnet"></a>Redes virtuais

|VNET|Modelo de implantação| Gateway de rede virtual|
|---|---|---|---|
| Hub-RM| [Resource Manager](./tutorial-site-to-site-portal.md)| [Sim](tutorial-create-gateway-portal.md)|
| Spoke-RM | [Resource Manager](./tutorial-site-to-site-portal.md)| Não |
| Spoke-Classic | [Clássico](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | Não |

### <a name="permissions"></a><a name="permissions"></a>Permissões

As contas usadas para criar um emparelhamento de rede virtual precisam ter as funções ou as permissões necessárias. No exemplo a seguir, se você estivesse emparelhando as duas redes virtuais chamadas **Hub-RM** e **spoke-clássico**, sua conta deve ter as seguintes funções ou permissões para cada rede virtual:

|VNET|Modelo de implantação|Função|Permissões|
|---|---|---|---|
|Hub-RM|Gerenciador de Recursos|[Colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clássico|[Colaborador de rede clássica](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|Spoke-Classic|Gerenciador de Recursos|[Colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clássico|[Colaborador de rede clássica](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Saiba mais sobre [funções internas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e como atribuir permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (somente para o Resource Manager).

## <a name="same-deployment-model"></a><a name="same"></a>Mesmo modelo de implantação

Nesse cenário, as redes virtuais estão no modelo de implantação do Gerenciador de recursos. Use as etapas a seguir para criar ou atualizar os emparelhamentos de rede virtual para habilitar o tráfego de gateway.

### <a name="to-add-a-peering-and-enable-transit"></a>Para adicionar um emparelhamento e habilitar o trânsito

1. No [portal do Azure](https://portal.azure.com), crie ou atualize o emparelhamento de rede virtual do Hub-RM. Navegue até a rede virtual **Hub-RM** . Selecione **emparelhamentos** e **+ Adicionar** para abrir **Adicionar emparelhamento**.
1. Na página **Adicionar emparelhamento** , configure os valores para **essa rede virtual**.

   * Nome do link de emparelhamento: nomeie o link. Exemplo: **HubRMToSpokeRM**
   * Tráfego para rede virtual remota: **permitir**
   * Tráfego encaminhado da rede virtual remota: **permitir**
   * Gateway de rede virtual: **usar o gateway da rede virtual**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="A captura de tela mostra adicionar emparelhamento.":::

1. Na mesma página, continue em para configurar os valores para a **rede virtual remota**.

   * Nome do link de emparelhamento: nomeie o link. Exemplo: **SpokeRMtoHubRM**
   * Modelo de implantação: **Gerenciador de recursos**
   * Rede virtual: **spoke-RM**
   * Tráfego para rede virtual remota: **permitir**
   * Tráfego encaminhado da rede virtual remota: **permitir**
   * Gateway de rede virtual: **usar o gateway da rede virtual remota**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="Captura de tela mostra valores para a rede virtual remota.":::

1. Selecione **Adicionar** para criar o emparelhamento.
1. Verifique o status de emparelhamento como **conectado** em ambas as redes virtuais.

### <a name="to-modify-an-existing-peering-for-transit"></a>Para modificar um emparelhamento existente para trânsito

Se o emparelhamento já tiver sido criado, você poderá modificar o emparelhamento de trânsito.

1. Navegue até a rede virtual. Selecione **emparelhamentos** e selecione o emparelhamento que você deseja modificar.

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="A captura de tela mostra os emparelhamentos selecionados.":::

1. Atualize o emparelhamento VNet.

   * Tráfego para rede virtual remota: **permitir**
   * Tráfego encaminhado para a rede virtual; **Permitir**
   * Gateway de rede virtual: **usar o gateway da rede virtual remota**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="A captura de tela mostra modificar gateway de emparelhamento.":::

1. **Salve** as configurações de emparelhamento.

### <a name="powershell-sample"></a><a name="ps-same"></a>Exemplo do PowerShell

Use também o PowerShell para criar ou atualizar o emparelhamento com o exemplo acima. Substitua as variáveis pelos nomes das redes virtuais e dos grupos de recursos.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="different-deployment-models"></a><a name="different"></a>Modelos de implantação diferentes

Nessa configuração, o spoke de VNet spoke **-clássico** está no modelo de implantação clássico e o Hub vnet do Hub **-RM** está no modelo de implantação do Gerenciador de recursos. Ao configurar o trânsito entre os modelos de implantação, o gateway de rede virtual deve ser configurado para a VNet do Resource Manager, não para a VNet clássica.

Para essa configuração, você só precisa configurar a rede virtual **Hub-RM** . Você não precisa configurar nada na VNet do **spoke clássico** .

1. Na portal do Azure, navegue até a rede virtual **Hub-RM** , selecione **emparelhamentos** e, em seguida, selecione **+ Adicionar**.
1. Na página **Adicionar emparelhamento** , configure os seguintes valores:

   * Nome do link de emparelhamento: nomeie o link. Exemplo: **HubRMToClassic**
   * Tráfego para rede virtual remota: **permitir**
   * Tráfego encaminhado da rede virtual remota: **permitir**
   * Gateway de rede virtual: **usar o gateway da rede virtual**
   * Rede virtual remota: **clássica**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="Adicionar página de emparelhamento para spoke-clássico":::

1. Verifique se a assinatura está correta e, em seguida, selecione a rede virtual na lista suspensa.
1. Selecione **Adicionar** para adicionar o emparelhamento.
1. Verifique o status de emparelhamento como **conectado** na rede virtual Hub-RM. 

Para essa configuração, você não precisa configurar nada na rede virtual do **spoke-clássico** . Quando o status mostrar **conectado**, a rede virtual do spoke poderá usar a conectividade por meio do gateway de VPN na rede virtual do Hub.

### <a name="powershell-sample"></a><a name="ps-different"></a>Exemplo do PowerShell

Use também o PowerShell para criar ou atualizar o emparelhamento com o exemplo acima. Substitua a variáveis e a ID da assinatura pelos valores de rede virtual, dos grupos de recursos e da assinatura. Você precisa apenas criar o emparelhamento de rede virtual em uma rede virtual de hub.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [as restrições e os comportamentos do emparelhamento de rede virtual](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) e [as configurações de emparelhamento de rede virtual](../virtual-network/virtual-network-manage-peering.md#create-a-peering) antes de criar um emparelhamento de rede virtual para uso em produção.
* Saiba como [criar uma topologia de rede hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) com o emparelhamento de rede virtual e o trânsito de gateway.
* [Crie o emparelhamento de rede virtual com o mesmo modelo de implantação](../virtual-network/tutorial-connect-virtual-networks-portal.md).
* [Crie o emparelhamento de rede virtual com modelos de implantação diferentes](../virtual-network/create-peering-different-deployment-models.md).