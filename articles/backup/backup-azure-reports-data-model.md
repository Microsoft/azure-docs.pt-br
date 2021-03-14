---
title: Modelo de dados para eventos de diagnóstico de backup do Azure
description: Esse modelo de dados está em referência ao modo específico de recurso de envio de eventos de diagnóstico para Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 0d75af6d2b41aad0b5f821dd1f6409b30f7ca531
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499587"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Modelo de dados para eventos de diagnóstico de backup do Azure

> [!NOTE]
>
> Para criar exibições de relatório personalizadas, é recomendável usar [funções do sistema em logs de Azure monitor](backup-reports-system-functions.md) em vez de trabalhar com as tabelas brutas listadas abaixo.

## <a name="coreazurebackup"></a>CoreAzureBackup

Esta tabela fornece informações sobre entidades de backup de núcleo, como cofres e itens de backup.

| **Campo**                         | **Tipo de Dados** | **Descrição**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Texto          | Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação. |
| OperationName                     | Texto          | Este campo representa o nome da operação atual-BackupItem, BackupItemAssociation ou ProtectedContainer. |
| Categoria                          | Texto          | Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor. Por exemplo, CoreAzureBackup. |
| AgentVersion                      | Texto          | Número de versão do backup do agente ou do agente de proteção (no caso do SC DPM e MABS) |
| AzureBackupAgentVersion           | Texto          | Versão do agente de Backup do Azure no servidor de gerenciamento de backup |
| AzureDataCenter                   | Texto          | Data Center em que o cofre está localizado                       |
| BackupItemAppVersion              | Texto          | Versão do aplicativo do item de backup                       |
| BackupItemFriendlyName            | Texto          | Nome amigável do item de backup                             |
| BackupItemName                    | Texto          | Nome do item de backup                                      |
| BackupItemProtectionState         | Texto          | Estado de proteção do item de backup                          |
| BackupItemFrontEndSize            | Texto          | Tamanho de front-end do item de backup                            |
| BackupItemType                    | Texto          | Tipo de item de backup. Por exemplo: VM, fileFolder             |
| BackupItemUniqueId                | Texto          | Identificador único do item de backup                         |
| BackupManagementServerType        | Texto          | Tipo de servidor de gerenciamento de backup, como em MABS, SC DPM     |
| BackupManagementServerUniqueId    | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup      |
| BackupManagementType              | Texto          | Tipo de provedor para o servidor executando o trabalho de backup. Por exemplo, IaaSVM, FileFolder |
| BackupManagementServerName        | Texto          | Nome do servidor de gerenciamento de backup                         |
| BackupManagementServerOSVersion   | Texto          | Versão do SO do servidor de gerenciamento de backup                   |
| BackupManagementServerVersion     | Texto          | Versão do servidor de gerenciamento de backup                      |
| LatestRecoveryPointLocation       | Texto          | Local do ponto de recuperação mais recente para o item de backup    |
| LatestRecoveryPointTime           | Datetime      | Data e hora do ponto de recuperação mais recente para o item de backup   |
| OldestRecoveryPointLocation       | Texto          | Local do ponto de recuperação mais antigo para o item de backup    |
| OldestRecoveryPointTime           | Datetime      | Data e hora do ponto de recuperação mais recente para o item de backup   |
| PolicyUniqueId                    | Texto          | ID exclusiva para identificar a política                             |
| ProtectedContainerFriendlyName    | Texto          | Nome amigável do servidor protegido                        |
| ProtectedContainerLocation        | Texto          | Se o contêiner protegido está localizado no local ou no Azure |
| ProtectedContainerName            | Texto          | Nome do contêiner protegido                            |
| ProtectedContainerOSType          | Texto          | Tipo de so do contêiner protegido                          |
| ProtectedContainerOSVersion       | Texto          | Versão do SO do contêiner protegido                        |
| ProtectedContainerProtectionState | Texto          | Estado de proteção do contêiner protegido                  |
| ProtectedContainerType            | Texto          | Se o contêiner protegido é um servidor ou um contêiner  |
| ProtectedContainerUniqueId        | Texto          | ID exclusiva usada para identificar o contêiner protegido para tudo, exceto VMs com backup usando o DPM, MABS |
| ProtectedContainerWorkloadType    | Texto          | Tipo de backup do contêiner protegido. Por exemplo, IaaSVMContainer |
| ProtectionGroupName               | Texto          | Nome do grupo de proteção no qual o item de backup está protegido, para SC DPM e MABS, se aplicável |
| ResourceGroupName                 | Texto          | Grupo de recursos do recurso (por exemplo, cofre dos serviços de recuperação) para dados que estão sendo coletados |
| schemaVersion                     | Texto          | Este campo denota a versão atual do esquema. É **v2** |
| SecondaryBackupProtectionState    | Texto          | Se a proteção secundária está habilitada para o item de backup  |
| Estado                             | Texto          | Estado do objeto de item de backup. Por exemplo, Ativo, Excluído |
| StorageReplicationType            | Texto          | Tipo de replicação de armazenamento para o cofre. Por exemplo, georedundante |
| SubscriptionId                    | Texto          | Identificador de assinatura do recurso (por exemplo, cofre dos serviços de recuperação) para o qual os dados são coletados |
| VaultName                         | Texto          | Nome do cofre                                            |
| VaultTags                         | Texto          | Marcas associadas ao recurso do cofre                    |
| VaultUniqueId                     | Texto          | Identificador exclusivo do cofre                             |
| SourceSystem                      | Texto          | Sistema de origem dos dados atuais-Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Essa tabela fornece detalhes sobre campos relacionados ao alerta.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador exclusivo para o recurso sobre quais dados são coletados. Por exemplo, uma ID de recurso do cofre dos serviços de recuperação |
| OperationName                  | Texto          | Nome da operação atual. Por exemplo, alerta            |
| Categoria                       | Texto          | Categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupAlerts |
| AlertCode                      | Texto          | Código para identificar exclusivamente um tipo de alerta                     |
| AlertConsolidationStatus       | Texto          | Identificar se o alerta é um alerta consolidado ou não         |
| AlertOccurrenceDateTime        | Datetime      | Data e hora em que o alerta foi criado                     |
| AlertRaisedOn                  | Texto          | Tipo de entidade em que o alerta é gerado                        |
| AlertSeverity                  | Texto          | Severidade do alerta. Por exemplo, crítico                 |
| AlertStatus                    | Texto          | Status do alerta. Por exemplo, ativo                     |
| AlertTimeToResolveInMinutes    | Número        | Tempo necessário para resolver um alerta. Em branco para alertas ativos.     |
| AlertType                      | Texto          | Tipo de alerta. Por exemplo, backup                           |
| AlertUniqueId                  | Texto          | Identificador exclusivo do alerta gerado                    |
| BackupItemUniqueId             | Texto          | Identificador exclusivo do item de backup associado ao alerta |
| BackupManagementServerUniqueId | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| BackupManagementType           | Texto          | Tipo de provedor para o servidor executando o trabalho de backup, por exemplo, IaaSVM, fileFolder |
| CountOfAlertsConsolidated      | Número        | Número de alertas consolidados se for um alerta consolidado  |
| ProtectedContainerUniqueId     | Texto          | Identificador exclusivo do servidor protegido associado ao alerta |
| RecommendedAction              | Texto          | Ação recomendada para resolver o alerta                      |
| schemaVersion                  | Texto          | Versão atual do esquema, por exemplo, **V2**            |
| Estado                          | Texto          | Estado atual do objeto de alerta, por exemplo, Ativo, Excluído |
| StorageUniqueId                | Texto          | ID exclusiva usada para identificar a entidade de armazenamento                |
| VaultUniqueId                  | Texto          | ID exclusiva usada para identificar o cofre relacionado ao alerta    |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Esta tabela apresenta campos relacionados a instâncias protegidas básicas.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador exclusivo do recurso sobre o qual os dados são coletados. Por exemplo, uma ID do recurso do cofre dos Serviços de Recuperação |
| OperationName                  | Texto          | Nome da operação, por exemplo ProtectedInstance         |
| Categoria                       | Texto          | Categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Texto          | ID exclusiva do item de backup                                 |
| BackupManagementServerUniqueId | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| BackupManagementType           | Texto          | Tipo de provedor para o servidor executando o trabalho de backup, por exemplo, IaaSVM, fileFolder |
| ProtectedContainerUniqueId     | Texto          | ID exclusiva para identificar o contêiner protegido no qual o trabalho é executado |
| ProtectedInstanceCount         | Texto          | Contagem de instâncias protegidas para o item de backup associado ou o contêiner protegido nessa data e hora |
| schemaVersion                  | Texto          | Versão atual do esquema, por exemplo, **V2**            |
| Estado                          | Texto          | Estado do objeto de item de backup, por exemplo, ativo, excluído |
| VaultUniqueId                  | Texto          | Identificador exclusivo do cofre protegido associado à instância protegida |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Esta tabela fornece detalhes sobre campos relacionados ao trabalho.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador de recurso para os dados sendo coletados. Por exemplo, ID do recurso do cofre dos Serviços de Recuperação |
| OperationName                  | Texto          | Este campo representa o nome da operação atual - Trabalho    |
| Categoria                       | Texto          | Este campo representa a categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Texto          | Campo para especificar se o trabalho é ad hoc ou agendado           |
| BackupItemUniqueId             | Texto          | ID exclusiva usada para identificar o item de backup relacionado à entidade de armazenamento |
| BackupManagementServerUniqueId | Texto          | ID exclusiva usada para identificar o serviço de gerenciamento de backup relacionado à entidade de armazenamento |
| BackupManagementType           | Texto          | Tipo de provedor para executar o backup, por exemplo, IaaSVM, FileFolder para qual esse trabalho pertence |
| DataTransferredInMB            | Número        | Dados transferidos em MB para este trabalho                          |
| JobDurationInSecs              | Número        | Duração total do trabalho em segundos                                |
| JobFailureCode                 | Texto          | Cadeia de caracteres de código de falha pela qual a falha no trabalho ocorreu    |
| JobOperation                   | Texto          | Operação para a qual o trabalho é executado, por exemplo, backup, restauração, configurar backup |
| JobOperationSubType            | Texto          | Subtipo da operação de trabalho. Por exemplo, ' log ', no caso do trabalho de backup de log |
| JobStartDateTime               | Datetime      | Data e hora em que o trabalho iniciou a execução                       |
| JobStatus                      | Texto          | Status do trabalho concluído, por exemplo, Concluído, Com Falha   |
| JobUniqueId                    | Texto          | ID exclusiva para identificar o trabalho                                |
| ProtectedContainerUniqueId     | Texto          | Identificador exclusivo do servidor protegido associado ao trabalho |
| RecoveryJobDestination         | Texto          | Destino de um trabalho de recuperação, em que os dados são recuperados   |
| RecoveryJobRPDateTime          | Datetime      | A data, a hora em que o ponto de recuperação que está sendo recuperado foi criado |
| RecoveryJobLocation            | Texto          | O local onde o ponto de recuperação que está sendo recuperado foi armazenado |
| RecoveryLocationType           | Texto          | Tipo do local de recuperação                                |
| schemaVersion                  | Texto          | Versão atual do esquema, por exemplo, **V2**            |
| Estado                          | Texto          | Estado atual do objeto de trabalho, por exemplo, ativo, excluído |
| VaultUniqueId                  | Texto          | Identificador exclusivo do cofre protegido associado ao trabalho |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Esta tabela fornece detalhes sobre campos relacionados a políticas.

