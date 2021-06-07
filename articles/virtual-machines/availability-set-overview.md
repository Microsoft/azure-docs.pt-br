---
title: Visão geral dos conjuntos de disponibilidade
description: Saiba mais sobre os conjuntos de disponibilidade no Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: c0bbc218723ea75064d9eb6f9c5f8f8c0d18a10b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510444"
---
# <a name="availability-sets-overview"></a>Visão geral dos conjuntos de disponibilidade

Este artigo fornece uma visão geral dos recursos de disponibilidade das VMs (máquinas virtuais) do Azure.

## <a name="what-is-an-availability-set"></a>O que é um conjunto de disponibilidade? 

Um conjunto de disponibilidade é um agrupamento lógico de VMs que permite que o Azure entenda como o seu aplicativo foi criado para fornecer redundância e disponibilidade. Recomenda-se que duas ou mais VMs sejam criadas dentro de um conjunto de disponibilidade para fornecer um aplicativo altamente disponível e para atender o [SLA de 99,95% do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Não há nenhum custo para o conjunto de disponibilidade em si, você paga apenas por cada instância de VM que criar.

## <a name="how-do-availability-sets-work"></a>Como funcionam os conjuntos de disponibilidade?
Cada máquina virtual em seu conjunto de disponibilidade receberá um **domínio de atualização** e um **domínio de falha** da plataforma subjacente do Azure. Para determinado conjunto de disponibilidade, cinco domínios de atualização não configuráveis pelo usuário são atribuídos por padrão (é possível aumentar as implantações do Resource Manager para fornecer até 20 domínios de atualização) para indicar grupos de máquinas virtuais e hardware físico subjacente que pode ser reinicializado ao mesmo tempo. Quando mais do que cinco máquinas virtuais são configuradas com um único conjunto de disponibilidade, a sexta máquina virtual será alocada com o mesmo domínio de atualização da primeira máquina virtual, a sétima com o mesmo domínio de atualização da segunda máquina virtual e assim sucessivamente. A ordem de reinicialização dos domínios de atualização pode não ser sequencial durante a manutenção planejada, mas apenas um domínio de atualização é reinicializado por vez. Um domínio de atualização reinicializado recebe 30 minutos para recuperação antes do início da manutenção em um domínio de atualização diferente.

Os domínios de falha definem o grupo de máquinas virtuais que compartilham uma fonte de energia e chave de rede comum. Por padrão, as máquinas virtuais configuradas em seu conjunto de disponibilidade são separadas em até três domínios de falha para implantações do Gerenciador de recursos. Embora colocar suas máquinas virtuais em um conjunto de disponibilidade não proteja seu aplicativo de falhas de sistema operacional e nem específicas de aplicativo, isso limita o impacto das potencias falhas físicas de hardware, panes de rede ou interrupções de energia.

![Conjuntos de disponibilidade](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)


Para VMs que usam [Azure Managed Disks](./faq-for-disks.md), as VMs são alinhadas aos domínios de falha de disco gerenciado quando usam um conjunto de disponibilidade gerenciada. Esse alinhamento garante que todos os discos gerenciados anexados a uma VM fiquem no mesmo domínio de falha de disco gerenciado. 

Somente as VMs com discos gerenciados podem ser criadas em um conjunto de disponibilidade gerenciado. O número de domínios de falha de disco gerenciado varia por região - dois ou três domínios de falha de disco gerenciados por região. 

![Conjunto de disponibilidade gerenciado](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="next-steps"></a>Próximas etapas
Agora você pode começar a usar esses recursos de redundância e disponibilidade para criar seu ambiente do Azure. Para obter informações de práticas recomendadas, confira [Práticas recomendadas de disponibilidade do Azure](/azure/architecture/checklist/resiliency-per-service).

