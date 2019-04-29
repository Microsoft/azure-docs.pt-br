---
title: 'Serviço de Backup do Azure: Monitorar o Backup do Azure com o Azure Monitor'
description: Monitorar cargas de trabalho de Backup do Azure e criar alertas personalizados usando o Azure Monitor
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics; Backup do Azure; Alertas; Configurações de diagnóstico; Grupos de ação
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 15bb64917fa58ba2d13c6f372640957508ab29c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60700082"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>Monitoramento em escala usando o Azure Monitor

O [internos de monitoramento e alerta artigo](backup-azure-monitoring-built-in-monitor.md) listado o monitoramento e alertas sobre a capacidade em um único Cofre de serviços de recuperação e que é oferecido sem nenhuma infraestrutura de gerenciamento adicionais. No entanto, o serviço interno é limitado nos cenários a seguir.

- Dados de monitoramento de vários cofres RS entre assinaturas
- Se o email não é o canal de notificação preferencial
- Se os usuários desejam receber alertas para mais cenários
- Se você deseja exibir informações de componente local como o System Center DPM (SC DPM) no Azure, que não é mostrado na [trabalhos de Backup](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) ou [alertas de Backup](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) no portal.

## <a name="using-log-analytics-workspace"></a>Usando o espaço de trabalho do Log Analytics

> [!NOTE]
> Dados de backups de VM do Azure, o agente MAB, System Center DPM (SC DPM), backups do SQL em VMs do Azure estão sendo sejam enviados ao espaço de trabalho do Log Analytics por meio de configurações de diagnóstico. Suporte para backups de compartilhamento de arquivos do Azure, servidor de Backup do Microsoft Azure (MABS) está disponível em breve.

Estamos aproveitando os recursos de dois serviços do Azure - **configurações de diagnóstico** (para enviar dados de vários recursos do Azure Resource Manager para outro recurso) e **Log Analytics** (LA - gerar onde é possível definir outros canais de notificação usando grupos de ações de alertas personalizados) para monitorar em escala. A seções a seguir detalham como usá-la para monitorar o Backup do Azure em grande escala.

### <a name="configuring-diagnostic-settings"></a>Configurando definições de diagnóstico

Um recurso do Azure Resource Manager, como o cofre dos serviços de recuperação do Azure registra todas as informações possíveis sobre as operações agendadas e operações de usuário disparado como dados de diagnóstico. Clique em 'Configurações diagnóstico' na seção de monitoramento e especifique o destino para dados de diagnóstico do cofre RS.

