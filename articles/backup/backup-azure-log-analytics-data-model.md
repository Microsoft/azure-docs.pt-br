---
title: O Azure Monitor registra o modelo de dados para Backup do Azure
description: Este artigo fala sobre o Azure Monitor registra os detalhes do modelo de dados para dados de Backup do Azure.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: dd4dad2cc3e541d3b6866c02341161dc1d9e1e6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61234906"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modelo de dados do Log Analytics para dados de Backup do Azure

Use o modelo de dados do Log Analytics para criar alertas personalizados do Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Usando o modelo de dados de Backup do Azure

Você pode usar os seguintes campos fornecidos como parte do modelo de dados para criar visuais, consultas personalizadas e painel de acordo com seus requisitos.

### <a name="alert"></a>Alerta

Essa tabela fornece detalhes sobre campos relacionados ao alerta.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| AlertUniqueId_s |Text |Identificador exclusivo do alerta gerado |
| AlertType_s |Text |Tipo de alerta, por exemplo, Backup |
| AlertStatus_s |Text |Status do alerta, por exemplo, Ativo |
| AlertOccurrenceDateTime_s |Data/hora |Data e hora em que o alerta foi criado |
| AlertSeverity_s |Text |Gravidade do alerta, por exemplo, Crítico |
|AlertTimeToResolveInMinutes_s    | Número        |Tempo necessário para resolver um alerta. Espaço em branco para alertas ativos.         |
|AlertConsolidationStatus_s   |Text         |Identifica se o alerta é um alerta consolidado ou não         |
|CountOfAlertsConsolidated_s     |Número         |Número de alertas consolidados se ele for um alerta consolidado          |
|AlertRaisedOn_s     |Text         |Tipo de entidade que o alerta é gerado em         |
|AlertCode_s     |Text         |Código para identificar exclusivamente um tipo de alerta         |
|RecommendedAction_s   |Text         |Ação recomendada para resolver o alerta         |
| EventName_s |Text |O nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Identificador exclusivo do item backup associado ao alerta |
| SchemaVersion_s |Text |A versão atual do esquema, por exemplo **V2** |
| State_s |Text |Estado atual do objeto de alerta, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Text |Tipo de provedor para executar o backup, por exemplo, IaaSVM, FileFolder ao qual esse alerta pertence |
| OperationName |Text |Nome da operação atual, por exemplo, alerta |
| Category |Text |Categoria de dados de diagnóstico enviados por push para os logs do Azure Monitor. Sempre AzureBackupReport |
| Resource |Text |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Text |Identificador exclusivo do item protegido associado ao alerta |
| VaultUniqueId_s |Text |Identificador exclusivo do cofre protegido associado ao alerta |
| SourceSystem |Text |Sistema de origem dos dados atuais - Azure |
| ResourceId |Text |Identificador exclusivo para o recurso sobre quais dados são coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Text |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Text |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="backupitem"></a>BackupItem

Esta tabela fornece detalhes sobre os campos relacionados ao item de backup.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| EventName_s |Text |O nome do evento. Sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Identificador único do item de backup |
| BackupItemId_s |Text |Identificador do item de backup |
| BackupItemName_s |Text |ID do item de backup |
| BackupItemFriendlyName_s |Text |Nome amigável do item de backup |
| BackupItemType_s |Texto |Tipo de item de backup, por exemplo, VM, FileFolder |
| ProtectionState_s |Text |Estado de proteção atual do item de backup, por exemplo, Protegido, ProtectionStopped |
| ProtectionGroupName_s |Text | Nome do grupo de proteção do Item de Backup é protegido, do SC DPM e MABS, se aplicável|
| SecondaryBackupProtectionState_s |Text |Se a proteção secundária está habilitada para o item de backup|
| SchemaVersion_s |Text |Versão do esquema, por exemplo, **V2** |
| State_s |Text |Estado do objeto do item de backup, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Text |Tipo de provedor para executar o backup, por exemplo, IaaSVM, FileFolder para qual esse item de backup pertence |
| OperationName |Text |Nome da operação, por exemplo, BackupItem |
| Category |Text |Categoria de dados de diagnóstico enviados por push para os logs do Azure Monitor. Sempre AzureBackupReport |
| Resource |Text |Recursos para os quais dados são coletados, por exemplo, nome do cofre dos serviços de recuperação |
| SourceSystem |Text |Sistema de origem dos dados atuais - Azure |
| ResourceId |Text |Id do recurso para os dados sendo coletados, por exemplo, serviços de recuperação do Cofre de id de recurso |
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo cofre de Serviços de Recuperação) para dados sendo coletados |
| ResourceGroup |Text |grupo de Recursos do recurso (por exemplo cofre de Serviços de Recuperação) para dados sendo coletados |
| ResourceProvider |Text |fornecedor de Recurso para dados sendo coletados, por exemplo Microsoft.RecoveryServices |
| ResourceType |Text |Tipo de recurso para os dados que estão sendo coletados, por exemplo, os cofres |

