---
title: Modelo de dados de logs do Azure Monitor
description: Este artigo aborda detalhes de modelo de dados do Log Analytics do Azure Monitor para dados de Backup do Azure.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 004c5a6c0c2c4dcfcf13134bd5a5143ba647048f
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500981"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modelo de dados do Log Analytics para dados de Backup do Azure

Use o modelo de dados do Log Analytics para criar alertas personalizados do Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> * Esse modelo de dados se refere ao modo do Diagnóstico do Azure de envio de eventos de diagnóstico para o Log Analytics (LA). Para saber o modelo de dados para o novo modo específico do recurso, é possível consultar o seguinte artigo: [Modelo de dados dos eventos de diagnóstico do Backup do Azure](./backup-azure-reports-data-model.md)
> * Para criar exibições de relatório personalizadas, é recomendável usar [funções do sistema em logs de Azure monitor](backup-reports-system-functions.md) em vez de trabalhar com as tabelas brutas listadas abaixo.

## <a name="using-azure-backup-data-model"></a>Usando o modelo de dados de Backup do Azure

Você pode usar os campos a seguir fornecidos como parte do modelo de dados para criar visuais, consultas personalizadas e painéis de acordo com seus requisitos.

### <a name="alert"></a>Alerta

Essa tabela fornece detalhes sobre campos relacionados ao alerta.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| AlertUniqueId_s |Texto |Identificador exclusivo do alerta gerado |
| AlertType_s |Texto |Tipo de alerta, por exemplo, Backup |
| AlertStatus_s |Texto |Status do alerta, por exemplo, Ativo |
| AlertOccurrenceDateTime_s |Data/hora |Data e hora em que o alerta foi criado |
| AlertSeverity_s |Texto |Gravidade do alerta, por exemplo, Crítico |
|AlertTimeToResolveInMinutes_s    | Número        |Tempo necessário para resolver um alerta. Em branco para alertas ativos.         |
|AlertConsolidationStatus_s   |Texto         |Identificar se o alerta é um alerta consolidado ou não         |
|CountOfAlertsConsolidated_s     |Número         |Número de alertas consolidados se for um alerta consolidado          |
|AlertRaisedOn_s     |Texto         |Tipo de entidade em que o alerta é gerado         |
|AlertCode_s     |Texto         |Código para identificar exclusivamente um tipo de alerta         |
|RecommendedAction_s   |Texto         |Ação recomendada para resolver o alerta         |
| EventName_s |Texto |O nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador exclusivo do item backup associado ao alerta |
| SchemaVersion_s |Texto |Versão atual do esquema, por exemplo, **V2** |
| State_s |Texto |Estado atual do objeto de alerta, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de provedor para executar o backup, por exemplo, IaaSVM, fileFolder ao qual este alerta pertence |
| OperationName |Texto |Nome da operação atual, por exemplo, alerta |
| Categoria |Texto |Categoria de dados de diagnóstico enviados por push para os logs do Azure Monitor. Sempre AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedContainerUniqueId_s |Texto |Identificador exclusivo do item protegido associado ao alerta (costumava ser ProtectedServerUniqueId_s in V1)|
| VaultUniqueId_s |Texto |Identificador exclusivo do cofre protegido associado ao alerta |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador exclusivo para o recurso sobre quais dados são coletados. Por exemplo, uma ID do recurso do cofre dos Serviços de Recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="backupitem"></a>BackupItem

