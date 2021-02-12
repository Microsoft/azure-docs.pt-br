---
title: Perguntas frequentes – Fazer backup dos bancos de dados do SAP HANA em VMs do Azure
description: Neste artigo, descubra respostas a perguntas comuns sobre como fazer backup dos bancos de dados SAP HANA usando o serviço de Backup do Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: bf662600bafcd18b00c8f8d3b673fc3f9c110aca
ms.sourcegitcommit: 1d366d72357db47feaea20c54004dc4467391364
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95400200"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Perguntas frequentes – Fazer backup de bancos de dados SAP HANA em VMs do Azure

Este artigo responde perguntas comuns sobre como fazer backup dos bancos de dados SAP HANA usando o serviço de Backup do Azure.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Há suporte para quantos backups completos por dia?

Damos suporte a apenas um backup completo por dia. Você não pode ter backup diferencial e backup completo disparado no mesmo dia.

### <a name="do-successful-backup-jobs-create-alerts"></a>Trabalhos de backup bem-sucedidos criam alertas?

Não. Trabalhos de backup bem-sucedidos não geram alertas. Os alertas são enviados somente para trabalhos de backup com falha. O comportamento detalhado para alertas do portal é documentado [aqui](./backup-azure-monitoring-built-in-monitor.md). No entanto, se você estiver interessado em ter alertas mesmo para trabalhos bem-sucedidos, poderá usar [Azure monitor](./backup-azure-monitoring-use-azuremonitor.md).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Posso ver trabalhos de backup agendados no menu Trabalhos de Backup?

