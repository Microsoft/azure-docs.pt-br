---
title: Usar a visualização de grupos de posicionamento de proximidade para VMs Linux | Microsoft Docs
description: Saiba como criar e usar grupos de posicionamento de proximidade para máquinas virtuais do Linux no Azure.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: a3009c9863d8322e3fe34bd99d64b93f0aa3d858
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385737"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Visualização: Implantar VMs em grupos de posicionamento de proximidade usando CLI do Azure

Para obter as VMs o mais próximo possível, alcançando a menor latência possível, você deve implantá-las em um [grupo de posicionamento de proximidade](co-location.md#preview-proximity-placement-groups).

Um grupo de posicionamento de proximidade é um agrupamento lógico usado para garantir que os recursos de computação do Azure estejam fisicamente localizados próximos um do outro. Os grupos de posicionamento de proximidade são úteis para cargas de trabalho em que a baixa latência é um requisito.

> [!IMPORTANT]
> Os grupos de posicionamento de proximidade estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Os grupos de posicionamento de proximidade não estão disponíveis nessas regiões durante a visualização: **Leste do Japão**, **leste da Austrália** e **Índia central**.

## <a name="create-the-proximity-placement-group"></a>Criar o grupo de posicionamento de proximidade
Crie um grupo de posicionamento de [`az ppg create`](/cli/azure/ppg#az-ppg-create)proximidade usando. 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Listar grupos de posicionamento de proximidade

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

Você também pode criar um conjunto de dimensionamento em seu grupo de posicionamento de proximidade. Use o mesmo `--ppg` parâmetro com [AZ vmss Create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) para criar um conjunto de dimensionamento e todas as instâncias serão criadas no mesmo grupo de posicionamento de proximidade.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os comandos de [CLI do Azure](/cli/azure/ppg) para grupos de posicionamento de proximidade.
