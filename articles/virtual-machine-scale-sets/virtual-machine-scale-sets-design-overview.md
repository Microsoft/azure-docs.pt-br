---
title: Considerações de design para conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba mais sobre as considerações de design para seus conjuntos de dimensionamento de máquinas virtuais do Azure. Compare os recursos dos conjuntos de dimensionamento com recursos de VM.
keywords: máquina virtual linux, conjuntos de dimensionamento de máquina virtual
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 04446243ed827cca4972a4b606c4930e74a2c704
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774959"
---
# <a name="design-considerations-for-scale-sets"></a>Considerações de design para conjuntos de dimensionamento
Este artigo discute considerações de design para Conjuntos de Dimensionamento de Máquinas Virtuais. Para obter informações sobre o que são Conjuntos de Escala de Máquina Virtual, confira [Conjuntos de Dimensionamento de Máquina Virtual - Visão Geral](./overview.md).

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>Quando você usar conjuntos de dimensionamento, em vez de máquinas virtuais?
De modo geral, os conjuntos de dimensionamento são úteis para implantar a infraestrutura altamente disponível em que um conjunto de computadores têm configuração semelhante. No entanto, alguns recursos só estão disponíveis em conjuntos de dimensionamento, enquanto outros recursos só estão disponíveis em VMs. Para tomar uma decisão informada sobre quando usar cada tecnologia, primeiro é necessário analisar alguns dos recursos mais usados que estão disponíveis em conjuntos de dimensionamento, mas não em VMs:

### <a name="scale-set-specific-features"></a>Recursos específicos de conjunto de dimensionamento

