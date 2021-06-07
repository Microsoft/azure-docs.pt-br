---
title: Excluir um cluster do Service Fabric no Azure
description: Neste tutorial, você aprende a excluir um cluster do Service Fabric hospedado pelo Azure, bem como todos os seus recursos. Você pode excluir o grupo de recursos que contém o cluster ou excluir os recursos de maneira seletiva.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 60c43adddd4799a639c2d58e0293086eb1776588
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791081"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Tutorial: remover um cluster do Service Fabric em execução no Azure

Este tutorial é a quinta parte de uma série e mostra como excluir um cluster do Service Fabric em execução no Azure. Para excluir por completo um cluster do Service Fabric, também é necessário excluir os recursos usados por ele. Você tem duas opções: excluir o grupo de recursos do qual o cluster faz parte (o que exclui o recurso de cluster e quaisquer outros recursos no grupo de recursos) ou excluir especificamente o recurso de cluster e seus recursos associados (mas não os outros recursos no grupo de recursos).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Excluir um grupo de recursos e todos os seus recursos
> * Excluir os recursos de um grupo de recursos de maneira seletiva

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro no Azure usando um modelo
> * [Monitorar um cluster](service-fabric-tutorial-monitor-cluster.md)
> * [Reduzir ou escalar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * [Atualizar o runtime de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * Excluir um cluster


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale o [Azure PowerShell](/powershell/azure//install-az-ps) ou a [CLI do Azure](/cli/azure/install-azure-cli).
* Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) seguro no Azure

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Excluir o grupo de recursos que contém o cluster do Service Fabric
A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos.

Entre no Azure e selecione a ID de assinatura com a qual você deseja remover o cluster.  Você pode encontrar sua ID de assinatura fazendo logon no [Portal do Azure](https://portal.azure.com). Exclua o grupo de recursos e todos os recursos de cluster usando o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) ou o comando [az group delete](/cli/azure/group).

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>Excluir o recurso de cluster e os recursos associados de maneira seletiva
Se o grupo de recursos tiver apenas recursos relacionados ao cluster do Service Fabric que você deseja excluir, será mais fácil excluir o grupo de recursos inteiro. Se você quiser excluir de maneira seletiva os recursos no grupo de recursos e manter os recursos não associados ao cluster, siga estas etapas.

Liste os recursos no grupo de recursos:

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

Para cada um dos recursos que você deseja excluir, execute o seguinte script:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Para excluir o recurso de cluster, execute o seguinte script:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Excluir um grupo de recursos e todos os seus recursos
> * Excluir os recursos de um grupo de recursos de maneira seletiva

Agora que concluiu este tutorial, tente o seguinte:
* Saiba como inspecionar e gerenciar um cluster do Service Fabric usando o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
* Saiba como [aplicar patch no sistema operacional Windows](service-fabric-patch-orchestration-application.md) dos nós de cluster.
* Saiba como agregar e coletar eventos para [clusters do Windows](service-fabric-diagnostics-event-aggregation-wad.md) e como [configurar o Log Analytics](service-fabric-diagnostics-oms-setup.md) para monitorar eventos de cluster.
