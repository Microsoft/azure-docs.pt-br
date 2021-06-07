---
title: Monitorar o Azure Monitor com o Backup do Azure
description: Monitore as cargas de trabalho de Backup do Azure e crie alertas personalizados usando o Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 1800771bfff0afbcec8440383536734246ea8f5c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580742"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitorar em escala usando o Azure Monitor

O Backup do Azure oferece [funcionalidades internas de monitoramento e alertas](backup-azure-monitoring-built-in-monitor.md) em um cofre dos Serviços de Recuperação. Essas funcionalidades estão disponíveis sem nenhuma infraestrutura de gerenciamento adicional. Mas esse serviço interno é limitado nos seguintes cenários:

- Se você monitorar dados de vários cofres dos Serviços de Recuperação entre assinaturas
- Se o canal de notificação preferencial *não* for email
- Se os usuários quiserem alertas para mais cenários
- Se você quiser exibir informações de um componente local, como o System Center Data Protection Manager no Azure, que o portal não mostra em [**Trabalhos de Backup**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) ou [**Alertas de Backup**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Como usar o workspace do Log Analytics

### <a name="create-alerts-by-using-log-analytics"></a>Criar alertas usando o Log Analytics

No Azure Monitor, você pode criar seus alertas em um workspace do Log Analytics. No workspace, você usa *Grupos de ações do Azure* para selecionar o mecanismo de notificação preferencial.

> [!IMPORTANT]
> Para obter informações sobre o custo de criação dessa consulta, confira [Preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Abra a seção **Logs** do workspace do Log Analytics e crie uma consulta para seus logs. Quando você seleciona **Nova Regra de Alerta**, a página de criação de alertas do Azure Monitor abre, conforme mostrado na imagem a seguir.

![Criar um alerta no workspace do Log Analytics](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Aqui, o recurso já está marcado como o workspace do Log Analytics e a integração do grupo de ações é fornecida.

![A página de criação de alertas do Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Condição de alerta

A característica de definição de um alerta é sua condição de disparo. Selecione **Condição** para carregar automaticamente a consulta Kusto na página **Logs**, conforme mostrado na imagem a seguir. Aqui você pode editar a condição para atender às suas necessidades. Para obter mais informações, confira [exemplo de consultas do Kusto](#sample-kusto-queries).

![Como configurar uma condição de alerta](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Se necessário, você pode editar a consulta do Kusto. Escolha um limite, um período e uma frequência. O limite determina quando o alerta será gerado. O período é a janela de tempo em que a consulta é executada. Por exemplo, se o limite for maior que 0, o período será de 5 minutos e a frequência será de 5 minutos, então a regra executará a consulta a cada 5 minutos, examinando os 5 minutos anteriores. Se o número de resultados for maior que 0, você será notificado pelo grupo de ações selecionado.

> [!NOTE]
> Para executar a regra de alerta uma vez por dia, em todos os eventos/logs que foram criados no dia determinado, altere o valor de ' Period ' e ' Frequency ' para 1440, ou seja, 24 horas.

#### <a name="alert-action-groups"></a>Alertar grupos de ações

Use um grupo de ações para especificar um canal de notificação. Para ver os mecanismos de notificação disponíveis, em **Grupos de ações**, selecione **Criar**.

![Mecanismos de notificação disponíveis na janela "Adicionar grupo de ações"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Você pode atender a todos os requisitos de alerta e monitoramento do Log Analytics sozinho ou pode usar o Log Analytics para complementar as notificações internas.

Para obter mais informações, confira [Criar, exibir e gerenciar alertas de log usando o Azure Monitor](../azure-monitor/alerts/alerts-log.md) e [Criar e gerenciar grupos de ações no portal do Azure](../azure-monitor/alerts/action-groups.md).

### <a name="sample-kusto-queries"></a>Consultas de exemplo do Kusto

Os grafos padrão fornecem a você consultas do Kusto para cenários básicos nos quais você pode criar alertas. Você também pode modificar as consultas para obter os dados sobre os quais deseja ser alertado. Cole as seguintes consultas de exemplo do Kusto na página **Logs** e, em seguida, crie alertas nas consultas:

- Todos os trabalhos de backup bem-sucedidos

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- Todos os trabalhos de backup com falha

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- Todos os trabalhos de backup de VM do Azure bem-sucedidos

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Todos os trabalhos de backup de log de transações SQL bem-sucedidos

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Todos os trabalhos do Agente do Backup do Azure bem-sucedidos

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Armazenamento de backup consumido por item de backup

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs
    | sort by StorageConsumedInMBs desc
    ````

### <a name="diagnostic-data-update-frequency"></a>Frequência de atualização de dados de diagnóstico

Os dados de diagnóstico do cofre são enviados para o workspace do Log Analytics com um pouco de atraso. Cada evento chega ao workspace do Log Analytics *de 20 a 30 minutos* após ele ser enviado por push do cofre dos Serviços de Recuperação. Aqui estão mais detalhes sobre o atraso:

- Em todas as soluções, os alertas internos do serviço de backup são enviados por push assim que são criados. Eles geralmente aparecem no workspace do Log Analytics após 20 a 30 minutos.
- Em todas as soluções, os trabalhos de backup sob demanda e os trabalhos de restauração são enviados por push assim que são *concluídos*.
- Para todas as soluções, exceto backup do SQL, os trabalhos de backup agendados são enviados por push assim que são *concluídos*.
- Para o backup do SQL, como os backups de log podem ocorrer a cada 15 minutos, as informações para todos os trabalhos de backup agendados concluídos, incluindo logs, são colocadas em lote e enviadas por push a cada 6 horas.
- Em todas as soluções, outras informações, como o item de backup, a política, os pontos de recuperação, o armazenamento e assim por diante, são enviadas por push pelo menos *uma vez por dia.*
- Uma alteração na configuração de backup (como alteração de política ou edição de política) dispara um envio por push de todas as informações de backup relacionadas.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Como usar os logs de atividades do cofre dos Serviços de Recuperação

> [!CAUTION]
> As etapas a seguir se aplicam somente a *backups de VM do Azure.* Você não pode usar estas etapas para soluções como o Agente do Backup do Azure, os backups SQL no Azure ou os Arquivos do Azure.

Você também pode usar os logs de atividades para receber notificações de eventos, por exemplo, de êxito no backup. Para começar, siga estas etapas:

1. Entre no Portal do Azure.
2. Abra o cofre dos Serviços de Recuperação relevante.
3. Nas propriedades do cofre, abra a seção **Log de atividades**.

Para identificar o log apropriado e criar um alerta:

1. Verifique se você está recebendo logs de atividades para backups bem-sucedidos aplicando os filtros mostrados na imagem a seguir. Altere o valor **Timespan** conforme necessário para ver os registros.

   ![Como filtrar para localizar logs de atividades para backups de VM do Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. Selecione o nome da operação para ver os detalhes relevantes.
3. Selecione **Nova regra de alerta** para abrir a página **Criar regra**.
4. Crie um alerta seguindo as etapas em [Criar, exibir e gerenciar alertas do log de atividades usando o Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md).

   ![Nova regra de alerta](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Aqui, o recurso é o cofre dos Serviços de Recuperação em si. Repita as mesmas etapas para todos os cofres nos quais você deseja ser notificado por meio dos logs de atividades. A condição não terá limite, período nem frequência, pois esse alerta se baseia em eventos. Assim que o log de atividades relevante for gerado, o alerta será acionado.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Como usar o Log Analytics para monitorar em escala

Você pode ver todos os alertas criados dos logs de atividades e dos workspaces do Log Analytics no Azure Monitor. Basta abrir o painel **Alertas** à esquerda.

Embora você possa obter notificações por meio de logs de atividades, é altamente recomendável usar o Log Analytics em vez dos logs de atividades para monitoramento em escala. Veja por quê:

- **Cenários limitados**: as notificações pelos logs de atividades se aplicam somente aos backups de VM do Azure. As notificações devem ser configuradas para cada cofre dos Serviços de Recuperação.
- **Ajuste de definição**: a atividade de backup agendada não se ajusta à definição mais recente dos logs de atividades. Em vez disso, ela se alinha aos [logs de recursos](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Esse alinhamento causa efeitos inesperados quando os dados que fluem pelo canal do log de atividades são alterados.
- **Problemas com o canal do log de atividades**: nos cofres dos Serviços de Recuperação, os logs de atividades que são enviados do Backup do Azure seguem um novo modelo. Essa alteração afeta a geração de logs de atividades no Azure Government, no Azure Alemanha e no Azure China 21Vianet. Se os usuários desses serviços de nuvem criarem ou configurarem alertas dos logs de atividades no Azure Monitor, os alertas não serão disparados. Além disso, em todas as regiões públicas do Azure, se um usuário [coletar logs de atividades dos Serviços de Recuperação em um workspace do Log Analytics](../azure-monitor/essentials/activity-log.md), esses logs também não serão exibidos.

Use um workspace do Log Analytics para monitoramento e alertas em escala para todas as suas cargas de trabalho que são protegidas pelo Backup do Azure.

## <a name="next-steps"></a>Próximas etapas

Para criar consultas personalizadas, confira [Modelo de dados do Log Analytics](backup-azure-reports-data-model.md).
