---
title: Limites para recursos, SKUs, regiões
titleSuffix: Azure Kubernetes Service
description: Saiba mais sobre as cotas padrão, tamanhos de SKU de VM de nó restrito e disponibilidade de região do serviço de kubernetes do Azure (AKS).
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 9592b03f5bd9e9436db5665e040b4afd3b68b764
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87562728"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Cotas, restrições de tamanho de máquina virtual e disponibilidade de região no AKS (Serviço de Kubernetes do Azure)

Todos os serviços do Azure definem limites e cotas padrão para recursos e recursos. Determinados SKUs de VM (máquina virtual) também são restritos para uso.

Este artigo detalha os limites de recursos padrão para os recursos do AKS (serviço kubernetes do Azure) e a disponibilidade do AKS nas regiões do Azure.

## <a name="service-quotas-and-limits"></a>Cotas e limites de serviço

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infraestrutura provisionada

Todas as outras limitações de rede, computação e armazenamento se aplicam à infraestrutura provisionada. Para obter os limites relevantes, consulte [limites de serviço e assinatura do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> Quando você atualiza um cluster AKS, recursos adicionais são temporariamente consumidos. Esses recursos incluem endereços IP disponíveis em uma sub-rede de rede virtual ou em uma cota de vCPU de máquina virtual. Se você usar contêineres do Windows Server, a única abordagem endossada para aplicar as atualizações mais recentes aos nós é executar uma operação de atualização. Um processo de atualização de cluster com falha pode indicar que você não tem o espaço de endereço IP ou a cota de vCPU disponível para lidar com esses recursos temporários. Para obter mais informações sobre o processo de atualização de nó do Windows Server, consulte [atualizar um pool de nós no AKs][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Tamanhos de VM restritos

Cada nó em um cluster AKS contém uma quantidade fixa de recursos de computação, como vCPU e memória. Se um nó AKS contiver recursos de computação insuficientes, o pods poderá falhar ao ser executado corretamente. Para garantir que os pods *Kube do sistema* e seus aplicativos possam ser agendados de forma confiável, **não use os seguintes SKUs de VM no AKs**:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Para obter mais informações sobre os tipos de VM e seus recursos de computação, consulte [tamanhos de máquinas virtuais no Azure][vm-skus].

## <a name="region-availability"></a>Disponibilidade de região

Para obter a lista mais recente de onde você pode implantar e executar clusters, consulte [disponibilidade da região AKs][region-availability].

## <a name="next-steps"></a>Próximas etapas

Determinados limites e cotas padrão podem ser aumentados. Se o recurso der suporte a um aumento, solicite o aumento por meio de uma [solicitação de suporte do Azure][azure-support] (para **tipo de problema**, selecione **cota**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