Esta tabela fornece detalhes sobre os campos relacionados ao item de backup.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |O nome do evento. Sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |Identificador único do item de backup |
| BackupItemId_s |Texto |Identificador do item de backup (este campo é apenas para o esquema v1) |
| BackupItemName_s |Texto |ID do item de backup |
| BackupItemFriendlyName_s |Texto |Nome amigável do item de backup |
| BackupItemType_s |Texto |Tipo de item de backup, por exemplo, VM, FileFolder |
| BackupItemProtectionState_s |Texto |Estado de proteção do item de backup |
| BackupItemAppVersion_s |Texto |Versão do aplicativo do item de backup |
| ProtectionState_s |Texto |Estado de proteção atual do item de backup, por exemplo, Protegido, ProtectionStopped |
| ProtectionGroupName_s |Texto | Nome do grupo de proteção no qual o item de backup está protegido, para SC DPM e MABS, se aplicável|
| SecondaryBackupProtectionState_s |Texto |Se a proteção secundária está habilitada para o item de backup|
| SchemaVersion_s |Texto |Versão do esquema, por exemplo, **V2** |
| State_s |Texto |Estado do objeto do item de backup, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de provedor para executar o backup, por exemplo, IaaSVM, fileFolder ao qual este item de backup pertence |
| OperationName |Texto |Nome da operação, por exemplo, BackupItem |
| Categoria |Texto |Categoria de dados de diagnóstico enviados por push para os logs do Azure Monitor. Sempre AzureBackupReport |
| Recurso |Texto |Recursos para os quais dados são coletados, por exemplo, nome do cofre dos serviços de recuperação |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |ID do recurso para os dados sendo coletados, por exemplo, ID de recurso do cofre dos Serviços de Recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo cofre de Serviços de Recuperação) para dados sendo coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo cofre de Serviços de Recuperação) para dados sendo coletados |
| ResourceProvider |Texto |fornecedor de Recurso para dados sendo coletados, por exemplo Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para os dados que estão sendo coletados, por exemplo, os cofres |

### <a name="backupitemassociation"></a>BackupItemAssociation

Esta tabela fornece detalhes sobre associações de itens de backup com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento. É sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |ID exclusiva do item de backup |
| SchemaVersion_s |Texto |Este campo denota a versão atual do esquema. É **v2** |
| State_s |Texto |Estado atual do objeto de associação de item de backup, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| BackupItemSourceSize_s |Texto | Tamanho de front-end do item de backup |
| BackupManagementServerUniqueId_s |Texto | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados por push para Log Analytics. É AzureBackupReport |
| OperationName |Texto |Este campo representa o nome da operação atual - BackupItemAssociation |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedContainerUniqueId_s |Texto |Identificador exclusivo do item protegido associado ao item de backup (costumava ser ProtectedServerUniqueId_s in V1) |
| VaultUniqueId_s |Texto |Identificador exclusivo do cofre que contém o item de backup |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, ID do recurso do cofre dos Serviços de Recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceProvider |Texto |fornecedor de Recurso para dados sendo coletados, por exemplo Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para os dados que estão sendo coletados, por exemplo, os cofres |

### <a name="backupmanagementserver"></a>BackupManagementServer

Esta tabela fornece detalhes sobre associações de itens de backup com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
|BackupManagementServerName_s     |Texto         |Nome do servidor de gerenciamento de backup        |
|AzureBackupAgentVersion_s     |Texto         |Versão do agente de Backup do Azure no servidor de gerenciamento de backup          |
|BackupManagementServerVersion_s     |Texto         |Versão do servidor de gerenciamento de backup|
|BackupManagementServerOSVersion_s    |Texto            |Versão do SO do servidor de gerenciamento de backup|
|BackupManagementServerType_s     |Texto         |Tipo de servidor de gerenciamento de backup, como MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Texto         |Campo para identificar exclusivamente o servidor de gerenciamento de backup       |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, ID do recurso do cofre dos Serviços de Recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceProvider |Texto |fornecedor de Recurso para dados sendo coletados, por exemplo Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para os dados que estão sendo coletados, por exemplo, os cofres |

### <a name="job"></a>Trabalho