### <a name="backupitemassociation"></a>BackupItemAssociation

Esta tabela fornece detalhes sobre associações de itens de backup com várias entidades.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| EventName_s |Text |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |ID exclusivo do item de backup |
| SchemaVersion_s |Text |Este campo indica a versão atual do esquema, é **V2** |
| State_s |Text |Estado atual do objeto de associação de item de backup, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Text |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| BackupItemSourceSize_s |Text | Tamanho de front-end do item de backup |
| BackupManagementServerUniqueId_s |Text | Campo para identificar exclusivamente o servidor de gerenciamento de Backup do Item de Backup é protegido por meio do, se aplicável |
| Category |Text |Este campo representa a categoria dos dados de diagnóstico enviados por push para o Log Analytics, é AzureBackupReport |
| OperationName |Text |Este campo representa o nome da operação atual - BackupItemAssociation |
| Resource |Text |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Text |Identificador exclusivo para a política associada com o item de backup |
| ProtectedServerUniqueId_s |Text |Identificador exclusivo do servidor protegido associado com o item de backup |
| VaultUniqueId_s |Text |Identificador exclusivo do cofre que contém o item de backup |
| SourceSystem |Text |Sistema de origem dos dados atuais - Azure |
| ResourceId |Text |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceGroup |Text |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceProvider |Text |fornecedor de Recurso para dados sendo coletados, por exemplo Microsoft.RecoveryServices |
| ResourceType |Text |Tipo de recurso para os dados que estão sendo coletados, por exemplo, os cofres |

### <a name="backupmanagementserver"></a>BackupManagementServer

Esta tabela fornece detalhes sobre associações de itens de backup com várias entidades.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Nome do servidor de gerenciamento de Backup        |
|AzureBackupAgentVersion_s     |Text         |Versão do agente de Backup do Azure no servidor de gerenciamento de Backup          |
|BackupManagementServerVersion_s     |Text         |Versão do servidor de gerenciamento de Backup|
|BackupManagementServerOSVersion_s    |Text            |Versão do sistema operacional do servidor de gerenciamento de Backup|
|BackupManagementServerType_s     |Text         |Tipo de servidor de gerenciamento de Backup, como o MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Campo para identificar exclusivamente o servidor de gerenciamento de Backup       |
| SourceSystem |Text |Sistema de origem dos dados atuais - Azure |
| ResourceId |Text |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceGroup |Text |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceProvider |Text |fornecedor de Recurso para dados sendo coletados, por exemplo Microsoft.RecoveryServices |
| ResourceType |Text |Tipo de recurso para os dados que estão sendo coletados, por exemplo, os cofres |

### <a name="job"></a>Trabalho

