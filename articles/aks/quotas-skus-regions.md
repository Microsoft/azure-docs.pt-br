---
title: Cotas, SKUs e disponibilidade de região no serviço de Kubernetes do Azure (AKS)
description: Saiba mais sobre as cotas padrão, a tamanhos de SKU de VM de nó restrito e a disponibilidade de região do serviço de Kubernetes do Azure (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mlearned
ms.openlocfilehash: 318846cddecdf020e2e751d3a0b9e05fc83bba73
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614544"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Cotas, restrições de tamanho de máquina virtual e a disponibilidade de região no serviço de Kubernetes do Azure (AKS)

Todos os serviços do Azure defina limites e cotas de recursos padrão. Determinados SKUs de máquina virtual (VM) também são restritos para uso.

Este artigo fornece detalhes sobre os limites de recursos padrão para recursos de serviço de Kubernetes do Azure (AKS) e a disponibilidade de AKS em regiões do Azure.

## <a name="service-quotas-and-limits"></a>Cotas e limites de serviço

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infraestrutura provisionada

Todas as outras limitações de rede, computação e armazenamento se aplicam à infraestrutura provisionada. Para obter os limites relevantes, consulte [assinatura do Azure e limites de serviço](../azure-subscription-service-limits.md).

> [!IMPORTANT]
> Quando você atualizar um cluster do AKS, temporariamente serão consumidos recursos adicionais. Esses recursos incluem endereços IP disponíveis em uma sub-rede de rede virtual ou a cota de vCPU de máquina virtual. Se você usar contêineres do Windows Server (atualmente em visualização no AKS), a única abordagem endossada para aplicar as atualizações mais recentes para os nós é executar uma operação de atualização. Um processo de atualização de cluster com falha pode indicar que você não tem o IP endereço espaço ou vCPU cota disponível para lidar com esses recursos temporários. Para obter mais informações sobre o processo de atualização de nó do Windows Server, consulte [Upgrade de um pool de nós no AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Tamanhos de VM restritos

Cada nó em um cluster AKS contém uma quantidade fixa de recursos de computação, como memória e vCPU. Se um nó do AKS contém recursos de computação insuficiente, pods poderá falhar ser executado corretamente. Para garantir que o necessário *kube-system* pods e seus aplicativos podem ser agendadas com confiança, não use os seguintes SKUs de VM no AKS:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Para obter mais informações sobre tipos de VM e seus recursos de computação, consulte [tamanhos para máquinas virtuais no Azure][vm-skus].

## <a name="region-availability"></a>Disponibilidade de região

Para obter a lista mais recente em que você pode implantar e executar clusters, consulte [disponibilidade de região do AKS][region-availability].

## <a name="next-steps"></a>Próximas etapas

Determinados limites e cotas padrão podem ser aumentados. Se o recurso dá suporte a um aumento, solicitar o aumento por meio de um [solicitação de suporte do Azure][azure-support] (para **tipo de problema**, selecione **cota**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
