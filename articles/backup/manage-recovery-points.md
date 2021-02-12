---
title: Gerenciar pontos de recuperação
description: Saiba como o serviço de backup do Azure gerencia pontos de recuperação para máquinas virtuais
ms.topic: conceptual
ms.date: 11/08/2020
ms.openlocfilehash: 256df693aba0f799c24bcba6defe846e5c37ccaa
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428558"
---
# <a name="manage-recovery-points"></a>Gerenciar pontos de recuperação

Este artigo descreve como a retenção funciona para máquinas virtuais. Sempre que os backups acontecem, os pontos de recuperação são criados a partir dos quais as operações de restauração podem ser executadas.

Para máquinas virtuais, o backup inicial é um backup completo e os backups subsequentes são backups incrementais.

## <a name="recovery-points-and-retention"></a>Pontos de recuperação e retenção

### <a name="scheduled-initial-and-incremental-backup"></a>Backup inicial e incremental agendado

Vamos usar um exemplo simplificado da máquina virtual *v1* com um disco de dados composto por quatro blocos: bloco 1, bloco 2, bloco 3 e bloco 4. Cada bloco tem 16 KB de tamanho.

![Máquina virtual com quatro blocos](./media/manage-recovery-points/four-blocks.png)

**Etapa 1 – backup inicial:** O backup inicial é um backup completo. Ele atua como uma linha de base na qual backups incrementais subsequentes são aplicados. Suponha que haja dados gravados no bloco 1 e no bloco 2 na VM de origem. Os mesmos dados serão replicados como D1 e D2 no armazenamento do cofre dos serviços de recuperação.

![O backup inicial é replicado](./media/manage-recovery-points/initial-backup.png)

**Etapa 2-backup incremental 1:** Considere que há novos dados adicionados ao bloco 3 da VM. Os mesmos dados serão replicados no próximo backup incremental e somente o bloco que for alterado será armazenado como D3.  Durante cada etapa, mesmo se 1 KB do bloco for alterado, o bloco inteiro de 16 KB será carregado no ponto de recuperação.

![Primeiro backup incremental](./media/manage-recovery-points/first-incremental-backup.png)

**Etapa 3 – backup incremental 2:**  Agora, considere que há alterações de dados no bloco 3 e no bloco 2 na VM de origem. Essas alterações serão replicadas no próximo backup incremental como D3 ' e D2 '.