Esta tabela fornece detalhes sobre campos relacionados ao trabalho.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| EventName_s |Text |O nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Identificador único do item de backup |
| SchemaVersion_s |Text |Versão do esquema, por exemplo, **V2** |
| State_s |Text |Estado atual do objeto de trabalho, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Text |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Text |Este campo representa o nome da operação atual - Trabalho |
| Category |Text |Este campo representa a categoria de dados de diagnóstico enviados por push para os logs do Azure Monitor, é AzureBackupReport |
| Resource |Text |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Text |Identificador exclusivo do servidor protegido associado ao trabalho |
| ProtectedContainerUniqueId_s |Text | Id exclusiva para identificar o contêiner protegido, em que o trabalho é executado |
| VaultUniqueId_s |Text |Identificador exclusivo do cofre protegido |
| JobOperation_s |Text |Operação para a qual o trabalho é executado, por exemplo, backup, restauração, backup de configuração |
| JobStatus_s |Text |Status do trabalho concluído, por exemplo, Concluído, Com Falha |
| JobFailureCode_s |Text |Cadeia de caracteres de código de falha pela qual a falha no trabalho ocorreu |
| JobStartDateTime_s |Data/hora |Data e hora em que o trabalho iniciou a execução |
| BackupStorageDestination_s |Text |Destino do armazenamento de backup, por exemplo, Nuvem, Disco  |
| AdHocOrScheduledJob_s |Text | Campo para especificar se o trabalho é Ad Hoc ou agendada |
| JobDurationInSecs_s | Número |Duração total do trabalho em segundos |
| DataTransferredInMB_s | Número |Dados transferidos em MB para este trabalho|
| JobUniqueId_g |Text |ID exclusivo para identificar o trabalho |
| RecoveryJobDestination_s |Text | Destino de um trabalho de recuperação, onde os dados são recuperados |
| RecoveryJobRPDateTime_s |DateTime | A data, hora de criação do ponto de recuperação que está sendo recuperado |
| RecoveryJobRPLocation_s |Text | O local onde o ponto de recuperação que está sendo recuperado foi armazenado|
| SourceSystem |Text |Sistema de origem dos dados atuais - Azure |
| ResourceId |Text |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação|
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Text |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Text |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="policy"></a>Política

Esta tabela fornece detalhes sobre campos relacionados a políticas.

| Campo | Tipo de Dados | Versões aplicáveis | DESCRIÇÃO |
| --- | --- | --- | --- |
| EventName_s |Text ||Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Text ||Este campo indica a versão atual do esquema, é **V2** |
| State_s |Text ||Estado atual do objeto de política, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Text ||Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Text ||Este campo representa o nome da operação atual - Política |
| Category |Text ||Este campo representa a categoria de dados de diagnóstico enviados por push para os logs do Azure Monitor, é AzureBackupReport |
| Resource |Text ||Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Text ||ID exclusivo para identificar a política |
| PolicyName_s |Text ||Nome da política definido |
| BackupFrequency_s |Text ||Frequência com que os backups são executados, por exemplo, diária, semanal |
| BackupTimes_s |Text ||Data e hora quando os backups são agendados |
| BackupDaysOfTheWeek_s |Text ||Dias da semana nos quais os backups foram agendados |
| RetentionDuration_s |Número inteiro ||Duração de retenção para backups configurados |
| DailyRetentionDuration_s |Número inteiro ||Duração total da retenção em dias para backups configurados |
| DailyRetentionTimes_s |Text ||Data e hora quando a retenção diária foi configurada |
| WeeklyRetentionDuration_s |Número decimal ||Duração total da retenção em semanas para backups configurados |
| WeeklyRetentionTimes_s |Text ||Data e hora quando a retenção semanal foi configurada |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Dias da semana selecionados para a retenção semanal |
| MonthlyRetentionDuration_s |Número decimal ||Duração total da retenção em meses para backups configurados |
| MonthlyRetentionTimes_s |Text ||Data e hora quando a retenção mensal foi configurada |
| MonthlyRetentionFormat_s |Text ||Tipo de configuração de retenção mensal, por exemplo, diário, semanal |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Dias da semana selecionados para a retenção mensal |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Semanas do mês quando a retenção mensal é configurada, por exemplo, Primeira, Última, etc. |
| YearlyRetentionDuration_s |Número decimal ||Duração total da retenção em ano para backups configurados |
| YearlyRetentionTimes_s |Text ||Data e hora quando a retenção anual foi configurada |
| YearlyRetentionMonthsOfTheYear_s |Text ||Meses do ano selecionados para a retenção anual |
| YearlyRetentionFormat_s |Text ||Tipo de configuração de retenção anual, por exemplo, diário, semanal | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Dias do mês selecionados para a retenção anual |
| SynchronisationFrequencyPerDay_s |Número inteiro |v2|Número de vezes em um dia de que um backup de arquivo está sincronizado para SC DPM e MABS |
| DiffBackupFormat_s |Text |v2|Formato para backups diferenciais para o SQL no backup de VM do Azure |
| DiffBackupTime_s |Hora |v2|Tempo para backups diferenciais para o SQL no Backup de VM do Azure|
| DiffBackupRetentionDuration_s |Número decimal |v2|Duração de retenção para backups diferenciais para o SQL no Backup de VM do Azure|
| LogBackupFrequency_s |Número decimal |v2|Frequência de backups de Log para SQL|
| LogBackupRetentionDuration_s |Número decimal |v2|Duração de retenção para backups de Log do SQL no Backup de VM do Azure|
| DiffBackupDaysofTheWeek_s |Text |v2|Dias da semana para backups diferenciais para o SQL no Backup de VM do Azure|
| SourceSystem |Text ||Sistema de origem dos dados atuais - Azure |
| ResourceId |Text ||Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Text ||Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Text ||grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Text ||Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Text ||Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="policyassociation"></a>PolicyAssociation

