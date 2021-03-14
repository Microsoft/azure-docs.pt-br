---
title: Criar um grupo de posicionamento de proximidade usando o CLI do Azure
description: Saiba mais sobre como criar e usar grupos de posicionamento de proximidade para máquinas virtuais no Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: a347c9284608340811f9c2388df26129baeb8837
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505640"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Implantar VMs em grupos de posicionamento por proximidade usando a CLI do Azure

Para obter as VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-las em um [grupo de posicionamento de proximidade](../co-location.md#proximity-placement-groups).

Um grupo de posicionamento por proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos uns dos outros. Os grupos de posicionamento por proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.


## <a name="create-the-proximity-placement-group"></a>Criar o grupo de posicionamento de proximidade
Crie um grupo de posicionamento de proximidade usando [`az ppg create`](/cli/azure/ppg#az-ppg-create) . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Lista de grupos de posicionamento por proximidade

Você pode listar todos os seus grupos de posicionamento de proximidade usando a [lista AZ PPG](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Criar uma máquina virtual

Crie uma VM dentro do grupo de posicionamento de proximidade usando [New AZ VM](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

Você pode ver a VM no grupo de posicionamento de proximidade usando [AZ PPG show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Conjuntos de Disponibilidade
Você também pode criar um conjunto de disponibilidade em seu grupo de posicionamento de proximidade. Use o mesmo `--ppg` parâmetro com [AZ VM Availability – Set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) para criar um conjunto de disponibilidade e todas as VMs no conjunto de disponibilidade também serão criadas no mesmo grupo de posicionamento de proximidade.

## <a name="scale-sets"></a>Conjuntos de dimensionamento

Você também pode criar um conjunto de dimensionamento em seu grupo de posicionamento de proximidade. Use o mesmo `--ppg` parâmetro com [AZ vmss Create](/cli/azure/vmss#az_vmss_create) para criar um conjunto de dimensionamento e todas as instâncias serão criadas no mesmo grupo de posicionamento de proximidade.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os comandos de [CLI do Azure](/cli/azure/ppg) para grupos de posicionamento de proximidade.