![Configuração de diagnóstico cofre RS com LA como destino](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Você pode selecionar um espaço de trabalho de LA de outra assinatura, como o destino. *Selecionando o mesmo espaço de trabalho de LA para diversos cofres RS, você pode monitorar os cofres entre assinaturas em um único lugar.* Selecione 'AzureBackupReport' como o log de canal todas as informações no espaço de trabalho de LA de relacionadas a Backup do Azure.

> [!IMPORTANT]
> Depois de concluir a configuração, você deve aguardar 24 horas para concluir o push inicial de dados. Depois disso, todos os eventos são enviados, conforme mencionado na [seção frequência](#diagnostic-data-update-frequency).

### <a name="deploying-solution-to-log-analytics-workspace"></a>Implantar a solução ao espaço de trabalho do Log Analytics

Quando os dados estiverem dentro do espaço de trabalho de LA [implantar um modelo do github](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) em LA para visualizar os dados. Certifique-se de que fornecer o mesmo grupo de recursos, o nome do espaço de trabalho e o local do espaço de trabalho para identificar corretamente o espaço de trabalho e, em seguida, instalar esse modelo nele.

### <a name="view-azure-backup-data-using-log-analytics-la"></a>Exibir dados de Backup do Azure usando o Log Analytics (LA)

Depois que o modelo é implantado, a solução para o monitoramento de Backup do Azure será exibida na região de resumo de espaço de trabalho. Você pode percorrer por meio de

- O Azure Monitor -> "Mais" na seção "Insights" e escolha o espaço de trabalho relevante ou
- Espaços de trabalho do log Analytics -> selecione o espaço de trabalho relevante -> 'Espaço de trabalho Resumo' na seção geral.

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

Ao clicar em bloco, o modelo de designer abre uma série de gráficos sobre os dados de Backup do Azure, como monitoramento básicos

#### <a name="all-backup-jobs"></a>Todos os trabalhos de Backup

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>Trabalhos de restauração

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>Alertas de Backup do Azure embutidas para recursos do Azure

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>Alertas de Backup do Azure embutidas para recursos locais

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>Fontes de dados Active Directory

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>Armazenamento em nuvem cofre RS

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Os gráficos acima são fornecidos com o modelo e o cliente está livre para editar ou adicionar mais gráficos.

> [!IMPORTANT]
> Quando você implanta o modelo, ele cria essencialmente um bloqueio somente leitura e você precisa removê-lo para editar o modelo e salve. Para remover os bloqueios, observe o painel 'Bloqueios' na seção 'Configurações' do espaço de trabalho do Log Analytics.

### <a name="create-alerts-using-log-analytics"></a>Criar alertas usando o Log Analytics

O Azure Monitor permite que os usuários criem seus próprios alertas do espaço de trabalho de LA onde você pode *aproveitar os grupos de ação do Azure para selecionar seu mecanismo de notificação preferencial*. Clique em qualquer um dos gráficos acima para abrir a seção 'Logs' do espaço de trabalho de LA ***onde você pode editar as consultas e criar alertas sobre eles***.

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Clicar em "Nova regra de alerta" conforme mostrado acima, a tela de criação de alerta do Monitor do Azure será aberto.

Como você pode observar abaixo, o recurso já está marcado como o espaço de trabalho de LA e integração do grupo de ação é fornecida.

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> Observe que o impacto nos preço relevante de criar essa consulta é fornecido [aqui](https://azure.microsoft.com/pricing/details/monitor/).

#### <a name="alert-condition"></a>Condição de alerta

O principal aspecto é a condição de disparo do alerta. Clicar em 'Condição' carregará automaticamente a consulta Kusto na tela 'Logs' conforme mostrado abaixo e você pode editá-lo de acordo com seu cenário. Alguns exemplos de consulta Kusto estão descritos as [seção abaixo](#sample-kusto-queries).

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Editar a consulta do Kusto, se necessário, selecione o limite direito (que será decidir quando o alerta será acionado), o ponto à direita (janela de tempo para o qual a consulta é executada) e a frequência. Para por exemplo: Se o limite for maior que 0, o período é de 5 minutos e a frequência é de 5 minutos, em seguida, a regra é traduzida como "Executa a consulta a cada 5 minutos para os últimos 5 minutos e se o número de resultados for maior que 0, notifique-me via o grupo de ação selecionada"

#### <a name="action-group-integration"></a>Integração do grupo de ação

Grupos de ação especificam os canais de notificação disponíveis para o usuário. Clicar em "Criar novo" em "Grupos de ação" seção mostra a lista de mecanismos de notificação disponíveis.

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Saiba mais sobre o [alertas do espaço de trabalho de LA](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) sobre [grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) na documentação do Azure Monitor.

Portanto, você pode atender a todos os alertas e monitoramento de requisitos de LA sozinha ou usá-lo como uma técnica suplementar para mecanismos de notificação internos.

### <a name="sample-kusto-queries"></a>Exemplos de consulta Kusto

Os grafos padrão resultará em consultas Kusto para cenários básicos, na qual você pode criar alertas. Você também pode modificá-los para obter os dados que você deseja receber alertas sobre. Aqui, fornecemos alguns exemplos de consulta Kusto que você podem colagem na janela 'Logs' e, em seguida, criar um alerta na consulta.

#### <a name="all-successful-backup-jobs"></a>Todos os trabalhos de backup com êxito

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>Todas as falhas de trabalhos de backup

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>Todos os trabalhos de backup de VM do Azure bem-sucedida

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-sql-log-backup-jobs"></a>Todos os trabalhos de backup de Log do SQL com êxito

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-mab-agent-backup-jobs"></a>Todos os trabalhos de backup de agente MAB com êxito

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

### <a name="diagnostic-data-update-frequency"></a>Frequência de atualização de dados de diagnóstico

Os dados de diagnóstico do cofre é sejam enviados para o espaço de trabalho de LA com algum atraso. Cada evento chega ao espaço de trabalho LA ***com um atraso de 20 a 30 minutos depois que ela é enviada por push do cofre RS.***

- Os alertas internos do serviço de backup (em todas as soluções) são enviados por push assim que eles são criados. O que significa que eles normalmente são exibidos no espaço de trabalho LA após um atraso de 20 a 30 minutos.
- Trabalhos de backup ad hoc e trabalhos de restauração (em todas as soluções) são enviados por push assim que eles ***forem concluídas***.
- Os trabalhos de backup agendados de todas as soluções (exceto o backup do SQL) são enviados por push assim que eles ***forem concluídas***.
- Backup do SQL, já que podemos ter backups de log a cada 15 minutos, para todos os concluídos trabalhos de backup agendados, incluindo logs, as informações são em lote e enviadas por push a cada 6 horas.
- Todas as outras informações como o item de backup, política, pontos de recuperação, armazenamento etc. em todas as soluções são enviados por push **pelo menos uma vez por dia.**
- Uma alteração na configuração do backup, como alterar a edição da diretiva, etc. de política dispara um envio por push todos relacionados de informações de backup.

## <a name="using-rs-vaults-activity-logs"></a>Logs de atividades usando o Cofre de RS

Você também pode usar os logs de atividade para obter notificações de eventos, como sucesso de backup.

> [!CAUTION]
> **Observe que isso só é aplicável para backups de VM do Azure.** Você não pode usar isso para outras soluções como o agente de Backup do Azure, os backups do SQL no Azure, Azure arquivos etc.

### <a name="sign-in-into-azure-portal"></a>Entrar no portal do Azure

Entrar no portal do Azure e vá para o Cofre de serviços de recuperação do Azure relevante e clique na seção "Log de atividades" nas propriedades.

### <a name="identify-appropriate-log-and-create-alert"></a>Identificar o log apropriado e criar alerta

Aplique os filtros mostrados na figura a seguir para verificar se você está recebendo os logs de atividade de backups bem-sucedidos. Altere o intervalo de tempo adequadamente para exibir os registros.

![Logs de atividades para backups de VM do Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

Clique no segmento "JSON" para obter mais detalhes e exiba-os copiando e colando-os em um editor de texto. Ele deve exibir os detalhes do cofre e o item que disparou a atividade de log ou seja, o item de backup.

Depois, clique em "Adicionar alerta do log de atividade" para gerar alertas para todos esses logs.

Você pode clicar em "Adicionar alerta do log atividades" mostrada acima e ele abrirá a tela de criação de alerta que é semelhante à tela de criação de alerta [conforme descrito acima](#create-alerts-using-log-analytics).

Aqui, o recurso é o cofre RS em si e, portanto, você precisará repetir a mesma ação para todos os cofres no qual você deseja que a notificação por meio dos logs de atividade. A condição não terá qualquer limite, o período, a frequência, pois este é um alerta baseado em evento. Assim que o log de atividades relevantes é gerado, o alerta é disparado.

## <a name="recommendation"></a>Recomendações

***Todos os alertas criados a partir de logs de atividade e espaços de trabalho de LA podem ser exibidos no Azure Monitor no painel à esquerda 'Alertas'.***

Embora a notificação por meio dos logs de atividade pode ser usada, ***serviço de Backup do Azure recomenda usar LA para monitorar os logs de escala e a atividade não pelos seguintes motivos***.

- **Cenários limitados:** Aplicável somente para backups de VM do Azure e deve ser repetido para cada cofre RS.
- **Ajustar a definição:** A atividade de backup agendada não couber com a definição mais recente dos logs de atividades e se alinha com [logs de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-are-azure-monitor-diagnostic-logs). Isso levava a impacto inesperado quando os dados de bombeamento por meio do canal de log de atividade são alterados conforme indicado abaixo.
- **Problemas com o canal de log de atividade:** Mudamos para um novo modelo de bombeamento logs de atividade do Backup do Azure em cofres dos Serviços de Recuperação. Infelizmente, a mudança tiver afetado a geração de logs de atividades em nuvens Soberanas do Azure. Se os usuários de nuvem soberana do Azure criado/configuraram todos os alertas de logs de atividade por meio do Azure Monitor, eles não deverá ser disparados. Além disso, em todas as regiões públicas do Azure, se um usuário estiver coletando logs de atividade dos Serviços de Recuperação em um espaço de trabalho de Análise de log, conforme mencionado [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), esses logs também não seriam exibidos.

Portanto, é altamente recomendável usar o espaço de trabalho do Log analítico para o monitoramento e alertas em grande escala para todos os seus Azure Backup de cargas de trabalho protegidas.

## <a name="next-steps"></a>Próximas etapas

- Consulte a [modelo de dados do Log analytics](backup-azure-log-analytics-data-model.md) para criar consultas personalizadas.