Esta tabela fornece detalhes sobre campos relacionados ao trabalho.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |O nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador único do item de backup |
| SchemaVersion_s |Texto |Versão do esquema, por exemplo, **V2** |
| State_s |Texto |Estado atual do objeto de trabalho, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - Trabalho |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor. É AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |Identificador exclusivo do servidor protegido associado ao trabalho |
| ProtectedContainerUniqueId_s |Texto | ID exclusiva para identificar o contêiner protegido no qual o trabalho é executado |
| VaultUniqueId_s |Texto |Identificador exclusivo do cofre protegido |
| JobOperation_s |Texto |Operação para a qual o trabalho é executado, por exemplo, backup, restauração, backup de configuração |
| JobStatus_s |Texto |Status do trabalho concluído, por exemplo, Concluído, Com Falha |
| JobFailureCode_s |Texto |Cadeia de caracteres de código de falha pela qual a falha no trabalho ocorreu |
| JobStartDateTime_s |Data/hora |Data e hora em que o trabalho iniciou a execução |
| BackupStorageDestination_s |Texto |Destino do armazenamento de backup, por exemplo, Nuvem, Disco  |
| AdHocOrScheduledJob_s |Texto | Campo para especificar se o trabalho é ad hoc ou agendado |
| JobDurationInSecs_s | Número |Duração total do trabalho em segundos |
| DataTransferredInMB_s | Número |Dados transferidos em MB para este trabalho|
| JobUniqueId_g |Texto |ID exclusiva para identificar o trabalho |
| RecoveryJobDestination_s |Texto | Destino de um trabalho de recuperação, em que os dados são recuperados |
| RecoveryJobRPDateTime_s |Datetime | A data, a hora em que o ponto de recuperação que está sendo recuperado foi criado |
| RecoveryJobRPLocation_s |Texto | O local onde o ponto de recuperação que está sendo recuperado foi armazenado|
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, ID do recurso do cofre dos Serviços de Recuperação|
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="policy"></a>Política

Esta tabela fornece detalhes sobre campos relacionados a políticas.

| Campo | Tipo de Dados | Versões aplicáveis | Descrição |
| --- | --- | --- | --- |
| EventName_s |Texto ||Este campo representa o nome deste evento. É sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto ||Este campo denota a versão atual do esquema. É **v2** |
| State_s |Texto ||Estado atual do objeto de política, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto ||Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto ||Este campo representa o nome da operação atual - Política |
| Categoria |Texto ||Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor. É AzureBackupReport |
| Recurso |Texto ||Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Texto ||ID exclusiva para identificar a política |
| PolicyName_s |Texto ||Nome da política definido |
| BackupFrequency_s |Texto ||Frequência com que os backups são executados, por exemplo, diária, semanal |
| BackupTimes_s |Texto ||Data e hora quando os backups são agendados |
| BackupDaysOfTheWeek_s |Texto ||Dias da semana nos quais os backups foram agendados |
| RetentionDuration_s |Número Inteiro ||Duração de retenção para backups configurados |
| DailyRetentionDuration_s |Número Inteiro ||Duração total da retenção em dias para backups configurados |
| DailyRetentionTimes_s |Texto ||Data e hora quando a retenção diária foi configurada |
| WeeklyRetentionDuration_s |Número Decimal ||Duração total da retenção em semanas para backups configurados |
| WeeklyRetentionTimes_s |Texto ||Data e hora quando a retenção semanal foi configurada |
| WeeklyRetentionDaysOfTheWeek_s |Texto ||Dias da semana selecionados para a retenção semanal |
| MonthlyRetentionDuration_s |Número Decimal ||Duração total da retenção em meses para backups configurados |
| MonthlyRetentionTimes_s |Texto ||Data e hora quando a retenção mensal foi configurada |
| MonthlyRetentionFormat_s |Texto ||Tipo de configuração de retenção mensal, por exemplo, diário, semanal |
| MonthlyRetentionDaysOfTheWeek_s |Texto ||Dias da semana selecionados para a retenção mensal |
| MonthlyRetentionWeeksOfTheMonth_s |Texto ||Semanas do mês em que a retenção mensal é configurada, por exemplo, a primeira, a última |
| YearlyRetentionDuration_s |Número Decimal ||Duração total da retenção em ano para backups configurados |
| YearlyRetentionTimes_s |Texto ||Data e hora quando a retenção anual foi configurada |
| YearlyRetentionMonthsOfTheYear_s |Texto ||Meses do ano selecionados para a retenção anual |
| YearlyRetentionFormat_s |Texto ||Tipo de configuração de retenção anual, por exemplo, diário, semanal | |
| YearlyRetentionDaysOfTheMonth_s |Texto ||Dias do mês selecionados para a retenção anual |
| SynchronisationFrequencyPerDay_s |Número Inteiro |v2|Número de vezes em um dia em que um backup de arquivo é sincronizado para SC DPM e MABS |
| DiffBackupFormat_s |Texto |v2|Formato para backups diferenciais para SQL no backup da VM do Azure |
| DiffBackupTime_s |Hora |v2|Horário para backups diferenciais para SQL no backup de VM do Azure|
| DiffBackupRetentionDuration_s |Número Decimal |v2|Duração da retenção de backups diferenciais para SQL no backup de VM do Azure|
| LogBackupFrequency_s |Número Decimal |v2|Frequência de backups de log de transações para SQL|
| LogBackupRetentionDuration_s |Número Decimal |v2|Duração da retenção para backups de log para SQL no backup de VM do Azure|
| DiffBackupDaysofTheWeek_s |Texto |v2|Dias da semana para backups diferenciais para SQL no backup de VM do Azure|
| SourceSystem |Texto ||Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto ||Identificador de recurso para os dados sendo coletados. Por exemplo, ID do recurso do cofre dos Serviços de Recuperação |
| SubscriptionId |Texto ||Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto ||grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto ||Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto ||Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="policyassociation"></a>PolicyAssociation