O menu trabalho de backup mostrará apenas os trabalhos de backup sob demanda. Para trabalhos agendados, use o [Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

### <a name="are-future-databases-automatically-added-for-backup"></a>Bancos de dados futuros são adicionados automaticamente ao backup?

Não, atualmente não há suporte para isso.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Se eu excluir um banco de dados de uma instância, o que acontecerá com os backups?

Se um backup for removido de uma instância SAP HANA, os backups do banco de dados ainda serão tentados. Isso implica que o banco de dados excluído começa a ser exibido como não íntegro em **Itens de Backup** e ainda está protegido.
A maneira correta de parar a proteção desse banco de dados é executar **Parar o Backup com os dados de exclusão** nesse banco de dados.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Se eu alterar o nome do banco de dados depois que ele tiver sido protegido, qual será o comportamento?

Um banco de dados renomeado é tratado como um novo. Portanto, o serviço tratará essa situação como se o banco de dados não fosse encontrado e irá falhar os backups. O banco de dados renomeado será exibido como um novo e deverá ser configurado para ter proteção.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Quais são os pré-requisitos para fazer backup de bancos de dados SAP HANA em uma VM do Azure?

Consulte as seções [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [O que o script de pré-registro faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>Quais permissões devem ser definidas para que o Azure possa fazer backup de bancos de dados SAP HANA?

A execução do script de pré-registro define as permissões necessárias para permitir que o Azure faça backup dos bancos de dados SAP HANA. Você pode encontrar mais informações sobre o que o script de pré-registro faz [aqui](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>Os backups funcionarão após a migração do SAP HANA de SDC para MDC?

Consulte [esta seção](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) do guia de solução de problemas.

### <a name="what-should-be-done-while-upgrading-within-the-same-version"></a>O que deve ser feito durante a atualização na mesma versão?

Consulte [esta seção](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm) no guia de solução de problemas.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>O Backup HANA do Azure pode ser configurado em um IP virtual (balanceador de carga) e não em uma máquina virtual?

No momento, não temos a capacidade de configurar a solução apenas em um IP virtual. Precisamos de uma máquina virtual para executar a solução.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Como posso migrar um backup sob demanda para o sistema de arquivos local em vez do cofre do Azure?

1. Aguarde até que o backup em execução no momento seja concluído no banco de dados desejado (verifique o estúdio para conclusão).
1. Desabilite os backups de log e defina o backup do catálogo como **Filesystem** para o BD desejado usando as seguintes etapas:
1. Clique duas vezes em **SYSTEMDB** -> **configuração** -> **Selecionar Banco de Dados** -> **Filtrar (log)**
    1. Defina enable_auto_log_backup como **não**.
    1. Defina catalog_backup_using_backint como **false**.
1. Faça um backup sob demanda (completo/diferencial/incremental) no banco de dados desejado e aguarde a conclusão do backup e do catálogo.
1. Se você também quiser mover os backups de log para o sistema de arquivos, defina enable_auto_log_backup como **Sim**.
1. Reverta para as configurações anteriores para permitir que os backups fluam para o cofre do Azure:
    1. Defina enable_auto_log_backup como **Sim**.
    1. Defina catalog_backup_using_backint como **true**.

>[!NOTE]
>Mover os backups para o sistema de arquivos local e alternar novamente para o cofre do Azure pode causar uma interrupção da cadeia de logs dos backups de log no cofre. Isso irá disparar um backup completo, que, depois de concluído com êxito, iniciará o backup dos logs.

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>Como posso usar SAP HANA backup com minha configuração de replicação do HANA?

Atualmente, o backup do Azure não tem a capacidade de entender uma configuração HSR. Isso significa que os nós primários e secundários do HSR serão tratados como duas VMs individuais não relacionadas. Primeiro, você precisará configurar o backup no nó primário. Quando ocorre um failover, o backup deve ser configurado no nó secundário (que agora se torna o nó primário). Não há nenhum failover automático de backup para o outro nó.

Para fazer backup de dados do nó ativo (primário) em qualquer momento determinado, você pode alternar a **proteção** para o nó secundário, que agora se tornará o primário após o failover.

Para executar essa **proteção de comutador**, siga estas etapas:

- [Interromper a proteção](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (com reter dados) no primário
- Executar o [script de pré-registro](https://aka.ms/scriptforpermsonhana) no nó secundário
- [Descobrir os bancos de dados](tutorial-backup-sap-hana-db.md#discover-the-databases) no nó secundário e [configurar os backups](tutorial-backup-sap-hana-db.md#configure-backup) neles

Essas etapas devem ser executadas manualmente após cada failover. Você pode executar essas etapas por meio de linha de comando/HTTP REST, além da portal do Azure. Para automatizar essas etapas, você pode usar um runbook do Azure.

Aqui está um exemplo detalhado de como a **proteção de comutação** deve ser executada:

Neste exemplo, você tem dois nós – nó 1 (primário) e nó 2 (secundários) na configuração do HSR.  Os backups são configurados no nó 1. Conforme mencionado acima, não tente configurar backups no nó 2.

Quando o primeiro failover ocorre, o nó 2 torna-se o primário. E, em seguida,

1. Interrompa a proteção do nó 1 (primário anterior) com a opção manter dados.
1. Execute o script de pré-registro no nó 2 (que agora é o primário).
1. Descubra bancos de dados no nó 2, atribua a política de backup e configure backups.

Em seguida, um primeiro backup completo é disparado no nó 2 e, após a conclusão, os backups de log são iniciados.

Quando ocorrer o próximo failover, o nó 1 se tornará primário novamente e o nó 2 se tornará secundário. Agora, repita o processo:

1. Interrompa a proteção do nó 2 com a opção manter dados.
1. Executar o script de pré-registro no nó 1 (que se tornou o primário novamente)
1. Em seguida, [retome o backup](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) no nó 1 com a política necessária (já que os backups foram interrompidos anteriormente no nó 1).

Em seguida, o backup completo será disparado novamente no nó 1 e, depois que isso for concluído, os backups de log serão iniciados.

## <a name="restore"></a>Restaurar

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Por que não consigo ver o sistema HANA para o qual eu quero que meu banco de dados seja restaurado?

Verifique se todos os pré-requisitos para a restauração direcionar a instância SAP HANA são atendidos. Para obter mais informações, confira [Pré-requisitos – Restaurar bancos de dados SAP HANA na VM do Azure](./sap-hana-db-restore.md#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Por que a restauração Substituir BD está falhando para o meu banco de dados?

Verifique se a opção **Forçar Substituição** está selecionada durante a restauração.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Por que eu vejo o erro “Os sistemas de origem e destino para restauração são incompatíveis”?

Consulte a Nota [1642148](https://launchpad.support.sap.com/#/notes/1642148) do SAP HANA para ver quais tipos de restauração têm suporte no momento.

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>Posso usar um backup de um banco de dados em execução no SLES para restaurar para um sistema de do RHEL HANA ou vice-versa?

Sim, você pode usar backups de streaming disparados em um banco de dados HANA em execução no SLES para restaurá-lo para um sistema do RHEL HANA e vice-versa. Ou seja, a restauração entre OS sistemas operacionais é possível usando backups de streaming. No entanto, você precisará garantir que o sistema HANA que deseja restaurar e o sistema HANA usado para restauração, sejam compatíveis para restauração de acordo com o SAP. Consulte SAP HANA observação [1642148](https://launchpad.support.sap.com/#/notes/1642148) para ver quais tipos de restauração são compatíveis.

## <a name="policy"></a>Política

### <a name="different-options-available-during-creation-of-a-new-policy-for-sap-hana-backup"></a>Opções diferentes disponíveis durante a criação de uma nova política para SAP HANA backup

Antes de criar uma política, você deve estar claro sobre os requisitos de RPO e RTO e suas implicações de custo relevantes.

RPO (objetivo de ponto de recuperação) indica a quantidade de perda de dados aceitável para o usuário/cliente. Isso é determinado pela frequência de backup de log. Backups de log mais frequentes indicam RPO inferior e o valor mínimo com suporte pelo serviço de backup do Azure é 15 minutos. Portanto, a frequência de backup de log pode ser de 15 minutos ou mais.

RTO (Recovery-Time-Objection) indica a rapidez com que os dados devem ser restaurados para o último ponto no tempo disponível após um cenário de perda de dados. Isso depende da estratégia de recuperação empregada pelo HANA, que geralmente depende de quantos arquivos são necessários para a restauração. Isso também tem implicações de custo, e a tabela a seguir deve ajudar a compreender todos os cenários e suas implicações.

|Política de backup  |RTO  |Custo  |
|---------|---------|---------|
|Diário completo + logs     |   Mais rápido, já que precisamos apenas de uma cópia completa + logs necessários para a restauração pontual      |    A opção Costliest, pois uma cópia completa é realizada diariamente e, portanto, mais e mais dados são acumulados no back-end até o tempo de retenção   |
|Semanal Total + registros diários +     |   Mais lento do que a opção acima, mas mais rápido do que a próxima opção, pois exigimos uma cópia completa + um dos logs diferenciais + um para a restauração pontual      |    Opção menos cara, pois o diferencial diário é normalmente menor do que o completo e uma cópia completa é realizada apenas uma vez por semana      |
|Semanal completo + diário incremental + logs     |  Mais lento, pois precisamos de uma cópia completa + ' n' incrementais + logs para recuperação pontual       |     Opção menos dispendiosa, pois o incremental diário será menor do que o diferencial e uma cópia completa só será feita semanalmente    |

> [!NOTE]
> As opções acima são as mais comuns, mas não as únicas opções. Por exemplo, você pode ter um backup completo semanal + diferencial duas vezes por semana + logs.

Portanto, você pode selecionar a variante de política com base nos objetivos de RPO e RTO e nas considerações de custo.

### <a name="impact-of-modifying-a-policy"></a>Impacto da modificação de uma política

Alguns princípios devem ser mantidos em mente ao determinar o impacto de mudar a política de um item de backup da política 1 (P1) para a política 2 (P2) ou da política de edição 1 (P1).

- Todas as alterações também são aplicadas retroativamente. A política de backup mais recente também é aplicada nos pontos de recuperação feitos anteriormente. Por exemplo, suponha que a retenção completa diária seja de 30 dias e 10 pontos de recuperação foram feitos de acordo com a política ativa no momento. Se a retenção diária completa for alterada para 10 dias, a hora de expiração do ponto anterior também será recalculada como hora de início + 10 dias e excluída se tiver expirado.
- O escopo da alteração também inclui o dia do backup, o tipo de backup juntamente com a retenção. Por exemplo: se uma política for alterada de diário total para semanal completa em domingos, todos os inteiros anteriores que não estão nos domingos serão marcados para exclusão.
- Um pai não é excluído até que o filho esteja ativo/não expirado. Cada tipo de backup tem um tempo de expiração de acordo com a política ativa no momento. Mas um tipo de backup completo é considerado como pai para ' diferenciais ' posteriores, ' incrementais ' e ' logs '. Um ' diferencial ' e um ' log ' não são pais para outras pessoas. Um ' incremental ' pode ser um pai para ' incremental ' subsequente. Mesmo que um ' pai ' esteja marcado para exclusão, ele não será excluído de fato se os ' diferenciais ' ou ' logs ' filho não estiverem expirados. Por exemplo, se uma política for alterada de diário total para semanal completa em domingos, todos os inteiros anteriores que não estão nos domingos serão marcados para exclusão. Mas eles não são realmente excluídos até que os logs que foram feitos diariamente antes tenham expirado. Em outras palavras, elas são mantidas de acordo com a duração do log mais recente. Depois que os logs expirarem, os logs e esses inteiros serão excluídos.

Com esses princípios, você pode ler a tabela a seguir para entender as implicações de uma alteração de política.

|Política antiga/nova política  |Diários completos + logs  | Completos semanais + registros diferenciais diários + logs  |Completos semanais + incrementos diários + logs  |
|---------|---------|---------|---------|
|Diários completos + logs     |   -      |    Os preenchimentos anteriores que não estão no mesmo dia da semana são marcados para exclusão, mas mantidos até o período de retenção do log     |    Os preenchimentos anteriores que não estão no mesmo dia da semana são marcados para exclusão, mas mantidos até o período de retenção do log     |
|Completos semanais + registros diferenciais diários + logs     |   A retenção completa semanal anterior é recalculada de acordo com a política mais recente. Os diferenciais anteriores são excluídos imediatamente      |    -     |    Os diferenciais anteriores são excluídos imediatamente     |
|Completos semanais + incrementos diários + logs     |     A retenção completa semanal anterior é recalculada de acordo com a política mais recente. Os incrementos anteriores são excluídos imediatamente    |     Os incrementos anteriores são excluídos imediatamente    |    -     |

## <a name="next-steps"></a>Próximas etapas

Saiba como [fazer backup de bancos de dados do SAP HANA](./backup-azure-sap-hana-database.md) em execução em VMs do Azure.
