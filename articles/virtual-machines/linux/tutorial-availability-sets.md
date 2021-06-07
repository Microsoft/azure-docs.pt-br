---
title: Implantar VMs em um conjunto de disponibilidade usando a CLI do Azure
description: Neste tutorial, você aprenderá como usar a CLI do Azure para implantar máquinas virtuais altamente disponíveis em Conjuntos de Disponibilidade
documentationcenter: ''
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 7c45f08a339ca8878bb9e2840faa8a412f3e60e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765963"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-cli"></a>Criar e implantar máquinas virtuais em um conjunto de disponibilidade usando a CLI do Azure

Neste tutorial, você aprenderá a aumentar a disponibilidade e a confiabilidade de suas soluções de Máquina Virtual no Azure usando uma funcionalidade chamada Conjuntos de Disponibilidade. Os Conjuntos de disponibilidade garantem que as VMs implantadas no Azure sejam distribuídas entre vários clusters de hardware isolados. Isso garante que, se ocorrer uma falha de hardware ou de software no Azure, apenas um subconjunto de suas VMs será afetado e a solução geral permanecerá disponível e operacional.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM em um conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis

Este tutorial usa a CLI dentro do [Azure Cloud Shell](../../cloud-shell/overview.md), que é constantemente atualizada para a versão mais recente. Para abrir o Cloud Shell, selecione **Experimentar** na parte superior de um bloco de código qualquer.

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Crie um conjunto de disponibilidade usando [az vm availability-set create](/cli/azure/vm/availability-set). Nesse exemplo, o número de domínios de atualização e de falha são definidos para *2* para o conjunto de disponibilidade chamado *myAvailabilitySet* no grupo de recursos *myResourceGroupAvailability*.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create), em seguida, crie um conjunto de disponibilidade:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Os Conjuntos de Disponibilidade permitem que você isole os recursos em "domínios de falha" e "domínios de atualização". Um **domínio de falha** representa uma coleção isolada de recursos de servidor + rede + armazenamento. No exemplo anterior, o conjunto de disponibilidade em pelo menos dois domínios de falha quando nossas VMs são implantadas. O conjunto de disponibilidade também é distribuído entre dois **atualizar domínios**. Dois domínios de atualização garantem que durante a atualização de software do Azure os recursos de VM estarão isolados, impedindo que todos os softwares que executem em nossa VM sejam atualizados ao mesmo tempo.


## <a name="create-vms-inside-an-availability-set"></a>Criar VMs dentro de um conjunto de disponibilidade

As VMs devem ser criadas dentro do conjunto de disponibilidade para assegurar a distribuição correta pelo hardware. Uma VM existente não pode ser adicionada a um conjunto de disponibilidade após sua criação.

Quando uma VM é criada com [az vm create](/cli/azure/vm), você usa o parâmetro `--availability-set` para especificar o nome do conjunto de disponibilidade.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

Agora há duas máquinas virtuais dentro do conjunto de disponibilidade. Como elas estão no mesmo conjunto de disponibilidade, o Azure garantirá que as VMs e todos os seus recursos (incluindo discos de dados) sejam distribuídos entre o hardware físico isolado. Essa distribuição ajuda a garantir uma disponibilidade muito maior de nossa solução de VM geral.

A distribuição do conjunto de disponibilidade pode ser exibida no portal, vá para grupos de recursos > myResourceGroupAvailability > myAvailabilitySet. As VMs são distribuídas entre as duas falhas e domínios de atualização, conforme mostrado no exemplo a seguir:

![Conjunto de disponibilidade no portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Conferir os tamanhos de VM disponíveis

VMs adicionais podem ser adicionadas ao conjunto de disponibilidade mais tarde, onde os tamanhos de VM estão disponíveis no hardware. Use [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az_vm_availability_set_list_sizes) para listar todos os tamanhos disponíveis no cluster de hardware para o conjunto de disponibilidade:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM em um conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis

Avance para o próximo tutorial para saber mais sobre conjuntos de disponibilidade de máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento de máquinas virtuais](tutorial-create-vmss.md)

* Para saber mais sobre as zonas de disponibilidade, confira a [Documentação das Zonas de Disponibilidade](../../availability-zones/az-overview.md).
* Mais documentação sobre conjuntos de disponibilidade e Zonas de Disponibilidade também está disponível [aqui](../availability.md).
* Para experimentar zonas de disponibilidade, visite [Criar uma máquina virtual do Linux em uma zona de disponibilidade com a CLI do Azure](./create-cli-availability-zone.md)