Esta tabela fornece detalhes sobre associações de políticas com várias entidades.

| Campo | Tipo de Dados | Versões aplicáveis | Descrição |
| --- | --- | --- | --- |
| EventName_s |Texto ||Este campo representa o nome deste evento. É sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto ||Este campo denota a versão atual do esquema. É **v2** |
| State_s |Texto ||Estado atual do objeto de política, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto ||Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto ||Este campo representa o nome da operação atual - PolicyAssociation |
| Categoria |Texto ||Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor. É AzureBackupReport |
| Recurso |Texto ||Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Texto ||ID exclusiva para identificar a política |
| VaultUniqueId_s |Texto ||ID exclusiva do cofre ao qual essa política pertence |
| BackupManagementServerUniqueId_s |Texto |v2 |Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável        |
| SourceSystem |Texto ||Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto ||Identificador de recurso para os dados sendo coletados. Por exemplo, ID do recurso do cofre dos Serviços de Recuperação |
| SubscriptionId |Texto ||Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto ||grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto ||Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto ||Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="protected-container"></a>Contêiner protegido

Esta tabela apresenta campos básicos sobre contêineres protegidos. (costumava ser ProtectedServer na v1)

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Texto | Campo para identificar exclusivamente um contêiner protegido |
| ProtectedContainerOSType_s |Texto |Tipo de SO do contêiner protegido |
| ProtectedContainerOSVersion_s |Texto |Versão do SO do contêiner protegido |
| AgentVersion_s |Texto |Número de versão do backup do agente ou do agente de proteção (no caso do SC DPM e MABS) |
| BackupManagementType_s |Texto |Tipo de provedor para executar o backup. Por exemplo, IaaSVM, FileFolder |
| EntityState_s |Texto |Estado atual do objeto de servidor protegido. Por exemplo, Ativo, Excluído |
| ProtectedContainerFriendlyName_s |Texto |Nome amigável do servidor protegido |
| ProtectedContainerName_s |Texto |Nome do SO do contêiner protegido |
| ProtectedContainerWorkloadType_s |Texto |Tipo do contêiner protegido armazenado em backup. Por exemplo, IaaSVMContainer |
| ProtectedContainerLocation_s |Texto |Se o contêiner protegido está localizado no local ou no Azure |
| ProtectedContainerType_s |Texto |Se o contêiner protegido é um servidor ou um contêiner |
| ProtectedContainerProtectionState_s’  |Texto |Estado de proteção do contêiner protegido |

### <a name="storage"></a>Armazenamento

Esta tabela fornece detalhes sobre campos relacionados ao armazenamento.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| CloudStorageInBytes_s |Número Decimal |Armazenamento de backup de nuvem usado por backups, calculado com base no valor mais recente (este campo é somente para o esquema v1)|
| ProtectedInstances_s |Número Decimal |Número de instâncias protegidas utilizadas para calcular o armazenamento de front-end na cobrança, calculada baseada no valor mais recente |
| EventName_s |Texto |Este campo representa o nome deste evento. É sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo denota a versão atual do esquema. É **v2** |
| State_s |Texto |Estado atual do objeto de armazenamento, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - Armazenamento |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor. É AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |ID exclusiva do servidor protegido para o qual o armazenamento é calculado |
| VaultUniqueId_s |Texto |ID exclusiva do cofre para armazenamento é calculado |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, ID do recurso do cofre dos Serviços de Recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |
| StorageUniqueId_s |Texto |ID exclusiva usada para identificar a entidade de armazenamento |
| StorageType_s |Texto |Tipo de armazenamento, por exemplo, Nuvem, Volume, Disco |
| StorageName_s |Texto |Nome da entidade de armazenamento, por exemplo E:\ |
| StorageTotalSizeInGBs_s |Texto |Tamanho total do armazenamento, em GB, consumido pela entidade de armazenamento|

