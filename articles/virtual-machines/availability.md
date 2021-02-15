---
title: Opções de disponibilidade
description: Saiba mais sobre os recursos de disponibilidade para executar máquinas virtuais no Azure
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: a0ea9a0871dd57088473999b28553258ff210038
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628757"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Opções de disponibilidade para máquinas virtuais no Azure

Este artigo fornece uma visão geral dos recursos de disponibilidade das VMs (máquinas virtuais) do Azure.

## <a name="high-availability"></a>Alta disponibilidade

As cargas de trabalho normalmente se disseminam entre diferentes máquinas virtuais para obter alta taxa de transferência, desempenho e para criar redundância no caso de uma VM ser afetada devido a uma atualização ou outro evento. 

Há algumas opções que o Azure fornece para obter alta disponibilidade. Primeiro, vamos falar sobre construções básicas. 

### <a name="availability-zones"></a>Zonas de disponibilidade

[Zonas de disponibilidade](../availability-zones/az-overview.md) expandem o nível de controle de que você precisa para manter a disponibilidade dos aplicativos e dos dados em suas VMs. Uma zona de disponibilidade é uma zona fisicamente separada, dentro de uma região do Azure. Há três Zonas de Disponibilidade por região do Azure com suporte. 

Cada zona de disponibilidade tem uma rede, resfriamento e fonte de energia distintos. Ao arquitetar suas soluções para usar VMs replicadas em zonas, você pode proteger seus aplicativos e seus dados contra a perda de um data center. Se uma zona for comprometida, os aplicativos e os dados replicados ficarão instantaneamente disponíveis em outra zona. 

![Zonas de disponibilidade](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Saiba mais sobre como implantar uma VM do [Windows](./windows/create-powershell-availability-zone.md) ou do [Linux](./linux/create-cli-availability-zone.md) em uma Zona de Disponibilidade.


### <a name="fault-domains"></a>Domínios de falha

Um domínio de falha é um grupo lógico de hardwares subjacentes que compartilham a mesma fonte de alimentação e o mesmo comutador de rede, de forma semelhante a um rack em um datacenter local. 

### <a name="update-domains"></a>Atualizar domínios

Um domínio de atualização é um grupo lógico de hardwares subjacentes que podem passar por manutenção ou ser reinicializados ao mesmo tempo. 

Essa abordagem garante que pelo menos uma instância do aplicativo sempre permaneça em execução enquanto a plataforma Windows Azure passar por manutenção periódica. A ordem dos domínios de atualização sendo reinicializados pode não continuar sequencialmente durante a manutenção, mas apenas um domínio de atualização é reinicializado por vez.


## <a name="virtual-machines-scale-sets"></a>Conjuntos de Escala de Máquinas Virtuais 

Os conjuntos de dimensionamento de máquinas virtuais do Azure permitem criar e gerenciar um grupo de VMs com balanceamento de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente em resposta à demanda ou a um agendamento definido. Os conjuntos de dimensionamento fornecem alta disponibilidade para seus aplicativos e permitem que você gerencie, configure e atualize centralmente muitas VMs. Recomendamos que duas ou mais VMs sejam criadas dentro de um conjunto de dimensionamento para fornecer um aplicativo altamente disponível e para atender ao [SLA de 99,95% do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Não há nenhum custo para o conjunto de dimensionamento em si, você só paga por cada instância de VM que criar. Quando uma única VM está usando [SSDs premium do Azure](./disks-types.md#premium-ssd), o SLA do Azure se aplica a eventos de manutenção não planejada. As máquinas virtuais em um conjunto de dimensionamento podem ser implantadas em vários domínios de atualização e domínios de falha para maximizar a disponibilidade e a resiliência a interrupções devido a interrupções de data center e eventos de manutenção planejados ou não planejados. As máquinas virtuais em um conjunto de dimensionamento também podem ser implantadas em uma única zona de disponibilidade ou regiões. As opções de implantação de zona de disponibilidade podem ser diferentes com base no modo de orquestração.

**Domínios de falha e domínios de atualização**

Os conjuntos de dimensionamento de máquinas virtuais simplificam o design para alta disponibilidade alinhando domínios de falha e domínios de atualização. Você precisará definir apenas a contagem de domínios de falha para o conjunto de dimensionamento. O número de domínios de falha disponíveis para os conjuntos de dimensionamento pode variar por região. Consulte [gerenciar a disponibilidade de máquinas virtuais no Azure](./manage-availability.md).


## <a name="availability-sets"></a>Conjuntos de disponibilidade
Um conjunto de disponibilidade é um agrupamento lógico de VMs que permite que o Azure entenda como o seu aplicativo foi criado para fornecer redundância e disponibilidade. Recomenda-se que duas ou mais VMs sejam criadas dentro de um conjunto de disponibilidade para fornecer um aplicativo altamente disponível e para atender o [SLA de 99,95% do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Não há nenhum custo para o conjunto de disponibilidade em si, você paga apenas por cada instância de VM que criar. Quando uma única VM está usando [SSDs premium do Azure](./disks-types.md#premium-ssd), o SLA do Azure se aplica a eventos de manutenção não planejada.

Em um conjunto de disponibilidade, as VMs são distribuídas automaticamente entre esses domínios de falha. Essa abordagem limita o impacto de possíveis falhas de hardware físico, interrupções de rede ou interrupções de energia.

Para VMs que usam [Azure Managed Disks](./faq-for-disks.md), as VMs são alinhadas aos domínios de falha de disco gerenciado quando usam um conjunto de disponibilidade gerenciada. Esse alinhamento garante que todos os discos gerenciados anexados a uma VM fiquem no mesmo domínio de falha de disco gerenciado. 

Somente as VMs com discos gerenciados podem ser criadas em um conjunto de disponibilidade gerenciado. O número de domínios de falha de disco gerenciado varia por região - dois ou três domínios de falha de disco gerenciados por região. Você pode ler mais sobre esses gerenciados domínios de falha de disco para [VMs do Linux](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) ou [VMs do Windows](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

![Conjunto de disponibilidade gerenciado](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


As VMs em um conjunto de disponibilidade também são distribuídas automaticamente entre domínios de atualização. 

![Conjuntos de disponibilidade](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Próximas etapas
Agora você pode começar a usar esses recursos de redundância e disponibilidade para criar seu ambiente do Azure. Para obter informações de práticas recomendadas, confira [Práticas recomendadas de disponibilidade do Azure](/azure/architecture/checklist/resiliency-per-service).