- Depois de especificar a configuração do conjunto de dimensionamento, você pode atualizar a propriedade *Capacity* para implantar mais VMs em paralelo. Esse processo é melhor do que escrever um script para orquestrar a implantação de várias VMs individuais em paralelo.
- Você pode [usar o Dimensionamento Automático do Azure para dimensionar automaticamente um conjunto de dimensionamento](./virtual-machine-scale-sets-autoscale-overview.md), mas não VMs individuais.
- Você pode [refazer a imagem de VMs de conjunto de dimensionamento](/rest/api/compute/virtualmachinescalesets/reimage), mas [não de VMs individuais](/rest/api/compute/virtualmachines).
- Você pode [superprovisionar](#overprovisioning) VMs de conjunto de dimensionamento maior confiabilidade e tempos de implantação mais rápidos. Você não pode superprovisionar VMs individuais, a menos que escreva código personalizado para executar essa ação.
- Você pode especificar uma [política de atualização](./virtual-machine-scale-sets-upgrade-scale-set.md) para tornar mais fácil distribuir atualizações entre VMs em seu conjunto de dimensionamento. Com VMs individuais, você mesmo deve organizar as atualizações.

### <a name="vm-specific-features"></a>Recursos específicos de VM

No momento, alguns recursos só estão disponíveis em VMs:

- Você pode capturar uma imagem de uma VM individual, mas não de uma VM em um conjunto de dimensionamento.
- Você pode migrar uma VM individual de discos nativos para discos gerenciados, mas não pode migrar instâncias de VMs em um conjunto de dimensionamento.
- Você pode atribuir endereços IP públicos IPv6 a NICs (placas de interface de rede) virtuais de VMs individuais, mas não pode fazer isso para instâncias de VMs em um conjunto de dimensionamento. Você pode atribuir endereços IP públicos IPv6 a balanceadores de carga na frente de qualquer VM individual ou de VMs em um conjunto de dimensionamento.

## <a name="storage"></a>Armazenamento

### <a name="scale-sets-with-azure-managed-disks"></a>Conjuntos de dimensionamento com Azure Managed Disks
Conjuntos de dimensionamento podem ser criados com [Azure Managed Disks](../virtual-machines/managed-disks-overview.md), em vez de contas de armazenamento do Azure tradicionais. Os Managed Disks oferecem os seguintes benefícios:
- Você não precisa criar previamente um conjunto de contas de armazenamento do Azure para VMs de conjunto de dimensionamento.
- Você pode definir [discos de dados anexados](virtual-machine-scale-sets-attached-disks.md) para as VMs no seu conjunto de dimensionamento.
- Conjuntos de dimensionamento podem ser configurados para [oferecer suporte a até 1.000 VMs em um conjunto](virtual-machine-scale-sets-placement-groups.md). 

Se você tiver um modelo existente, também poderá [atualizar o modelo para usar Managed Disks](virtual-machine-scale-sets-convert-template-to-md.md).

### <a name="user-managed-storage"></a>Armazenamento gerenciado pelo usuário
Um conjunto de dimensionamento que não esteja definido com os Azure Managed Disks se baseia em contas de armazenamento criadas por usuários para armazenar os discos do sistema operacional das VMs no conjunto. Recomendamos uma taxa de 20 VMs ou menor por conta de armazenamento para atingir o máximo de E/S e também tirar proveito do _provisionamento em excesso_ (confira abaixo). Também recomendamos distribuir os caracteres iniciais dos nomes de conta de armazenamento pelo alfabeto. Isso ajuda a distribuir a carga entre diferentes sistemas internos. 


## <a name="overprovisioning"></a>Provisionamento em excesso
Conjuntos de dimensionamento atualmente usam o "superprovisionamento" de VMs como padrão. Com o provisionamento em excesso ativado, o conjunto de dimensionamento rotaciona mais VMs do que o solicitado e exclui as VMs extras assim que o número de VMs solicitado é provisionado com êxito. O provisionamento em excesso melhora as taxas de sucesso do provisionamento e reduz o tempo de implantação. Você não pagará por essas VMs extras e elas não contam nos seus limites de cota.

Embora o provisionamento em excesso melhore as taxas de sucesso do provisionamento, tal comportamento pode ser confuso para um aplicativo que não foi projetado para lidar com o surgimento e desaparecimento repentino de VMs extras. Para desativar o superprovisionamento, verifique se você tem a seguinte cadeia de caracteres no seu modelo: `"overprovision": "false"`. Mais detalhes podem ser encontrados na [Documentação da API REST do conjunto de dimensionamento](/rest/api/virtualmachinescalesets/create-or-update-a-set).

Se o seu conjunto de dimensionamento usa armazenamento gerenciado pelo usuário, e você desativar o provisionamento em excesso, você poderá ter mais de 20 VMs por conta de armazenamento, mas não é recomendável ultrapassar 40 VMs para não prejudicar o desempenho do E/S. 

## <a name="limits"></a>limites
Um conjunto de dimensionamento criado em uma imagem do Marketplace (também conhecido como uma imagem de plataforma) e configurado para usar Azure Managed Disks oferece suporte a uma capacidade de até 1.000 VMs. Se você configurar seu conjunto de dimensionamento para suportar mais de 100 VMs, nem todos os cenários funcionam da mesma maneira, o balanceamento de carga é um exemplo. Para obter mais informações, confira [Como trabalhar com conjuntos de dimensionamento grandes de máquinas virtuais](virtual-machine-scale-sets-placement-groups.md). 

Um conjunto de dimensionamento configurado com contas de armazenamento gerenciadas pelo usuário é atualmente limitada a 100 VMs (e são recomendadas 5 contas de armazenamento para essa escala).

Um conjunto de dimensionamento criado em uma imagem personalizada (criado por você) pode ter uma capacidade de até 600 VMs quando configuradas com discos gerenciados do Azure. Se o conjunto de dimensionamento é configurado com contas de armazenamento gerenciadas pelo usuário, ele deve criar todos os VHDs de disco do sistema operacional em uma conta de armazenamento. Como resultado, o número máximo recomendado de VMs em um conjunto de dimensionamento criado em uma imagem personalizada e em um armazenamento gerenciado pelo usuário é 20. Se você desativar o provisionamento em excesso, será possível ir até 40.

Para ter mais VMs do que esses limites permitem, você precisará implantar vários conjuntos de dimensionamento, conforme mostrado [neste modelo](https://azure.microsoft.com/resources/templates/301-custom-images-at-scale/).
