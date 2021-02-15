---
title: Manutenção e atualizações
description: Visão geral de manutenção e atualizações para máquinas virtuais em execução no Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: shants
ms.openlocfilehash: 3cf126caaaa0c518574418aca194ebd82cc4d6b9
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972060"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Manutenção para máquinas virtuais no Azure

O Azure atualiza a plataforma periodicamente para aprimorar a confiabilidade, o desempenho e a segurança da infraestrutura de host para máquinas virtuais. A finalidade dessas atualizações vai desde a aplicação de patch de componentes de software no ambiente de hospedagem até a atualização de componentes de rede ou o encerramento de hardware. 

As atualizações raramente afetam as VMs hospedadas. Quando as atualizações têm um efeito, o Azure escolhe o método menos impactante de atualizações:

- Se a atualização não exigir uma reinicialização, a VM será pausada, enquanto o host é atualizado ou a VM é migrada ao vivo para um host já atualizado. 
- Se a manutenção exigir uma reinicialização, você será notificado sobre a manutenção planejada. O Azure também fornece uma janela de tempo, na qual você pode iniciar a manutenção, em um momento mais oportuno para você. A janela de manutenção automática normalmente é de 35 dias, a menos que a manutenção seja urgente. O Azure está investindo em tecnologias para reduzir o número de casos em que a manutenção planejada de plataforma exige a reinicialização das VMs. Para obter instruções sobre como gerenciar a manutenção planejada, confira Lidar com notificações de manutenção planejada usando a [CLI](maintenance-notifications-cli.md) do Azure, o [PowerShell](maintenance-notifications-powershell.md) ou o [portal](maintenance-notifications-portal.md).

Esta página descreve como o Microsoft Azure executa os dois tipos de manutenção. Para obter mais informações sobre eventos não planejados (interrupções), confira  [Gerenciar a disponibilidade das VMs para Windows](./manage-availability.md) ou o artigo correspondente para [Linux](./manage-availability.md).

