---
title: Códigos de erro para máquinas virtuais do Azure Spot e instâncias de conjuntos de dimensionamento
description: Saiba mais sobre os códigos de erro que você poderia ver ao usar as máquinas virtuais do Azure Spot e as instâncias do conjunto de dimensionamento.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 6d43935365580233063deb0e523d222351c22d54
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670614"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Mensagens de erro para máquinas virtuais do Azure Spot e conjuntos de dimensionamento

Aqui estão alguns códigos de erro possíveis que você pode receber ao usar as máquinas virtuais e os conjuntos de dimensionamento do Azure Spot.


| Chave | Mensagem | Descrição |
|-----|---------|-------------|
| SkuNotAvailable | A camada solicitada para o recurso ' \<resource\> ' não está disponível atualmente no local ' \<location\> ' para a assinatura ' \<subscriptionID\> '. Tente outra camada ou implante em um local diferente. | Não há capacidade suficiente de máquina virtual do Azure Spot neste local para criar sua instância de VM ou conjunto de dimensionamento. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  A política de remoção pode ser definida somente em máquinas virtuais de ponto do Azure. | Essa VM não é uma máquina virtual de ponto do Azure, portanto, você não pode definir a política de remoção. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Não há suporte para a máquina virtual do Azure spot no conjunto de disponibilidade. | Você precisa optar por usar uma máquina virtual de ponto do Azure ou usar uma VM em um conjunto de disponibilidade, não pode escolher ambas. |
| AzureSpotFeatureNotEnabledForSubscription  |  Assinatura não habilitada com o recurso de máquina virtual do Azure Spot. | Use uma assinatura que dê suporte a máquinas virtuais do Azure Spot. |
| VMPriorityCannotBeApplied  |  O valor de prioridade especificado ' {0} ' não pode ser aplicado à máquina virtual ' {1} ', pois nenhuma prioridade foi especificada quando a máquina virtual foi criada. | Especifique a prioridade quando a VM for criada. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Não é possível executar a operação ' {0} ', pois o preço máximo fornecido ' {1} USD ' é inferior ao preço spot atual ' {2} USD ' para o tamanho da máquina virtual do Azure spot ' {3} '. | Selecione um preço máximo mais alto. Para obter mais informações, consulte informações de preço para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Valor de preço máximo inválido. Os únicos valores com suporte para o preço máximo são-1 ou um decimal maior que zero. O valor de preço máximo de-1 indica que a máquina virtual do Azure Spot não será removida por motivos de preço. | Insira um preço máximo válido. Para obter mais informações, consulte preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | A alteração máxima de preço não é permitida quando a VM ' {0} ' está alocada no momento. Desaloque e tente novamente. | Stop\Deallocate a VM para que você possa alterar o preço máximo. |
| MaxPriceChangeNotAllowed | A alteração máxima de preço não é permitida. | Você não pode alterar o preço máximo desta VM. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Não há suporte para a máquina virtual de ponto do Azure nesta versão de API. | A versão da API precisa ser 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  A máquina virtual do Azure Spot não tem suporte para esse tamanho de VM {0} . | Selecione outro tamanho de VM. Para obter mais informações, consulte [máquinas virtuais do Azure Spot](./spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  O preço máximo tem suporte apenas para máquinas virtuais do Azure Spot. | Para obter mais informações, consulte [máquinas virtuais do Azure Spot](./spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  A solicitação mover recursos contém uma máquina virtual de ponto do Azure. Sem suporte. Verifique os detalhes do erro para IDs de máquina virtual. | Não é possível mover as máquinas virtuais do Azure Spot. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  A solicitação mover recursos contém um conjunto de dimensionamento de máquinas virtuais do Azure Spot. Sem suporte. Verifique os detalhes do erro para as IDs do conjunto de dimensionamento de máquinas virtuais. | Não é possível mover as instâncias do conjunto de dimensionamento de máquinas virtuais do Azure Spot. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Não há suporte para a máquina virtual de ponto do Azure no conjunto de dimensionamento de máquinas virtuais com o modo de orquestração de VM. | Defina o modo de orquestração como conjunto de dimensionamento de máquinas virtuais para usar as instâncias de máquina virtual do Azure Spot. |


**Próximas etapas** Para obter mais informações, consulte [máquinas virtuais Spot](./spot-vms.md).