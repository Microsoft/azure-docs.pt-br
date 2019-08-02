---
title: Modelo de dados para os Backup do Azure
description: Este artigo aborda detalhes de modelo de dados do Power BI para relatórios de Backup do Azure.
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: adigan
ms.openlocfilehash: 66245a25af000d999ded87c58e3ba225b373b101
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465589"
---
# <a name="data-model-for-azure-backup-reports"></a>Modelo de dados para relatórios de Backup do Azure
Este artigo descreve o modelo de dados do Power BI usado para criar relatórios de Backup do Azure. Usando esse modelo de dados, você pode filtrar os relatórios existentes com base em campos relevantes e, mais importante, criar seus próprios relatórios usando tabelas e campos no modelo. 

## <a name="creating-new-reports-in-power-bi"></a>Criando novos relatórios no Power BI
O Power BI fornece recursos de personalização com o qual você pode [criar relatórios usando o modelo de dados](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Usando o modelo de dados de Backup do Azure
Você pode usar os seguintes campos fornecidos como parte do modelo de dados para criar relatórios e personalizar relatórios existentes.

### <a name="alert"></a>Alerta
Esta tabela fornece campos e agregações básicos em vários campos relacionados ao alerta.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Número inteiro |Número de alertas criados no período de tempo selecionado |
| %ActiveAlertsCreatedInPeriod |Porcentagem |Porcentagem de alertas ativos no período de tempo selecionado |
| %CriticalAlertsCreatedInPeriod |Porcentagem |Porcentagem de alertas críticos no período de tempo selecionado |
| AlertOccurrenceDate |Date |Data em que o alerta foi criado |
| AlertSeverity |Texto |Severidade do alerta, por exemplo, crítico |
| AlertStatus |Texto |Status do alerta, por exemplo, ativo |
| AlertType |Texto |Tipo de alerta gerado, por exemplo, Backup |
| AlertUniqueId |Texto |ID exclusiva do alerta gerado |
| AsOnDateTime |Date/Time |Hora da atualização mais recente para a linha selecionada |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Número decimal |Tempo médio (em minutos) para resolver o alerta para o período de tempo selecionado |
| EntityState |Texto |Estado atual do objeto do alerta, por exemplo, ativo, excluído |

### <a name="backup-item"></a>Item de backup
Esta tabela fornece campos e agregações básicos em vários campos relacionados ao item de backup.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #BackupItems |Número inteiro |Número de itens de backup |
| #UnprotectedBackupItems |Número inteiro |Número de itens de backup interrompidos para proteção ou configurados para backups, mas os backups não iniciados|
| AsOnDateTime |Date/Time |Hora da atualização mais recente para a linha selecionada |
| BackupItemFriendlyName |Texto |Nome amigável do item de backup |
| BackupItemId |Texto |ID do item de backup |
| BackupItemName |Texto |ID do item de backup |
| BackupItemType |Texto |Tipo de item de backup, por exemplo, VM, FileFolder |
| EntityState |Texto |Estado atual do objeto do item de backup, por exemplo, ativo, excluído |
| LastBackupDateTime |Date/Time |Hora do último backup para o item de backup selecionado |
| LastBackupState |Texto |Estado do último backup para o item de backup selecionado, por exemplo, com êxito, falha |
| LastSuccessfulBackupDateTime |Date/Time |Hora do último backup feito com sucesso para o item de backup selecionado |
| ProtectionState |Texto |Estado de proteção atual do item de backup, por exemplo, protegido, ProtectionStopped |

### <a name="calendar"></a>Calendário
Esta tabela fornece detalhes sobre os campos relacionados ao calendário.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| Date |Date |Data selecionada para filtrar dados |
| DateKey |Texto |Chave exclusiva para cada item de data |
| DayDiff |Número decimal |Diferença no dia para filtrar dados, por exemplo, 0 indica os dados do dia atual, -1 indica os dados do dia anterior, 0 e -1 indicam os dados do dia atual e anterior  |
| Mês |Texto |Mês do ano selecionado para filtrar dados, o mês começa no primeiro dia e termina no dia 31 |
| MonthDate | Date |Dia do mês quando o mês termina, selecionado para filtrar dados |
| MonthDiff |Número decimal |Diferença no mês para filtrar dados, por exemplo, 0 indica os dados do mês atual, -1 indica os dados do mês anterior, 0 e -1 indicam os dados do mês atual e anterior |
| Semana |Texto |Semana selecionada para filtrar dados, a semana começa no domingo e termina no sábado |
| WeekDate |Date |Data em que a semana termina, selecionada para filtrar dados |
| WeekDiff |Número decimal |Diferença na semana para filtrar dados, por exemplo, 0 indica os dados da semana atual, -1 indica os dados da semana anterior, 0 e -1 indicam os dados para a semana atual e anterior |
| Ano |Texto |Ano do calendário selecionado para filtrar dados |
| YearDate |Date |Data em que o ano termina, selecionada para filtrar dados |

### <a name="job"></a>Trabalho
Esta tabela fornece campos e agregações básicos em vários campos relacionados ao trabalho.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #JobsCreatedInPeriod |Número inteiro |Número de trabalhos criados no período de tempo selecionado |
| %FailuresForJobsCreatedInPeriod |Porcentagem |Porcentagem geral de trabalho com falhas no período de tempo selecionado |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Número decimal |Valor percentual de dados transferidos 80 em MB para os trabalhos de **backup** criados no período de tempo selecionado |
| AsOnDateTime |Date/Time |Hora da atualização mais recente para a linha selecionada |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Número decimal |Média de tempo em minutos para trabalhos de **backup concluídos** criados no período de tempo selecionado |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Número decimal |Média de tempo em minutos para trabalhos de **restauração concluídos** criados no período de tempo selecionado |
| BackupStorageDestination |Texto |Destino de armazenamento de backup, por exemplo, nuvem, disco  |
| EntityState |Texto |Estado atual do objeto de trabalho, por exemplo, ativo, excluído |
| JobFailureCode |Texto |Cadeia de caracteres de código de falha pela qual a falha no trabalho ocorreu |
| JobOperation |Texto |Operação para a qual o trabalho é executado, por exemplo, backup, restauração, backup de configuração |
| JobStartDate |Date |Data em que o trabalho começou a ser executado |
| JobStartDate |Time |Hora em que o trabalho começou a ser executado |
| JobStatus |Texto |Status do trabalho concluído, por exemplo, concluído, com falha |
| JobUniqueId |Texto |ID exclusivo para identificar o trabalho |

### <a name="policy"></a>Política
Esta tabela fornece campos e agregações básicos em vários campos relacionados à política.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #Policies |Número inteiro |Número de políticas de backup que existem no sistema |
| #PoliciesInUse |Número inteiro |Número de políticas que estão sendo usadas no momento para configurar backups |
| AsOnDateTime |Date/Time |Hora da atualização mais recente para a linha selecionada |
| BackupDaysOfTheWeek |Texto |Dias da semana nos quais os backups foram agendados |
| BackupFrequency |Texto |Frequência com que os backups são executados, por exemplo, diária, semanal |
| BackupTimes |Texto |Data e hora quando os backups são agendados |
| DailyRetentionDuration |Número inteiro |Duração total da retenção em dias para backups configurados |
| DailyRetentionTimes |Texto |Data e hora quando a retenção diária foi configurada |
| EntityState |Texto |Estado atual do objeto da política, por exemplo, ativo, excluído |
| MonthlyRetentionDaysOfTheMonth |Texto |Datas do mês selecionadas para a retenção mensal |
| MonthlyRetentionDaysOfTheMonth |Texto |Dias da semana selecionados para a retenção mensal |
| MonthlyRetentionDuration |Número decimal |Duração total da retenção em meses para backups configurados |
| MonthlyRetentionFormat |Texto |Tipo de configuração de retenção mensal, por exemplo, diário, semanal |
| MonthlyRetentionTimes |Texto |Data e hora quando a retenção mensal foi configurada |
| MonthlyRetentionWeeksOfTheMonth |Texto |Semanas do mês quando a retenção mensal é configurada, por exemplo, a primeira, a última, etc. |
| PolicyName |Texto |Nome da política definido |
| PolicyUniqueId |Texto |ID exclusivo para identificar a política |
| RetentionType |Texto |Tipo de política de retenção, por exemplo, diário, semanal, mensal, anual |
| WeeklyRetentionDaysOfTheWeek |Texto |Dias da semana selecionados para a retenção semanal |
| WeeklyRetentionDuration |Número decimal |Duração total da retenção em semanas para backups configurados |
| WeeklyRetentionTimes |Texto |Data e hora quando a retenção semanal foi configurada |
| YearlyRetentionDaysOfTheMonth |Texto |Dias do mês selecionados para a retenção anual |
| YearlyRetentionDaysOfTheMonth |Texto |Dias da semana selecionados para a retenção anual |
| YearlyRetentionDuration |Número decimal |Duração total da retenção em ano para backups configurados |
| YearlyRetentionFormat |Texto |Tipo de configuração de retenção anual, por exemplo, diário, semanal |
| YearlyRetentionMonthsOfTheYear |Texto |Meses do ano selecionados para a retenção anual |
| YearlyRetentionTimes |Texto |Data e hora quando a retenção anual foi configurada |
| YearlyRetentionWeeksOfTheMonth |Texto |Semanas do mês quando a retenção anual é configurada, por exemplo, a primeira, a última, etc. |

### <a name="protected-server"></a>Servidor protegido
Esta tabela fornece campos e agregações básicos em vários campos relacionados a servidores protegidos.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #ProtectedServers |Número inteiro |Número de servidores protegidos |
| AsOnDateTime |Date/Time |Hora da atualização mais recente para a linha selecionada |
| AzureBackupAgentOSType |Texto |Tipo de OS do Agente de Backup do Azure |
| AzureBackupAgentOSVersion |Texto |Versão do OS do Agente de Backup do Azure |
| AzureBackupAgentUpdateDate |Texto |Data quando o Agente de Backup foi atualizado |
| AzureBackupAgentVersion |Texto |Número de versão da versão de Backup do Agente |
| BackupManagementType |Texto |Tipo de provedor para executar o backup, por exemplo, IaaSVM, FileFolder |
| EntityState |Texto |Estado atual do objeto de servidor protegido, por exemplo, ativo, excluído |
| ProtectedServerFriendlyName |Texto |Nome amigável do servidor protegido |
| ProtectedServerName |Texto |Nome do servidor protegido |
| ProtectedServerType |Texto |Tipo de backup do servidor protegido, por exemplo, IaaSVMContainer |
| ProtectedServerName |Texto |Nome do servidor protegido para o qual o item de backup pertence |
| RegisteredContainerId |Texto |ID do contêiner registrado para backup |

### <a name="storage"></a>Armazenamento
Esta tabela fornece campos e agregações básicos em vários campos relacionados ao armazenamento.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #ProtectedInstances |Número decimal |Número de instâncias protegidas usadas para calcular o armazenamento de front-end no faturamento, calculado com base no valor mais recente de tempo selecionado |
| AsOnDateTime |Date/Time |Hora da atualização mais recente para a linha selecionada |
| CloudStorageInMB |Número decimal |Armazenamento de backup de nuvem usado por backups, calculado com base no valor mais recente de tempo selecionado |
| EntityState |Texto |Estado atual do objeto, por exemplo, ativo, excluído |
| LastUpdatedDate |Date |Data na qual a linha selecionada foi atualizada |

### <a name="time"></a>Time
Esta tabela fornece detalhes sobre os campos relacionados ao tempo.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| Hora |Time |Hora do dia, por exemplo, 1:00:00 PM |
| HourNumber |Número decimal |Número de horas do dia, por exemplo, 13.00 |
| Minuto |Número decimal |Minuto da hora |
| PeriodOfTheDay |Texto |O intervalo do período de tempo do dia, por exemplo, 12 a 3 AM |
| Time |Time |Hora do dia, por exemplo, 12:00:01 AM |
| TimeKey |Texto |Valor chave para representar a hora |

### <a name="vault"></a>Cofre
Esta tabela fornece campos e agregações básicos em vários campos relacionados ao cofre.

| Campo | Tipo de dados | Descrição |
| --- | --- | --- |
| #Vaults |Número inteiro |Número de cofres |
| AsOnDateTime |Date/Time |Hora da atualização mais recente para a linha selecionada |
| AzureDataCenter |Texto |Data center onde o cofre está localizado |
| EntityState |Texto |Estado atual do objeto do cofre, por exemplo, ativo, excluído |
| StorageReplicationType |Texto |Tipo de replicação de armazenamento para o cofre, por exemplo, GeoRedundant |
| SubscriptionId |Texto |ID da assinatura do cliente selecionado para geração de relatórios |
| VaultName |Texto |Nome do cofre |
| VaultTags |Texto |Marcas associadas ao cofre |

## <a name="next-steps"></a>Próximas etapas
Depois de examinar o modelo de dados para criar relatórios de Backup do Azure, consulte os seguintes artigos para obter mais detalhes sobre como criar e exibir relatórios no Power BI.

* [Criando relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrando relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
