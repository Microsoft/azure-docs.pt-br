---
title: Exclua os discos de VM do Hyper-V da recuperação de desastre para o Azure com Azure Site Recovery
description: Como excluir discos de VM do Hyper-V da replicação para o Azure com Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 373cffe96119af4a2fc0d74e2090e6cc24dcaf8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86131258"
---
# <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Este artigo descreve como excluir discos ao replicar VMs do Hyper-V para o Azure. Talvez você queira excluir discos da replicação por vários motivos:

- Certifique-se de que os dados não importantes que foram copiados no disco excluído não sejam replicados.
- Otimize a largura de banda de replicação consumida ou os recursos do lado do destino, excluindo discos que você não precisa replicar.
- Salve os recursos de armazenamento e de rede não replicando os dados de que você não precisa.

Antes de excluir discos da replicação:

- [Saiba mais](exclude-disks-replication.md) sobre a exclusão de discos.
- Examine os cenários e [exemplos](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) [comuns de exclusão](exclude-disks-replication.md#typical-scenarios) que mostram como a exclusão de um disco afeta a replicação, o failover e o failback.

## <a name="before-you-start"></a>Antes de começar

Observe o seguinte antes de começar:

- **Replicação**: por padrão, todos os discos em um computador são replicados.
- **Tipo de disco**:
    - Você pode excluir somente os discos básicos da replicação.
    - Você não pode excluir os discos do sistema operacional.
    - É recomendável que você não exclua discos dinâmicos. Site Recovery não pode identificar qual VHD é básico ou dinâmico na VM convidada.  Se você não excluir todos os discos de volume dinâmico dependentes, o disco dinâmico protegido se tornará um disco com falha em uma VM com failover e os dados nesse disco não estarão acessíveis.
- **Adicionar/remover/excluir discos**: depois de habilitar a replicação, você não pode adicionar/remover/excluir discos para replicação. Se desejar adicionar/remover ou excluir um disco, você precisará desabilitar a proteção para a VM e habilitá-la novamente.
- **Failover**: após o failover, se os aplicativos com falha precisarem excluir discos para funcionar, você precisará criar esses discos manualmente. Como alternativa, você pode integrar a automação do Azure em um plano de recuperação para criar o disco durante o failover do computador.
- **Failback**: ao fazer failback para o site local após o failover, os discos que você criou manualmente no Azure não sofrerão failback. Por exemplo, se você executar failover de três discos e criar dois discos diretamente em uma VM do Azure, somente três discos que sofreram failover serão submetidos a failback. Você não pode incluir discos que foram criados manualmente no failback ou na replicação inversa de VMs.

## <a name="exclude-disks"></a>Excluir discos

1. Para excluir discos quando você [habilita a replicação](./hyper-v-azure-tutorial.md) para uma VM do Hyper-V, depois de selecionar as VMs que deseja replicar, na página habilitar Propriedades de configuração de **replicação**  >  **Properties**  >  **Configure properties** , examine a coluna **discos para replicar** . Por padrão, todos os discos são selecionados para replicação.
2. Se você não quiser replicar um disco específico, em **discos para replicar** , limpe a seleção de todos os discos que você deseja excluir. 

    ![Excluir discos da replicação](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Próximas etapas
Depois que a implantação estiver configurada e em funcionamento, [saiba mais](failover-failback-overview.md) sobre o os diferentes tipos de failover.
