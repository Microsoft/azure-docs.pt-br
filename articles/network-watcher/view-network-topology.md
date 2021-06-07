---
title: Exibir a topologia de Rede Virtual do Azure | Microsoft Docs
description: Saiba como exibir os recursos em uma rede virtual e conheça as relações entre os recursos.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: c04da65af27ebd5ac654bc059ae004c157a20f33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84737522"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Exibir a topologia de uma Rede Virtual do Azure

Neste artigo, você aprenderá a exibir recursos em uma Rede Virtual do Microsoft Azure e as relações entre os recursos. Por exemplo, uma rede virtual contém sub-redes. Subredes contêm recursos, como as VMs (máquinas virtuais) do Azure. As VMs têm um ou mais adaptadores de rede. Cada sub-rede pode ter um Grupo de Segurança de Rede e uma tabela de rotas associada a ela. A funcionalidade de topologia do Observador de Rede do Azure permite exibir todos os recursos em uma rede virtual, os recursos associados com os recursos da rede virtual e as relações entre eles.

É possível usar o [portal do Azure](#azure-portal), a [CLI do Azure](#azure-cli) ou o [PowerShell](#powershell) para exibir uma topologia.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Exibir topologia – portal do Azure

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha as [permissões](required-rbac-permissions.md) necessárias.
2. No canto superior esquerdo do portal, selecione **Todos os serviços**.
3. Na caixa de filtros **Todos os serviços**, insira *Observador de Rede*. Quando o **Observador de Rede** for exibido nos resultados, selecione-o.
4. Selecione **Topologia**. Gerar uma topologia requer um observador de rede na mesma região da rede virtual que você deseja gerar a topologia. Se você não tiver um observador de rede habilitado na nessa região, observadores de rede serão criados automaticamente para você em todas as regiões. Os observadores de rede são criados em um grupo de recursos nomeado **NetworkWatcherRG**.
5. Selecione uma assinatura, o grupo de recursos de uma rede virtual para o qual você deseja exibir a topologia e, em seguida, selecione a rede virtual. Na figura a seguir, uma topologia é mostrada para uma rede virtual nomeada *MyVnet*, no grupo de recursos *MyResourceGroup*:

    ![Exibir topologia](./media/view-network-topology/view-topology.png)

    Como você viu na imagem anterior, a rede virtual contém três sub-redes. Uma sub-rede tem uma VM implantada nela. A VM tem um adaptador de rede anexado e um endereço IP público associado com ela. As outras duas sub-redes têm uma tabela de rotas associadas com elas. Cada tabela de rota contém duas rotas. Uma sub-rede tem um Grupo de Segurança de Rede associado a ela. Informações sobre a topologia são mostradas apenas para recursos que estão:
    
    - Dentro do mesmo grupo de recursos e da região que a rede virtual *myVnet*. Por exemplo, um Grupo de Segurança de Rede que existe em um grupo de recursos diferente de *MyResourceGroup* não será exibido, mesmo que o Grupo de Segurança de Rede esteja associado a uma sub-rede na rede virtual *MyVnet*.
    - Dentro, ou associados aos recursos dentro da rede virtual *myVnet*. Por exemplo, um Grupo de Segurança de Rede que não está associado com uma sub-rede ou adaptador de rede na rede virtual *myVnet* não será exibido, mesmo que o Grupo de Segurança de Rede esteja no grupo de recursos *MyResourceGroup*.

   A topologia mostrada na imagem é a da rede virtual criada após a implantação do **script de exemplo Rotear tráfego por meio de uma solução de virtualização de rede**, que pode ser implantado usando a [CLI do Azure](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) ou o [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Selecione **Baixar topologia** para baixar a imagem como um arquivo editável, no formato svg.

Os recursos mostrados no diagrama são um subconjunto dos componentes na rede virtual. Por exemplo, enquanto um Grupo de Segurança de Rede é exibido, as regras de segurança nele não são mostradas no diagrama. Embora não sejam diferenciadas no diagrama, as linhas representam uma das duas relações: *contenção* ou *associação*. Para ver a lista completa de recursos da rede virtual e o tipo de relação entre os recursos, gere a topologia com o [PowerShell](#powershell) ou a [CLI do Azure](#azure-cli).

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Exibir topologia – CLI do Azure

É possível executar os comandos nestas etapas:
- No Azure Cloud Shell, selecione **Experimente** na parte superior direita de qualquer comando. O Azure Cloud Shell é um shell de interativo gratuito com ferramentas comuns do Azure pré-instaladas e configuradas para usar com sua conta.
- Executar a CLI do seu computador. Se você executar a CLI do seu computador, as etapas neste artigo exigirão a CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

A conta que você usa deve ter as [permissões](required-rbac-permissions.md) necessárias.

1. Se você já tiver um observador de rede na mesma região da rede virtual para a qual você deseja criar uma topologia, pule para a etapa 3. Crie um grupo de recursos para conter um observador de rede com [az group create](/cli/azure/group). O exemplo a seguir cria o grupo de recursos na região *eastus*:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Crie um observador de rede com a [configuração az network watcher](/cli/azure/network/watcher#az-network-watcher-configure). O exemplo a seguir cria um observador de rede na região *eastus*:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Exibir a topologia com [az network watcher show-topology](/cli/azure/network/watcher#az-network-watcher-show-topology). O exemplo a seguir exibe a topologia de um grupo de recursos chamado *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Informações de topologia são retornadas somente para os recursos que estão no mesmo grupo de recursos de *MyResourceGroup* e na mesma região do observador de rede. Por exemplo, um Grupo de Segurança de Rede que existe em um grupo de recursos diferente de *MyResourceGroup* não será exibido, mesmo que o Grupo de Segurança de Rede esteja associado a uma sub-rede na rede virtual *MyVnet*.

   Saiba mais sobre as relações e [propriedades](#properties) nos resultados. Se você não tiver uma rede virtual existente para exibir uma topologia, será possível criar uma usando o script de exemplo [Rotear o tráfego por meio de uma solução de virtualização de rede](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Para exibir um diagrama da topologia e baixá-lo em um arquivo editável, use o [portal](#azure-portal).

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Exibir topologia – PowerShell

É possível executar os comandos nestas etapas:
- No Azure Cloud Shell, selecione **Experimente** na parte superior direita de qualquer comando. O Azure Cloud Shell é um shell de interativo gratuito com ferramentas comuns do Azure pré-instaladas e configuradas para usar com sua conta.
- Executar o PowerShell do seu computador. Se você executar o PowerShell do seu computador, este artigo exigirá o `Az` módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

A conta que você usa deve ter as [permissões](required-rbac-permissions.md) necessárias.

1. Se você já tiver um observador de rede na mesma região da rede virtual para a qual você deseja criar uma topologia, pule para a etapa 3. Crie um grupo de recursos para conter um observador de rede com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). O exemplo a seguir cria o grupo de recursos na região *eastus*:

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Crie um observador de rede com [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). O exemplo a seguir cria um observador de rede na região eastus:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Recupere uma instância do observador de rede com [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). O exemplo a seguir recupera um observador de rede na região Leste dos EUA:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Recupere uma topologia com [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). O exemplo a seguir recupera uma topologia de rede virtual no grupo de recursos *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Informações de topologia são retornadas somente para os recursos que estão no mesmo grupo de recursos de *MyResourceGroup* e na mesma região do observador de rede. Por exemplo, um Grupo de Segurança de Rede que existe em um grupo de recursos diferente de *MyResourceGroup* não será exibido, mesmo que o Grupo de Segurança de Rede esteja associado a uma sub-rede na rede virtual *MyVnet*.

   Saiba mais sobre as relações e [propriedades](#properties) nos resultados. Se você não tiver uma rede virtual existente para exibir uma topologia, será possível criar uma usando o script de exemplo [Rotear o tráfego por meio de uma solução de virtualização de rede](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Para exibir um diagrama da topologia e baixá-lo em um arquivo editável, use o [portal](#azure-portal).

## <a name="relationships"></a>Relações

Todos os recursos retornados em uma topologia têm um dos seguintes tipos de relação com outro recurso:

| Tipo de relação | Exemplo                                                                                                |
| ---               | ---                                                                                                    |
| Contenção       | Uma rede virtual contém uma sub-rede. Uma sub-rede contém um adaptador de rede.                            |
| Associação        | Um adaptador de rede está associado com uma VM. Um endereço IP público está associado com um adaptador de rede. |

## <a name="properties"></a>Propriedades

Todos os recursos retornados em uma topologia têm as seguintes propriedades:

- **Nome**: o nome do recurso.
- **ID**: o URI do recurso.
- **Local**: a região do Azure em que o recurso existe.
- **Associações**: uma lista de associações com o objeto referenciado. Cada associação tem as seguintes propriedades:
    - **AssociationType**: faz referência à relação entre o objeto filho e o pai. Os valores válidos são *Contém* ou *Associado*.
    - **Nome**: o nome do recurso referenciado.
    - **ResourceId**: o URI do recurso referenciado na associação.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [diagnosticar um problema de filtro de tráfego de rede de ou para uma VM](diagnose-vm-network-traffic-filtering-problem.md) usando a funcionalidade de verificação de fluxo de IP do Observador de Rede
- Saiba como [diagnosticar um problema de roteamento em uma VM](diagnose-vm-network-routing-problem.md) usando a funcionalidade de próximo salto do Observador de Rede