Esta tabela fornece detalhes sobre associações de políticas com várias entidades.

| Campo | Tipo de Dados | Versões aplicáveis | DESCRIÇÃO |
| --- | --- | --- | --- |
| EventName_s |Text ||Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Text ||Este campo indica a versão atual do esquema, é **V2** |
| State_s |Text ||Estado atual do objeto de política, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Text ||Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Text ||Este campo representa o nome da operação atual - PolicyAssociation |
| Category |Text ||Este campo representa a categoria de dados de diagnóstico enviados por push para os logs do Azure Monitor, é AzureBackupReport |
| Resource |Text ||Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Text ||ID exclusivo para identificar a política |
| VaultUniqueId_s |Text ||ID exclusivo do cofre ao qual esta política pertence |
| BackupManagementServerUniqueId_s |Text |v2 |Campo para identificar exclusivamente o servidor de gerenciamento de Backup do Item de Backup é protegido por meio do, se aplicável        |
| SourceSystem |Text ||Sistema de origem dos dados atuais - Azure |
| ResourceId |Text ||Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Text ||Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Text ||grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Text ||Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Text ||Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="protected-container"></a>Contêiner protegido

Esta tabela fornece campos básicos sobre contêineres protegidos. (Era ProtectedServer na v1)

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Campo para identificar exclusivamente um contêiner protegido |
| ProtectedContainerOSType_s |Text |Tipo de sistema operacional do contêiner protegido |
| ProtectedContainerOSVersion_s |Text |Versão do sistema operacional do contêiner protegido |
| AgentVersion_s |Text |Número de versão do agente de Backup ou o agente de proteção (no caso do SC DPM e MABS) |
| BackupManagementType_s |Text |Tipo de provedor para executar o backup, por exemplo, IaaSVM, FileFolder |
| EntityState_s |Text |Estado atual do objeto de servidor protegido, por exemplo, ativo, excluído |
| ProtectedContainerFriendlyName_s |Text |Nome amigável do servidor protegido |
| ProtectedContainerName_s |Text |Nome do contêiner protegido |
| ProtectedContainerWorkloadType_s |Text |Tipo do contêiner protegido de backup, por exemplo, IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Se o contêiner protegido está localizada localmente ou no Azure |
| ProtectedContainerType_s |Text |Se o contêiner protegido é um servidor ou em um contêiner |

### <a name="storage"></a>Armazenamento

Esta tabela fornece detalhes sobre campos relacionados ao armazenamento.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| CloudStorageInBytes_s |Número decimal |Armazenamento de backup na nuvem usado por backups, calculado baseado no valor mais recente |
| ProtectedInstances_s |Número decimal |Número de instâncias protegidas utilizadas para calcular o armazenamento de front-end na cobrança, calculada baseada no valor mais recente |
| EventName_s |Text |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Este campo indica a versão atual do esquema, é **V2** |
| State_s |Text |Estado atual do objeto de armazenamento, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Text |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Text |Este campo representa o nome da operação atual - Armazenamento |
| Category |Text |Este campo representa a categoria de dados de diagnóstico enviados por push para os logs do Azure Monitor, é AzureBackupReport |
| Resource |Text |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Text |ID exclusivo do servidor protegido para o qual o armazenamento é calculado |
| VaultUniqueId_s |Text |ID exclusivo do cofre para armazenamento é calculado |
| SourceSystem |Text |Sistema de origem dos dados atuais - Azure |
| ResourceId |Text |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Text |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Text |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="storageassociation"></a>StorageAssociation