![Segundo backup incremental](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>Backup sob demanda

Você pode optar por executar um backup sob demanda de uma VM a qualquer momento depois de configurar a proteção nela.

- O backup sob demanda será um backup completo se for disparado antes do primeiro backup inicial agendado.
- Se o backup inicial for concluído e um backup sob demanda for disparado, ele será um backup incremental.
- O tempo de retenção dos pontos de recuperação criados para um backup sob demanda é o valor de retenção que você especifica ao disparar o backup.

### <a name="storage-cost"></a>Custo de armazenamento

O **ponto de recuperação** criado para o backup inicial contém todos os blocos que têm os dados. Os pontos de recuperação incrementais subsequentes consistem apenas nos blocos que alteraram os dados. Os custos de armazenamento correspondem à soma de todos os blocos que abrangem todos os pontos de recuperação.

Vamos usar o exemplo acima para entender o custo de armazenamento após cada etapa:

|Etapa  |Tipo de backup  |Blocos alterados  |Tipo de armazenamento |
|------|---------|---------|---------|
|1     |     Backup inicial    | Bloco 1, bloco 2        |    Correspondente ao ponto de recuperação 1 (D1 + D2)     |
|2     |  Backup incremental 1       |  Bloco 3       |   Correspondente ao ponto de recuperação 1 (D1 + D2) + ponto de recuperação 2 (D3)      |
|3     |    Backup incremental 2     |    Bloco 2, bloco 3     |   Correspondente ao ponto de recuperação 1 (D1 + D2) + ponto de recuperação 2 (D3) + ponto de recuperação 3 (D2 ' + D3 ')      |

### <a name="recovery-point-expiration"></a>Expiração do ponto de recuperação

Cada ponto de recuperação tem uma duração de retenção, conforme especificado na política de backup. A limpeza ocorre em intervalos regulares e todos os pontos de recuperação expirados são limpos.

Quando o ponto de recuperação expira, ele é excluído ou mesclado.

### <a name="case-1-initial-recovery-point-expires"></a>Caso 1: o ponto de recuperação inicial expira

Quando o ponto de recuperação inicial expira, ele é mesclado com o próximo ponto de recuperação incremental. Todos os blocos de dados substituídos no ponto de recuperação incremental são excluídos e o restante é mesclado. Em seguida, o backup incremental se torna o backup completo inicial. Vamos examinar com um exemplo:

- O *ponto de recuperação 1* criado durante o backup inicial tem o backup completo da VM.
- Quando o *ponto de recuperação 1* expira, o *ponto de recuperação 2* é o próximo backup completo.
- O bloqueio D1 é mesclado com o *ponto de recuperação 2* e D2 é excluído, pois os dados no bloco 2 são substituídos no *ponto de recuperação 2*. Essa alteração é capturada como bloco D2 '.
- O bloco D1 é retido nos pontos de recuperação consecutivos como estão, até que haja alterações feitas nele antes do próximo backup.

![Primeiro caso](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>Caso 2: o ponto de recuperação incremental entre-entre expira

- Se o *ponto de recuperação 2* expirar antes do *ponto de recuperação 1* , os dados do *ponto de recuperação 2* serão mesclados com o próximo ponto de recuperação disponível: *ponto de recuperação 3*. Portanto, o bloco D3 é mesclado com o *ponto de recuperação 3*.
- O *ponto de recuperação 1* ainda é o backup completo com o bloco D1 e D2.

![Segundo caso](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>Caso 3: o ponto de recuperação sob demanda expira

Neste exemplo, uma política de agendamento (backup diário) está agendada para ser executada com período de retenção de *n* dias.  Se um backup sob demanda for disparado no quarto dia antes do próximo backup agendado e seu período de retenção for especificado como 10 dias, ele ainda será um backup incremental. Um ponto de recuperação ( *RP1 sob demanda* ) será criado após o *ponto de recuperação 3* e antes do *ponto de recuperação 4*.  No final do dia 14, o ponto de recuperação sob demanda ( *RP1 sob demanda* ) fica expirado e será mesclado com o próximo ponto de recuperação disponível. Os blocos de dados que ainda estão presentes no servidor são mesclados, enquanto os blocos de dados alterados (substituídos ou excluídos) são excluídos do ponto de recuperação expirado.

![Terceiro caso](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>Impacto da alteração de política em pontos de recuperação

Quando uma política é modificada, ela é aplicada a pontos de recuperação novos e existentes. Para obter mais informações, consulte [impacto da alteração de política em pontos de recuperação](backup-architecture.md#impact-of-policy-change-on-recovery-points).

### <a name="impact-of-stop-protection-on-recovery-points"></a>Impacto da interrupção da proteção nos pontos de recuperação

Há duas maneiras de interromper a proteção de uma VM:

- **Interrompa a proteção e exclua os dados de backup.** Esta opção impedirá que todos os trabalhos de backup futuros protejam sua VM e exclua todos os pontos de recuperação. Se a [exclusão reversível](backup-azure-security-feature-cloud.md) estiver habilitada, os dados excluídos serão mantidos por 14 dias. Os encargos não são cobrados por itens no estado de exclusão reversível. Os dados podem ser reexcluídos dentro do período de 14 dias. Se a exclusão reversível não estiver habilitada, os dados serão limpos imediatamente e você não poderá restaurar a VM ou usar a opção **retomar backup** .
- **Interromper a proteção e manter os dados de backup.** Esta opção impedirá que todos os trabalhos de backup futuros protejam sua VM. No entanto, o serviço de backup do Azure sempre manterá os pontos de recuperação que foram armazenados em backup. Você precisará pagar para manter os pontos de recuperação no cofre (consulte [preços de backup do Azure](https://azure.microsoft.com/pricing/details/backup/) para obter detalhes). Você poderá restaurar a VM, se necessário. Se você decidir retomar a proteção da VM, poderá usar a opção **retomar backup** . Depois de retomar o backup, as regras de retenção serão aplicadas aos pontos de expiração. Você também pode excluir os dados de backup usando a opção  **excluir dados de backup** .

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>Impacto da exclusão de uma VM sem interromper a proteção

Excluir uma VM sem interromper a proteção tem impacto sobre os pontos de recuperação e é um cenário indesejável. O ideal é que os backups sejam interrompidos antes da exclusão da máquina virtual. Como o recurso não existe, os backups agendados falharão com o [erro VMNotFoundV2](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found). Os pontos de recuperação serão limpos periodicamente de acordo com a política de retenção, mas a última cópia da máquina virtual permanecerá para sempre e você será cobrado de acordo. Dependendo do seu cenário, você tem as duas opções a seguir:

- **Opção 1:** Restaure a VM usando qualquer um dos pontos de recuperação. Se você quiser recuperar a VM excluída, restaure usando o mesmo nome e no mesmo grupo de recursos. Se você proteger a VM restaurada no mesmo cofre, os pontos de recuperação existentes serão automaticamente anexados.
- **Opção 2:** Vá para o cofre dos serviços de recuperação e interrompa a proteção com excluir dados.

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>Impacto de pontos de recuperação expirados para itens no estado de exclusão reversível

Se a [exclusão reversível](backup-azure-security-feature-cloud.md) estiver habilitada para o cofre dos serviços de recuperação, o ponto de recuperação expirado permanecerá no estado de exclusão reversível e não será limpo. Não há encargos incorridos quando um ponto de recuperação está no estado excluído de maneira reversível.

### <a name="impact-of-churn-on-backup-performance"></a>Impacto da rotatividade no desempenho de backup

Suponha que o armazenamento total de uma VM seja de 8 TB e a variação seja de 5%. Em seguida, o armazenamento de backup incremental correspondente será de 5% de 8 TB que é de 0,4 TB. A rotatividade mais alta corresponde ao armazenamento de back-end maior para backups incrementais subsequentes. A rotatividade afeta o desempenho do backup. Maior a rotatividade, o processo de backup mais lento e maior o consumo do armazenamento de back-end.

Para entender como a rotatividade afeta o desempenho do backup, examine este cenário:

|Máquinas virtuais  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|Número de discos de dados    | 4 (A1, A2, A3, A4)        | 4 (B1, B2, B3, B4)        |  4 (C1, C2, C3, C4)       |
|Tamanho de cada disco   |      4 TB   | 4 TB        |  4 TB       |
|Variação de dados de backup    |   A1-4 TB      | B1-1 TB; B2-1 TB <br> B3-1 TB; B4-1 TB  |   C1-2 TB; C4-2 TB      |

O desempenho do backup estará na ordem VM2>VM3>VM1. O motivo para isso é que os dados com rotatividade são distribuídos entre os vários discos. Como o backup de discos ocorre em paralelo, o VM2 mostrará o melhor desempenho.

## <a name="next-steps"></a>Próximas etapas

- [Arquitetura e componentes de backup do Azure](backup-architecture.md)
