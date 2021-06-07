---
title: Benefício Híbrido do Azure para conjuntos de dimensionamento de máquinas virtuais do Linux
description: Saiba como Benefício Híbrido do Azure pode se aplicar ao conjunto de dimensionamento de máquinas virtuais para ajudá-lo a economizar dinheiro em suas máquinas virtuais Linux em execução no Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.subservice: linux
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: 09e41b4071ad02120f7d303b32bc87bc0ba39e2c
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105549270"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set-public-preview"></a>Benefício Híbrido do Azure para conjunto de dimensionamento de máquinas virtuais do Linux (visualização pública)

**Benefício híbrido do Azure para o conjunto de dimensionamento de máquinas virtuais do Linux está em visualização pública agora**. O benefício do AHB pode ajudá-lo a reduzir o custo de execução dos [conjuntos de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)RHEL e SLES.

Com esse benefício, você paga apenas pelo custo de infraestrutura do seu conjunto de dimensionamento. O benefício está disponível para todas as imagens RHEL e SLES Marketplace PAYG (pré-pago).


>[!NOTE]
> Este artigo descreve o Benefício Híbrido do Azure para Linux VMSS. Há um [artigo disponível separado [aqui AHB para VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/azure-hybrid-benefit-linux), que já está disponível para clientes do Azure desde novembro de 2020.

## <a name="benefit-description"></a>Descrição do benefício
O Azure híbrido permite que você use as licenças de acesso à nuvem existentes do Red Hat ou SUSE e converta com flexibilidade as instâncias do conjunto de dimensionamento de máquinas virtuais para a cobrança de BYOS (traga sua própria assinatura). 

O conjunto de dimensionamento de máquinas virtuais implantado por meio de imagens do PAYG Marketplace cobrará a taxa de infraestrutura e software. Com AHB, as instâncias de PAYG podem ser convertidas em um modelo de cobrança BYOS sem reimplantação.

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Benefício Híbrido do Azure visualização de custos em VMs do Linux.":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>Escopo da elegibilidade Benefício Híbrido do Azure para Linux
Benefício Híbrido do Azure está disponível para todas as imagens RHEL e SLES PAYG do Azure Marketplace. O benefício ainda não está disponível para imagens RHEL ou SLES BYOS ou para imagens personalizadas do Azure Marketplace.

As instâncias de host dedicadas do Azure e os benefícios híbridos do SQL não são elegíveis para Benefício Híbrido do Azure se você já estiver usando o benefício com VMs do Linux.

## <a name="get-started"></a>Introdução

### <a name="red-hat-customers"></a>Clientes do Red Hat

Benefício Híbrido do Azure para RHEL está disponível para clientes Red Hat que atendem a esses dois critérios:

- Ter assinaturas RHEL ativas ou não usadas qualificadas para uso no Azure
- Habilitou uma ou mais dessas assinaturas para uso no Azure com o programa de [acesso à nuvem do Red Hat](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

> [!IMPORTANT]
> Verifique se a assinatura correta foi habilitada no programa de [acesso à nuvem](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) .

Para começar a usar o benefício do Red Hat:

1. Habilite suas assinaturas do RHEL qualificado no Azure usando a [interface do cliente do Red Hat Cloud Access](https://access.redhat.com/management/cloud).

   As assinaturas do Azure que você fornecer durante o processo de habilitação de acesso à nuvem do Red Hat terão permissão para usar o recurso Benefício Híbrido do Azure.
1. Aplique Benefício Híbrido do Azure a qualquer um dos conjuntos de dimensionamento de máquinas virtuais PAYG e novos do RHEL existentes. Você pode usar portal do Azure ou CLI do Azure para habilitar o benefício.
1. Siga [as próximas etapas](https://access.redhat.com/articles/5419341) recomendadas para configurar fontes de atualização para suas VMs RHEL e para obter diretrizes de conformidade de assinatura do RHEL.


### <a name="suse-customers"></a>Clientes do SUSE

Para começar a usar o benefício do SUSE:

1. Registre-se no programa de nuvem pública SUSE.
1. Aplique o benefício ao conjunto de dimensionamento de máquinas virtuais recentemente criado ou existente por meio do portal do Azure ou CLI do Azure.
1. Registre suas VMs que estão recebendo o benefício com uma fonte separada de atualizações.


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Habilitar e desabilitar o benefício no portal do Azure 
A experiência do portal para habilitar e desabilitar o AHB no conjunto de dimensionamento de máquinas virtuais **não está disponível no momento**.

## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>Habilitar e desabilitar o benefício usando o CLI do Azure

Você pode usar o `az vmss update` comando para atualizar as VMs existentes. Para VMs RHEL, execute o comando com um `--license-type` parâmetro de `RHEL_BYOS` . Para VMs SLES, execute o comando com um `--license-type` parâmetro de `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Exemplo da CLI para habilitar o benefício
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Exemplo da CLI para desabilitar o benefício
Para desabilitar o benefício, use um `--license-type` valor de `None` :

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> Os conjuntos de dimensionamento têm uma ["política de atualização"](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) que determinam como as VMs são atualizadas com o modelo de conjunto de dimensionamento mais recente. Portanto, se seu VMSS tiver a política de atualização ' automática ', o benefício do AHB será aplicado automaticamente à medida que as instâncias de VM forem atualizadas. Se VMSS tiver a política de atualização ' sem interrupção ', com base nas atualizações agendadas, AHB será aplicado.
No caso da política de atualização ' manual ', você precisará executar a "atualização manual" de cada VM existente.  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>Exemplo da CLI para atualizar as instâncias do conjunto de dimensionamento de máquinas virtuais no caso de uma política de "atualização manual" 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>Aplicar o Benefício Híbrido do Azure em tempo de criação do conjunto de dimensionamento de máquinas virtuais 
Além de aplicar o Benefício Híbrido do Azure ao conjunto de dimensionamento de máquinas virtuais pago pelo uso existente, você pode chamá-lo no momento da criação do conjunto de dimensionamento de máquinas virtuais. Os benefícios disso são threefold:
- Você pode provisionar as instâncias do conjunto de dimensionamento de máquinas virtuais PAYG e BYOS usando a mesma imagem e processo.
- Ele permite alterações futuras no modo de licenciamento, algo não disponível com uma imagem somente BYOS.
- As instâncias do conjunto de dimensionamento de máquinas virtuais serão conectadas à RHUI (Red Hat Update Infrastructure) por padrão, para garantir que elas permaneçam atualizadas e protegidas. Você pode alterar o mecanismo atualizado após a implantação a qualquer momento.

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>Exemplo da CLI para criar um conjunto de dimensionamento de máquinas virtuais com o benefício do AHB
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>Próximas etapas
* [Saiba como criar e atualizar VMs e adicionar tipos de licença (RHEL_BYOS, SLES_BYOS) para Benefício Híbrido do Azure usando o CLI do Azure](/cli/azure/vmss)
