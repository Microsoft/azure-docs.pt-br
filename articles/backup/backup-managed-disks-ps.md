---
title: Fazer backup de Managed Disks do Azure usando Azure PowerShell
description: Saiba como fazer backup de Managed Disks do Azure usando Azure PowerShell.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2e286128185c1ac7fe4861a9b06de52e10d4139a
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630327"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Fazer backup de Managed Disks do Azure usando Azure PowerShell

Este artigo explica como fazer backup [do disco gerenciado do Azure](../virtual-machines/managed-disks-overview.md) usando o Azure PowerShell.

Neste artigo, você aprenderá a:

- Criar um cofre de backup

- Criar uma política de backup

- Configurar um backup de um disco do Azure

- Executar um trabalho de backup sob demanda

Para obter informações sobre a disponibilidade da região de backup em disco do Azure, cenários e limitações com suporte, consulte a [matriz de suporte](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Criar um cofre de backup

Um cofre de backup é uma entidade de armazenamento no Azure que mantém dados de backup para várias cargas de trabalho mais recentes com suporte no backup do Azure, como o banco de dados do Azure para servidores PostgreSQL e discos do Azure. Os cofres de backup facilitam a organização dos dados de backup, minimizando a sobrecarga de gerenciamento. Os cofres de backup são baseados no modelo de Azure Resource Manager do Azure, que fornece recursos avançados para ajudar a proteger os dados de backup.

Antes de criar um cofre de backup, escolha a redundância de armazenamento dos dados no cofre. Em seguida, continue a criar o cofre de backup com essa redundância de armazenamento e o local. Neste artigo, criaremos um cofre de backup "TestBkpVault" na região "westus" no grupo de recursos "testBkpVaultRG". Use o comando [New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) para criar um cofre de backup. Saiba mais sobre como [criar um cofre de backup](./backup-vault-overview.md#create-a-backup-vault).

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

Após a criação do cofre, vamos criar uma política de backup para proteger os discos do Azure.

## <a name="create-a-backup-policy"></a>Criar uma política de backup

Para entender os componentes internos de uma política de backup para o backup em disco do Azure, recupere o modelo de política usando o comando [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true). Esse comando retorna um modelo de política padrão para um determinado tipo de fonte de dados. Use este modelo de política para criar uma nova política.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

O modelo de política consiste em um gatilho (que decide o que dispara o backup) e um ciclo de vida (que decide quando excluir/copiar/mover o backup). No backup de disco do Azure, o valor padrão para o gatilho é um gatilho agendado por hora para cada 4 horas (PT4H) e para manter cada backup por 7 dias.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

O backup em disco do Azure oferece vários backups por dia. Se você precisar de backups mais frequentes, escolha a frequência de backup por **hora** com a capacidade de fazer backups com intervalos de cada 4, 6, 8 ou 12 horas. Os backups são agendados com base no intervalo de **tempo** selecionado. Por exemplo, se você selecionar a **cada 4 horas**, os backups serão feitos em aproximadamente no intervalo de cada 4 horas para que os backups sejam distribuídos igualmente ao longo do dia. Se um backup de um dia for suficiente, escolha a frequência de backup **diária** . Na frequência de backup diário, você pode especificar a hora do dia em que os backups são feitos. É importante observar que a hora do dia indica a hora de início do backup e não a hora em que o backup foi concluído. O tempo necessário para concluir a operação de backup depende de vários fatores, incluindo o tamanho do disco e a taxa de rotatividade entre backups consecutivos. No entanto, o backup em disco do Azure é um backup sem agente que usa [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md), o que não afeta o desempenho do aplicativo de produção.

   >[!NOTE]
   > Embora o cofre selecionado possa ter a configuração de redundância global, atualmente o backup em disco do Azure dá suporte apenas ao armazenamento de instantâneos de snapshot. Todos os backups são armazenados em um grupo de recursos em sua assinatura e não são copiados para o armazenamento do cofre de backup.

Para saber mais detalhes sobre a criação de políticas, consulte o documento [política de backup em disco do Azure](backup-managed-disks.md#create-backup-policy) .

Se você quiser editar a frequência por hora ou o período de retenção, use os comandos [Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) e/ou [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) . Depois que o objeto de política tiver todos os valores desejados, vá para criar uma nova política do objeto de política usando o [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true).

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>Configurar backup

Depois que o cofre e a política são criados, há três pontos críticos que o usuário precisa considerar para proteger um disco do Azure.

### <a name="key-entities-involved"></a>Entidades-chave envolvidas

#### <a name="disk-to-be-protected"></a>Disco a ser protegido

Busque a ID do ARM do disco a ser protegido. Isso funcionará como o identificador do disco. Usaremos um exemplo de um disco chamado "PSTestDisk" em um grupo de recursos "diskrg" em uma assinatura diferente.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>Grupo de recursos de instantâneo

Os instantâneos de disco são armazenados em um grupo de recursos em sua assinatura. Como uma diretriz, é recomendável criar um grupo de recursos dedicado como um repositório de armazenamento de instantâneo a ser usado pelo serviço de backup do Azure. Ter um grupo de recursos dedicado permite restringir as permissões de acesso no grupo de recursos, fornecendo segurança e facilidade de gerenciamento dos dados de backup. Observe a ID do ARM para o grupo de recursos em que você deseja inserir os instantâneos do disco

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Cofre de backup

Os cofres de backup exigem permissões em disco e o grupo de recursos de instantâneo para poder disparar instantâneos e gerenciar seu ciclo de vida. A identidade gerenciada atribuída pelo sistema do cofre é usada para atribuir essas permissões. Use o comando [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) para habilitar a identidade gerenciada atribuída pelo sistema para o cofre dos serviços de recuperação.

### <a name="assign-permissions"></a>Atribuir permissões

O usuário precisa atribuir algumas permissões via RBAC ao cofre (representado pelo MSI do cofre) e o disco relevante e/ou o RG do disco. Eles podem ser executados por meio do portal ou do PowerShell. Todas as permissões relacionadas são detalhadas nos pontos 1, 2, 3 nesta [seção](backup-managed-disks.md#configure-backup).

### <a name="prepare-the-request"></a>Preparar a solicitação

Depois que todas as permissões relevantes forem definidas, a configuração do backup será executada em duas etapas. Primeiro, preparamos a solicitação relevante usando o cofre, política, disco e grupo de recursos de instantâneos relevantes usando o comando [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) . Em seguida, enviamos a solicitação para proteger o disco usando o comando [New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Busque a instância de backup relevante na qual o usuário deseja disparar um backup usando [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true)

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Você pode especificar uma regra de retenção ao disparar o backup. Para exibir as regras de retenção na política, navegue pelo objeto de política para regras de retenção. No exemplo abaixo, a regra com o nome ' default ' é exibida e usaremos essa regra para o backup sob demanda

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

Dispare um backup sob demanda usando o comando [backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) .

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>Acompanhamento de trabalhos

Acompanhe todos os trabalhos usando o comando [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) . Você pode listar todos os trabalhos e buscar um detalhe de trabalho específico.

Você também pode usar AZ. ResourceGraph para acompanhar todos os trabalhos em todos os cofres de backup. Use o comando [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) para obter o trabalho relevante que pode estar em qualquer cofre de backup.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Próximas etapas

- [Restaurar Managed Disks do Azure usando Azure PowerShell](restore-managed-disks-ps.md)
