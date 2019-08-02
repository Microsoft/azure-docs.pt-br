---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171902"
---
O Azure atualiza periodicamente sua plataforma para melhorar a confiabilidade, desempenho e segurança da infraestrutura de host para máquinas virtuais. A finalidade dessas atualizações varia de componentes de software no ambiente de hospedagem para atualização de componentes de rede ou desativação de hardware de aplicação de patch. 

Raramente, as atualizações afetam as VMs hospedadas. Quando as atualizações tiverem um efeito, o Azure escolhe o método menos impacto para atualizações:

- Se a atualização não exigir uma reinicialização, a VM está em pausa enquanto o host é atualizado, ou a VM é migradas ao vivo para um host já atualizado.

- Se a manutenção requer uma reinicialização, você será notificado de que a manutenção planejada. O Azure também fornece uma janela de tempo em que você pode iniciar a manutenção por conta própria, cada vez que funciona para você. A janela de manutenção de autoatendimento normalmente é 30 dias, a menos que a manutenção é urgente. Azure está investindo em tecnologias para reduzir o número de casos em que a manutenção planejada plataforma requer as VMs sejam reinicializados. 

Esta página descreve como o Microsoft Azure executa os dois tipos de manutenção. Para obter mais informações sobre eventos não planejados (interrupções), consulte [gerenciar a disponibilidade das máquinas virtuais para Windows](../articles/virtual-machines/windows/manage-availability.md) ou para ler o artigo correspondente [Linux](../articles/virtual-machines/linux/manage-availability.md).

