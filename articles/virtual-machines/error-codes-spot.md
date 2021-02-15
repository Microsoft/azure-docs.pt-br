---
title: Códigos de erro para VMs de ponto do Azure e instâncias de conjuntos de dimensionamento
description: Saiba mais sobre os códigos de erro que você poderia ver ao usar VMs Spot e instâncias do conjunto de dimensionamento.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 3e151145bd3c4d1db87ef5d1b0d553555a580279
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816738"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Mensagens de erro para VMs Spot e conjuntos de dimensionamento

Aqui estão alguns códigos de erro possíveis que você pode receber ao usar VMs Spot e conjuntos de dimensionamento.


| Chave | Mensagem | Descrição |
|-----|---------|-------------|
| SkuNotAvailable | A camada solicitada para o recurso ' \<resource\> ' não está disponível atualmente no local ' \<location\> ' para a assinatura ' \<subscriptionID\> '. Tente outra camada ou implante em um local diferente. | Não há capacidade do Azure Spot suficiente neste local para criar sua instância de conjunto de dimensionamento ou VM. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  A política de remoção pode ser definida somente em máquinas virtuais de ponto do Azure. | Essa VM não é uma VM Spot, portanto, você não pode definir a política de remoção. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Não há suporte para a máquina virtual do Azure spot no conjunto de disponibilidade. | Você precisa optar por usar uma VM spot ou usar uma VM em um conjunto de disponibilidade, não pode escolher ambas. |
| AzureSpotFeatureNotEnabledForSubscription  |  Assinatura não habilitada com o recurso Azure Spot. | Use uma assinatura que dê suporte a VMs pontuais. |
| VMPriorityCannotBeApplied  |  O valor de prioridade especificado ' {0} ' não pode ser aplicado à máquina virtual ' {1} ', pois nenhuma prioridade foi especificada quando a máquina virtual foi criada. | Especifique a prioridade quando a VM for criada. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Não é possível executar a operação ' {0} ', pois o preço máximo fornecido ' {1} USD ' é inferior ao preço spot atual ' {2} USD ' para o tamanho de VM do Azure spot ' {3} '. | Selecione um preço máximo mais alto. Para obter mais informações, consulte informações de preço para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Valor de preço máximo inválido. Os únicos valores com suporte para o preço máximo são-1 ou um decimal maior que zero. O valor de preço máximo de-1 indica que a máquina virtual do Azure Spot não será removida por motivos de preço. | Insira um preço máximo válido. Para obter mais informações, consulte preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | A alteração máxima de preço não é permitida quando a VM ' {0} ' está alocada no momento. Desaloque e tente novamente. | Stop\Deallocate a VM para que você possa alterar o preço máximo. |
| MaxPriceChangeNotAllowed | A alteração máxima de preço não é permitida. | Você não pode alterar o preço máximo desta VM. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Não há suporte para o Azure Spot nesta versão de API. | A versão da API precisa ser 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  O Azure Spot não tem suporte para esse tamanho de VM {0} . | Selecione outro tamanho de VM. Para obter mais informações, consulte [máquinas virtuais Spot](./spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  O preço máximo tem suporte apenas para máquinas virtuais do Azure Spot. | Para obter mais informações, consulte [máquinas virtuais Spot](./spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  A solicitação mover recursos contém uma máquina virtual de ponto do Azure. Não há suporte para esse recurso no momento. Verifique os detalhes do erro para obter as IDs da máquina virtual. | Não é possível mover VMs pontuais. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  A solicitação mover recursos contém um conjunto de dimensionamento de máquinas virtuais do Azure Spot. Não há suporte para esse recurso no momento. Verifique os detalhes do erro para as IDs do conjunto de dimensionamento de máquinas virtuais. | Não é possível mover as instâncias do conjunto de escala Spot. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Não há suporte para a máquina virtual de ponto do Azure no conjunto de dimensionamento de máquinas virtuais com o modo de orquestração de VM. | Defina o modo de orquestração como conjunto de dimensionamento de máquinas virtuais para usar instâncias especiais. |


**Próximas etapas** Para obter mais informações, consulte [máquinas virtuais Spot](./spot-vms.md).