| **Campo**                       | **Tipo de Dados**  | **Descrição**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Texto           | Identificador exclusivo do recurso sobre o qual os dados são coletados. Por exemplo, uma ID do recurso do cofre dos Serviços de Recuperação |
| OperationName                   | Texto           | Nome da operação, por exemplo, política ou PolicyAssociation |
| Categoria                        | Texto           | Categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Texto           | Dias da semana nos quais os backups foram agendados            |
| BackupFrequency                 | Texto           | Frequência com que os backups são executados. Por exemplo, diário, semanal |
| BackupManagementType            | Texto           | Tipo de provedor para o servidor executando o trabalho de backup. Por exemplo, IaaSVM, fileFolder |
| BackupManagementServerUniqueId  | Texto           | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| BackupTimes                     | Texto           | Data e hora quando os backups são agendados                     |
| DailyRetentionDuration          | Número Inteiro   | Duração total da retenção em dias para backups configurados      |
| DailyRetentionTimes             | Texto           | Data e hora quando a retenção diária foi configurada            |
| DiffBackupDaysOfTheWeek         | Texto           | Dias da semana para backups diferenciais para SQL no backup de VM do Azure |
| DiffBackupFormat                | Texto           | Formato para backups diferenciais para SQL no backup da VM do Azure   |
| DiffBackupRetentionDuration     | Número Decimal | Duração da retenção para backups diferenciais para SQL no backup de VM do Azure |
| DiffBackupTime                  | Hora           | Horário para backups diferenciais para SQL no backup de VM do Azure     |
| LogBackupFrequency              | Número Decimal | Frequência de backups de log de transações para SQL                            |
| LogBackupRetentionDuration      | Número Decimal | Duração da retenção para backups de log para SQL no backup de VM do Azure |
| MonthlyRetentionDaysOfTheMonth  | Texto           | Semanas do mês em que a retenção mensal é configurada.  Por exemplo, primeiro, último |
| MonthlyRetentionDaysOfTheMonth   | Texto           | Dias da semana selecionados para a retenção mensal              |
| MonthlyRetentionDuration        | Texto           | Duração total da retenção em meses para backups configurados    |
| MonthlyRetentionFormat          | Texto           | Tipo de configuração para retenção mensal. Por exemplo, diariamente com base no dia, semanal para a semana com base |
| MonthlyRetentionTimes           | Texto           | Data e hora quando a retenção mensal foi configurada           |
| MonthlyRetentionWeeksOfTheMonth | Texto           | Semanas do mês em que a retenção mensal é configurada.   Por exemplo, primeiro, último |
| PolicyName                      | Texto           | Nome da política definido                                   |
| PolicyUniqueId                  | Texto           | ID exclusiva para identificar a política                             |
| PolicyTimeZone                  | Texto           | Fuso horário no qual os campos de hora da política são especificados nos logs |
| RetentionDuration               | Texto           | Duração de retenção para backups configurados                    |
| RetentionType                   | Texto           | Tipo de retenção                                            |
| schemaVersion                   | Texto           | Este campo denota a versão atual do esquema, é **v2** |
| Estado                           | Texto           | Estado atual do objeto de política. Por exemplo, ativo, excluído |
| SynchronisationFrequencyPerDay  | Número Inteiro   | Número de vezes em um dia em que um backup de arquivo é sincronizado para SC DPM e MABS |
| VaultUniqueId                   | Texto           | ID exclusiva do cofre ao qual essa política pertence          |
| WeeklyRetentionDaysOfTheWeek    | Texto           | Dias da semana selecionados para a retenção semanal               |
| WeeklyRetentionDuration         | Número Decimal | Duração da retenção semanal total em semanas para backups configurados |
| WeeklyRetentionTimes            | Texto           | Data e hora quando a retenção semanal foi configurada            |
| YearlyRetentionDaysOfTheMonth   | Texto           | Dias do mês selecionados para a retenção anual             |
| YearlyRetentionDaysOfTheMonth    | Texto           | Dias da semana selecionados para a retenção anual               |
| YearlyRetentionDuration         | Número Decimal | Duração total da retenção em ano para backups configurados     |
| YearlyRetentionFormat           | Texto           | Tipo de configuração para retenção anual, por exemplo, diária para o dia, semanal para a semana com base |
| YearlyRetentionMonthsOfTheYear  | Texto           | Meses do ano selecionados para a retenção anual             |
| YearlyRetentionTimes            | Texto           | Data e hora quando a retenção anual foi configurada            |
| YearlyRetentionWeeksOfTheMonth  | Texto           | Semanas do mês selecionadas para retenção anual             |
| SourceSystem                    | Texto           | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Esta tabela fornece detalhes sobre campos relacionados ao armazenamento.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação |
| OperationName                  | Texto          | Este campo representa o nome da operação atual-armazenamento ou StorageAssociation |
| Categoria                       | Texto          | Este campo representa a categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupStorage |
| BackupItemUniqueId             | Texto          | ID exclusiva usada para identificar o item de backup para VMs com backup usando o DPM, MABS |
| BackupManagementServerUniqueId | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| BackupManagementType           | Texto          | Tipo de provedor para o servidor executando o trabalho de backup. Por exemplo, IaaSVM, fileFolder |
| PreferredWorkloadOnVolume      | Texto          | Carga de trabalho para a qual este volume é o armazenamento preferencial      |
| ProtectedContainerUniqueId     | Texto          | Identificador exclusivo do contêiner protegido associado ao item de backup |
| schemaVersion                  | Texto          | Versão do esquema. Por exemplo, **v2**                   |
| Estado                          | Texto          | Estado do objeto de item de backup. Por exemplo, ativo, excluído |
| StorageAllocatedInMBs          | Número        | Tamanho do armazenamento alocado pelo item de backup correspondente no armazenamento correspondente do tipo disco |
| StorageConsumedInMBs           | Número        | Tamanho do armazenamento consumido pelo item de backup correspondente no armazenamento correspondente |
| StorageName                    | Texto          | Nome da entidade de armazenamento. Por exemplo, E:\                      |
| StorageTotalSizeInGBs          | Texto          | Tamanho total do armazenamento, em GB, consumido pela entidade de armazenamento     |
| StorageType                    | Texto          | Tipo de armazenamento, por exemplo, Nuvem, Volume, Disco             |
| StorageUniqueId                | Texto          | ID exclusiva usada para identificar a entidade de armazenamento                |
| VaultUniqueId                  | Texto          | ID exclusiva usada para identificar o cofre relacionado à entidade de armazenamento |
| VolumeFriendlyName             | Texto          | Nome amigável do volume de armazenamento                          |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="valid-operation-names-for-each-table"></a>Nomes de operação válidos para cada tabela