Dentro de uma VM, você pode obter notificações sobre a próxima manutenção por [usando agendado eventos para Windows](../articles/virtual-machines/windows/scheduled-events.md) ou para [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Para obter instruções sobre como gerenciar a manutenção planejada, consulte [notificações de manutenção para Linux planejada](../articles/virtual-machines/linux/maintenance-notifications.md) ou para ler o artigo correspondente [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Manutenção que não requer uma reinicialização

Conforme mencionado anteriormente, a maioria das atualizações de plataforma não afetam as VMs de cliente. Quando uma atualização sem impacto não for possível, o Azure escolhe o mecanismo de atualização que é o mínimo de impacto para máquinas virtuais do cliente. 

Manutenção de impacto diferente de zero a maioria dos pausa a máquina virtual para menos de 10 segundos. Em alguns casos, o Azure usa mecanismos de manutenção de preservação de memória. Esses mecanismos pausar a máquina virtual por até 30 segundos e preservam a memória RAM. A VM é reiniciada e seu relógio é sincronizado automaticamente. 

Manutenção de preservação de memória funciona para mais de 90% das VMs do Azure. Ele não funciona para série H, M, N e G. Cada vez mais, o Azure usa tecnologias de migração ao vivo e melhora os mecanismos de manutenção de preservação de memória para reduzir as durações de pausar.  

Essas operações de manutenção que não exigem uma reinicialização são domínio de falha aplicado por vez. Eles pararem de se o usuário receber qualquer sinais de integridade de aviso. 

Esses tipos de atualizações podem afetar alguns aplicativos. Quando a VM estiver migradas ao vivo para um host diferente, algumas cargas de trabalho confidenciais podem mostrar uma ligeira degradação do desempenho nos minutos levam ao pausar a máquina virtual. Para se preparar para manutenção da VM e reduzir o impacto durante a manutenção do Azure, tente [usando agendado eventos para Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md) para tais aplicativos. Azure está trabalhando em recursos de controle de manutenção para esses aplicativos confidenciais. 

### <a name="live-migration"></a>Migração ao vivo

Migração ao vivo é uma operação que não exige uma reinicialização e que preserva a memória para a VM. Ele faz uma pausa ou o congelamento, normalmente durando não mais de 5 segundos. Com exceção de G, série H, M e N, toda a infraestrutura como um serviço (IaaS) para VMs, é elegível para migração ao vivo. VMs qualificadas representam mais de 90 por cento das VMs de IaaS que são implantados em frota do Azure. 

A plataforma Azure inicia a migração ao vivo nos seguintes cenários:
- Manutenção planejada
- Falha de hardware
- Otimizações de alocação

Alguns cenários de manutenção planejada usam migração ao vivo, e você pode usar eventos agendados para saber com antecedência quando ao vivo começarão a operações de migração.

Migração ao vivo também pode ser usada para mover as VMs quando algoritmos de aprendizado de máquina do Azure prever uma falha de hardware iminente ou quando você deseja otimizar as alocações de VM. Para obter mais informações sobre a modelagem de previsão que detecta instâncias de hardware degradado, consulte [resiliência melhorando a VM do Azure com o aprendizado de máquina de previsão e migração ao vivo](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Notificações de migração ao vivo são exibidos no portal do Azure em que o Monitor e a integridade do serviço de log, bem como em eventos agendados, se você usar esses serviços.

## <a name="maintenance-that-requires-a-reboot"></a>Manutenção que exige uma reinicialização

No caso raro em que as VMs precisam ser reinicializadas para manutenção planejada, você será notificado com antecedência. Manutenção planejada tem duas fases: a fase de autoatendimento e uma fase de manutenção agendada.

Durante o *Self-service fase*, que normalmente dura quatro semanas, se você iniciar a manutenção em suas VMs. Como parte de autoatendimento, você pode consultar cada VM para ver seu status e o resultado da última solicitação de manutenção.

Quando você inicia a manutenção de autoatendimento, sua VM é reimplantada em um nó já atualizado. Porque a VM reinicializa, o disco temporário é perdido e os endereços IP dinâmicos associados com a interface de rede virtual são atualizados.

Se um erro ocorrer durante a manutenção de autoatendimento, a operação é interrompida, a VM não será atualizada e você obtém a opção para repetir a manutenção de autoatendimento. 

Quando termina a fase de autoatendimento, o *fase de manutenção agendada* começa. Durante essa fase, você ainda pode consultar para a fase de manutenção, mas você não pode iniciar a manutenção.

Para obter mais informações sobre como gerenciar a manutenção que exige uma reinicialização, consulte [notificações de manutenção para Linux planejada](../articles/virtual-machines/linux/maintenance-notifications.md) ou para ler o artigo correspondente [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerações sobre disponibilidade durante a manutenção agendada 

Se você decidir aguardar até a fase de manutenção agendada, há algumas coisas que você deve considerar para manter a disponibilidade mais alta de suas VMs. 

#### <a name="paired-regions"></a>Regiões emparelhadas

Cada região do Azure é emparelhada com outra região dentro do mesmo ambiente de rede geográfico. Juntos, eles tornam um par de regiões. Durante a fase de manutenção agendada, o Azure atualiza apenas as VMs em uma única região de um par de regiões. Por exemplo, ao atualizar a VM no Centro-Norte dos EUA, Azure não atualiza qualquer VM no Centro-Sul dos EUA ao mesmo tempo. No entanto, outras regiões, como o Norte da Europa, podem estar em manutenção simultaneamente com Leste dos EUA. Noções básicas sobre como funcionam os pares de região podem ajudá-lo a melhor distribuir suas VMs entre regiões. Para obter mais informações, consulte [pares de regiões do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidade e conjuntos de dimensionamento

Ao implantar uma carga de trabalho em VMs do Azure, você pode criar as VMs em um *conjunto de disponibilidade* para fornecer alta disponibilidade para seu aplicativo. Usando conjuntos de disponibilidade, você pode garantir que durante eventos de manutenção ou interrupção que exigem uma reinicialização, pelo menos uma VM está disponível.

Em um conjunto de disponibilidade, VMs individuais são distribuídas em até 20 domínios de atualização (UDs). Durante a manutenção agendada, apenas uma UD é atualizada a qualquer momento. UDs necessariamente não forem atualizada em sequência. 

Máquina virtual *conjuntos de dimensionamento* são recursos que você pode usar para implantar e gerenciar um conjunto de VMs idênticas como um único recurso de computação do Azure. O conjunto de dimensionamento é implantado automaticamente entre UDs, como as VMs em um conjunto de disponibilidade. Como com conjuntos de disponibilidade, quando você usa conjuntos de dimensionamento, apenas uma UD é atualizada a qualquer momento determinado durante a manutenção agendada.

Para obter mais informações sobre como configurar suas VMs para alta disponibilidade, consulte [gerenciar a disponibilidade de suas máquinas virtuais para Windows](../articles/virtual-machines/windows/manage-availability.md) ou para ler o artigo correspondente [Linux](../articles/virtual-machines/linux/manage-availability.md).