### <a name="storageassociation"></a>StorageAssociation

Esta tabela apresenta campos básicos relacionados ao armazenamento que conectam o armazenamento a outras entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- |  --- |
| StorageUniqueId_s |Texto |ID exclusiva usada para identificar a entidade de armazenamento |
| SchemaVersion_s |Texto |Este campo denota a versão atual do esquema. É **v2** |
| BackupItemUniqueId_s |Texto |ID exclusiva usada para identificar o item de backup relacionado à entidade de armazenamento |
| BackupManagementServerUniqueId_s |Texto |ID exclusiva usada para identificar o serviço de gerenciamento de backup relacionado à entidade de armazenamento|
| VaultUniqueId_s |Texto |ID exclusiva usada para identificar o cofre relacionado à entidade de armazenamento|
| StorageConsumedInMBs_s |Número|Tamanho do armazenamento consumido pelo item de backup correspondente no armazenamento correspondente |
| StorageAllocatedInMBs_s |Número |Tamanho do armazenamento alocado para o item de backup correspondente do tipo Disco|

### <a name="vault"></a>Cofre

Esta tabela fornece detalhes sobre campos relacionados ao cofre.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento. É sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo denota a versão atual do esquema. É **v2** |
| State_s |Texto |Estado atual do objeto do sofre, por exemplo, Ativo, Excluído |
| OperationName |Texto |Este campo representa o nome da operação atual - Cofre |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor. É AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| VaultUniqueId_s |Texto |ID exclusiva do cofre |
| VaultName_s |Texto |Nome do cofre |
| AzureDataCenter_s |Texto |Data center onde o cofre está localizado |
| StorageReplicationType_s |Texto |Tipo de replicação de armazenamento para o cofre, por exemplo, GeoRedundant |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, ID do recurso do cofre dos Serviços de Recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="backup-management-server"></a>Servidor de gerenciamento de backup

Esta tabela apresenta campos básicos sobre servidores de gerenciamento de backup.

|Campo  |Tipo de Dados  | Descrição  |
|---------|---------|----------|
|BackupManagementServerName_s     |Texto         |Nome do servidor de gerenciamento de backup        |
|AzureBackupAgentVersion_s     |Texto         |Versão do agente de Backup do Azure no servidor de gerenciamento de backup          |
|BackupManagementServerVersion_s     |Texto         |Versão do servidor de gerenciamento de backup|
|BackupManagementServerOSVersion_s     |Texto            |Versão do SO do servidor de gerenciamento de backup|
|BackupManagementServerType_s     |Texto         |Tipo de servidor de gerenciamento de backup, como MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Texto         |Campo para identificar exclusivamente o servidor de gerenciamento de backup       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Esta tabela especifica as cargas de trabalho às quais um volume está associado.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| StorageUniqueId_s |Texto |ID exclusiva usada para identificar a entidade de armazenamento |
| BackupItemType_s |Texto |As cargas de trabalho para as quais este volume é o armazenamento preferencial|

### <a name="protectedinstance"></a>ProtectedInstance

Esta tabela apresenta campos relacionados a instâncias protegidas básicas.

| Campo | Tipo de Dados |Versões aplicáveis | Descrição |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Texto |v2|ID exclusiva usada para identificar o item de backup para VMs com backup usando o DPM, MABS|
| ProtectedContainerUniqueId_s |Texto |v2|ID exclusiva usada para identificar o contêiner protegido para tudo, exceto VMs com backup usando o DPM, MABS|
| ProtectedInstanceCount_s |Texto |v2|Contagem de instâncias protegidas para o item de backup associado ou o contêiner protegido nessa data e hora|

