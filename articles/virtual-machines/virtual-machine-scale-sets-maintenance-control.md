---
title: Visão geral do controle de manutenção para atualizações de imagem do so em conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como controlar quando as atualizações automáticas de imagem do sistema operacional são distribuídas para os conjuntos de dimensionamento de máquinas virtuais do Azure usando o controle de manutenção.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532573"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>Visualização: controle de manutenção para conjuntos de dimensionamento de máquinas virtuais do Azure 

Gerencie [atualizações automáticas de imagem de so](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) para seus conjuntos de dimensionamento de máquinas virtuais usando o controle de manutenção.

O controle de manutenção permite que você decida quando aplicar atualizações aos discos do sistema operacional em seus conjuntos de dimensionamento de máquinas virtuais por meio de uma experiência mais fácil e mais previsível. 

As configurações de manutenção funcionam em assinaturas e grupos de recursos.

Todo o fluxo de trabalho se resume a estas etapas: 
- Crie uma configuração de manutenção.
- Associe um conjunto de dimensionamento de máquinas virtuais a uma configuração de manutenção.
- Habilitar atualizações automáticas do so.

> [!IMPORTANT]
> O controle de manutenção para atualizações de imagem do so em conjuntos de dimensionamento de máquinas virtuais do Azure está atualmente em visualização pública.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="limitations"></a>Limitações

- As VMs devem estar em um conjunto de dimensionamento.
- O usuário deve ter acesso de **colaborador de recurso** .
- A duração da manutenção deve ser de 5 horas ou mais na configuração de manutenção.
- A recorrência de manutenção deve ser definida como ' Day ' na configuração de manutenção


## <a name="management-options"></a>Opções de gerenciamento

Você pode criar e gerenciar configurações de manutenção usando qualquer uma das seguintes opções:

- [PowerShell do Azure](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>Próximas etapas

Saiba como controlar quando a manutenção é aplicada aos conjuntos de dimensionamento de máquinas virtuais do Azure usando o controle de manutenção e o PowerShell.

> [!div class="nextstepaction"]
> [Controle de manutenção do conjunto de dimensionamento de máquinas virtuais usando o PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)
