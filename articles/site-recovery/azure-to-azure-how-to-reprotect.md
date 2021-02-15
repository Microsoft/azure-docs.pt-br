---
title: Proteger novamente as VMs do Azure para a região primária com Azure Site Recovery | Microsoft Docs
description: Descreve como proteger novamente as VMs do Azure após o failover, a região secundária para a primária, usando Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 3b9edab6e908b4506a92c78aa8f3f53277b9c17b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360864"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Proteger novamente VMs do Azure que sofreram failover para a região primária

Quando você faz [failover](site-recovery-failover.md) de VMs do Azure de uma região para outra usando [Azure site Recovery](site-recovery-overview.md), as VMs são inicializadas na região secundária, em um estado **desprotegido** . Se você quiser fazer failback das VMs para a região primária, execute as seguintes tarefas:

1. Proteja novamente as VMs na região secundária, para que elas iniciem a replicação para a região primária.
1. Depois que a nova proteção for concluída e as VMs estiverem replicando, você poderá fazer failover da região secundária para a primária.

## <a name="prerequisites"></a>Pré-requisitos

- O failover de VM da região primária para a secundária deve ser confirmado.
- O local de destino primário deve estar disponível e você deve ser capaz de acessar ou criar recursos nessa região.

## <a name="reprotect-a-vm"></a>Proteger novamente uma VM

