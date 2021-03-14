---
title: Gerenciar domínios de falha em conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como escolher o número correto de FDs ao criar um conjunto de dimensionamento de máquinas virtuais.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 8c114d6260cf81bcc4fb256fc8a09947ab9ce1d8
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502477"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Escolher o número correto de domínios de falha para o conjunto de dimensionamento de máquinas virtuais
Por padrão, os conjuntos de dimensionamento de máquinas virtuais são criados com cinco domínios de falha em regiões do Azure sem zonas. Para as regiões que dão suporte à implantação zonal de conjuntos de dimensionamento de máquinas virtuais e essa opção está selecionada, o valor padrão da contagem de domínios de falha é 1 para cada uma das zonas. FD=1, nesse caso, implica que as instâncias de VM pertencentes ao conjunto de dimensionamento serão distribuídas em vários racks com base no melhor esforço.

Também é possível considerar o alinhamento do número de domínios de falha do conjunto de dimensionamento com o número de domínios de falha de Managed Disks. Esse alinhamento poderá ajudar a evitar perda de quorum, se um domínio de falha de Managed Disks inteiro for desativado. A contagem de FD pode ser definida como menor ou igual ao número de domínios de falha de Managed Disks disponíveis em cada uma das regiões. Consulte este [documento](../virtual-machines/availability.md) para saber mais sobre o número de domínios de falha de Managed Disks por região.

## <a name="rest-api"></a>API REST
Você pode definir a propriedade `properties.platformFaultDomainCount` como 1, 2 ou 3 (padrão de 3, se não for especificado). Consulte a documentação da API REST [aqui](/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>CLI do Azure
Você pode definir o parâmetro `--platform-fault-domain-count` como 1, 2 ou 3 (padrão de 3, se não for especificado). Consulte a documentação da CLI do Azure [aqui](/cli/azure/vmss#az-vmss-create).

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [recursos de redundância e disponibilidade](../virtual-machines/availability.md) para ambientes do Azure.
