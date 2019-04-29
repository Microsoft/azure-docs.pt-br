---
title: Gerenciar conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure | Microsoft Docs
description: 'Comandos comuns da CLI do Azure para gerenciar conjuntos de dimensionamento de máquinas virtuais, por exemplo: como iniciar e parar uma instância ou alterar a capacidade do conjunto de dimensionamento.'
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: b49182ebdcc93c4a51a55f27c3e0bf7a45307b7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618074"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Gerenciar um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure
Durante todo o ciclo de vida do conjunto de dimensionamento de uma máquina virtual, você poderá precisar executar uma ou mais tarefas de gerenciamento. Além disso, talvez você deseje criar scripts que automatizam várias tarefas do ciclo de vida. Este artigo fornece detalhes sobre alguns dos comandos comuns da CLI do Azure que permitem executar essas tarefas.

Para concluir essas tarefas de gerenciamento, é necessário ter a CLI do Azure mais recente. Para obter informações, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se for necessário criar um conjunto de dimensionamento de máquinas virtuais, será possível [criar um conjunto de dimensionamento com a CLI do Azure](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Exibir informações sobre um conjunto de dimensionamento
Para exibir as informações gerais sobre um conjunto de dimensionamento, use [az vmss show](/cli/azure/vmss). O exemplo a seguir obtém informações sobre o conjunto de dimensionamento chamado *myScaleSet* no grupo de recursos *myResourceGroup*. Insira seus próprios nomes da seguinte maneira:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Exibição de VMs em um conjunto de escala
Para exibir uma lista de instância de VM em um conjunto de dimensionamento, use [az vmss list-instances](/cli/azure/vmss). O exemplo a seguir lista todas as instâncias de VM no conjunto de dimensionamento denominado *myScaleSet* no grupo de recursos *myResourceGroup*. Forneça os seus próprios valores para esses nomes:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Para exibir informações adicionais sobre uma instância específica de VM, adicione o parâmetro `--instance-id` a [az vmss get-instance-view](/cli/azure/vmss) e especifique uma instância para exibição. O exemplo a seguir exibe informações sobre a instância de VM *0* no conjunto de dimensionamento chamado *myScaleSet* no grupo de recursos *myResourceGroup*. Insira seus próprios nomes da seguinte maneira:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Informações de conexão de lista para VMs
Para se conectar às VMs em um conjunto de dimensionamento, use SSH ou RDP para um endereço IP público e número da porta atribuídos. Por padrão, as regras de NAT (conversão de endereços de rede) são adicionadas ao Azure Load Balancer, que encaminha o tráfego de conexão remota para cada VM. Para listar os endereços e as portas para se conectar a instâncias de VM em um conjunto de dimensionamento, use [az vmss list-instance-connection-info](/cli/azure/vmss). O exemplo a seguir lista informações de conexão para instâncias de VM no conjunto de dimensionamento chamado *myScaleSet* e no grupo de recursos *myResourceGroup*. Forneça os seus próprios valores para esses nomes:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Os comandos anteriores mostravam informações sobre o conjunto de escala e as instâncias de VM. Para aumentar ou diminuir o número de instâncias no conjunto de dimensionamento, você pode alterar a capacidade. O conjunto de dimensionamento cria ou remove o número necessário de VMs e depois configura as VMs para receber o tráfego do aplicativo.

Para ver o número de instâncias que você fez em um conjunto de escala, use [az vmss show](/cli/azure/vmss) e consulte *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Manualmente, é possível aumentar ou diminuir o número de máquinas virtuais no conjunto de dimensionamento com [az vmss scale](/cli/azure/vmss). O seguinte exemplo aumenta o número de VMs no conjunto de dimensionamento para *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Demora alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Se você diminuir a capacidade de um conjunto de dimensionamento, as máquinas virtuais com as IDs de instância mais alta são removidas primeiro.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Parar e iniciar VMs em um conjunto de dimensionamento
Para parar uma ou mais VMs em um conjunto de dimensionamento, use [az vmss stop](/cli/azure/vmss#az-vmss-stop). O parâmetro `--instance-ids` permite que você especifique uma ou mais VMs a serem paradas. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são paradas. Para parar várias VMs, separe cada ID de instância com um espaço.

O exemplo a seguir para a instância *0* no conjunto de dimensionamento chamado *myScaleSet* e o grupo de recursos *myResourceGroup*. Use seus próprios valores, da seguinte maneira:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

VMs paradas permanecem alocadas e continuam a incorrer em encargos de computação. Se, em vez disso, você deseja que as VMs sejam desalocadas e incorram somente em encargos de armazenamento, use [az vmss deallocate](/cli/azure/vmss). Para desalocar várias VMs, separe cada ID de instância com um espaço. O exemplo a seguir para e desaloca a instância *0* no conjunto de dimensionamento chamado *myScaleSet* e o grupo de recursos *myResourceGroup*. Use seus próprios valores, da seguinte maneira:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Iniciar VMs em um conjunto de dimensionamento
Para iniciar uma ou mais VMs em um conjunto de dimensionamento, use [az vmss start](/cli/azure/vmss). O parâmetro `--instance-ids` permite que você especifique uma ou mais VMs a serem iniciadas. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são iniciadas. Para iniciar várias VMs, separe cada ID de instância com um espaço.

O exemplo a seguir inicia a instância *0* no conjunto de dimensionamento chamado *myScaleSet* e o grupo de recursos *myResourceGroup*. Use seus próprios valores, da seguinte maneira:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Reiniciar VMs em um conjunto de dimensionamento
Para reiniciar uma ou mais VMs em um conjunto de dimensionamento, use [az vmss restart](/cli/azure/vmss). O parâmetro `--instance-ids` permite que você especifique uma ou mais VMs para reiniciar. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são reiniciadas. Para reiniciar várias VMs, separe cada ID de instância com um espaço.

O exemplo a seguir reinicia a instância *0* no conjunto de dimensionamento chamado *myScaleSet* e o grupo de recursos *myResourceGroup*. Use seus próprios valores, da seguinte maneira:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Remover VMs de um conjunto de dimensionamento
Para remover uma ou mais VMs em um conjunto de dimensionamento, use [az vmss delete-instances](/cli/azure/vmss). O parâmetro `--instance-ids` permite que você especifique uma ou mais VMs para remover. Se você especificar * para a ID de instância, todas as VMs no conjunto de dimensionamento são removidas. Para remover várias VMs, separe cada ID de instância com um espaço.

O exemplo a seguir remove a instância *0* no conjunto de dimensionamento chamado *myScaleSet* e o grupo de recursos *myResourceGroup*. Use seus próprios valores, da seguinte maneira:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Próximas etapas
Outras tarefas comuns dos conjuntos de dimensionamento incluem como [implantar um aplicativo](virtual-machine-scale-sets-deploy-app.md) e [atualizar instâncias de VM](virtual-machine-scale-sets-upgrade-scale-set.md). Você também pode usar a CLI do Azure para [configurar regras de dimensionamento automático](virtual-machine-scale-sets-autoscale-overview.md).
