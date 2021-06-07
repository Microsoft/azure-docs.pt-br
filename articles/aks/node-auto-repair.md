---
title: Reparando automaticamente os nós do AKS (serviço kubernetes do Azure)
description: Saiba mais sobre a funcionalidade de reparo automático do nó e como o AKS corrige os nós de trabalho interrompidos.
services: container-service
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 781a1ffebb40b0cce9f18699d308db90633e8626
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89490098"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Reparo automático do nó do AKS (serviço de kubernetes do Azure)

O AKS verifica continuamente o estado de integridade dos nós de trabalho e executa o reparo automático dos nós se eles se tornarem não íntegros. Este documento informa os operadores sobre como a funcionalidade automática de reparo de nó se comporta para nós do Windows e do Linux. Além dos reparos do AKS, a plataforma de VM do Azure [executa a manutenção em máquinas virtuais][vm-updates] que também têm problemas. O AKS e as VMs do Azure trabalham em conjunto para minimizar as interrupções de serviço para clusters.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Como o AKS verifica se há nós não íntegros

O AKS usa regras para determinar se um nó não está íntegro e precisa de reparo. O AKS usa as regras a seguir para determinar se o reparo automático é necessário.

* O nó relata o status de **não legível** em verificações consecutivas dentro de um período de 10 minutos
* O nó não relata um status dentro de 10 minutos

Você pode verificar manualmente o estado de integridade de seus nós com kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Como o reparo automático funciona

> [!Note]
> AKS inicia as operações de reparo com a conta de usuário **AKs-remediator**.

Se um nó não estiver íntegro com base nas regras acima e permanecer não íntegro por 10 minutos consecutivos, as ações a seguir serão executadas.

1. Reinicializar o nó
1. Se a reinicialização não for bem-sucedida, refazer a imagem do nó
1. Se a reimagem for malsucedida, criar e refazer a imagem de um novo nó

Se nenhuma das ações for bem-sucedida, correções adicionais serão investigadas por engenheiros de AKS. Se vários nós não estiverem íntegros durante uma verificação de integridade, cada nó será reparado individualmente antes do início de outro reparo.

## <a name="next-steps"></a>Próximas etapas

Use as [Zonas de Disponibilidade][availability-zones] para aumentar a HA com suas cargas de trabalho de cluster do AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
