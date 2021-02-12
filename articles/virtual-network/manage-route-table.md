---
title: Criar, alterar ou excluir uma tabela de rotas do Azure
titlesuffix: Azure Virtual Network
description: Saiba onde encontrar informações sobre o roteamento de tráfego de rede virtual e como criar, alterar ou excluir uma tabela de rotas.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: a8c38f4e84a6a653c3a4d887c82340409fc363af
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220585"
---
# <a name="create-change-or-delete-a-route-table"></a>Criar, alterar ou excluir uma tabela de rotas

O Azure roteia o tráfego automaticamente entre redes virtuais, redes locais e sub-redes do Azure. Se você desejar alterar qualquer roteamento padrão do Azure, poderá criar uma tabela de rotas para fazer isso. Se você for novo no roteamento em redes virtuais, poderá saber mais sobre ela no [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md) ou concluindo um [tutorial](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Antes de começar

Se você não tiver uma, configure uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Em seguida, conclua uma dessas tarefas antes de iniciar as etapas em qualquer seção deste artigo:

- **Usuários do portal**: entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

- **Usuários do PowerShell**: execute os comandos no [Azure cloud Shell](https://shell.azure.com/powershell)ou execute o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Na guia Azure Cloud Shell navegador, localize a lista suspensa **selecionar ambiente** e escolha **PowerShell** se ela ainda não estiver selecionada.

    Se você estiver executando o PowerShell localmente, use Azure PowerShell módulo versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az.Network` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Também execute `Connect-AzAccount` para criar uma conexão com o Azure.

- **Usuários da CLI (interface de linha de comando) do Azure**: execute os comandos no [Azure cloud Shell](https://shell.azure.com/bash)ou execute a CLI do seu computador. Use CLI do Azure versão 2.0.31 ou posterior se você estiver executando o CLI do Azure localmente. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Também execute `az login` para criar uma conexão com o Azure.

A conta que você faz logon ou conecta-se ao Azure com deve ser atribuída à [função de colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Há um limite para quantas tabelas de rotas você pode criar por assinatura e local do Azure. Para obter detalhes, consulte [limites de rede-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No menu do [portal do Azure](https://portal.azure.com) ou na **Página Inicial**, selecione **Criar um recurso**.

1. Na caixa de pesquisa, insira *Tabela de rotas*. Quando a **Tabela de rotas** é exibida nos resultados da pesquisa, selecione-a.

1. Na página **Tabela de rotas**, selecione **Criar**.

1. Na caixa de diálogo **criar tabela de rotas** :

    1. Insira um **nome** para a tabela de rotas.
    1. Escolha sua **Assinatura**.
    1. Escolha um **grupo de recursos** existente ou selecione **criar novo** para criar um novo grupo de recursos.
    1. Escolha um **Local**.
    1. Se você planeja associar a tabela de rotas a uma sub-rede em uma rede virtual que está conectada à sua rede local por meio de um gateway de VPN e não deseja propagar suas rotas locais para as interfaces de rede na sub-rede, defina a propagação de **rota do gateway de rede virtual** como **desabilitada**.

1. Selecione **criar** para criar sua nova tabela de rotas.

### <a name="create-route-table---commands"></a>Criar tabela de rotas-comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Exibir tabelas de rotas

Acesse o [portal do Azure](https://portal.azure.com) para gerenciar sua rede virtual. Pesquise por **Tabela de rotas** e selecione essa opção. As tabelas de rotas que existem na sua assinatura estão listadas.

### <a name="view-route-table---commands"></a>Exibir tabela de rotas-comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network Route-tabela List](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Exibir detalhes de uma tabela de rotas

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar sua rede virtual. Pesquise por **Tabela de rotas** e selecione essa opção.

1. Na lista tabela de rotas, escolha a tabela de rotas para a qual você deseja exibir detalhes.

1. Na página tabela de rotas, em **configurações**, exiba as **rotas** na tabela de rotas ou as **sub-redes** às quais a tabela de rotas está associada.

Para saber mais sobre configurações comuns do Azure, veja as seguintes informações:

- [Log de atividades](../azure-monitor/platform/platform-logs-overview.md)
- [Controle de acesso (IAM)](../role-based-access-control/overview.md)
- [Marcas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Bloqueios](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Script de automação](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Exibir detalhes da tabela de rotas – comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network Route-tabela show](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Alterar uma tabela de rotas

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar sua rede virtual. Pesquise por **Tabela de rotas** e selecione essa opção.

1. Na lista tabela de rotas, escolha a tabela de rotas que você deseja alterar.

As alterações mais comuns são [Adicionar](#create-a-route) rotas, [remover](#delete-a-route) rotas, [associar](#associate-a-route-table-to-a-subnet) tabelas de rotas a sub-redes ou [dissociar](#dissociate-a-route-table-from-a-subnet) tabelas de rotas de sub-redes.

### <a name="change-a-route-table---commands"></a>Alterar uma tabela de rotas – comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network Route-Table Update](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Opcionalmente, você pode associar uma tabela de rotas a uma sub-rede. Uma tabela de rotas pode ser associada a zero ou mais sub-redes. Como as tabelas de rotas não estão associadas a redes virtuais, você deve associar uma tabela de rotas a cada sub-rede à qual deseja que a tabela de rotas seja associada. O Azure roteia todo o tráfego que sai da sub-rede com base nas rotas que você criou nas tabelas de rota, [rotas padrão](virtual-networks-udr-overview.md#default)e rotas propagadas de uma rede local, se a rede virtual estiver conectada a um gateway de rede virtual do Azure (EXPRESSROUTE ou VPN). Você só pode associar uma tabela de rotas a sub-redes em redes virtuais que existem no mesmo local e assinatura do Azure que a tabela de rotas.

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar sua rede virtual. Pesquise por **Redes virtuais** e selecione essa opção.

1. Na lista rede virtual, escolha a rede virtual que contém a sub-rede à qual você deseja associar uma tabela de rotas.

1. Na barra de menus da rede virtual, escolha **sub-redes**.

1. Selecione a sub-rede à qual você deseja associar a tabela de rotas.

1. Em **tabela de rotas**, escolha a tabela de rotas que você deseja associar à sub-rede.

1. Clique em **Salvar**.

Se sua rede virtual estiver conectada a um gateway de VPN do Azure, não associe uma tabela de rotas à [sub-rede de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) que inclui uma rota com um destino de *0.0.0.0/0*. Isso pode impedir que o gateway funcione corretamente. Para obter mais informações sobre como usar *0.0.0.0/0* em uma rota, consulte [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Associar uma tabela de rotas – comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Desassociar uma tabela de rotas de uma sub-rede

Quando você desassocia uma tabela de rota de uma sub-rede, o Azure roteia o tráfego com base em suas [rotas padrão](virtual-networks-udr-overview.md#default).

1. Acesse o [portal do Azure](https://portal.azure.com) para gerenciar sua rede virtual. Pesquise por **Redes virtuais** e selecione essa opção.

1. Na lista rede virtual, escolha a rede virtual que contém a sub-rede da qual você deseja dissociar uma tabela de rotas.

1. Na barra de menus da rede virtual, escolha **sub-redes**.

1. Selecione a sub-rede da qual você deseja desassociar a tabela de rotas.

1. Em **tabela de rotas**, escolha **nenhum**.

1. Clique em **Salvar**.

### <a name="dissociate-a-route-table---commands"></a>Dissociar uma tabela de rotas – comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Excluir uma tabela de rotas

Não é possível excluir uma tabela de rotas associada a nenhuma sub-rede. [Desassocie](#dissociate-a-route-table-from-a-subnet) uma tabela de rota de todas as sub-redes antes de tentar excluí-la.

1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar suas tabelas de rotas. Pesquise por **Tabela de rotas** e selecione essa opção.

1. Na lista tabela de rotas, escolha a tabela de rotas que você deseja excluir.

1. Selecione **excluir** e, em seguida, selecione **Sim** na caixa de diálogo de confirmação.

### <a name="delete-a-route-table---commands"></a>Excluir uma tabela de rotas – comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network rota-tabela Delete](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Criar uma rota

Há um limite para quantas rotas por tabela de rotas podem ser criadas por assinatura e local do Azure. Para obter detalhes, consulte [limites de rede-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar suas tabelas de rotas. Pesquise por **Tabela de rotas** e selecione essa opção.

1. Na lista tabela de rotas, escolha a tabela de rotas à qual você deseja adicionar uma rota.

1. Na barra de menus da tabela de rotas, escolha **rotas**  >  **Adicionar**.

1. Insira um **nome de rota** exclusivo para a rota dentro da tabela de rotas.

1. Insira o **prefixo de endereço**, na notação CIDR (roteamento sem classe Inter-Domain), para a qual você deseja rotear o tráfego. O prefixo não pode ser duplicado em mais de uma rota dentro da tabela de rotas, embora o prefixo possa estar dentro de outro prefixo. Por exemplo, se você definiu *10.0.0.0/16* como um prefixo em uma rota, ainda poderá definir outra rota com o prefixo de endereço *10.0.0.0/22* . O Azure seleciona uma rota para o tráfego com base na correspondência de prefixo mais longa. Para saber mais, confira [como o Azure seleciona uma rota](virtual-networks-udr-overview.md#how-azure-selects-a-route).

1. Escolha um **tipo de próximo salto**. Para saber mais sobre os tipos de próximo salto, consulte [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md).

1. Se você escolher um **tipo do próximo salto** de **dispositivo virtual**, insira um endereço IP para o **endereço do próximo salto**.

1. Selecione **OK**.

### <a name="create-a-route---commands"></a>Criar um comando de rota

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Exibir rotas

Uma tabela de rotas contém zero ou mais rotas. Para saber mais sobre as informações listadas ao exibir rotas, consulte [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md).

1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar suas tabelas de rotas. Pesquise por **Tabela de rotas** e selecione essa opção.

1. Na lista tabela de rotas, escolha a tabela de rotas para a qual você deseja exibir rotas.

1. Na barra de menus da tabela de rotas, escolha **rotas** para ver a lista de rotas.

### <a name="view-routes---commands"></a>Exibir rotas-comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network Route-tabela Route List](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Exibir detalhes de uma rota

1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar suas tabelas de rotas. Pesquise por **Tabela de rotas** e selecione essa opção.

1. Na lista tabela de rotas, escolha a tabela de rotas que contém a rota para a qual você deseja exibir detalhes.

1. Na barra de menus da tabela de rotas, escolha **rotas** para ver a lista de rotas.

1. Selecione a rota cujos detalhes você deseja exibir.

### <a name="view-details-of-a-route---commands"></a>Exibir detalhes de um comando de rota

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network rota-tabela rota mostrar](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Alterar uma rota

1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar suas tabelas de rotas. Pesquise por **Tabela de rotas** e selecione essa opção.

1. Na lista tabela de rotas, escolha a tabela de rotas que contém a rota que você deseja alterar.

1. Na barra de menus da tabela de rotas, escolha **rotas** para ver a lista de rotas.

1. Escolha a rota que você deseja alterar.

1. Altere as configurações existentes para suas novas configurações e selecione **Salvar**.

### <a name="change-a-route---commands"></a>Alterar um comando de rota

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network Route-tabela Route Update](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Excluir uma rota

1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar suas tabelas de rotas. Pesquise por **Tabela de rotas** e selecione essa opção.

1. Na lista tabela de rotas, escolha a tabela de rotas que contém a rota que você deseja excluir.

1. Na barra de menus da tabela de rotas, escolha **rotas** para ver a lista de rotas.

1. Escolha a rota que você deseja excluir.

1. Selecione **excluir** e, em seguida, selecione **Sim** na caixa de diálogo de confirmação.

### <a name="delete-a-route---commands"></a>Excluir um comando de rota

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network Route-tabela Route Delete](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Exibir rotas efetivas

As rotas efetivas para cada interface de rede anexada à VM são uma combinação de tabelas de rotas que você criou, as rotas padrão do Azure e todas as rotas propagadas de redes locais por meio do Border Gateway Protocol (BGP) por meio de um gateway de rede virtual do Azure. Entender as rotas em vigor para um adaptador de rede é útil ao solucionar problemas de roteamento. Você pode exibir as rotas em vigor para qualquer interface de rede conectada a uma VM em execução.

1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar suas VMs. Pesquise por **Máquinas virtuais** e selecione essa opção.

1. Na lista máquina virtual, escolha a VM para a qual você deseja exibir rotas efetivas.

1. Na barra de menus da VM, escolha **rede**.

1. Selecione o nome de um adaptador de rede.

1. Na barra de menus da interface de rede, selecione **rotas efetivas**.

1. Examine a lista de rotas efetivas para ver se existe a rota correta para onde você deseja rotear o tráfego. Saiba mais sobre os tipos de próximo salto que você vê nesta lista no [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Exibir rotas efetivas-comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network NIC show-efetivo-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Validar o roteamento entre dois pontos de extremidade

Você pode determinar o tipo de próximo salto entre uma máquina virtual e o endereço IP de outro recurso do Azure, um recurso local ou um recurso na Internet. Determinar o roteamento do Azure é útil ao solucionar problemas de roteamento. Para concluir essa tarefa, você deve ter um observador de rede existente. Se você não tiver um observador de rede existente, crie um, concluindo as etapas em [criar uma instância do observador de rede](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Vá para o [portal do Azure](https://portal.azure.com) para gerenciar seus observadores de rede. Procure e selecione **observador de rede**.

1. Na barra de menus do observador de rede, escolha **próximo salto**.

1. No **observador de rede | Página do próximo salto** :

    1. Escolha sua **assinatura** e o **grupo de recursos** da VM de origem da qual você deseja validar o roteamento.

    1. Escolha a **máquina virtual** e a **interface de rede** que está anexada à VM.
    
    1. Insira um **endereço IP de origem** atribuído à interface de rede da qual você deseja validar o roteamento.

    1. Insira um **endereço IP de destino** para o qual você deseja validar o roteamento.

1. Selecione **Próximo salto**.

Após uma breve espera, o Azure informa o tipo do próximo salto e a ID da rota que encaminharam o tráfego. Saiba mais sobre os tipos de próximo salto que você vê retornados no [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Validar o roteamento entre dois pontos de extremidade-comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [AZ Network observador mostrar-próximo salto](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Permissões

Para realizar tarefas em tabelas de rota e rotas, sua conta deve ser atribuída à [função de colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as ações apropriadas listadas na tabela a seguir:

| Ação                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Ler uma tabela de rotas                                    |
| Microsoft.Network/routeTables/write                             |   Criar ou atualizar tabela de rotas                        |
| Microsoft.Network/routeTables/delete                            |   Excluir uma tabela de rotas                                  |
| Microsoft.Network/routeTables/join/action                       |   Associar uma tabela de rotas a uma sub-rede                   |
| Microsoft.Network/routeTables/routes/read                       |   Ler uma rota                                          |
| Microsoft.Network/routeTables/routes/write                      |   Criar ou atualizar uma rota                              |
| Microsoft.Network/routeTables/routes/delete                     |   Excluir uma rota                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Obter tabela de rotas em vigor para uma interface de rede |
| Microsoft.Network/networkWatchers/nextHop/action                |   Obtém o próximo salto de uma VM                           |

## <a name="next-steps"></a>Próximas etapas

- Criar uma tabela de rotas usando o [PowerShell](powershell-samples.md) ou [CLI do Azure](cli-samples.md) scripts de exemplo ou modelos do Azure [Resource Manager](template-samples.md)
- Criar e atribuir [definições de Azure Policy](./policy-reference.md) para redes virtuais