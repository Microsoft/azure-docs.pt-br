---
title: Opções de disponibilidade para máquinas virtuais do Azure
description: Saiba mais sobre as opções de disponibilidade para executar máquinas virtuais no Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 821c50b5cb48bddb586c3589326a7f31c7bafded
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604525"
---
# <a name="availability-options-for-azure-virtual-machines"></a>Opções de disponibilidade para máquinas virtuais do Azure
Este artigo fornece uma visão geral das opções de disponibilidade para VMs (máquinas virtuais) do Azure.

## <a name="availability-zones"></a>Zonas de disponibilidade

As [zonas de disponibilidade](../availability-zones/az-overview.md?context=/azure/virtual-machines/context/context) expandem o nível de controle que você precisa para manter a disponibilidade dos aplicativos e dos dados em suas VMs. Uma zona de disponibilidade é uma zona fisicamente separada, dentro de uma região do Azure. Há três Zonas de Disponibilidade por região do Azure com suporte. 

Cada zona de disponibilidade tem uma rede, resfriamento e fonte de energia distintos. Ao projetar suas soluções para usar VMs replicadas em zonas, você pode proteger seus aplicativos e dados contra a perda de um data center. Se uma zona for comprometida, os aplicativos e os dados replicados ficarão instantaneamente disponíveis em outra zona. 

## <a name="availability-sets"></a>Conjuntos de disponibilidade
Um [conjunto de disponibilidade](availability-set-overview.md) é um agrupamento lógico de VMs que permite que o Azure entenda como seu aplicativo foi criado para fornecer redundância e disponibilidade. Recomenda-se que duas ou mais VMs sejam criadas dentro de um conjunto de disponibilidade para fornecer um aplicativo altamente disponível e para atender o [SLA de 99,95% do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Não há nenhum custo para o conjunto de disponibilidade em si, você paga apenas por cada instância de VM que criar.


## <a name="virtual-machines-scale-sets"></a>Conjuntos de Escala de Máquinas Virtuais 

Os [conjuntos de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/overview.md?context=/azure/virtual-machines/context/context) permitem criar e gerenciar um grupo de VMs com balanceamento de carga. O número de instâncias de VM pode aumentar ou diminuir automaticamente em resposta à demanda ou a um agendamento definido. Os conjuntos de dimensionamento fornecem alta disponibilidade para seus aplicativos e permitem que você gerencie, configure e atualize centralmente muitas VMs. Recomendamos que duas ou mais VMs sejam criadas dentro de um conjunto de dimensionamento para fornecer um aplicativo altamente disponível e para atender ao [SLA de 99,95% do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Não há nenhum custo para o conjunto de dimensionamento em si, você só paga por cada instância de VM que criar.

As máquinas virtuais em um conjunto de dimensionamento também podem ser implantadas em uma única zona de disponibilidade ou regiões. As opções de implantação de zona de disponibilidade podem ser diferentes com base no [modo de orquestração](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md?context=/azure/virtual-machines/context/context).

## <a name="load-balancer"></a>Balanceador de carga
Combine o [Azure Load Balancer](../load-balancer/load-balancer-overview.md) com uma zona de disponibilidade ou um conjunto de disponibilidade para obter a maior resiliência do aplicativo. O Balanceador de Carga do Azure distribui o tráfego entre as múltiplas máquinas virtuais. Para as nossas máquinas virtuais de camadas padrões, o Balanceador de carga do Azure está incluso. Nem todas as camadas de máquinas virtuais incluem o Azure Load Balancer. Para obter mais informações sobre balanceamento de carga de suas máquinas virtuais, consulte **balanceamento de carga de máquinas virtuais** para [Linux](linux/tutorial-load-balancer.md) ou [Windows](windows/tutorial-load-balancer.md).


## <a name="azure-storage-redundancy"></a>Redundância do Armazenamento do Azure
A redundância do Armazenamento do Azure sempre armazena várias cópias dos seus dados para que eles sejam protegidos contra eventos planejados e não planejados, incluindo falhas de hardware transitórias, interrupções de energia ou rede e desastres naturais de grandes proporções. A redundância garante que sua conta de armazenamento atenda às suas metas de disponibilidade e durabilidade mesmo diante de falhas.

Ao decidir qual opção de redundância é melhor para seu cenário, considere as compensações entre custos menores e maior disponibilidade. Os fatores que ajudam a determinar qual opção de redundância você deve escolher incluem:
- Como os dados são replicados na região primária
- Se seus dados são replicados para uma segunda região que está geograficamente distante para a região primária, para proteger contra desastres regionais
- Se o aplicativo requer acesso de leitura aos dados replicados na região secundária se a região primária ficar indisponível por qualquer motivo

Para obter mais informações, consulte [redundância de armazenamento do Azure](../storage/common/storage-redundancy.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Como uma organização, você precisa adotar uma estratégia de BCDR (continuidade de negócios e recuperação de desastre) que mantém seus dados seguros e seus aplicativos e cargas de trabalho online, quando ocorrem interrupções planejadas e não planejadas.

[Azure site Recovery](../site-recovery/site-recovery-overview.md) ajuda a garantir a continuidade dos negócios mantendo os aplicativos de negócios e as cargas de trabalho em execução durante interrupções. O Site Recovery replica as cargas de trabalho em execução em máquinas físicas e virtuais (VMs) de um site primário para um local secundário. Quando uma interrupção ocorre no seu site primário, você faz failover para o local secundário e acessa os aplicativos a partir daí. Depois que a localização primária estiver novamente em execução, você poderá fazer failback a ela.

O Site Recovery pode gerenciar a replicação para:
- VMs do Azure que replicam entre regiões do Azure.
- VMs locais, VMs do Azure Stack e servidores físicos.

## <a name="next-steps"></a>Próximas etapas
- [Criar uma máquina virtual em uma zona de disponibilidade](/azure/virtual-machines/linux/create-cli-availability-zone)
- [Criar uma máquina virtual em um conjunto de disponibilidade](/azure/virtual-machines/linux/tutorial-availability-sets)
- [Criar um conjunto de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/quick-create-portal.md)