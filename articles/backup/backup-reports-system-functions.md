---
title: Funções do sistema em logs de Azure Monitor
description: Gravar consultas personalizadas em logs de Azure Monitor usando funções do sistema
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: acb45e6ad0250a1f8d10377fdd509e40051f25b9
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564901"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Funções do sistema em logs de Azure Monitor

O backup do Azure fornece um conjunto de funções, chamadas de funções de sistema ou funções de solução, que estão disponíveis por padrão em seus espaços de trabalho de Log Analytics (LA).
 
Essas funções operam em dados nas [tabelas brutas de backup do Azure](./backup-azure-reports-data-model.md) em la e retornam dados formatados que ajudam a recuperar facilmente as informações de todas as suas entidades relacionadas ao backup, usando consultas simples. Os usuários podem passar parâmetros para essas funções para filtrar os dados retornados por essas funções. 

É recomendável usar funções do sistema para consultar os dados de backup na LA espaços de trabalho para criar relatórios personalizados, pois eles fornecem vários benefícios, conforme detalhado na seção abaixo.

## <a name="benefits-of-using-system-functions"></a>Benefícios do uso de funções do sistema

* **Consultas mais simples**: o uso de funções ajuda a reduzir o número de junções necessárias em suas consultas. Por padrão, as funções retornam esquemas "achatados", que incorporam todas as informações pertencentes à entidade (instância de backup, trabalho, cofre e assim por diante) que estão sendo consultadas. Por exemplo, se você precisar obter uma lista de trabalhos de backup bem-sucedidos por nome de item de backup e seu contêiner associado, uma chamada simples para a função **_AzureBackup_getJobs ()** fornecerá todas essas informações para cada trabalho. Por outro lado, consultar as tabelas brutas diretamente exigiria que você executasse várias junções entre as tabelas [AddonAzureBackupJobs](./backup-azure-reports-data-model.md#addonazurebackupjobs) e [CoreAzureBackup](./backup-azure-reports-data-model.md#coreazurebackup) .

* **Transição mais suave do evento de diagnóstico herdado**: usar funções do sistema ajuda a fazer a transição tranqüila do [evento de diagnóstico herdado](./backup-azure-diagnostic-events.md#legacy-event) (AzureBackupReport no modo AzureDiagnostics) para os [eventos específicos do recurso](./backup-azure-diagnostic-events.md#diagnostics-events-available-for-azure-backup-users). Todas as funções do sistema fornecidas pelo backup do Azure permitem que você especifique um parâmetro que permite que você escolha se a função deve consultar dados somente das tabelas específicas do recurso ou consultar dados da tabela herdada e das tabelas específicas do recurso (com eliminação de duplicação de registros).
    * Se você tiver migrado com êxito para as tabelas específicas de recursos, poderá optar por excluir a tabela herdada de ser consultada pela função.
    * Se você estiver atualmente no processo de migração e tiver alguns dados nas tabelas herdadas que você precisa para análise, você pode optar por incluir a tabela herdada. Quando a transição for concluída e você não precisar mais de dados da tabela herdada, poderá simplesmente atualizar o valor do parâmetro passado para a função em suas consultas, para excluir a tabela herdada.
    * Se você ainda estiver usando apenas a tabela herdada, as funções continuarão a funcionar se você optar por incluir a tabela herdada por meio do mesmo parâmetro. No entanto, é recomendável [mudar para as tabelas específicas do recurso](./backup-azure-diagnostic-events.md#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace) no início.

* **Reduz a possibilidade de quebra de consultas personalizadas**: se o backup do Azure apresentar melhorias no esquema das tabelas de la subjacentes para acomodar cenários de relatório futuros, a definição das funções também será atualizada para levar em conta as alterações de esquema. Portanto, se você usar funções do sistema para criar consultas personalizadas, suas consultas não serão interrompidas, mesmo se houver alterações no esquema subjacente das tabelas.

> [!NOTE]
> As funções do sistema são mantidas pela Microsoft e suas definições não podem ser editadas pelos usuários. Se você precisar de funções editáveis, poderá criar [funções salvas](../azure-monitor/logs/functions.md) em la.

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Tipos de funções de sistema oferecidas pelo backup do Azure

* **Funções principais**: são funções que ajudam a consultar qualquer uma das principais entidades de backup do Azure, como instâncias de backup, cofres, políticas, trabalhos e entidades de cobrança. Por exemplo, a função **_AzureBackup_getBackupInstances** retorna uma lista de todas as instâncias de backup que existem no seu ambiente a partir do último dia concluído (em UTC). Os parâmetros e o esquema retornado para cada uma dessas funções principais são resumidos abaixo neste artigo.

* **Funções de tendência**: são funções que retornam registros históricos para suas entidades relacionadas ao backup (por exemplo, instâncias de backup, grupos de cobrança) e permitem obter informações de tendência diária, semanal e mensal em métricas-chave (por exemplo, contagem, armazenamento consumido) pertencentes a essas entidades. Os parâmetros e o esquema retornado para cada uma dessas funções de tendência são resumidos abaixo neste artigo.

> [!NOTE]
> Atualmente, as funções do sistema retornam dados para até o último dia concluído (em UTC). Os dados do dia parcial atual não são retornados. Portanto, se você estiver procurando recuperar registros para o dia atual, precisará usar as tabelas de LA não processada.


## <a name="list-of-system-functions"></a>Lista de funções do sistema

### <a name="core-functions"></a>Core Functions

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults ()

Essa função retorna a lista de todos os cofres de serviços de recuperação em seu ambiente do Azure que estão associados ao espaço de trabalho LA.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Obrigatório?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Use esse parâmetro junto com o parâmetro RangeEnd somente se precisar buscar todos os registros relacionados ao cofre no período de tempo de RangeStart para RangeEnd. Por padrão, o valor de RangeStart e RangeEnd é nulo, o que fará com que a função recupere somente o registro mais recente de cada cofre. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Use esse parâmetro junto com o parâmetro RangeStart somente se precisar buscar todos os registros relacionados ao cofre no período de tempo de RangeStart para RangeEnd. Por padrão, o valor de RangeStart e RangeEnd é nulo, o que fará com que a função recupere somente o registro mais recente de cada cofre. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de assinaturas em que existem dados de backup. A especificação de uma lista separada por vírgulas de IDs de assinatura como um parâmetro para essa função ajuda a recuperar somente os cofres que estão nas assinaturas especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as assinaturas. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de regiões em que existem dados de backup. A especificação de uma lista separada por vírgulas de regiões como um parâmetro para essa função ajuda a recuperar somente os cofres que estão nas regiões especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as regiões. | N | "lesteus, oesteus"|
| VaultList    |Use esse parâmetro para filtrar a saída da função para um determinado conjunto de cofres. A especificação de uma lista separada por vírgulas de nomes de cofre como um parâmetro para essa função ajuda a recuperar registros pertencentes apenas aos cofres especificados. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todos os cofres. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Use esse parâmetro para filtrar a saída da função para registros pertencentes a um tipo de cofre específico. Atualmente, o único tipo de cofre com suporte é "Microsoft. Recoveryservices/Vaults", que é o valor padrão desse parâmetro | N | "Microsoft. Recoveryservices/Vaults"|
| ExcludeLegacyEvent  | Use esse parâmetro para escolher se deseja consultar dados na tabela herdada AzureDiagnostics ou não. Se o valor desse parâmetro for false, a função consultará dados da tabela AzureDiagnostics e das tabelas específicas do recurso. Se o valor desse parâmetro for true, a função consultará dados somente de tabelas específicas do recurso. O valor padrão é true. | N | true |

**Campos retornados**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando a ID exclusiva do cofre |
| ID | Azure Resource Manager (ARM) ID do cofre |
| Name | Nome do cofre |
| SubscriptionId | ID da assinatura na qual o cofre existe |
| Localização | Local no qual o cofre existe |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento associado ao cofre |
| Marcações | Marcas do cofre |
| TimeGenerated | Carimbo de data/hora do registro |
| Tipo |  Tipo do cofre, que é "Microsoft. Recoveryservices/cofres"|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies ()

Essa função retorna a lista de políticas de backup que estão sendo usadas em seu ambiente do Azure juntamente com informações detalhadas sobre cada política, como o tipo de fonte de dados, o tipo de replicação de armazenamento e assim por diante.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Obrigatório?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Use esse parâmetro juntamente com o parâmetro RangeStart somente se precisar buscar todos os registros relacionados à política no período de tempo de RangeStart para RangeEnd. Por padrão, o valor de RangeStart e RangeEnd é nulo, o que fará com que a função recupere somente o registro mais recente de cada política. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Use esse parâmetro junto com o parâmetro RangeStart somente se precisar buscar todos os registros relacionados à política no período de tempo de RangeStart para RangeEnd. Por padrão, o valor de RangeStart e RangeEnd é nulo, o que fará com que a função recupere somente o registro mais recente de cada política. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de assinaturas em que existem dados de backup. A especificação de uma lista separada por vírgulas de IDs de assinatura como um parâmetro para essa função ajuda a recuperar somente as políticas que estão nas assinaturas especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as assinaturas. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de regiões em que existem dados de backup. A especificação de uma lista separada por vírgulas de regiões como um parâmetro para essa função ajuda a recuperar somente as políticas que estão nas regiões especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as regiões. | N | "lesteus, oesteus"|
| VaultList    |Use esse parâmetro para filtrar a saída da função para um determinado conjunto de cofres. A especificação de uma lista separada por vírgulas de nomes de cofre como um parâmetro para essa função ajuda a recuperar registros de políticas pertencentes apenas aos cofres especificados. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros de políticas em todos os cofres. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Use esse parâmetro para filtrar a saída da função para registros pertencentes a um tipo de cofre específico. Atualmente, o único tipo de cofre com suporte é "Microsoft. Recoveryservices/Vaults", que é o valor padrão desse parâmetro. | N | "Microsoft. Recoveryservices/Vaults"|
| ExcludeLegacyEvent  | Use esse parâmetro para escolher se deseja consultar dados na tabela herdada AzureDiagnostics ou não. Se o valor desse parâmetro for false, a função consultará dados da tabela AzureDiagnostics e das tabelas específicas do recurso. Se o valor desse parâmetro for true, a função consultará dados somente de tabelas específicas do recurso. O valor padrão é true. | N | true |
| BackupSolutionList | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de soluções de backup usadas em seu ambiente do Azure. Por exemplo, se você especificar "backup de máquina virtual do Azure, SQL no backup de VM do Azure, DPM" como o valor desse parâmetro, a função retornará apenas os registros relacionados aos itens de backup usando o backup de máquina virtual do Azure, o SQL no backup de VM do Azure ou o DPM no backup do Azure. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função retorne registros pertencentes a todas as soluções de backup com suporte nos relatórios de backup (os valores com suporte são "backup de máquina virtual do Azure", "SQL no backup de VM do Azure", "SAP HANA no backup de VM do Azure", "backup do Azure (arquivos do Azure)", "agente de backup do Azure", "DPM", "Servidor de Backup do Azure" ou uma combinação separada por vírgulas de qualquer um desses valores). | N | "Backup de máquina virtual do Azure, SQL no backup de VM do Azure, DPM, agente de backup do Azure" |

**Campos retornados**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando a ID exclusiva da política |
| ID | Azure Resource Manager (ARM) ID da política |
| Name | Nome da política |
| Solução de backup | Solução de backup à qual a política está associada. Por exemplo, backup de VM do Azure, SQL no backup de VM do Azure e assim por diante. |
| TimeGenerated | Carimbo de data/hora do registro |
| VaultUniqueId | Chave estrangeira que se refere ao cofre associado à política |
| VaultResourceId | Azure Resource Manager (ARM) ID do cofre associado à política |
| VaultName | Nome do cofre associado à política |
| VaultTags | Marcas do cofre associadas à política |
| VaultLocation | Local do cofre associado à política |
| VaultSubscriptionId | ID da assinatura do cofre associado à política |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento do cofre associado à política |
| Vaulttype | Tipo do cofre, que é "Microsoft. Recoveryservices/cofres" |
| ExtendedProperties | Propriedades adicionais da política |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs ()

Essa função retorna uma lista de todos os trabalhos relacionados a backup e restauração que foram disparados em um intervalo de tempo especificado, juntamente com informações detalhadas sobre cada trabalho, como status do trabalho, duração do trabalho, dados transferidos e assim por diante.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Obrigatório?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Use esse parâmetro junto com o parâmetro RangeEnd para recuperar a lista de todos os trabalhos que foram iniciados no período de RangeStart para RangeEnd. | S | "2021-03-03 00:00:00" |
| RangeEnd     | Use esse parâmetro junto com o parâmetro RangeStart para recuperar a lista de todos os trabalhos que foram iniciados no período de RangeStart para RangeEnd. | S |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de assinaturas em que existem dados de backup. A especificação de uma lista separada por vírgulas de IDs de assinatura como um parâmetro para essa função ajuda a recuperar somente os trabalhos associados aos cofres nas assinaturas especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as assinaturas. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de regiões em que existem dados de backup. A especificação de uma lista separada por vírgulas de regiões como um parâmetro para essa função ajuda a recuperar somente os trabalhos associados aos cofres nas regiões especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as regiões. | N | "lesteus, oesteus"|
| VaultList    | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de cofres. A especificação de uma lista separada por vírgulas de nomes de cofre como um parâmetro para essa função ajuda a recuperar trabalhos pertencentes apenas aos cofres especificados. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise trabalhos em todos os cofres. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Use esse parâmetro para filtrar a saída da função para registros pertencentes a um tipo de cofre específico. Atualmente, o único tipo de cofre com suporte é "Microsoft. Recoveryservices/Vaults", que é o valor padrão desse parâmetro. | N | "Microsoft. Recoveryservices/Vaults"|
| ExcludeLegacyEvent  | Use esse parâmetro para escolher se deseja consultar dados na tabela herdada AzureDiagnostics ou não. Se o valor desse parâmetro for false, a função consultará dados da tabela AzureDiagnostics e das tabelas específicas do recurso. Se o valor desse parâmetro for true, a função consultará dados somente de tabelas específicas do recurso. O valor padrão é true. | N | true |
| BackupSolutionList | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de soluções de backup usadas em seu ambiente do Azure. Por exemplo, se você especificar "backup de máquina virtual do Azure, SQL no backup de VM do Azure, DPM" como o valor desse parâmetro, a função retornará apenas os registros relacionados aos itens de backup usando o backup de máquina virtual do Azure, o SQL no backup de VM do Azure ou o DPM no backup do Azure. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função retorne registros pertencentes a todas as soluções de backup com suporte nos relatórios de backup (os valores com suporte são "backup de máquina virtual do Azure", "SQL no backup de VM do Azure", "SAP HANA no backup de VM do Azure", "backup do Azure (arquivos do Azure)", "agente de backup do Azure", "DPM", "Servidor de Backup do Azure" ou uma combinação separada por vírgulas de qualquer um desses valores). | N | "Backup de máquina virtual do Azure, SQL no backup de VM do Azure, DPM, agente de backup do Azure" |
| JobOperationList | Use esse parâmetro para filtrar a saída da função para um tipo específico de trabalho. Por exemplo, backup ou restauração. Por padrão, o valor desse parâmetro é "*", que faz com que a função pesquise trabalhos de backup e restauração. | N | Backup | 
| JobStatusList | Use esse parâmetro para filtrar a saída da função para um status de trabalho específico. Por exemplo, concluído, falha e assim por diante. Por padrão, o valor desse parâmetro é "*", que faz com que a função Pesquise todos os trabalhos, independentemente do status. | N | "Falha, CompletedWithWarnings" |
| JobFailureCodeList | Use esse parâmetro para filtrar a saída da função para um código de falha específico. Por padrão, o valor desse parâmetro é "*", que faz com que a função Pesquise todos os trabalhos, independentemente do código de falha. | N | "Success"
| DatasourceSetName | Use esse parâmetro para filtrar a saída da função para um recurso pai específico. Por exemplo, para retornar o SQL nas instâncias de backup de VM do Azure que pertencem à máquina virtual "TestVM", especifique _TestVM_ como o valor desse parâmetro. Por padrão, o valor é "*", que faz com que a função pesquise registros em todas as instâncias de backup. | N | TestVM |
| BackupInstanceName | Use esse parâmetro para procurar trabalhos em uma instância de backup específica por nome. Por padrão, o valor é "*", que faz com que a função pesquise registros em todas as instâncias de backup. | N | TestVM |
| ExcludeLog | Use esse parâmetro para excluir os trabalhos de log de serem retornados pela função (ajuda no desempenho da consulta). Por padrão, o valor desse parâmetro é true, o que faz com que a função exclua trabalhos de log. | N | true


**Campos retornados**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando a ID exclusiva do trabalho |
| OperationCategory | Categoria da operação que está sendo executada. Por exemplo, backup, restauração |
| Operação | Detalhes da operação que está sendo executada. Por exemplo, log (para backup de log)|
| Status | Status do trabalho. Por exemplo, concluído, falha, CompletedWithWarnings |
| ErrorTitle | Código de falha do trabalho |
| StartTime | Data e hora em que o trabalho foi iniciado |
| DurationInSecs | Duração do trabalho em segundos |
| DataTransferredInMBs | Dados transferidos pelo trabalho em MBs |
| RestoreJobRPDateTime | A data e a hora em que o ponto de recuperação que está sendo recuperado foi criado |
| RestoreJobRPLocation | O local onde o ponto de recuperação que está sendo recuperado foi armazenado |
| BackupInstanceUniqueId | Chave estrangeira que se refere à instância de backup associada ao trabalho |
| BackupInstanceId | Azure Resource Manager (ARM) ID da instância de backup associada ao trabalho |
| BackupInstanceFriendlyName | Nome da instância de backup associada ao trabalho |
| DatasourceResourceId | Azure Resource Manager (ARM) ID da fonte de base subjacente associada ao trabalho. Por exemplo, Azure Resource Manager (ARM) ID da VM |
| DatasourceFriendlyName | Nome amigável da fonte de base subjacente associada ao trabalho |
| DatasourceType | Tipo de fonte de origem associada ao trabalho. Por exemplo, "Microsoft. Compute/virtualMachines" |
| BackupSolution | Solução de backup à qual o trabalho está associado. Por exemplo, backup de VM do Azure, SQL no backup de VM do Azure e assim por diante. |
| DatasourceSetResourceId | Azure Resource Manager (ARM) ID do recurso pai da fonte de origem (sempre que aplicável). Por exemplo, para um SQL na fonte de dados da VM do Azure, esse campo conterá a ID do Azure Resource Manager (ARM) da VM na qual o banco de dados SQL existe |
| DatasourceSetType | Tipo do recurso pai da fonte de recursos (sempre que aplicável). Por exemplo, para um SAP HANA na fonte de dado da VM do Azure, esse campo será Microsoft. Compute/virtualMachines, já que o recurso pai é uma VM do Azure |
| VaultResourceId | Azure Resource Manager (ARM) ID do cofre associado ao trabalho |
| VaultUniqueId | Chave estrangeira que se refere ao cofre associado ao trabalho |
| VaultName | Nome do cofre associado ao trabalho |
| VaultTags | Marcas do cofre associadas ao trabalho |
| VaultSubscriptionId | ID da assinatura do cofre associado ao trabalho |
| VaultLocation | Local do cofre associado ao trabalho |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento do cofre associado ao trabalho |
| Vaulttype | Tipo do cofre, que é "Microsoft. Recoveryservices/cofres" |
| TimeGenerated | Carimbo de data/hora do registro |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances ()

Essa função retorna a lista de instâncias de backup associadas aos seus cofres dos serviços de recuperação, juntamente com informações detalhadas sobre cada instância de backup, como consumo de armazenamento em nuvem, política associada e assim por diante.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Obrigatório?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Use esse parâmetro junto com o parâmetro RangeEnd somente se você precisar buscar todos os registros relacionados à instância de backup no período de tempo de RangeStart para RangeEnd. Por padrão, o valor de RangeStart e RangeEnd é nulo, o que fará com que a função recupere somente o registro mais recente para cada instância de backup. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Use esse parâmetro junto com o parâmetro RangeStart somente se você precisar buscar todos os registros relacionados à instância de backup no período de tempo de RangeStart para RangeEnd. Por padrão, o valor de RangeStart e RangeEnd é nulo, o que fará com que a função recupere somente o registro mais recente para cada instância de backup. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de assinaturas em que existem dados de backup. A especificação de uma lista separada por vírgulas de IDs de assinatura como um parâmetro para essa função ajuda a recuperar somente as instâncias de backup que estão nas assinaturas especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as assinaturas. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de regiões em que existem dados de backup. A especificação de uma lista separada por vírgulas de regiões como um parâmetro para essa função ajuda a recuperar somente as instâncias de backup que estão nas regiões especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as regiões. | N | "lesteus, oesteus"|
| VaultList    |Use esse parâmetro para filtrar a saída da função para um determinado conjunto de cofres. A especificação de uma lista separada por vírgulas de nomes de cofre como um parâmetro para essa função ajuda a recuperar registros de instâncias de backup pertencentes apenas aos cofres especificados. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros de instâncias de backup em todos os cofres. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Use esse parâmetro para filtrar a saída da função para registros pertencentes a um tipo de cofre específico. Atualmente, o único tipo de cofre com suporte é "Microsoft. Recoveryservices/Vaults", que é o valor padrão desse parâmetro. | N | "Microsoft. Recoveryservices/Vaults"|
| ExcludeLegacyEvent  | Use esse parâmetro para escolher se deseja consultar dados na tabela herdada AzureDiagnostics ou não. Se o valor desse parâmetro for false, a função consultará dados da tabela AzureDiagnostics e das tabelas específicas do recurso. Se o valor desse parâmetro for true, a função consultará dados somente de tabelas específicas do recurso. O valor padrão é true. | N | true |
| BackupSolutionList | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de soluções de backup usadas em seu ambiente do Azure. Por exemplo, se você especificar "backup de máquina virtual do Azure, SQL no backup de VM do Azure, DPM" como o valor desse parâmetro, a função retornará apenas os registros relacionados aos itens de backup usando o backup de máquina virtual do Azure, o SQL no backup de VM do Azure ou o DPM no backup do Azure. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função retorne registros pertencentes a todas as soluções de backup com suporte nos relatórios de backup (os valores com suporte são "backup de máquina virtual do Azure", "SQL no backup de VM do Azure", "SAP HANA no backup de VM do Azure", "backup do Azure (arquivos do Azure)", "agente de backup do Azure", "DPM", "Servidor de Backup do Azure" ou uma combinação separada por vírgulas de qualquer um desses valores). | N | "Backup de máquina virtual do Azure, SQL no backup de VM do Azure, DPM, agente de backup do Azure" |
| ProtectionInfoList | Use esse parâmetro para escolher se deseja incluir somente as instâncias de backup que estão ativamente protegidas ou para incluir também as instâncias para as quais a proteção foi interrompida e as instâncias para as quais o backup inicial está pendente. Os valores com suporte são "protected", "ProtectionStopped", "InitialBackupPending" ou uma combinação separada por vírgulas de qualquer um desses valores. Por padrão, o valor é "*", que faz com que a função Pesquise todas as instâncias de backup, independentemente dos detalhes da proteção. | N | Protected |
| DatasourceSetName | Use esse parâmetro para filtrar a saída da função para um recurso pai específico. Por exemplo, para retornar o SQL nas instâncias de backup de VM do Azure que pertencem à máquina virtual "TestVM", especifique _TestVM_ como o valor desse parâmetro. Por padrão, o valor é "*", que faz com que a função pesquise registros em todas as instâncias de backup. | N | TestVM |
| BackupInstanceName | Use esse parâmetro para procurar uma instância de backup específica por nome. Por padrão, o valor é "*", que faz com que a função Pesquise todas as instâncias de backup. | N | TestVM |
| DisplayAllFields | Use esse parâmetro para escolher se deseja recuperar apenas um subconjunto dos campos retornados pela função. Se o valor desse parâmetro for false, a função eliminará as informações relacionadas ao armazenamento e ao ponto de retenção da saída da função. Isso será útil se você estiver usando essa função como uma etapa intermediária em uma consulta maior e precisar otimizar o desempenho da consulta, eliminando as colunas que você não precisa para análise. Por padrão, o valor desse parâmetro é true, o que faz com que a função retorne todos os campos pertencentes à instância de backup. | N | true |

**Campos retornados**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando a ID exclusiva da instância de backup |
| ID | Azure Resource Manager (ARM) ID da instância de backup |
| FriendlyName | Nome amigável da instância de backup |
| ProtectionInfo | Informações sobre as configurações de proteção da instância de backup. Por exemplo, proteção configurada, proteção interrompida, backup inicial pendente |
| LatestRecoveryPoint | Data e hora do último ponto de recuperação associado à instância de backup |
| OldestRecoveryPoint | Data e hora do ponto de recuperação mais antigo associado à instância de backup |
| SourceSizeInMBs | Tamanho de front-end da instância de backup em MBs |
| VaultStore_StorageConsumptionInMBs | Armazenamento em nuvem total consumido pela instância de backup no cofre-camada padrão |
| DataSourceFriendlyName | Nome amigável da fonte de fontes correspondente à instância de backup |
| BackupSolution | Solução de backup à qual a instância de backup está associada. Por exemplo, backup de VM do Azure, SQL no backup de VM do Azure e assim por diante. |
| DatasourceType | Tipo de DataSource correspondente à instância de backup. Por exemplo, "Microsoft. Compute/virtualMachines" |
| DatasourceResourceId | Azure Resource Manager (ARM) ID da fonte de base subjacente correspondente à instância de backup. Por exemplo, Azure Resource Manager (ARM) ID da VM |
| DatasourceSetFriendlyName | Nome amigável do recurso pai da fonte de recursos (sempre que aplicável). Por exemplo, para um SQL na fonte de dados da VM do Azure, esse campo conterá o nome da VM na qual o banco de dados SQL existe |
| DatasourceSetResourceId | Azure Resource Manager (ARM) ID do recurso pai da fonte de origem (sempre que aplicável). Por exemplo, para um SQL na fonte de dados da VM do Azure, esse campo conterá a ID do Azure Resource Manager (ARM) da VM na qual o banco de dados SQL existe |
| DatasourceSetType | Tipo do recurso pai da fonte de recursos (sempre que aplicável). Por exemplo, para um SAP HANA na fonte de dado da VM do Azure, esse campo será Microsoft. Compute/virtualMachines, já que o recurso pai é uma VM do Azure |
| PolicyName | Nome da política associada à instância de backup |
| PolicyUniqueId | Chave estrangeira que se refere à política associada à instância de backup  |
| PolicyId | Azure Resource Manager (ARM) ID da política associada à instância de backup |
| VaultResourceId | Azure Resource Manager (ARM) ID do cofre associado à instância de backup |
| VaultUniqueId | Chave estrangeira que se refere ao cofre associado à instância de backup |
| VaultName | Nome do cofre associado à instância de backup |
| VaultTags | Marcas do cofre associadas à instância de backup |
| VaultSubscriptionId | ID da assinatura do cofre associado à instância de backup |
| VaultLocation | Local do cofre associado à instância de backup |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento do cofre associado à instância de backup |
| Vaulttype | Tipo do cofre, que é "Microsoft. Recoveryservices/cofres" |
| TimeGenerated | Carimbo de data/hora do registro |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups ()

Essa função retorna uma lista de todas as entidades de cobrança relacionadas ao backup (grupos de cobrança), juntamente com informações sobre os principais componentes de cobrança, como tamanho de front-end e armazenamento em nuvem total.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Obrigatório?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Use esse parâmetro junto com o parâmetro RangeEnd somente se você precisar buscar todos os registros relacionados ao grupo de cobrança no período de tempo de RangeStart para RangeEnd. Por padrão, o valor de RangeStart e RangeEnd é nulo, o que fará com que a função recupere somente o registro mais recente de cada grupo de cobrança. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Use esse parâmetro junto com o parâmetro RangeStart somente se você precisar buscar todos os registros relacionados ao grupo de cobrança no período de tempo de RangeStart para RangeEnd. Por padrão, o valor de RangeStart e RangeEnd é nulo, o que fará com que a função recupere somente o registro mais recente de cada grupo de cobrança. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de assinaturas em que existem dados de backup. A especificação de uma lista separada por vírgulas de IDs de assinatura como um parâmetro para essa função ajuda a recuperar somente os grupos de cobrança que estão nas assinaturas especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as assinaturas. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de regiões em que existem dados de backup. A especificação de uma lista separada por vírgulas de regiões como um parâmetro para essa função ajuda a recuperar somente os grupos de cobrança que estão nas regiões especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as regiões. | N | "lesteus, oesteus"|
| VaultList    |Use esse parâmetro para filtrar a saída da função para um determinado conjunto de cofres. A especificação de uma lista separada por vírgulas de nomes de cofre como um parâmetro para essa função ajuda a recuperar registros de instâncias de backup pertencentes apenas aos cofres especificados. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros de grupos de cobrança em todos os cofres. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Use esse parâmetro para filtrar a saída da função para registros pertencentes a um tipo de cofre específico. Atualmente, o único tipo de cofre com suporte é "Microsoft. Recoveryservices/Vaults", que é o valor padrão desse parâmetro. | N | "Microsoft. Recoveryservices/Vaults"|
| ExcludeLegacyEvent  | Use esse parâmetro para escolher se deseja consultar dados na tabela herdada AzureDiagnostics ou não. Se o valor desse parâmetro for false, a função consultará dados da tabela AzureDiagnostics e das tabelas específicas do recurso. Se o valor desse parâmetro for true, a função consultará dados somente de tabelas específicas do recurso. O valor padrão é true. | N | true |
| BackupSolutionList | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de soluções de backup usadas em seu ambiente do Azure. Por exemplo, se você especificar "backup de máquina virtual do Azure, SQL no backup de VM do Azure, DPM" como o valor desse parâmetro, a função retornará apenas os registros relacionados aos itens de backup usando o backup de máquina virtual do Azure, o SQL no backup de VM do Azure ou o DPM no backup do Azure. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função retorne registros pertencentes a todas as soluções de backup com suporte nos relatórios de backup (os valores com suporte são "backup de máquina virtual do Azure", "SQL no backup de VM do Azure", "SAP HANA no backup de VM do Azure", "backup do Azure (arquivos do Azure)", "agente de backup do Azure", "DPM", "Servidor de Backup do Azure" ou uma combinação separada por vírgulas de qualquer um desses valores). | N | "Backup de máquina virtual do Azure, SQL no backup de VM do Azure, DPM, agente de backup do Azure" |
| BillingGroupName | Use esse parâmetro para procurar um grupo de cobrança específico por nome. Por padrão, o valor é "*", que faz com que a função Pesquise todos os grupos de cobrança. | N | TestVM |

**Campos retornados**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando a ID exclusiva do grupo de cobrança |
| FriendlyName | Nome amigável do grupo de cobrança |
| Name | Nome do grupo de cobrança |
| Tipo | Tipo de grupo de cobrança. Por exemplo, ProtectedContainer ou BackupItem |
| SourceSizeInMBs | Tamanho de front-end do grupo de cobrança em MBs |
| VaultStore_StorageConsumptionInMBs | Armazenamento em nuvem total consumido pelo grupo de cobrança no cofre-camada padrão |
| BackupSolution | Solução de backup à qual o grupo de cobrança está associado. Por exemplo, backup de VM do Azure, SQL no backup de VM do Azure e assim por diante. |
| VaultResourceId | Azure Resource Manager (ARM) ID do cofre associado ao grupo de cobrança |
| VaultUniqueId | Chave estrangeira que se refere ao cofre associado ao grupo de cobrança |
| VaultName | Nome do cofre associado ao grupo de cobrança |
| VaultTags | Marcas do cofre associadas ao grupo de cobrança |
| VaultSubscriptionId | ID da assinatura do cofre associado ao grupo de cobrança |
| VaultLocation | Local do cofre associado ao grupo de cobrança |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento do cofre associado ao grupo de cobrança |
| Vaulttype | Tipo do cofre, que é "Microsoft. Recoveryservices/cofres" |
| TimeGenerated | Carimbo de data/hora do registro |
| ExtendedProperties | Propriedades adicionais do grupo de cobrança |

### <a name="trend-functions"></a>Funções de tendência

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends ()

Essa função retorna registros históricos para cada instância de backup, permitindo que você exiba as principais tendências diárias, semanais e mensais relacionadas à contagem de instâncias de backup e ao consumo de armazenamento, em vários níveis de granularidade.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Obrigatório?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Use esse parâmetro junto com o parâmetro RangeEnd para recuperar todos os registros relacionados à instância de backup no período de tempo de RangeStart para RangeEnd. | S | "2021-03-03 00:00:00" |
| RangeEnd     | Use esse parâmetro junto com o parâmetro RangeStart para recuperar todos os registros relacionados à instância de backup no período de tempo de RangeStart para RangeEnd. | S |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de assinaturas em que existem dados de backup. A especificação de uma lista separada por vírgulas de IDs de assinatura como um parâmetro para essa função ajuda a recuperar somente as instâncias de backup que estão nas assinaturas especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as assinaturas. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de regiões em que existem dados de backup. A especificação de uma lista separada por vírgulas de regiões como um parâmetro para essa função ajuda a recuperar somente as instâncias de backup que estão nas regiões especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as regiões. | N | "lesteus, oesteus"|
| VaultList    |Use esse parâmetro para filtrar a saída da função para um determinado conjunto de cofres. A especificação de uma lista separada por vírgulas de nomes de cofre como um parâmetro para essa função ajuda a recuperar registros de instâncias de backup pertencentes apenas aos cofres especificados. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros de instâncias de backup em todos os cofres. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Use esse parâmetro para filtrar a saída da função para registros pertencentes a um tipo de cofre específico. Atualmente, o único tipo de cofre com suporte é "Microsoft. Recoveryservices/Vaults", que é o valor padrão desse parâmetro. | N | "Microsoft. Recoveryservices/Vaults"|
| ExcludeLegacyEvent  | Use esse parâmetro para escolher se deseja consultar dados na tabela herdada AzureDiagnostics ou não. Se o valor desse parâmetro for false, a função consultará dados da tabela AzureDiagnostics e das tabelas específicas do recurso. Se o valor desse parâmetro for true, a função consultará dados somente de tabelas específicas do recurso. O valor padrão é true. | N | true |
| BackupSolutionList | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de soluções de backup usadas em seu ambiente do Azure. Por exemplo, se você especificar "backup de máquina virtual do Azure, SQL no backup de VM do Azure, DPM" como o valor desse parâmetro, a função retornará apenas os registros relacionados aos itens de backup usando o backup de máquina virtual do Azure, o SQL no backup de VM do Azure ou o DPM no backup do Azure. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função retorne registros pertencentes a todas as soluções de backup com suporte nos relatórios de backup (os valores com suporte são "backup de máquina virtual do Azure", "SQL no backup de VM do Azure", "SAP HANA no backup de VM do Azure", "backup do Azure (arquivos do Azure)", "agente de backup do Azure", "DPM", "Servidor de Backup do Azure" ou uma combinação separada por vírgulas de qualquer um desses valores). | N | "Backup de máquina virtual do Azure, SQL no backup de VM do Azure, DPM, agente de backup do Azure" |
| ProtectionInfoList | Use esse parâmetro para escolher se deseja incluir somente as instâncias de backup que estão ativamente protegidas ou para incluir também as instâncias para as quais a proteção foi interrompida e as instâncias para as quais o backup inicial está pendente. Os valores com suporte são "protected", "ProtectionStopped", "InitialBackupPending" ou uma combinação separada por vírgulas de qualquer um desses valores. Por padrão, o valor é "*", que faz com que a função Pesquise todas as instâncias de backup, independentemente dos detalhes da proteção. | N | Protected |
| DatasourceSetName | Use esse parâmetro para filtrar a saída da função para um recurso pai específico. Por exemplo, para retornar o SQL nas instâncias de backup de VM do Azure que pertencem à máquina virtual "TestVM", especifique _TestVM_ como o valor desse parâmetro. Por padrão, o valor é "*", que faz com que a função pesquise registros em todas as instâncias de backup. | N | TestVM |
| BackupInstanceName | Use esse parâmetro para procurar uma instância de backup específica por nome. Por padrão, o valor é "*", que faz com que a função Pesquise todas as instâncias de backup. | N | TestVM |
| DisplayAllFields | Use esse parâmetro para escolher se deseja recuperar apenas um subconjunto dos campos retornados pela função. Se o valor desse parâmetro for false, a função eliminará as informações relacionadas ao armazenamento e ao ponto de retenção da saída da função. Isso será útil se você estiver usando essa função como uma etapa intermediária em uma consulta maior e precisar otimizar o desempenho da consulta, eliminando as colunas que você não precisa para análise. Por padrão, o valor desse parâmetro é true, o que faz com que a função retorne todos os campos pertencentes à instância de backup. | N | true |
| AggregationType | Use esse parâmetro para especificar a granularidade de tempo na qual os dados devem ser recuperados. Se o valor desse parâmetro for "Daily", a função retornará um registro por instância de backup por dia, permitindo que você analise as tendências diárias do consumo de armazenamento e da contagem de instâncias de backup. Se o valor desse parâmetro for "Weekly", a função retornará um registro por instância de backup por semana, permitindo que você analise as tendências semanais. Da mesma forma, você pode especificar "mensalmente" para analisar as tendências mensais. O valor padrão é "Daily". Se você estiver exibindo dados em intervalos de tempo maiores, é recomendável usar "semanalmente" ou "mensalmente" para melhor desempenho de consulta e facilidade de análise de tendência. | N | Quinzenal |

**Campos retornados**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando a ID exclusiva da instância de backup |
| ID | Azure Resource Manager (ARM) ID da instância de backup |
| FriendlyName | Nome amigável da instância de backup |
| ProtectionInfo | Informações sobre as configurações de proteção da instância de backup. Por exemplo, proteção configurada, proteção interrompida, backup inicial pendente |
| LatestRecoveryPoint | Data e hora do último ponto de recuperação associado à instância de backup |
| OldestRecoveryPoint | Data e hora do ponto de recuperação mais antigo associado à instância de backup |
| SourceSizeInMBs | Tamanho de front-end da instância de backup em MBs |
| VaultStore_StorageConsumptionInMBs | Armazenamento em nuvem total consumido pela instância de backup no cofre-camada padrão |
| DataSourceFriendlyName | Nome amigável da fonte de fontes correspondente à instância de backup |
| BackupSolution | Solução de backup à qual a instância de backup está associada. Por exemplo, backup de VM do Azure, SQL no backup de VM do Azure e assim por diante. |
| DatasourceType | Tipo de DataSource correspondente à instância de backup. Por exemplo, "Microsoft. Compute/virtualMachines" |
| DatasourceResourceId | Azure Resource Manager (ARM) ID da fonte de base subjacente correspondente à instância de backup. Por exemplo, Azure Resource Manager (ARM) ID da VM |
| DatasourceSetFriendlyName | Nome amigável do recurso pai da fonte de recursos (sempre que aplicável). Por exemplo, para um SQL na fonte de dados da VM do Azure, esse campo conterá o nome da VM na qual o banco de dados SQL existe |
| DatasourceSetResourceId | Azure Resource Manager (ARM) ID do recurso pai da fonte de origem (sempre que aplicável). Por exemplo, para um SQL na fonte de dados da VM do Azure, esse campo conterá a ID do Azure Resource Manager (ARM) da VM na qual o banco de dados SQL existe |
| DatasourceSetType | Tipo do recurso pai da fonte de recursos (sempre que aplicável). Por exemplo, para um SAP HANA na fonte de dado da VM do Azure, esse campo será Microsoft. Compute/virtualMachines, já que o recurso pai é uma VM do Azure |
| PolicyName | Nome da política associada à instância de backup |
| PolicyUniqueId | Chave estrangeira que se refere à política associada à instância de backup  |
| PolicyId | Azure Resource Manager (ARM) ID da política associada à instância de backup |
| VaultResourceId | Azure Resource Manager (ARM) ID do cofre associado à instância de backup |
| VaultUniqueId | Chave estrangeira que se refere ao cofre associado à instância de backup |
| VaultName | Nome do cofre associado à instância de backup |
| VaultTags | Marcas do cofre associadas à instância de backup |
| VaultSubscriptionId | ID da assinatura do cofre associado à instância de backup |
| VaultLocation | Local do cofre associado à instância de backup |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento do cofre associado à instância de backup |
| Vaulttype | Tipo do cofre, que é "Microsoft. Recoveryservices/cofres" |
| TimeGenerated | Carimbo de data/hora do registro |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends ()

Essa função retorna registros históricos para cada entidade de cobrança, permitindo que você veja as principais tendências diárias, semanais e mensais relacionadas ao tamanho de front-end e ao consumo de armazenamento, em vários níveis de granularidade.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Obrigatório?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Use esse parâmetro junto com o parâmetro RangeEnd para recuperar todos os registros relacionados ao grupo de cobrança no período de tempo de RangeStart para RangeEnd. | S | "2021-03-03 00:00:00" |
| RangeEnd     | Use esse parâmetro junto com o parâmetro RangeStart para recuperar todos os registros relacionados ao grupo de cobrança no período de tempo de RangeStart para RangeEnd. | S |"2021-03-10 00:00:00"|
| VaultSubscriptionList  | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de assinaturas em que existem dados de backup. A especificação de uma lista separada por vírgulas de IDs de assinatura como um parâmetro para essa função ajuda a recuperar somente os grupos de cobrança que estão nas assinaturas especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as assinaturas. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de regiões em que existem dados de backup. A especificação de uma lista separada por vírgulas de regiões como um parâmetro para essa função ajuda a recuperar somente os grupos de cobrança que estão nas regiões especificadas. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros em todas as regiões. | N | "lesteus, oesteus"|
| VaultList    |Use esse parâmetro para filtrar a saída da função para um determinado conjunto de cofres. A especificação de uma lista separada por vírgulas de nomes de cofre como um parâmetro para essa função ajuda a recuperar registros de instâncias de backup pertencentes apenas aos cofres especificados. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função pesquise registros de grupos de cobrança em todos os cofres. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Use esse parâmetro para filtrar a saída da função para registros pertencentes a um tipo de cofre específico. Atualmente, o único tipo de cofre com suporte é "Microsoft. Recoveryservices/Vaults", que é o valor padrão desse parâmetro. | N | "Microsoft. Recoveryservices/Vaults"|
| ExcludeLegacyEvent  | Use esse parâmetro para escolher se deseja consultar dados na tabela herdada AzureDiagnostics ou não. Se o valor desse parâmetro for false, a função consultará dados da tabela AzureDiagnostics e das tabelas específicas do recurso. Se o valor desse parâmetro for true, a função consultará dados somente de tabelas específicas do recurso. O valor padrão é true. | N | true |
| BackupSolutionList | Use esse parâmetro para filtrar a saída da função para um determinado conjunto de soluções de backup usadas em seu ambiente do Azure. Por exemplo, se você especificar "backup de máquina virtual do Azure, SQL no backup de VM do Azure, DPM" como o valor desse parâmetro, a função retornará apenas os registros relacionados aos itens de backup usando o backup de máquina virtual do Azure, o SQL no backup de VM do Azure ou o DPM no backup do Azure. Por padrão, o valor desse parâmetro é ' * ', que faz com que a função retorne registros pertencentes a todas as soluções de backup com suporte nos relatórios de backup (os valores com suporte são "backup de máquina virtual do Azure", "SQL no backup de VM do Azure", "SAP HANA no backup de VM do Azure", "backup do Azure (arquivos do Azure)", "agente de backup do Azure", "DPM", "Servidor de Backup do Azure" ou uma combinação separada por vírgulas de qualquer um desses valores). | N | "Backup de máquina virtual do Azure, SQL no backup de VM do Azure, DPM, agente de backup do Azure" |
| BillingGroupName | Use esse parâmetro para procurar um grupo de cobrança específico por nome. Por padrão, o valor é "*", que faz com que a função Pesquise todos os grupos de cobrança. | N | TestVM |
| AggregationType | Use esse parâmetro para especificar a granularidade de tempo na qual os dados devem ser recuperados. Se o valor desse parâmetro for "Daily", a função retornará um registro por grupo de cobrança por dia, permitindo que você analise as tendências diárias de consumo de armazenamento e tamanho de front-end. Se o valor desse parâmetro for "Weekly", a função retornará um registro por instância de backup por semana, permitindo que você analise as tendências semanais. Da mesma forma, você pode especificar "mensalmente" para analisar as tendências mensais. O valor padrão é "Daily". Se você estiver exibindo dados em intervalos de tempo maiores, é recomendável usar "semanalmente" ou "mensalmente" para melhor desempenho de consulta e facilidade de análise de tendência. | N | Quinzenal |

**Campos retornados**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando a ID exclusiva do grupo de cobrança |
| FriendlyName | Nome amigável do grupo de cobrança |
| Name | Nome do grupo de cobrança |
| Tipo | Tipo de grupo de cobrança. Por exemplo, ProtectedContainer ou BackupItem |
| SourceSizeInMBs | Tamanho de front-end do grupo de cobrança em MBs |
| VaultStore_StorageConsumptionInMBs | Armazenamento em nuvem total consumido pelo grupo de cobrança no cofre-camada padrão |
| BackupSolution | Solução de backup à qual o grupo de cobrança está associado. Por exemplo, backup de VM do Azure, SQL no backup de VM do Azure e assim por diante. |
| VaultResourceId | Azure Resource Manager (ARM) ID do cofre associado ao grupo de cobrança |
| VaultUniqueId | Chave estrangeira que se refere ao cofre associado ao grupo de cobrança |
| VaultName | Nome do cofre associado ao grupo de cobrança |
| VaultTags | Marcas do cofre associadas ao grupo de cobrança |
| VaultSubscriptionId | ID da assinatura do cofre associado ao grupo de cobrança |
| VaultLocation | Local do cofre associado ao grupo de cobrança |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento do cofre associado ao grupo de cobrança |
| Vaulttype | Tipo do cofre, que é "Microsoft. Recoveryservices/cofres" |
| TimeGenerated | Carimbo de data/hora do registro |
| ExtendedProperties | Propriedades adicionais do grupo de cobrança |

## <a name="sample-queries"></a>Consultas de exemplo

Abaixo estão algumas consultas de exemplo para ajudá-lo a começar a usar as funções do sistema.

- Todos os trabalhos de backup de VM do Azure com falha em um determinado intervalo de tempo

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Todos os trabalhos de backup de log SQL em um determinado intervalo de tempo

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Tendência semanal de armazenamento de backup consumida para a VM "TestVM"

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre os relatórios de backup](./configure-reports.md)