Cada registro nas tabelas acima tem um nome de **operação** associado. Um nome de operação descreve o tipo de registro (e também indica quais campos na tabela são preenchidos para esse registro). Cada tabela (categoria) dá suporte a um ou mais nomes de operação distintos. Veja abaixo um resumo dos nomes de operação com suporte para cada uma das tabelas acima.

| **Nome da tabela/categoria**                   | **Nomes de operação com suporte** | **Descrição**              |
| ------------------------------------------- | ------------------------------|----------------------------- |
| CoreAzureBackup | BackupItem | Representa um registro que contém todos os detalhes de um determinado item de backup, como ID, nome, tipo, etc. |
| CoreAzureBackup | BackupItemAssociation | Representa um mapeamento entre um item de backup e seu contêiner protegido associado (se aplicável). |
| CoreAzureBackup | BackupItemFrontEndSizeConsumption | Representa um mapeamento entre um item de backup e seu tamanho de front-end. |
| CoreAzureBackup | ProtectedContainer | Representa um registro que contém todos os detalhes de um determinado contêiner protegido, como ID, nome, tipo, etc. |
| CoreAzureBackup | ProtectedContainerAssociation | Representa um mapeamento entre um contêiner protegido e o cofre usado para seu backup. |
| CoreAzureBackup | Cofre | Representa um registro que contém todos os detalhes de um determinado cofre, por exemplo. ID, nome, marcas, local etc. |
| CoreAzureBackup | RecoveryPoint | Representa um registro que contém o ponto de recuperação mais antigo e mais recente para um determinado item de backup. |
| AddonAzureBackupJobs | Trabalho |  Representa um registro que contém todos os detalhes de um determinado trabalho. Por exemplo, operação de trabalho, hora de início, status, etc. |
| AddonAzureBackupAlerts | Alerta | Representa um registro que contém todos os detalhes de um determinado alerta. Por exemplo, hora de criação do alerta, severidade, status, etc.  |
| AddonAzureBackupStorage | Armazenamento | Representa um registro que contém todos os detalhes de uma determinada entidade de armazenamento. Por exemplo, nome de armazenamento, tipo etc. |
| AddonAzureBackupStorage | StorageAssociation | Representa um mapeamento entre um item de backup e o armazenamento em nuvem total consumido pelo item de backup. |
| AddonAzureBackupProtectedInstance | ProtectedInstance | Representa um registro que contém a contagem de instâncias protegidas para cada item de backup ou contêiner. Para o backup de VM do Azure, a contagem de instâncias protegidas está disponível no nível de item de backup, para outras cargas de trabalho que está disponível no nível de contêiner protegido. |
| AddonAzureBackupPolicy | Política |  Representa um registro que contém todos os detalhes de uma política de backup e retenção. Por exemplo, ID, nome, configurações de retenção, etc. |
| AddonAzureBackupPolicy | PolicyAssociation | Representa um mapeamento entre um item de backup e a política de backup aplicada a ele. |   

Geralmente, você precisará executar junções entre tabelas diferentes, bem como conjuntos diferentes de registros que fazem parte da mesma tabela (diferenciada pelo nome da operação) para obter todos os campos necessários para sua análise. Consulte as [consultas de exemplo](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries) para começar. 

## <a name="next-steps"></a>Próximas etapas

- [Saiba como enviar dados de diagnóstico para Log Analytics](./backup-azure-diagnostic-events.md)
- [Saiba como escrever consultas em tabelas específicas de recursos](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)