1. Em **cofre**  >  **itens replicados**, clique com o botão direito do mouse na VM com failover e selecione **proteger novamente**. A direção de nova proteção deve mostrar da secundária para a primária.

   ![Captura de tela mostra uma máquina virtual com um menu contextual com a nova proteção selecionada.](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Examine o grupo de recursos, a rede, o armazenamento e os conjuntos de disponibilidade. Em seguida, clique em **OK**. Se houver algum recurso marcado como novo, ele será criado como parte do processo de nova proteção.
1. O trabalho de nova proteção propaga os dados mais recentes para o site de destino. Após a conclusão do trabalho, a replicação delta ocorre. Em seguida, você pode fazer failover de volta para o site primário. Você pode selecionar a conta de armazenamento ou a rede que você deseja usar ao proteger novamente, usando a opção personalizar.

   ![Opção de personalização](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Personalizar as configurações de nova proteção

Você pode personalizar as seguintes propriedades da VM de destino durante a nova proteção.

![Personalizar](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriedade |Observações  |
|---------|---------|
|Grupo de recursos de destino | Modifique o grupo de recursos de destino no qual a VM é criada. Como parte da nova proteção, a VM de destino é excluída. Você pode escolher um novo grupo de recursos sob o qual criar a VM após o failover. |
|Rede virtual de destino | A rede de destino não pode ser alterada durante o trabalho de nova proteção. Para alterar a rede, refaça o mapeamento de rede. |
|Armazenamento de destino (a VM secundária não usa discos gerenciados) | Você pode alterar a conta de armazenamento que a VM usa após o failover. |
|Discos gerenciados de réplica (a VM secundária usa discos gerenciados) | O Site Recovery cria discos gerenciados de réplica na região primária para espelhar discos gerenciados da VM secundária. |
|Armazenamento em cache | Você pode especificar uma conta de armazenamento de cache a ser usada durante a replicação. Por padrão, uma nova conta de armazenamento em cache é criada, caso ela não exista. |
|Conjunto de disponibilidade | Se a VM na região secundária for parte de um conjunto de disponibilidade, você poderá escolher um conjunto de disponibilidade para a VM de destino na região primária. Por padrão, o Site Recovery tenta localizar o conjunto de disponibilidade existente na região primária e usá-lo. Durante a personalização, você pode especificar um novo conjunto de disponibilidade. |

### <a name="what-happens-during-reprotection"></a>O que acontece ao proteger novamente?

Por padrão, ocorre o seguinte:

1. Uma conta de armazenamento de cache é criada na região onde a VM com failover está em execução.
1. Se a conta de armazenamento de destino (a conta de armazenamento original na região primária) não existir, uma nova será criada. O nome da conta de armazenamento atribuído é o nome da conta de armazenamento usada pela VM secundária, sufixada com `asr` .
1. Se a sua VM usar discos gerenciados, os discos gerenciados de réplica serão criados na região primária para armazenar os dados replicados dos discos da VM secundária.
1. Se o conjunto de disponibilidade de destino não existir, um novo será criado como parte do trabalho de nova proteção, se necessário. Se você tiver personalizado as configurações de nova proteção, o conjunto selecionado será usado.

Quando você dispara um trabalho de nova proteção e a VM de destino existe, ocorre o seguinte:

1. A VM do lado de destino é desligada quando ela está em execução.
1. Se a VM estiver usando discos gerenciados, uma cópia do disco original será criada com um `-ASRReplica` sufixo. Os discos originais são excluídos. As `-ASRReplica` cópias são usadas para replicação.
1. Se a VM estiver usando discos não gerenciados, os discos de dados da VM de destino são desanexados e usados para replicação. Uma cópia do disco do sistema operacional é criada e anexada na VM. O disco do sistema operacional original é desanexado e usado para replicação.
1. Somente alterações entre o disco de origem e o disco de destino são sincronizadas. Os diferenciais são computados comparando ambos os discos e, em seguida, transferidos. Verifique abaixo para encontrar o tempo estimado para concluir a nova proteção.
1. Após a conclusão da sincronização, a replicação delta é iniciada e um ponto de recuperação é criado em linha com a política de replicação.

Quando você dispara um trabalho de nova proteção e a VM de destino e os discos não existem, ocorre o seguinte:

1. Se a VM estiver usando discos gerenciados, os discos de réplica serão criados com o `-ASRReplica` sufixo. As `-ASRReplica` cópias são usadas para replicação.
1. Se a VM estiver usando discos não gerenciados, réplicas dos discos originais serão criadas na conta de armazenamento de destino.
1. Todos discos são copiados da região com failover para a nova região de destino.
1. Após a conclusão da sincronização, a replicação delta é iniciada e um ponto de recuperação é criado em linha com a política de replicação.

#### <a name="estimated-time-to-do-the-reprotection"></a>Tempo estimado para a nova proteção

Na maioria dos casos, Azure Site Recovery não replica os dados completos para a região de origem.
As seguintes condições determinam a quantidade de dados replicada:

1. Se os dados da VM de origem forem excluídos, corrompidos ou inacessíveis por algum motivo, como uma alteração/exclusão do grupo de recursos, durante a nova proteção, ocorrerá uma replicação inicial completa, pois não há dados disponíveis na região de origem a ser usada.
1. Se os dados da VM de origem estiverem acessíveis, somente os diferenciais serão computados comparando os discos e, em seguida, transferidos. Verifique a tabela abaixo para obter o tempo estimado.

|Situação de exemplo | Tempo necessário para proteger novamente |
|---|---|
|A região de origem tem uma VM com um disco padrão de 1 TB.<br/>Somente 127 GB de dados são usados e o restante do disco está vazio.<br/>O tipo de disco é Standard com taxa de transferência de 60 MBps.<br/>Nenhum dado alterado após o failover.| Tempo aproximado: 60-90 minutos.<br/> Durante a nova proteção, Site Recovery preencherá a soma de verificação de todos os dados. Isso opera em 45MBps, portanto, o tempo total que levará é de 127 GB/45 MBps, aproximadamente 45 minutos.<br/>Um tempo de sobrecarga é necessário para Site Recovery a escala automática, aproximadamente 20-30 minutos. |
|A região de origem tem uma VM com um disco padrão de 1 TB.<br/>Somente 127 GB de dados são usados e o restante do disco está vazio.<br/>O tipo de disco é Standard com taxa de transferência de 60 MBps.<br/>45 GB de alteração de dados após o failover.| Tempo aproximado: 2,5 a 3 horas.<br/> Durante a nova proteção, Site Recovery preencherá a soma de verificação de todos os dados. Isso opera em 45MBps, portanto, o tempo total que levará é de 127 GB/45 MBps, aproximadamente 45 minutos.<br/>A velocidade de transferência é de aproximadamente 16% da taxa de transferência ou 9,6 MBps. Portanto, o tempo de transferência para aplicar alterações de 45 GB com 45 GB/9,6 MBps, aproximadamente 80 minutos.<br/>Um tempo de sobrecarga é necessário para Site Recovery a escala automática, aproximadamente 20-30 minutos. |
|A região de origem tem uma VM com um disco padrão de 1 TB.<br/>Somente 20 GB de dados são usados e o restante do disco está vazio.<br/>O tipo de disco é Standard com taxa de transferência de 60 MBps.<br/>Os dados iniciais no disco imediatamente após o failover foram 15 GB. Houve uma alteração de dados de 5 GB após o failover. O total de dados preenchidos é, portanto, 20 GB.| Tempo aproximado: 1 a 1,5 horas.<br/>Como os dados populados no disco são inferiores a 10% do tamanho do disco, executamos uma replicação inicial completa.<br/> A velocidade de transferência é de aproximadamente 16% da taxa de transferência ou 9,6 MBps. Portanto, o tempo de transferência para aplicar alterações de 20 GB com 20 GB/9,6 MBps, aproximadamente 36 minutos.<br/>Um tempo de sobrecarga é necessário para Site Recovery a escala automática, aproximadamente 20-30 minutos. |
|A região de origem tem uma VM com disco Premium de 1 TB.<br/>Somente 127 GB de dados são usados e o restante do disco está vazio.<br/>O tipo de disco é Premium com taxa de transferência de 200 MBps.<br/>Nenhum dado alterado após o failover.| Tempo aproximado: 2 horas.<br/>Durante a nova proteção, Site Recovery preencherá a soma de verificação de todos os dados. Isso opera em 25MBps (até 16% da taxa de transferência do disco), portanto, o tempo total que levará é de 127 GB/25 MBps, aproximadamente 87 minutos.<br/>Um tempo de sobrecarga é necessário para Site Recovery a escala automática, aproximadamente 20-30 minutos. |
|A região de origem tem uma VM com disco Premium de 1 TB.<br/>Somente 127 GB de dados são usados e o restante do disco está vazio.<br/>O tipo de disco é Premium com taxa de transferência de 200 MBps.<br/>45 GB de alteração de dados após o failover.| Tempo aproximado: 2,5 a 3 horas.<br/>Durante a nova proteção, Site Recovery preencherá a soma de verificação de todos os dados. Isso opera em 25MBps (até 16% da taxa de transferência do disco), portanto, o tempo total que levará é de 127 GB/25 MBps, aproximadamente 87 minutos.</br>A velocidade de transferência é aproximadamente 16% da taxa de transferência ou 32MBps. Portanto, o tempo de transferência para aplicar alterações de 45 GB com 45 GB/32 MBps, aproximadamente 24 minutos.<br/>Um tempo de sobrecarga é necessário para Site Recovery a escala automática, aproximadamente 20-30 minutos. |
|A região de origem tem uma VM com disco Premium de 1 TB.<br/>Somente 20 GB de dados são usados e o restante do disco está vazio.<br/>O tipo de disco é Premium com taxa de transferência de 200 MBps.<br/>Os dados iniciais no disco imediatamente após o failover foram 15 GB. Houve uma alteração de dados de 5 GB após o failover. O total de dados preenchidos é, portanto, 20 GB| Tempo aproximado: 30-45 minutos.<br/>Como os dados populados no disco são inferiores a 10% do tamanho do disco, executamos uma replicação inicial completa.<br/>A velocidade de transferência é aproximadamente 16% da taxa de transferência ou 32MBps. Portanto, o tempo de transferência para aplicar alterações de 20 GB com 20 GB/32 MBps, aproximadamente 11 minutos.<br/>Um tempo de sobrecarga é necessário para Site Recovery a escala automática, aproximadamente 20-30 minutos |

Quando a VM é protegida novamente após o failback para a região primária (ou seja, se a VM for protegida novamente da região primária para a região de DR), a VM de destino e as NICs associadas serão excluídas.

Quando a VM é protegida novamente da região de DR para a região primária, não excluímos a VM primária aluna e as NICs associadas.

## <a name="next-steps"></a>Próximas etapas

Depois que a VM estiver protegida, você poderá iniciar um failover. O failover desliga a VM na região secundária e cria e inicializa a VM na região primária, com um breve tempo de inatividade durante esse processo. Recomendamos que você escolha um horário apropriado para esse processo e que execute um failover de teste antes de iniciar um failover completo para o site primário. [Saiba mais](site-recovery-failover.md) sobre o failover de Azure site Recovery.