De uma VM, você pode obter notificações sobre manutenção futura [usando os Eventos Agendados para Windows](./windows/scheduled-events.md) ou para [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Manutenção que não exige uma reinicialização

A maioria das atualizações de plataforma não afeta as VMs do cliente. Quando uma atualização sem impacto não é possível, o Azure escolhe o mecanismo de atualização menos impactante para as VMs dos clientes. 

A manutenção de impacto mais diferente de zero pausa a VM por menos de 10 segundos. Em determinados casos, o Azure usa mecanismos de manutenção de preservação de memória. Esses mecanismos pausam a VM por até 30 segundos e preservam a memória na RAM. Depois, a VM é reiniciada e o respectivo relógio é sincronizado automaticamente. 

A manutenção de preservação de memória funciona para mais de 90% das VMs do Azure. Ela não funciona para as séries G, M, N e H. Cada vez mais, o Azure usa tecnologias de migração ao vivo e aprimora o mecanismo de manutenção de preservação de memória para reduzir as durações da pausa.  

Essas operações de manutenção que não exigem uma reinicialização são aplicadas a um domínio de falha por vez. Elas serão interrompidas se receberem sinais de integridade de aviso das ferramentas de monitoramento de plataforma. 

Esses tipos de atualizações podem afetar alguns aplicativos. Quando a VM é migrada ao vivo para um host diferente, algumas cargas de trabalho confidenciais podem mostrar uma ligeira degradação do desempenho em alguns minutos que levam a pausar a máquina virtual. Para se preparar para a manutenção da VM e reduzir o impacto durante a manutenção do Azure, experimente [usar os Eventos Agendados para Windows](./windows/scheduled-events.md) ou [Linux](./linux/scheduled-events.md) para esses aplicativos. 

Para obter maior controle sobre todas as atividades de manutenção, incluindo impacto zero e atualizações sem reinicialização, você pode usar o recurso de Controle de Manutenção. Você precisa estar usando [Hosts Dedicados do Azure](./dedicated-hosts.md) ou uma [VM isolada](../security/fundamentals/isolation-choices.md). O controle de manutenção oferece a opção de ignorar todas as atualizações de plataforma e aplicar as atualizações à sua escolha de tempo em uma janela sem interrupção de 35 dias. Para obter mais informações, confira [Controlar atualizações com o Controle de Manutenção e a CLI do Azure](maintenance-control.md).


### <a name="live-migration"></a>Migração ao vivo

A migração ao vivo é uma operação que não exige uma reinicialização e preserva a memória da VM. Ela causa uma pausa ou um congelamento, normalmente com duração não superior a cinco segundos. Exceto para as séries G, M, N e H, todas as VMs de IaaS (infraestrutura como serviço) são qualificadas para a migração ao vivo. As VMs qualificadas representam mais de 90% das VMs de IaaS implantadas na frota do Azure. 

A plataforma Azure inicia a migração ao vivo nos seguintes cenários:
- Manutenção planejada
- Falha de hardware
- Otimizações de alocação

Alguns cenários de manutenção planejada usam a migração ao vivo, e você pode usar Eventos Agendados para saber com antecedência quando as operações de migração ao vivo serão iniciadas.

A migração ao vivo também pode ser usada para mover VMs quando os algoritmos do Azure Machine Learning preveem uma falha de hardware iminente ou quando você deseja otimizar as alocações de VM. Para obter mais informações sobre modelagem preditiva que detecta instâncias de hardware degradado, confira [Improving Azure VM resiliency with predictive machine learning and live migration](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure) (Aprimorar a resiliência da VM do Azure com o machine learning de previsão e a migração ao vivo). As notificações de migração ao vivo aparecem na portal do Azure nos logs de Monitoramento e de Integridade do Serviço, bem como nos Eventos Agendados se você usar esses serviços.

## <a name="maintenance-that-requires-a-reboot"></a>Manutenção que requer uma reinicialização

Quando as VMs precisarem ser reinicializadas para manutenção planejada, você será notificado com antecedência. A manutenção planejada tem duas fases: uma fase de autoatendimento e uma fase de manutenção agendada.

Durante a fase de *autoatendimento*, que geralmente dura quatro semanas, você inicia a manutenção em suas VMs. Como parte do autoatendimento, você pode consultar cada VM para ver o status e o resultado de sua última solicitação de manutenção.

Quando você inicia a manutenção de autoatendimento, sua VM é reimplantada em um nó já atualizado. Como a VM é reiniciada, o disco temporário é perdido e os endereços IP dinâmicos associados ao adaptador de rede virtual são atualizados.

Se surgir um erro durante a manutenção de autoatendimento, a operação será interrompida, a VM não será atualizada e você terá a opção de tentar novamente a manutenção de autoatendimento. 

Quando a fase de autoatendimento termina, a *fase de manutenção agendada* é iniciada. Durante essa fase, você ainda pode consultar a fase de manutenção, mas não pode iniciar a manutenção por conta própria.

Para obter mais informações sobre como gerenciar a manutenção que exige uma reinicialização, confira **Lidar com notificações de manutenção planejada** usando a [CLI](maintenance-notifications-cli.md) do Azure, o [PowerShell](maintenance-notifications-powershell.md) ou o [portal](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerações sobre disponibilidade durante a manutenção agendada 

Se você decidir aguardar até a fase de manutenção agendada, há alguns aspectos a serem considerados para manter a disponibilidade mais alta de suas VMs. 

#### <a name="paired-regions"></a>Regiões emparelhadas

Cada região do Azure é emparelhada com outra na mesma proximidade geográfica. Juntas, elas fazem um par de regiões. Durante a fase de manutenção agendada, o Azure atualiza somente as VMs em uma só região de um par de regiões. Por exemplo, ao atualizar a VM no Centro-Norte dos EUA, o Azure não atualiza nenhuma VM no Centro-Sul dos EUA ao mesmo tempo. No entanto, outras regiões, como o Norte da Europa, podem estar em manutenção simultaneamente com Leste dos EUA. Noções básicas sobre como funcionam os pares de região podem ajudá-lo a melhor distribuir suas VMs entre regiões. Para obter mais informações, consulte [pares de regiões do Azure](../best-practices-availability-paired-regions.md).

#### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidade e conjuntos de dimensionamento

Ao implantar uma carga de trabalho usando VMs do Azure, é possível criar as VMs em um *conjunto de disponibilidade* para fornecer alta disponibilidade para o aplicativo. Usando conjuntos de disponibilidade, você pode garantir que, durante uma interrupção ou eventos de manutenção que exijam uma reinicialização, pelo menos uma VM esteja disponível.

Em um conjunto de disponibilidade, VMs individuais são distribuídas em até 20 domínios de atualização. Durante a manutenção agendada, somente um domínio é atualizado em um período específico. Os domínios de atualização não são necessariamente atualizados em sequência. 

Os *conjuntos de dimensionamento* de máquinas virtuais são um recurso de computação do Azure que pode ser usado para implantar e gerenciar um conjunto de VMs idênticas como um só recurso. O conjunto de dimensionamento é implantado automaticamente nos UDs, como VMs em um conjunto de disponibilidade. Assim como com conjuntos de disponibilidade, quando você usa conjuntos de dimensionamento, somente um UD é afetado em um determinado momento durante a manutenção agendada.

Para obter mais informações sobre como configurar suas VMs para alta disponibilidade, confira  [Gerenciar a disponibilidade das VMs para Windows](./manage-availability.md) ou o artigo correspondente para [Linux](./manage-availability.md).

#### <a name="availability-zones"></a>Zonas de disponibilidade

As zonas de disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. 

Uma zona de disponibilidade é uma combinação de um domínio de falha e um domínio de atualização. Se você criar três ou mais VMs em três zonas em uma região do Azure, as VMs serão efetivamente distribuídas em três domínios de falha e três domínios de atualização. A plataforma do Azure reconhece essa distribuição nos domínios de atualização para garantir que as VMs em diferentes zonas não sejam atualizadas ao mesmo tempo.

Cada atualização de infraestrutura é distribuída zona por zona, dentro de uma só região. No entanto, você pode ter uma implantação em andamento na Zona 1 e uma implantação diferente em andamento na Zona 2, ao mesmo tempo. Implantações não são todas serializadas. Porém, uma só implantação distribui uma zona por vez para reduzir o risco.

## <a name="next-steps"></a>Próximas etapas 

Você pode usar a [CLI do Azure](maintenance-notifications-cli.md), o [Azure PowerShell](maintenance-notifications-powershell.md) ou o [portal](maintenance-notifications-portal.md) para gerenciar a manutenção planejada.