---
title: Diagnosticar um problema de roteamento da máquina virtual do Azure | Microsoft Docs
description: Saiba como diagnosticar um problema de roteamento da máquina virtual, visualizando as rotas efetivas para uma máquina virtual.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: 1c23244707179e05c63ed44b5915e58eefd3f4a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84705037"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnosticar um problema de roteamento da máquina virtual

Neste artigo, você aprenderá como diagnosticar um problema de roteamento, visualizando as rotas que são efetivas para um adaptador de rede em uma VM (máquina virtual). O Azure cria várias rotas padrão para cada sub-rede da rede virtual. É possível substituir as rotas padrão do Azure, definindo rotas em uma tabela de rotas e associando a tabela de rotas a uma sub-rede. A combinação de rotas que você cria, rotas padrão do Azure e quaisquer rotas propagadas da rede local por meio de um gateway de VPN do Azure (se a rede virtual estiver conectada à rede local) por meio do BGP (Border Gateway Protocol), são as rotas efetivas para todos os adaptadores de rede em uma sub-rede. Se você não estiver familiarizado com conceitos de rede virtual, adaptador de rede ou roteamento, consulte [Visão geral da rede virtual](virtual-networks-overview.md), [Adaptador de rede](virtual-network-network-interface.md) e [Visão geral do roteamento](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Cenário

Você tenta conectar uma VM, mas a conexão falha. Para determinar por que não é possível conectar a VM, você pode exibir as rotas efetivas para um adaptador rede usando o [portal do Azure](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell) ou a [CLI do Azure](#diagnose-using-azure-cli).

As etapas a seguir pressupõem que há uma VM existente para visualizar as rotas efetivas. Se você não tiver uma VM existente, primeiro implemente uma VM [ Linux ](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [ Windows ](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para concluir as tarefas deste artigo. Os exemplos neste artigo são para uma VM chamada *myVM* com uma interface de rede chamada *myVMNic1*. A VM e o adaptador de rede estão em um grupo de recursos nomeado *myResourceGroup*, e estão na região *Leste dos EUA*. Altere os valores nas etapas, conforme apropriado, para a VM em que você diagnosticando o problema.

## <a name="diagnose-using-azure-portal"></a>Diagnosticar usando o portal do Azure

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta do Azure que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).
2. Na parte superior do portal do Azure, insira o nome de uma VM que esteja em estado de execução, na caixa de pesquisa. Quando o nome da VM aparecer nos resultados da pesquisa, selecione-o.
3. Em **configurações** à esquerda, selecione **rede** e navegue até o recurso de interface de rede selecionando seu nome.
     ![Exibir interfaces de rede](./media/diagnose-network-routing-problem/view-nics.png)
4. À esquerda, selecione **rotas efetivas**. As rotas efetivas para uma interface de rede denominada **myVMNic1** são mostradas na figura a seguir: ![ Exibir rotas efetivas](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Se houver vários adaptadores de rede conectados à VM, você poderá exibir as rotas efetivas para qualquer adaptador de rede, selecionando-a. Como cada adaptador de rede pode estar em uma sub-rede diferente, cada adaptador de rede pode ter diferentes rotas efetivas.

    No exemplo mostrado na figura anterior, as rotas listadas são rotas padrão que o Azure cria para cada sub-rede. A lista tem pelo menos essas rotas, mas pode ter rotas adicionais, dependendo dos recursos que você pode ter habilitado para a rede virtual, como se estivesse sendo emparelhada por outra rede virtual ou conectado à rede local por meio de um gateway de VPN do Azure. Para saber mais sobre cada uma das rotas e outras rotas que podem ser exibidas para o adaptador de rede, consulte [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md). Se a lista tiver um grande número de rotas, talvez seja mais fácil selecionar **Baixar** para baixar um arquivo .csv com a lista de rotas.

Embora as rotas efetivas tenham sido exibidas na VM nas etapas anteriores, você também poderá exibir rotas efetivas por meio de:
- **Interface de rede individual**: saiba como [ visualizar uma interface de rede](virtual-network-network-interface.md#view-network-interface-settings).
- **Tabela de rotas individuais**: Saiba como [exibir uma tabela de rotas](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnosticar usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

É possível executar os comandos a seguir no [Azure Cloud Shell](https://shell.azure.com/powershell) ou executando o PowerShell no computador. O Azure Cloud Shell é um shell interativo gratuito. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Se você executar o PowerShell do seu computador, precisará do módulo Azure PowerShell, versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` no computador para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se estiver executando o PowerShell localmente, também precisará executar `Connect-AzAccount` para fazer logon no Azure com uma conta que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).

Obtenha as rotas efetivas para uma interface de rede com [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). O exemplo a seguir obtém as rotas efetivas para uma interface de rede chamada *myVMNic1*, que está em um grupo de recursos chamado *MyResource* Group:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Para reconhecer as informações retornadas na saída, consulte [Visão geral do roteamento](virtual-networks-udr-overview.md). A saída será retornada apenas se a VM estiver no estado em execução. Se houver vários adaptadores de rede conectadas à VM, será possível revisar as rotas efetivas para cada adaptador de rede. Como cada adaptador de rede pode estar em uma sub-rede diferente, cada adaptador de rede pode ter diferentes rotas efetivas. Se ainda houver um problema de comunicação, consulte [diagnóstico adicional](#additional-diagnosis) e [considerações](#considerations).

Se você não souber o nome de um adaptador de rede, mas souber o nome da VM à qual o adaptador de rede está conectado, os comandos a seguir retornarão as IDs de todos os adaptadores de rede conectados a uma VM:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Você receberá uma saída semelhante ao exemplo a seguir:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

Na saída anterior, o nome da interface de rede é *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnosticar usando a CLI do Azure

É possível executar os comandos a seguir no [Azure Cloud Shell](https://shell.azure.com/bash) ou executando a CLI no computador. Este artigo requer a CLI do Azure versão 2.0.32 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando a CLI do Azure localmente, também precisará executar `az login` e fazer logon no Azure com uma conta que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).

Obtenha as rotas efetivas para um adaptador de rede com [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). O exemplo a seguir obtém as rotas efetivas para uma interface de rede chamada *myVMNic1* que está em um grupo de recursos chamado *MyResource* Group:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Para reconhecer as informações retornadas na saída, consulte [Visão geral do roteamento](virtual-networks-udr-overview.md). A saída será retornada apenas se a VM estiver no estado em execução. Se houver vários adaptadores de rede conectadas à VM, será possível revisar as rotas efetivas para cada adaptador de rede. Como cada adaptador de rede pode estar em uma sub-rede diferente, cada adaptador de rede pode ter diferentes rotas efetivas. Se ainda houver um problema de comunicação, consulte [diagnóstico adicional](#additional-diagnosis) e [considerações](#considerations).

Se você não souber o nome de um adaptador de rede, mas souber o nome da VM à qual o adaptador de rede está conectado, os comandos a seguir retornarão as IDs de todos os adaptadores de rede conectados a uma VM:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Resolver um problema

Resolver problemas de roteamento normalmente consiste em:

- Adicionar uma rota personalizada para substituir uma das rotas padrão do Azure. Saiba como [adicionar uma rota personalizada](manage-route-table.md#create-a-route).
- Alterar ou remover uma rota personalizada que pode causar roteamento para um local indesejado. Saiba como [alterar](manage-route-table.md#change-a-route) ou [excluir](manage-route-table.md#delete-a-route) uma rota personalizada.
- Garantir que a tabela de rotas que contém quaisquer rotas personalizadas definidas esteja associada à sub-rede na qual o adaptador de rede está. Saiba como [associar uma tabela de rotas a uma sub-rede](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Garantir que dispositivos como o gateway de VPN do Azure ou os dispositivos virtuais de rede que você implantou estejam operacionais. Use a funcionalidade [Diagnóstico do VPN](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Observador de Rede para determinar qualquer problema com um gateway de VPN do Azure.

Se ainda houver problemas de comunicação, confira [Considerações](#considerations) e Diagnóstico adicional.

## <a name="considerations"></a>Considerações

Considere os pontos a seguir ao solucionar problemas de comunicação:

- O roteamento é baseado no LPM (prefixo correspondente mais longo) entre as rotas que você definiu, o BGP (Border Gateway Protocol) e as rotas do sistema. Se houver mais de uma rota com a mesma correspondência de LPM, uma rota será selecionada com base na origem na ordem listada em [Visão geral do roteamento](virtual-networks-udr-overview.md#how-azure-selects-a-route). Com rotas efetivas, somente será possível ver rotas efetivas que são uma correspondência LPM, com base em todas as rotas disponíveis. Ver como as rotas são avaliadas para um adaptador de rede torna muito mais fácil solucionar problemas de rotas específicas que podem estar afetando a comunicação de VM.
- Se você definiu rotas personalizadas para uma NVA (solução de virtualização de rede), com *Solução de Virtualização* como o tipo do próximo salto, assegure-se de que o encaminhamento de IP está habilitado na NVA que recebe o tráfego, ou os pacotes são removidos. Saiba mais sobre [habilitar encaminhamento de IP para um adaptador de rede](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Além disso, o sistema operacional ou o aplicativo dentro da NVA também deve poder encaminhar o tráfego e ser configurado para isso.
- Se você criou uma rota para 0.0.0.0/0, todo o tráfego de saída da Internet será roteado para o próximo salto especificado, como um gateway de VPN ou NVA. A criação dessa rota é muitas vezes referida como túnel forçado. Conexões remotas usando os protocolos RDP ou SSH da Internet para a VM podem não funcionar com essa rota, dependendo de como o próximo salto lida com o tráfego. O túnel forçado pode ser habilitado:
    - Ao usar VPN site a site, crie uma rota com um tipo do próximo salto de *Gateway de VPN*. Saiba mais sobre [configuração de túnel forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Se um 0.0.0.0/0 (rota padrão) for anunciado pelo BGP através de um gateway de rede virtual ao usar VPN de site a site ou um circuito ExpressRoute. Saiba mais sobre como usar BGP com [VPN site a site](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- Para que o tráfego de emparelhamento de rede virtual funcione corretamente, uma rota do sistema com um tipo do próximo salto do *Emparelhamento de VNet* deve existir para o intervalo de prefixo da rede virtual emparelhada. Se essa rota não existir e o link de emparelhamento de rede virtual estiver **conectado**:
    - Aguarde alguns segundos e tente novamente. Se for um link de emparelhamento estabelecido recentemente, ocasionalmente levará mais tempo para propagar rotas a todos os adaptadores de rede em uma sub-rede. Para saber mais sobre emparelhamento de rede virtual, consulte [Visão geral do emparelhamento de rede virtual](virtual-network-peering-overview.md) e [gerenciar emparelhamento de rede virtual](virtual-network-manage-peering.md).
    - As regras do grupo de segurança de rede podem estar afetando a comunicação. Para obter mais informações, consulte [ Diagnosticar um problema no filtro de tráfego de máquina virtual](diagnose-network-traffic-filter-problem.md).
- Embora o Azure atribua rotas padrão a cada adaptador de rede do Azure, se houver vários adaptadores de rede anexados à VM, somente o adaptador de rede principal receberá uma rota padrão (0.0.0.0/0), ou gateway, dentro do sistema operacional da VM. Saiba como criar uma rota padrão para adaptadores de rede secundários anexados a uma VM [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics). Saiba mais sobre [adaptadores de rede primários e secundários](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Diagnóstico adicional

* Para executar um teste rápido para determinar o tipo do próximo salto para o tráfego destinado a um local, use a funcionalidade [Próximo Salto](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Observador de Rede do Azure. O próximo salto informa qual é o tipo do próximo salto para o tráfego destinado a um local especificado.
* Se não houver rotas que causem falha na comunicação de rede de uma VM, o problema pode ser devido ao software de firewall em execução no sistema operacional da VM
* Se você [forçar tráfego de túnel](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para um dispositivo local por meio de um gateway de VPN, ou NVA, talvez não consiga conectar uma VM pela Internet, dependendo de como configurou o roteamento para os dispositivos. Confirme se o roteamento configurado para o dispositivo roteia o tráfego para um endereço IP público ou privado para a VM.
* Use a funcionalidade [solucionar problemas de conexão](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Observador de Rede para determinar o roteamento, a filtragem e as causas dos problemas de comunicação de saída no sistema operacional.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre todas as tarefas, propriedades e configurações para uma [tabela de rotas e rotas](manage-route-table.md).
- Saiba mais sobre todos [tipos do próximo salto, rotas do sistema e como o Azure seleciona uma rota](virtual-networks-udr-overview.md).
