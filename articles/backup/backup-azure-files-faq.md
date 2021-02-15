---
title: Perguntas frequentes sobre fazer backup de Arquivos do Azure
description: Neste artigo, descubra respostas para perguntas comuns sobre como proteger seus compartilhamentos de arquivo do Azure com o serviço de Backup do Azure.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: e2b6afb25e189ee2848f25c0ba59d843baf37090
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940828"
---
# <a name="questions-about-backing-up-azure-files"></a>Perguntas sobre como fazer backup de Arquivos do Azure

Este artigo responde perguntas frequentes sobre como fazer backup de Arquivos do Azure. Em algumas das respostas, há links para artigos com informações abrangentes. Você também pode postar perguntas sobre o serviço de Backup do Azure na página [Perguntas e respostas da Microsoft para discussão](/answers/topics/azure-backup.html).

Para verificar rapidamente as seções neste artigo, use os links à direita, em **Neste artigo**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configurando o trabalho de backup para Arquivos do Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Por que não consigo ver algumas das minhas contas de armazenamento que desejo proteger e que contêm compartilhamentos de arquivo válidos do Azure?

Veja a [Matriz de Suporte para backup de compartilhamentos de arquivo do Azure](azure-file-share-support-matrix.md) para garantir que a conta de armazenamento pertença a um dos tipos de conta de armazenamento com suporte. Também é possível que a Conta de Armazenamento que você está procurando esteja já protegida ou registrada com outro Cofre. [Cancelar o registro da conta de armazenamento](manage-afs-backup.md#unregister-a-storage-account) do cofre para descobrir a Conta de Armazenamento em outros cofres para proteção.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Por que não vejo alguns dos meus compartilhamentos de arquivos do Azure na conta de armazenamento quando estou tentando configurar o backup?

Verifique se o compartilhamento de arquivos do Azure já está protegido no mesmo cofre dos Serviços de Recuperação ou se foi excluído recentemente.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Posso proteger compartilhamentos de arquivos conectados a um grupo de sincronização na Sincronização de arquivos do Azure?

Sim. A proteção dos Compartilhamentos de Arquivo do Azure conectados a Grupos de Sincronização está habilitada.

### <a name="when-trying-to-back-up-file-shares-i-selected-a-storage-account-to-discover-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Ao tentar fazer backup de compartilhamentos de arquivos, selecionei uma conta de armazenamento para descobrir os compartilhamentos de arquivos nele. No entanto, eu não os protegi. Como fazer para proteger esses compartilhamentos de arquivo com outro cofre?

Ao tentar fazer backup, a seleção de uma Conta de Armazenamento para ver os compartilhamentos de arquivo dentro dela gera o registro da Conta de Armazenamento no cofre em que isso foi feito. Se você optar por proteger os compartilhamentos de arquivos com um cofre diferente, [cancele o registro](manage-afs-backup.md#unregister-a-storage-account) da Conta de Armazenamento escolhida no cofre.

### <a name="why-cant-i-change-the-vault-to-configure-backup-for-the-file-share"></a>Por que não posso alterar o cofre para configurar o backup para o compartilhamento de arquivos?

Se a conta de armazenamento já estiver registrada com um cofre ou outros compartilhamentos de arquivos na conta de armazenamento forem protegidos usando um cofre, você não terá a opção de alterá-la. Todos os compartilhamentos de arquivos em uma conta de armazenamento podem ser protegidos somente pelo mesmo cofre. Se desejar alterar o cofre, você precisará [interromper a proteção de todos os compartilhamentos de arquivos na conta de armazenamento](manage-afs-backup.md#stop-protection-on-a-file-share) do cofre conectado, cancelar o [registro](manage-afs-backup.md#unregister-a-storage-account) da conta de armazenamento e, em seguida, escolher um cofre diferente para proteção.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>É possível alterar o Cofre em que faço backup dos meus compartilhamentos de arquivos?

Sim. No entanto, você precisará [parar a proteção no compartilhamento de arquivo](manage-afs-backup.md#stop-protection-on-a-file-share) do cofre conectado, [cancelar o registro](manage-afs-backup.md#unregister-a-storage-account) dessa Conta de Armazenamento e protegê-la em um cofre diferente.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Posso proteger dois compartilhamentos de arquivo diferentes da mesma conta de armazenamento em cofres diferentes?

Não. Os compartilhamentos de arquivos em uma mesma Conta de Armazenamento só podem ser protegidos pelo mesmo Cofre.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>O que devo fazer se meus backups começarem a falhar devido ao erro de limite máximo atingido?

Você pode ter até 200 instantâneos para um compartilhamento de arquivos a qualquer hora. O limite inclui instantâneos tirados pelo Backup do Azure conforme definido pela sua política. Se os backups começarem a falhar após atingir o limite, exclua os instantâneos Sob Demanda para ter êxito nos backups futuros.

## <a name="restore"></a>Restaurar

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Posso recuperar a partir de um compartilhamento de arquivos do Azure excluído?

Se o compartilhamento de arquivos estiver no estado de exclusão reversível, primeiro você precisará restaurar o compartilhamento de arquivos para executar a operação de restauração. A operação de restauração colocará o compartilhamento de arquivos no estado ativo, onde você poderá restaurar para qualquer ponto no tempo. Para saber como restaurar o compartilhamento de arquivos, visite [este link](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) ou consulte o [script para restaurar o compartilhamento de arquivos](./scripts/backup-powershell-script-undelete-file-share.md). Se o compartilhamento de arquivos for excluído permanentemente, você não poderá restaurar o conteúdo e os instantâneos.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Posso restaurar a partir de backups se parar a proteção em um compartilhamento de arquivos do Azure?

Sim. Caso tenha escolhido **Reter Dados de Backup**, ao parar a proteção, você poderá restaurar a partir de todos os pontos de restauração existentes.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>O que acontecerá se eu cancelar um trabalho de restauração em andamento?

Se um trabalho de restauração em andamento for cancelado, o processo de restauração será interrompido e todos os arquivos restaurados antes do cancelamento permanecerão no destino configurado (local original ou alternativo) sem nenhuma reversão.

## <a name="manage-backup"></a>Gerenciar backups

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Posso usar o PowerShell para configurar/gerenciar/restaurar backups de compartilhamentos de Arquivos do Azure?

Sim. Veja a documentação detalhada [aqui](backup-azure-afs-automation.md).

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Posso acessar os instantâneos tirados por Backups do Azure e montá-los?

Todos os instantâneos feitos pelo Backup do Azure podem ser acessados exibindo instantâneos no portal, no PowerShell ou na CLI. Para saber mais sobre instantâneos de compartilhamento de arquivo do Azure, confira [Visão geral dos instantâneos de compartilhamento dos Arquivos do Azure](../storage/files/storage-snapshots-files.md).

### <a name="what-happens-after-i-move-a-backed-up-file-share-to-a-different-subscription"></a>O que acontece depois de mover um compartilhamento de arquivos de backup para uma assinatura diferente?

Quando um compartilhamento de arquivos é movido para uma assinatura diferente, ele é considerado um novo compartilhamento de arquivos pelo backup do Azure. Estas são as etapas recomendadas:
 
Cenário: digamos que você tenha um compartilhamento de arquivos *FS1* na assinatura *S1* e ele seja protegido usando o cofre *v1* . Agora você deseja mover o compartilhamento de arquivos para a assinatura *S2*.
 
1.  Mova a conta de armazenamento e o compartilhamento de arquivos (FS1) desejados para a assinatura diferente (S2).
2.  No cofre v1, dispare a operação parar proteção com excluir dados para o FS1.
3.  Cancele o registro da conta de armazenamento que hospeda o FS1 do cofre v1.
4.  Reconfigure o backup para o FS1, agora movido para S2, com um cofre (v2) na assinatura S2. 
 
Observe que depois de reconfigurar o backup com o v2, os instantâneos que foram feitos com v1 não serão mais gerenciados pelo backup do Azure. Portanto, você precisará excluir esses instantâneos manualmente de acordo com suas necessidades.

### <a name="can-i-move-my-backed-up-file-share-to-a-different-resource-group"></a>Posso mover meu compartilhamento de arquivos de backup para um grupo de recursos diferente?
 
Sim, você pode mover o compartilhamento de arquivos de backup para um grupo de recursos diferente. No entanto, você precisará reconfigurar o backup para o compartilhamento de arquivos, pois ele será tratado como um novo recurso pelo backup do Azure. Além disso, os instantâneos criados antes da movimentação do grupo de recursos não serão mais gerenciados pelo backup do Azure. Portanto, você precisará excluir esses instantâneos manualmente de acordo com suas necessidades.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Qual é a retenção máxima que posso configurar para backups?

Veja a [matriz de suporte](azure-file-share-support-matrix.md) para obter detalhes sobre a retenção máxima. O Backup do Azure faz um cálculo em tempo real do número de instantâneos, quando você insere os valores de retenção ao configurar a política de backup. Assim que o número de instantâneos correspondentes aos valores de retenção definidos excede 200, o portal mostra um aviso solicitando que você ajuste os valores de retenção. Isso ocorre para que você não exceda o limite do número máximo de instantâneos com suporte dos Arquivos do Azure para qualquer compartilhamento de arquivo em qualquer ponto no tempo.

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>Qual é o impacto nos pontos de recuperação e instantâneos existentes quando modifico a política de Backup de um compartilhamento de arquivo do Azure, para mudar de "Política Diária" para "Política de GFS"?

Quando você modifica uma política Diária de backup para política de GFS (adicionando retenção semanal/mensal/anual), ocorre o comportamento a seguir:

- **Retenção**: Se você estiver adicionando retenção semanal/mensal/anual como parte da modificação de política, todos os pontos de recuperação futuros criados como parte do backup agendado são marcados de acordo com a nova política. Todos os pontos de recuperação existentes ainda são considerados como pontos de recuperação diários e, portanto, não são marcados como semanais/mensais/anuais.

- **Limpeza de instantâneos e pontos de recuperação**:

  - Se a retenção diária for estendida, a data de validade dos pontos de recuperação existentes é atualizada de acordo com o valor de retenção diário configurado na nova política.
  - Se a retenção diária for reduzida, os pontos de recuperação e os instantâneos existentes são marcados para exclusão no próximo trabalho de execução de limpeza, de acordo com o valor de retenção diário configurado na nova política, e depois excluídos.

Exemplo de como isso funciona:

#### <a name="existing-policy-p1"></a>Política existente [P1]

|Tempo de retenção |Agenda |Retenção  |
|---------|---------|---------|
|Diário    |    Todos os dias às 20:00    |  100 dias       |

#### <a name="new-policy-modified-p1"></a>Nova política [P1 modificada]

| Tempo de retenção | Agenda                       | Retenção |
| -------------- | ------------------------------ | --------- |
| Diário          | Todos os dias às 21:00              | 50 dias   |
| Semanal         | No domingo às 21:00              | 3 semanas   |
| Mensal        | Na última segunda-feira às 21:00         | 1 mês   |
| Anual         | Em janeiro, no terceiro domingo às 21:00 | 4 anos   |

#### <a name="impact"></a>Impacto

1. A data de validade dos pontos de recuperação existentes é ajustada de acordo com o valor de retenção diário da nova política, ou seja, 50 dias. Portanto, qualquer ponto de recuperação com mais de 50 dias é marcado para exclusão.

2. Os pontos de recuperação existentes não são marcados como semanais/mensais/anuais com base na nova política.

3. Todos os backups futuros são acionados de acordo com o novo agendamento, ou seja, às 21:00.

4. A data de validade de todos os pontos de recuperação futuros é alinhada com a nova política.

>[!NOTE]
>As alterações da política afetam apenas os pontos de recuperação criados como parte da execução do trabalho de backup agendado. Para backups sob demanda, a retenção é determinada pelo valor **Reter até** especificado no momento do backup.

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>Qual é o impacto nos pontos de recuperação existentes quando eu modifico uma política de GFS existente?

Quando uma nova política é aplicada nos compartilhamentos de arquivo, todos os backups agendados futuros são feitos de acordo com o agendamento configurado na política modificada.  A retenção de todos os pontos de recuperação existentes é alinhada de acordo com os novos valores de retenção configurados. Portanto, se a retenção for estendida, os pontos de recuperação existentes são marcados para serem retidos de acordo com a nova política. Se a retenção for reduzida, eles são marcados para remoção no próximo trabalho de limpeza e depois excluídos.

Exemplo de como isso funciona:

#### <a name="existing-policy-p2"></a>Política existente [P2]

| Tempo de retenção | Agenda           | Retenção |
| -------------- | ------------------ | --------- |
| Diário          | Todos os dias às 20:00 | 50 dias   |
| Semanal         | Na segunda-feira às 20:00  | 3 semanas   |

#### <a name="new-policy-modified-p2"></a>Nova política [P2 modificada]

| Tempo de retenção | Agenda               | Retenção |
| -------------- | ---------------------- | --------- |
| Diário          | Todos os dias às 21:00     | 10 dias   |
| Semanal         | Na segunda-feira às 21:00      | 2 semanas   |
| Mensal        | Na última segunda-feira às 21:00 | 2 meses  |

#### <a name="impact-of-change"></a>Impacto da alteração

1. A data de validade dos pontos de recuperação existentes é alinhada de acordo com o valor de retenção diário, ou seja, 10 dias. Portanto, qualquer ponto de recuperação diário com mais de 10 dias será excluído.

2. A data de validade dos pontos de recuperação semanais existentes será alinhada de acordo com o novo valor de retenção semanal, ou seja, duas semanas. Portanto, qualquer ponto de recuperação semanal com mais de duas semanas será excluído.

3. Os pontos de recuperação mensais só serão criados como parte de backups futuros com base na nova configuração de política.

4. A data de validade de todos os pontos de recuperação futuros é alinhada com a nova política.

>[!NOTE]
>As alterações da política afetam apenas os pontos de recuperação criados como parte do backup agendado. Para backups sob demanda, a retenção é determinada pelo valor **Reter até** especificado no momento do backup.

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas ao fazer backup de compartilhamentos de arquivos do Azure](troubleshoot-azure-files.md)
