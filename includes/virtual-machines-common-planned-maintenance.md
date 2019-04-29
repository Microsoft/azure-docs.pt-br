---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 12/14/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c26c037455b6d14a906894ec39bf46630826950b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301690"
---
O Azure atualiza a plataforma periodicamente para aprimorar a confiabilidade, desempenho e segurança da infraestrutura de host para máquinas virtuais. Essas atualizações vão desde a aplicação de patch de componentes de software no ambiente de hospedagem, a atualização de componentes de rede até o encerramento de hardware. A maioria dessas atualizações não têm impacto nas máquinas virtuais hospedadas. No entanto, há casos em que as atualizações possuem um impacto e o Azure escolhe o método menos impacto de atualizações:

- Se uma atualização não reinicializável for possível, a VM estará em pausa, enquanto o host é atualizado ou quando estiver ativo migrados para um host já atualizado.

- Se a manutenção requer uma reinicialização, você receberá um aviso informando para quando a manutenção está planejada. O Azure fornecerá uma janela de tempo, onde você pode iniciar a manutenção, em um momento mais oportuno para você. Azure está investindo em tecnologias para reduzir os casos, quando as VMs precisam ser reinicializadas para manutenção planejada de plataforma. 

Esta página descreve como o Microsoft Azure executa os dois tipos de manutenção. Para obter mais informações sobre eventos não planejados (interrupções), consulte Gerenciar a disponibilidade das máquinas virtuais para [Windows](../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).

Você pode obter notificação na VM sobre manutenção futura usando os Eventos Agendados para [Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Para obter instruções sobre como gerenciar a manutenção planejada, consulte “Administrando notificações de manutenção planejada” para [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-not-requiring-a-reboot"></a>Manutenção não exigir uma reinicialização

A meta para a maioria dos manutenção que não requer uma reinicialização é pausar de menos de 10 segundos para a VM. Em determinados casos de preservação de memória, os mecanismos de manutenção são usados, o que para a máquina virtual por até 30 segundos e preserva a memória em RAM. A máquina virtual é reiniciada e o relógio da máquina virtual é sincronizado automaticamente. Cada vez mais, o Azure está usando tecnologias de migração ao vivo e melhorando o mecanismo de manutenção de preservação de memória para reduzir a duração da pausa.

Essas operações de manutenção não reiniciáveis são o domínio de falha aplicado pelo domínio de falha e o andamento é interrompido quando algum sinal de aviso de integridade é recebido. 

Alguns aplicativos podem ser afetados por esses tipos de atualizações. No caso da VM estar sendo migrada para um host diferente, algumas cargas de trabalho confidenciais poderão notar uma ligeira degradação do desempenho em alguns minutos que levam a pausar a máquina virtual. Tais aplicativos podem se beneficiar do uso para os Eventos Agendados [Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md) para se preparar para manutenção da VM e não têm impacto durante a manutenção do Azure. O Azure também está trabalhando em recursos de controle de manutenção para tais aplicativos extremamente confidenciais. 


## <a name="maintenance-requiring-a-reboot"></a>Manutenção que exige uma reinicialização

Quando as VMs precisarem ser reinicializadas para manutenção planejada, você será notificado com antecedência. A manutenção planejada tem duas fases: uma janela de autoatendimento e uma janela de manutenção agendada.

A **janela de autoatendimento** permite que você inicie a manutenção em suas VMs. Durante esse tempo, você pode consultar cada VM para ver seu status e verificar o resultado de sua última solicitação de manutenção.

Quando você inicia a manutenção de autoatendimento, sua VM é reimplantada em um nó já atualizado. Como a VM é reiniciada, o disco temporário é perdido e os endereços IP dinâmicos associados ao adaptador de rede virtual são atualizados.

Se você iniciar a manutenção de autoatendimento e ocorrer um erro durante o processo, a operação é interrompida, a VM não é atualizada e você obterá a opção de tentar novamente a manutenção de autoatendimento. 

Quando a janela de autoatendimento tiver passado, a **janela de manutenção agendada** começa. Durante essa janela de tempo você ainda pode consultar a janela de manutenção, mas não iniciará a manutenção por conta própria.

Para obter informações sobre como gerenciar a manutenção que exige reinicialização, consulte “Notificações de manutenção planejada” para [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerações sobre disponibilidade durante a manutenção planejada 

Se você decidir aguardar até a janela de manutenção planejada, há algumas coisas a serem consideradas para manter a disponibilidade mais alta de suas VMs. 

#### <a name="paired-regions"></a>Regiões emparelhadas

Cada região do Azure é emparelhada com outra região na mesma área geográfica e formando juntas um par regional. Na fase de manutenção agendada, o Azure atualizará somente as VMs em uma única região de um par de regiões. Por exemplo, ao atualizar as Máquinas Virtuais no Centro-Norte dos EUA, o Azure não atualizará qualquer Máquina Virtual no Centro-Sul dos EUA ao mesmo tempo. No entanto, outras regiões, como o Norte da Europa, podem estar em manutenção simultaneamente com Leste dos EUA. Noções básicas sobre como funcionam os pares de região podem ajudá-lo a melhor distribuir suas VMs entre regiões. Para obter mais informações, consulte [pares de regiões do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidade e conjuntos de dimensionamento

Ao implantar uma carga de trabalho usando VMs do Azure, é possível criar as VMs em um conjunto de disponibilidade para fornecer alta disponibilidade para o aplicativo. Isso garante que, durante uma interrupção ou eventos de manutenção, pelo menos uma máquina virtual estará disponível.

Em um conjunto de disponibilidade, VMs individuais são distribuídas em até 20 domínios de atualização (UDs). Durante a manutenção planejada, somente um único domínio é atualizado em um período específico. A ordem dos domínios de atualização que são afetados não necessariamente acontece de forma sequencial. 

Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que permite implantar e gerenciar um conjunto de VMs idênticas como um único recurso. O conjunto de dimensionamento é implantado automaticamente nos domínios de atualização, como VMs em um conjunto de disponibilidade. Assim como com conjuntos de disponibilidade, com conjuntos de dimensionamento somente um domínio de atualização é afetado em um determinado momento durante a manutenção agendada.

Para obter mais informações sobre como configurar as máquinas virtuais para alta disponibilidade, consulte Gerenciar a disponibilidade de suas máquinas virtuais do [Windows](../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).
