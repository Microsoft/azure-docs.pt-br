---
title: Excluir discos de VM do VMware da recuperação de desastre para o Azure com Azure Site Recovery
description: Como excluir discos de VM do VMware da replicação para o Azure com Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: c4842172ff181b5cdbe7f6fecf69da8755ae43fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86129867"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Excluir discos da replicação de VM do VMware para o Azure

Este artigo descreve como excluir discos ao replicar VMs VMware para o Azure para recuperação de desastre. Talvez você queira excluir discos da replicação por vários motivos:

- Certifique-se de que os dados não importantes que foram copiados no disco excluído não sejam replicados.
- Otimize a largura de banda de replicação consumida ou os recursos do lado do destino, excluindo discos que você não precisa replicar.
- Salve os recursos de armazenamento e de rede não replicando os dados de que você não precisa.

Antes de excluir discos da replicação:

- [Saiba mais](exclude-disks-replication.md) sobre a exclusão de discos.
- Examine os cenários e [exemplos](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) [comuns de exclusão](exclude-disks-replication.md#typical-scenarios) que mostram como a exclusão de um disco afeta a replicação, o failover e o failback.

## <a name="before-you-start"></a>Antes de começar

 Observe o seguinte antes de começar:

- **Replicação**: por padrão, todos os discos em um computador são replicados.
- **Tipo de disco**: somente discos básicos podem ser excluídos da replicação. Você não pode excluir o sistema operacional ou discos dinâmicos.
- **Serviço de mobilidade**: para excluir um disco da replicação, você deve instalar manualmente o serviço de mobilidade no computador antes de habilitar a replicação. Não é possível usar a instalação por push, já que esse método instala o serviço de mobilidade em uma VM somente após a replicação ser habilitada.  
- **Adicionar/remover/excluir discos**: depois de habilitar a replicação, você não pode adicionar/remover/excluir discos para replicação. Se desejar adicionar/remover ou excluir discos, você precisará desabilitar a proteção do computador e habilitá-la novamente.
- **Failover**: após o failover, se os aplicativos com falha forem necessários discos excluídos para funcionar, você precisará criar esses discos manualmente. Como alternativa, você pode integrar a automação do Azure em um plano de recuperação para criar o disco durante o failover do computador.
- **Failback-Windows**: quando você realiza o failback para o site local após o failover, os discos do Windows que você criar manualmente no Azure não sofrerão failback. Por exemplo, se você executar failover de três discos e criar dois discos diretamente nas VMs do Azure, somente os três discos que sofreram failover serão submetidos a failback.
- **Failback-Linux**: para o failback de computadores Linux, os discos que você cria manualmente no Azure são submetidos a failback. Por exemplo, se você executar failover de três discos e criar dois discos diretamente nas VMs do Azure, todos os cinco serão submetidos a failback. Você não pode excluir discos que foram criados manualmente no failback ou em nova proteção de VMs.



## <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

1. Ao [habilitar a replicação](./hyper-v-azure-tutorial.md) para uma VM VMware, depois de selecionar as VMs que você deseja replicar, na página **habilitar**  >  Propriedades de configurar**Propriedades**de replicação  >  **Configure properties** , examine a coluna **discos para replicar** . Por padrão, todos os discos são selecionados para replicação.
2. Se você não quiser replicar um disco específico, em **discos para replicar** , limpe a seleção de todos os discos que você deseja excluir. 

    ![Excluir discos da replicação](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Próximas etapas
Depois que a implantação estiver configurada e em funcionamento, [saiba mais](failover-failback-overview.md) sobre o os diferentes tipos de failover.