### <a name="recoverypoint"></a>RecoveryPoint

Esta tabela apresenta campos básicos relacionados ao ponto de recuperação.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| BackupItemUniqueId_s |Texto |ID exclusiva usada para identificar o item de backup para VMs com backup usando o DPM, MABS|
| OldestRecoveryPointTime_s |Texto |Data e hora do ponto de recuperação mais antigo para o item de backup|
| OldestRecoveryPointLocation_s |Texto |Local do ponto de recuperação mais antigo para o item de backup|
| LatestRecoveryPointTime_s |Texto |Data e hora do ponto de recuperação mais recente para o item de backup|
| LatestRecoveryPointLocation_s |Texto |Local do ponto de recuperação mais recente para o item de backup|

## <a name="sample-kusto-queries"></a>Consultas de exemplo do Kusto

Abaixo estão alguns exemplos para ajudá-lo a gravar consultas em dados de backup do Azure que residem na tabela Diagnóstico do Azure:

- Todos os trabalhos de backup bem-sucedidos

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Todos os trabalhos de backup com falha

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Todos os trabalhos de backup de VM do Azure bem-sucedidos

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

- Todos os trabalhos de backup de log de transações SQL bem-sucedidos

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

- Todos os trabalhos do agente de Backup do Azure bem-sucedidos

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

## <a name="v1-schema-vs-v2-schema"></a>Esquema V1 em comparação com esquema V2

Anteriormente, os dados de diagnóstico para o agente de backup do Azure e o backup de VM do Azure foram enviados para Diagnóstico do Azure tabela em um esquema referido como ***v1 Schema** _. Subsequentemente, novas colunas foram adicionadas para dar suporte a outros cenários e cargas de trabalho, e os dados de diagnóstico foram enviados por push em um novo esquema referido como _ *_v2 Schema_* *.  

Por motivos de compatibilidade com versões anteriores, os dados de diagnóstico para o agente de backup do Azure e o backup de VM do Azure atualmente são enviados para a tabela Diagnóstico do Azure nos esquemas V1 e V2 (com o esquema v1 agora em um caminho de substituição). É possível identificar quais registros no Log Analytics são do esquema V1 filtrando registros para SchemaVersion_s=="V1" em suas consultas de log.

Veja a terceira coluna "Descrição" no [modelo de dados](#using-azure-backup-data-model) descrito acima para identificar quais colunas pertencem somente ao esquema V1.

### <a name="modifying-your-queries-to-use-the-v2-schema"></a>Modificando suas consultas para usar o esquema v2

Como o esquema v1 está em um caminho de reprovação, é recomendável usar apenas o esquema V2 em todas as suas consultas personalizadas nos dados de diagnóstico do backup do Azure. Abaixo está um exemplo de como atualizar suas consultas para remover a dependência no esquema V1:

1. Identifique se sua consulta está usando qualquer campo aplicável somente ao esquema v1. Suponha que você tenha uma consulta para listar todos os itens de backup e seus servidores protegidos associados da seguinte maneira:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | distinct BackupItemUniqueId_s, ProtectedServerUniqueId_s
    ````

    A consulta acima usa o campo ProtectedServerUniqueId_s, que é aplicável somente ao esquema v1. O esquema v2 equivalente desse campo é ProtectedContainerUniqueId_s (consulte as tabelas acima). O campo BackupItemUniqueId_s é aplicável até o esquema V2 e o mesmo campo pode ser usado nesta consulta.

2. Atualize a consulta para usar os nomes de campo de esquema v2. É uma prática recomendada usar o filtro **em que SchemaVersion_s = = "v2"** em todas as suas consultas, para que somente os registros correspondentes ao esquema v2 sejam analisados pela consulta:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | where SchemaVersion_s=="V2"
    | distinct BackupItemUniqueId_s, ProtectedContainerUniqueId_s
    ````

## <a name="next-steps"></a>Próximas etapas

Depois de examinar o modelo de dados, você pode começar a [criar consultas personalizadas](../azure-monitor/visualize/tutorial-logs-dashboards.md) nos logs do Azure Monitor para criar seu próprio painel.