Esta tabela fornece campos básicos relacionados ao armazenamento, conectar o armazenamento a outras entidades.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Id exclusiva usada para identificar a entidade de armazenamento |
| SchemaVersion_s |Text |Este campo indica a versão atual do esquema, é **V2** |
| BackupItemUniqueId_s |Text |Id exclusiva usada para identificar o item de backup relacionado à entidade de armazenamento |
| BackupManagementServerUniqueId_s |Text |Id exclusiva usada para identificar o servidor de gerenciamento de backup relacionado à entidade de armazenamento|
| VaultUniqueId_s |Text |Id exclusiva usada para identificar o cofre relacionado à entidade de armazenamento|
| StorageConsumedInMBs_s |Número|Tamanho de armazenamento consumido pelo item de backup correspondente no armazenamento correspondente |
| StorageAllocatedInMBs_s |Número |Tamanho do armazenamento alocado pelo item de backup correspondente no armazenamento correspondente do tipo de disco|

### <a name="vault"></a>Cofre

Esta tabela fornece detalhes sobre campos relacionados ao cofre.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| EventName_s |Text |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Este campo indica a versão atual do esquema, é **V2** |
| State_s |Text |Estado atual do objeto do sofre, por exemplo, Ativo, Excluído |
| OperationName |Text |Este campo representa o nome da operação atual - Cofre |
| Category |Text |Este campo representa a categoria de dados de diagnóstico enviados por push para os logs do Azure Monitor, é AzureBackupReport |
| Resource |Text |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| VaultUniqueId_s |Text |ID exclusivo do cofre |
| VaultName_s |Text |Nome do cofre |
| AzureDataCenter_s |Text |Data center onde o cofre está localizado |
| StorageReplicationType_s |Text |Tipo de replicação de armazenamento para o cofre, por exemplo, GeoRedundant |
| SourceSystem |Text |Sistema de origem dos dados atuais - Azure |
| ResourceId |Text |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Text |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Text |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Text |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="backup-management-server"></a>Servidor de gerenciamento de backup

Esta tabela fornece campos básicos sobre servidores de gerenciamento de Backup.

|Campo  |Tipo de Dados  | DESCRIÇÃO  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |Nome do servidor de gerenciamento de Backup        |
|AzureBackupAgentVersion_s     |Text         |Versão do agente de Backup do Azure no servidor de gerenciamento de Backup          |
|BackupManagmentServerVersion_s     |Text         |Versão do servidor de gerenciamento de Backup|
|BackupManagmentServerOSVersion_s     |Text            |Versão do sistema operacional do servidor de gerenciamento de Backup|
|BackupManagementServerType_s     |Text         |Tipo de servidor de gerenciamento de Backup, como o MABS, SC DPM|
|BackupManagmentServerUniqueId_s     |Text         |Campo para identificar exclusivamente o servidor de gerenciamento de Backup       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Esta tabela especifica um Volume está associado com as cargas de trabalho.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| StorageUniqueId_s |Text |Id exclusiva usada para identificar a entidade de armazenamento |
| BackupItemType_s |Text |As cargas de trabalho para o qual esse volume é o armazenamento preferido|

### <a name="protectedinstance"></a>ProtectedInstance

Esta tabela fornece campos relacionados de instâncias protegidas básico.

| Campo | Tipo de Dados |Versões aplicáveis | DESCRIÇÃO |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Id exclusiva usada para identificar o item de backup de VMs por backup usando o DPM, o MABS|
| ProtectedContainerUniqueId_s |Text |v2|Id exclusiva usada para identificar o contêiner protegido para tudo, exceto as VMs por backup usando o DPM, o MABS|
| ProtectedInstanceCount_s |Text |v2|Contagem de instâncias protegidas para o item de backup associado ou um contêiner protegido em que a data-hora|

### <a name="recoverypoint"></a>RecoveryPoint

Esta tabela fornece recuperação básica aponte campos relacionados.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Id exclusiva usada para identificar o item de backup de VMs por backup usando o DPM, o MABS|
| OldestRecoveryPointTime_s |Text |Data hora do ponto de recuperação mais antigo para o item de backup|
| OldestRecoveryPointLocation_s |Text |Local do ponto de recuperação mais antigo para o item de backup|
| LatestRecoveryPointTime_s |Text |Data hora do último ponto de recuperação para o item de backup|
| LatestRecoveryPointLocation_s |Text |Local do ponto de recuperação mais recente para o item de backup|

## <a name="next-steps"></a>Próximas etapas

Depois de examinar o modelo de dados, você pode começar [criando consultas personalizadas](../azure-monitor/learn/tutorial-logs-dashboards.md) nos logs do Azure Monitor para criar seu próprio painel.
