---
title: Fazer backup e recuperar VMs do Azure com o PowerShell
description: Descreve como fazer backup e recuperar VMs do Azure usando o backup do Azure com o PowerShell
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: f59c18aecf577bc7f7d0b1360dd36504305af893
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633165"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Fazer backup e restaurar VMs do Azure com o PowerShell

Este artigo explica como fazer backup e restaurar uma VM do Azure em um cofre de serviços de recuperação de [backup do Azure](backup-overview.md) usando cmdlets do PowerShell.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> * Crie um cofre dos Serviços de Recuperação e defina o contexto do cofre.
> * Definir uma política de backup
> * Aplicar a política de backup para proteger várias máquinas virtuais
> * Gatilho de um trabalho de backup sob demanda para as máquinas virtuais protegidas. Antes de você poder fazer backup (ou proteger) uma máquina virtual, você deve concluir os [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o ambiente para proteger suas VMs.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre os cofres dos serviços de recuperação.
* [Examine](backup-architecture.md#architecture-built-in-azure-vm-backup) a arquitetura do backup de VM do Azure, [saiba mais sobre](backup-azure-vms-introduction.md) o processo de backup e [examine](backup-support-matrix-iaas.md) o suporte, as limitações e os pré-requisitos.
* Examine a hierarquia de objetos do PowerShell para serviços de recuperação.

## <a name="recovery-services-object-hierarchy"></a>Hierarquia de objetos dos Serviços de Recuperação

A hierarquia de objetos é resumida no diagrama a seguir.

![Hierarquia de objetos dos Serviços de Recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Examine a referência de [referência do cmdlet](/powershell/module/az.recoveryservices/) **AZ. recoveryservices** na biblioteca do Azure.

## <a name="set-up-and-register"></a>Configurar e registrar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para começar:

1. [Baixe a versão mais recente do PowerShell](/powershell/azure/install-az-ps)

2. Localize os cmdlets do PowerShell do Backup do Azure disponíveis digitando o seguinte comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Os aliases e os cmdlets do Azure Backup, do Azure Site Recovery e do cofre do Recovery Services são exibidos. A imagem a seguir é um exemplo do que você verá. Não é a lista completa de cmdlets.

    ![lista de serviços de recuperação](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Entre em sua conta do Azure usando **Connect-AzAccount**. Esse cmdlet abre uma página da Web que solicita suas credenciais de conta:

    * Como alternativa, é possível incluir as credenciais de conta como um parâmetro no cmdlet **Connect-AzAccount**, usando o parâmetro **-Credential**.
    * Se você for um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário, usando seu nome de domínio de locatárioid ou de locatário primário. Por exemplo: **Connect-AzAccount-Tenant "fabrikam.com"**

4. Associe a assinatura que deseja usar com a conta, uma vez que uma conta pode ter várias assinaturas:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Se você estiver usando o backup do Azure pela primeira vez, deverá usar o cmdlet **[Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** para registrar o provedor de serviços de recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Você pode verificar se os provedores foram registrados com êxito usando os seguintes comandos:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    Na saída do comando, o **RegistrationState** deve mudar para **Registered**. Caso contrário, basta executar o cmdlet **[Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** novamente.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

As etapas a seguir orientarão você durante a criação de um cofre dos Serviços de Recuperação. Um cofre dos Serviços de Recuperação é diferente de um cofre de Backup.

1. O cofre dos Serviços de Recuperação é um recurso do Resource Manager e, portanto, você precisará colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um grupo de recursos com o cmdlet **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** . Ao criar um grupo de recursos, especifique o nome e o local para o grupo de recursos.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Use o cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) para criar um cofre dos Serviços de Recuperação. Lembre-se de especificar o mesmo local para o cofre usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Especifique o tipo de redundância de armazenamento a usar. Você pode usar o [LRS (armazenamento com redundância local)](../storage/common/storage-redundancy.md#locally-redundant-storage), o [grs (armazenamento com redundância geográfica)](../storage/common/storage-redundancy.md#geo-redundant-storage)ou o [ZRS (armazenamento com redundância de zona)](../storage/common/storage-redundancy.md#zone-redundant-storage). O exemplo a seguir mostra que a opção **-BackupStorageRedundancy** para o *testvault* está definida como **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Muitos cmdlets do Backup do Azure exigem o objeto do cofre dos Serviços de Recuperação como entrada. Por esse motivo, é conveniente armazenar o objeto de backup do cofre dos Serviços de Recuperação em uma variável.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Para exibir todos os cofres na assinatura, use [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante ao exemplo a seguir, observe que o ResourceGroupName e o local associado são fornecidos.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Fazer backup de VMs do Azure

Use um cofre dos Serviços de Recuperação para proteger as máquinas virtuais. Antes de aplicar a proteção, defina o contexto de cofre (o tipo de dados protegidos no cofre) e verifique a política de proteção. A política de proteção é a agenda de quando o trabalho de backup é executado e de quanto tempo cada instantâneo de backup é mantido.

### <a name="set-vault-context"></a>Definir o contexto de cofre

Antes de habilitar a proteção em uma VM, use [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) para definir o contexto do cofre. Depois que o contexto de cofre é definido, ele se aplica a todos os cmdlets subsequentes. O exemplo a seguir define o contexto de cofre para o cofre, *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Buscar a ID do cofre

Planejamos substituir a configuração de contexto do cofre de acordo com as diretrizes de Azure PowerShell. Em vez disso, você pode armazenar ou buscar a ID do cofre e passá-la para comandos relevantes. Portanto, se você não tiver definido o contexto do cofre ou quiser especificar o comando a ser executado para um determinado cofre, passe a ID do cofre como "-vaultid" para todo o comando relevante, da seguinte maneira:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Ou

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Modificando configurações de replicação de armazenamento

Use o comando [set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) para definir a configuração de replicação de armazenamento do cofre como LRS/grs

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> A Redundância do Armazenamento só poderá ser modificada se não houver itens de backup protegidos no cofre.

### <a name="create-a-protection-policy"></a>Crie uma política de proteção

Quando você cria um cofre dos Serviços de Recuperação, ele vem com proteção e políticas de retenção padrão. A política de proteção padrão dispara um trabalho de backup diariamente em um horário especificado. A política de retenção padrão retém o ponto de recuperação diário por 30 dias. Você pode usar a política padrão para proteger rapidamente sua VM e editá-la posteriormente com detalhes diferentes.

Use **[Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** para exibir as políticas de proteção disponíveis no cofre. Você pode usar este cmdlet para obter uma política específica ou para exibir as políticas associadas a um tipo de carga de trabalho. O exemplo a seguir obtém as políticas para o tipo de carga de trabalho, AzureVM.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

A saída deverá ser semelhante ao seguinte exemplo:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> O fuso horário do campo BackupTime no PowerShell é UTC. No entanto, quando o tempo de backup é mostrado no Portal do Azure, o horário é ajustado para seu fuso horário local.
>
>

Uma política de proteção de backup está associada a pelo menos uma política de retenção. Uma política de retenção define por quanto tempo um ponto de recuperação é mantido até ser excluído.

* Use [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) para exibir a política de retenção padrão.
* Da mesma forma, você pode usar [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) para obter a política de agendamento padrão.
* O cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) cria um objeto do PowerShell que mantém as informações da política de backup.
* Os objetos de política de retenção e agendamento são usados como entradas para o cmdlet New-AzRecoveryServicesBackupProtectionPolicy.

Por padrão, uma hora de início é definida no objeto de política de agenda. Use o exemplo a seguir para alterar a hora de início para a hora de início desejada. A hora de início desejada também deve estar em UTC. O exemplo a seguir pressupõe que a hora de início desejada seja 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Você precisa fornecer a hora de início apenas em 30 minutos. No exemplo acima, ele pode ser apenas "01:00:00" ou "02:30:00". A hora de início não pode ser "01:15:00"

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. O exemplo usa essas variáveis para definir os parâmetros ao criar uma política de proteção, *NewPolicy*.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

A saída deverá ser semelhante ao seguinte exemplo:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Habilitar proteção

Depois de definir a política de proteção, você deve habilitar a política para um item. Use [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) para habilitar a proteção. Habilitar a proteção envolve dois objetos – o item e a política. Depois de a política ter sido associada ao cofre, o fluxo de trabalho de backup será disparado no momento definido no agendamento da política.

> [!IMPORTANT]
> Ao usar o PowerShell para habilitar o backup para várias VMs de uma vez, verifique se uma única política não tem mais de 100 VMs associadas a ela. Essa é uma [melhor prática recomendada](./backup-azure-vm-backup-faq.yml#is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy). Atualmente, o cliente PowerShell não bloqueia explicitamente se há mais de 100 VMs, mas há planos de adicionar essa verificação no futuro.

Os exemplos a seguir permitem a proteção do item, V2VM, usando a política NewPolicy. Os exemplos diferem com base em se a VM está criptografada e em qual tipo de criptografia.

Para ativar a proteção em **VMs do Gerenciador de Recursos não criptografadas**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Para habilitar a proteção em VMs criptografadas (criptografadas usando BEK e KEK), você deve conceder a permissão de serviço de Backup do Azure para ler as chaves e segredos do Cofre de chaves.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Para habilitar a proteção em **VMs criptografadas (criptografadas usando apenas BEK)**, você deve conceder a permissão de serviço de Backup do Azure para ler segredos do Cofre de chaves.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Se você estiver usando a nuvem do Azure governamental, use o valor `ff281ffe-705c-4f53-9f37-a40e6f2c68f3` para o parâmetro **servicePrincipalName** no cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .
>

Se você quiser fazer backup seletivo de alguns discos e excluir outros, conforme mencionado nesses [cenários](selective-disk-backup-restore.md#scenarios), poderá configurar a proteção e fazer backup somente dos discos relevantes, conforme documentado [aqui](selective-disk-backup-restore.md#enable-backup-with-powershell).

## <a name="monitoring-a-backup-job"></a>Monitoramento de um trabalho de backup

Você pode monitorar operações de longa duração, como trabalhos de backup, sem usar o Portal do Azure. Para obter o status de um trabalho em andamento, use o cmdlet [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) . Esse cmdlet obtém os trabalhos de backup para um cofre específico, o qual está especificado no contexto de cofre. O exemplo a seguir obtém o status de um trabalho em andamento como uma matriz e armazena o status na variável $joblist.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

A saída deverá ser semelhante ao seguinte exemplo:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Em vez de sondar esses trabalhos para conclusão – que é um código adicional desnecessário, use o cmdlet [Wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) . Esse cmdlet pausa a execução até que o trabalho seja concluído ou o valor de tempo limite especificado seja atingido.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Gerenciar backups de VM do Azure

### <a name="modify-a-protection-policy"></a>Modificar uma política de proteção

Para modificar a política de proteção, use [set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) para modificar os objetos SchedulePolicy ou RetentionPolicy.

#### <a name="modifying-scheduled-time"></a>Modificando a hora agendada

Quando você cria uma política de proteção, ela recebe uma hora de início por padrão. Os exemplos a seguir mostram como modificar a hora de início de uma política de proteção.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that you want to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Modificando a retenção

O exemplo a seguir altera retenção de ponto de recuperação para 365 dias.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Configurando a retenção de instantâneo de restauração instantânea

> [!NOTE]
> Do Azure PowerShell versão 1.6.0 em diante, é possível atualizar o período de retenção do instantâneo de restauração instantânea na política usando o PowerShell

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

O valor padrão será 2. Você pode definir o valor com um mínimo de 1 e o máximo de 5. Para políticas de backup semanais, o período é definido como 5 e não pode ser alterado.

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>Criando o grupo de recursos de backup do Azure durante a retenção de instantâneo

> [!NOTE]
> Do Azure PowerShell versão 3.7.0 em diante, é possível criar e editar o grupo de recursos criado para armazenar instantâneos instantâneos.

Para saber mais sobre as regras de criação do grupo de recursos e outros detalhes relevantes, consulte a documentação [grupo de recursos do backup do Azure para máquinas virtuais](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines) .

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="exclude-disks-for-a-protected-vm"></a>Excluir discos de uma VM protegida

O backup de VM do Azure fornece um recurso para excluir seletivamente ou incluir discos que são úteis nesses [cenários](selective-disk-backup-restore.md#scenarios). Se a máquina virtual já estiver protegida pelo backup de VM do Azure e se for feito o backup de todos os discos, você poderá modificar a proteção para incluir ou excluir seletivamente os discos, conforme mencionado [aqui](selective-disk-backup-restore.md#modify-protection-for-already-backed-up-vms-with-powershell).

### <a name="trigger-a-backup"></a>Disparar um backup

Use [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) para disparar um trabalho de backup. Se for o backup inicial, ele é um backup completo. Os backups posteriores fazem uma cópia incremental. O exemplo a seguir usa um backup de VM para ser retido por 60 dias.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

A saída deverá ser semelhante ao seguinte exemplo:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> O fuso horário dos campos StartTime e EndTime mostrado no PowerShell é UTC. No entanto, quando a hora é exibida no Portal do Azure, ela é ajustada para seu fuso horário local.
>
>

### <a name="change-policy-for-backup-items"></a>Alterar política para itens de backup

Você pode modificar a política existente ou alterar a política do item de backup de Policy1 para Policy2. Para alternar políticas para um item de backup, busque a política relevante e faça backup do item e use o comando [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) com o item de backup como o parâmetro.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

O comando aguarda até que o backup de configuração seja concluído e retorna a saída a seguir.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Parar a proteção

#### <a name="retain-data"></a>Reter dados

Se você quiser interromper a proteção, poderá usar o cmdlet [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) do PowerShell. Isso interromperá os backups agendados, mas os dados submetidos a backup até agora são mantidos para sempre.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Excluir dados de backup

Para remover completamente os dados de backup armazenados no cofre, adicione o sinalizador '-RemoveRecoveryPoints '/alterne para o [comando de proteção ' Disable '](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Restaurar uma VM do Azure

Há uma diferença importante entre a restauração de uma VM usando o portal do Azure e a restauração de uma VM usando o PowerShell. Com o PowerShell, a operação de restauração é concluída quando são criadas as informações dos discos e de configuração do ponto de recuperação. A operação de restauração não cria a máquina virtual. Para criar uma máquina virtual com base no disco, consulte a seção [Criar a VM com base em discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Se você não quiser restaurar toda a VM, mas deseja restaurar ou recuperar alguns arquivos de um backup de VM do Azure, consulte a [seção de recuperação de arquivo](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> A operação de restauração não cria a máquina virtual.
>
>

O gráfico a seguir mostra a hierarquia de objetos do RecoveryServicesVault até o BackupRecoveryPoint.

![Hierarquia de objetos dos Serviços de Recuperação mostrando BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Para restaurar dados de backup, identifique o item de backup e o ponto de recuperação que mantém os dados pontuais. Use [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para restaurar dados do cofre para sua conta.

As etapas básicas para restaurar uma VM do Azure são:

* Selecione a VM.
* Escolha um ponto de recuperação.
* Restaure os discos.
* Crie a VM de discos armazenados.

### <a name="select-the-vm-when-restoring-files"></a>Selecionar a VM (ao restaurar arquivos)

Para obter o objeto do PowerShell que identifica o item correto de backup, comece do contêiner no cofre e desça progressivamente na hierarquia de objetos. Para selecionar o contêiner que representa a VM, use o cmdlet [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) e redirecione-o para o cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-files"></a>Escolher um ponto de recuperação (ao restaurar arquivos)

Use o cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) para listar todos os pontos de recuperação para o item de backup. Em seguida, escolha o ponto de recuperação a ser restaurado. Se você não tiver certeza de qual ponto de recuperação usar, é uma boa prática escolher o ponto RecoveryPointType = AppConsistent mais recente na lista.

No script a seguir, a variável, **$RP**, é uma matriz de pontos de recuperação para o item de backup selecionado, dos últimos sete dias. A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice 0. Use a indexação de matriz padrão do PowerShell para selecionar o ponto de recuperação. No exemplo, $rp[0] seleciona o último ponto de recuperação.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

A saída deverá ser semelhante ao seguinte exemplo:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Restaure os discos

Use o cmdlet [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para restaurar os dados e a configuração de um item de backup para um ponto de recuperação. Depois de identificar um ponto de recuperação, use-o como o valor para o parâmetro **-RecoveryPoint**. No exemplo acima, **$RP [0]** foi o ponto de recuperação a ser usado. No código de exemplo abaixo, **$rp [0]** é o ponto de recuperação a ser usado para a restauração do disco.

Para restaurar as informações de discos e de configuração:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Restaurar discos gerenciados

> [!NOTE]
> Se a VM com suporte tiver discos gerenciados e você quiser restaurá-los como discos gerenciados, apresentamos a capacidade de Azure PowerShell módulo do RM v 6.7.0. em diante.
>
>

Forneça um parâmetro adicional **TargetResourceGroupName** para especificar o RG para o qual os discos gerenciados serão restaurados.

> [!IMPORTANT]
> É altamente recomendável usar o parâmetro **TargetResourceGroupName** para restaurar discos gerenciados, pois isso resulta em melhorias significativas de desempenho. Se esse parâmetro não for fornecido, você não poderá se beneficiar da funcionalidade de restauração instantânea e a operação de restauração será mais lenta em comparação. Se a finalidade for restaurar discos gerenciados como discos não gerenciados, não forneça esse parâmetro e torne a intenção clara fornecendo o `-RestoreAsUnmanagedDisks` parâmetro. O `-RestoreAsUnmanagedDisks` parâmetro está disponível de Azure PowerShell 3.7.0 em diante. Em versões futuras, será obrigatório fornecer qualquer um desses parâmetros para a experiência de restauração correta.
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

O arquivo **VMConfig.JSON** será restaurado para a conta de armazenamento, e os discos gerenciados serão restaurados para o RG de destino especificado.

A saída deverá ser semelhante ao seguinte exemplo:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Use o cmdlet [Wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) para aguardar a conclusão do trabalho de restauração.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Depois que o trabalho de restauração for concluído, use o cmdlet [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) para obter os detalhes da operação de restauração. A propriedade JobDetails tem as informações necessárias para recompilar a VM.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

#### <a name="restore-selective-disks"></a>Restaurar discos seletivos

Um usuário pode restaurar seletivamente alguns discos em vez do conjunto de backup completo. Forneça os LUNs de disco necessários como parâmetro para restaurá-los somente em vez de todo o conjunto, conforme documentado [aqui](selective-disk-backup-restore.md#restore-selective-disks-with-powershell).

> [!IMPORTANT]
> Uma delas tem que fazer backup de discos seletivamente para restaurar discos seletivamente. Mais detalhes são fornecidos [aqui](selective-disk-backup-restore.md#selective-disk-restore).

Depois de restaurar os discos, vá para a próxima seção para criar a VM.

#### <a name="restore-disks-to-a-secondary-region"></a>Restaurar discos para uma região secundária

Se a restauração entre regiões estiver habilitada no cofre com o qual você protegeu suas VMs, os dados de backup serão replicados para a região secundária. Você pode usar os dados de backup para executar uma restauração. Execute as seguintes etapas para disparar uma restauração na região secundária:

1. [Busque a ID do cofre](#fetch-the-vault-id) com a qual suas VMs estão protegidas.
1. Selecione o [item de backup correto a ser restaurado](#select-the-vm-when-restoring-files).
1. Selecione o ponto de recuperação apropriado na região secundária que você deseja usar para executar a restauração.

    Para concluir esta etapa, execute este comando:

    ```powershell
    $rp=Get-AzRecoveryServicesBackupRecoveryPoint -UseSecondaryRegion -Item $backupitem -VaultId $targetVault.ID
    $rp=$rp[0]
    ```

1. Execute o cmdlet [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) com o `-RestoreToSecondaryRegion` parâmetro para disparar uma restauração na região secundária.

    Para concluir esta etapa, execute este comando:

    ```powershell
    $restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -VaultLocation $targetVault.Location -RestoreToSecondaryRegion -RestoreOnlyOSDisk
    ```

    A saída será semelhante ao exemplo seguinte:

    ```output
    WorkloadName     Operation             Status              StartTime                 EndTime          JobID
    ------------     ---------             ------              ---------                 -------          ----------
    V2VM             CrossRegionRestore   InProgress           4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
    ```

1. Execute o cmdlet [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) com o `-UseSecondaryRegion` parâmetro para monitorar o trabalho de restauração.

    Para concluir esta etapa, execute este comando:

    ```powershell
    Get-AzRecoveryServicesBackupJob -From (Get-Date).AddDays(-7).ToUniversalTime() -To (Get-Date).ToUniversalTime() -UseSecondaryRegion -VaultId $targetVault.ID
    ```

    A saída será semelhante ao exemplo seguinte:

    ```output
    WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
    ------------     ---------            ------               ---------                 -------                   -----
    V2VM             CrossRegionRestore   InProgress           2/8/2021 4:24:57 PM                                 2d071b07-8f7c-4368-bc39-98c7fb2983f7
    ```

## <a name="replace-disks-in-azure-vm"></a>Substituir discos na VM do Azure

Para substituir os discos e as informações de configuração, execute as seguintes etapas:

* Etapa 1: [restaurar os discos](backup-azure-vms-automation.md#restore-the-disks)
* Etapa 2: [desanexar o disco de dados usando o PowerShell](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-powershell)
* Etapa 3: [anexar o disco de dados à VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md)

## <a name="create-a-vm-from-restored-disks"></a>Criar uma máquina virtual de discos restaurados

Depois de restaurar os discos, use as seguintes etapas para criar e configurar a máquina virtual do disco.

> [!NOTE]
>
> 1. O módulo AzureAz 3.0.0 ou superior é necessário. <br>
> 2. Para criar VMs criptografadas de discos restaurados, a função do Azure deverá ter permissão para executar a ação **Microsoft.KeyVault/vaults/deploy/action**. Se sua função não tiver essa permissão, crie uma função personalizada com essa ação. Para obter mais informações, consulte [funções personalizadas do Azure](../role-based-access-control/custom-roles.md). <br>
> 3. Após a restauração dos discos, você pode obter um modelo de implantação para criar diretamente uma nova VM. Você não precisa de cmdlets diferentes do PowerShell para criar VMs gerenciadas/não gerenciadas que são criptografadas/descriptografadas.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Criar uma VM usando o modelo de implantação

Os detalhes do trabalho resultante fornecem o URI do modelo, o qual pode ser consultado e implantado.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

O modelo não pode ser acessado diretamente, pois está sob a conta de armazenamento de um cliente e o contêiner fornecido. Precisamos da URL completa (junto com um token SAS temporário) para acessar esse modelo.

1. Primeiro extraia o nome do modelo do templateBlobURI. O formato é mencionado abaixo. Você pode usar a operação Split no PowerShell para extrair o nome do modelo final desta URL.

    ```http
    https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
    ```

2. Em seguida, a URL completa pode ser gerada conforme explicado [aqui](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-powershell#provide-sas-token-during-deployment).

    ```powershell
    Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
    $templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
    ```

3. Implante o modelo para criar uma nova VM, conforme explicado [aqui](../azure-resource-manager/templates/deploy-powershell.md).

    ```powershell
    New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI
    ```

### <a name="create-a-vm-using-the-config-file"></a>Criar uma VM usando o arquivo de configuração

A seção a seguir lista as etapas necessárias para criar uma VM usando o arquivo "VMConfig".

> [!NOTE]
> É altamente recomendável usar o modelo de implantação detalhado acima para criar uma VM. Esta seção (pontos 1 a 6) será preterida em breve.

1. Consulte as propriedades do disco restaurado para obter os detalhes do trabalho.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Defina o contexto do armazenamento do Azure e restaure o arquivo de configuração JSON.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. Use o arquivo de configuração JSON para criar a configuração da VM.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Anexe o disco do sistema operacional e os discos de dados. Esta etapa fornece exemplos para várias configurações de VM gerenciadas e criptografadas. Use o exemplo que atenda à sua configuração de VM.

    * **VMs não gerenciadas e não criptografadas** - use a amostra a seguir para VMs não gerenciadas e não criptografadas.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
            $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **VMs não gerenciadas e criptografadas com o Azure AD (somente BEK)** – para VMs não gerenciadas e criptografadas com o Azure AD (criptografadas usando apenas BEK), é necessário restaurar o segredo do cofre de chaves antes de poder conectar discos. Para obter mais informações, confira [Restore an encrypted virtual machine from an Azure Backup recovery point](backup-azure-restore-key-secret.md) (Restaurar uma máquina virtual criptografada de um ponto de recuperação do Backup do Azure). O exemplo a seguir mostra como anexar discos de dados e de SO a VMs criptografadas. Ao definir o disco do sistema operacional, certifique-se de mencionar o tipo de sistema operacional relevante.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **VMs não gerenciadas e criptografadas com o Azure AD (BEK e KEK)** – para VMs não gerenciadas e criptografadas com o Azure AD (criptografadas usando BEK e KEK), restaure a chave e o segredo para o cofre de chaves antes de anexar os discos. Para obter mais informações, confira [Restore an encrypted virtual machine from an Azure Backup recovery point (Restaurar uma máquina virtual criptografada de um ponto de recuperação do Backup do Azure)](backup-azure-restore-key-secret.md). O exemplo a seguir mostra como anexar discos de dados e de SO a VMs criptografadas.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **VMs criptografadas e não gerenciadas sem o Azure AD (apenas BEK)** – para VMs criptografadas não gerenciadas sem o Azure AD (criptografadas usando apenas BEK), se **keyVault/segredo de origem não estiverem disponíveis**, restaure os segredos para o cofre de chaves usando o procedimento em [Restore an non-encrypted virtual machine from an Azure Backup recovery point (Restaurar uma máquina virtual não criptografada de um ponto de recuperação do Backup do Azure)](backup-azure-restore-key-secret.md). Em seguida, execute os scripts a seguir para definir detalhes de criptografia no blob restaurado do sistema operacional (essa etapa não é necessária para um blob de dados). O $dekurl pode ser buscado do keyVault restaurado.

    O script a seguir precisa ser executado somente quando o keyvault/segredo de origem não estiver disponível.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    Depois que os **segredos forem disponibilizados** e os detalhes de criptografia também estiverem definidos no blob do sistema operacional, anexe os discos usando o script abaixo.

    Se o keyvault/segredos de origem já estiverem disponíveis, o script acima não precisará ser executado.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **VMs criptografadas e não gerenciadas sem o Azure AD (BEK e KEK)** – para VMs criptografadas não gerenciadas sem o Azure AD (criptografadas usando BEK e KEK), se **keyVault/chave/segredo de origem não estiverem disponíveis**, restaure a chave e os segredos para o cofre de chaves usando o procedimento em [Restore an non-encrypted virtual machine from an Azure Backup recovery point (Restaurar uma máquina virtual não criptografada de um ponto de recuperação do Backup do Azure)](backup-azure-restore-key-secret.md). Em seguida, execute os scripts a seguir para definir detalhes de criptografia no blob restaurado do sistema operacional (essa etapa não é necessária para um blob de dados). O $dekurl e $kekurl podem ser buscados do keyVault restaurado.

    O script a seguir precisa ser executado somente quando o keyvault/chave/segredo de origem não estiver disponível.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    Depois que **chave/segredos forem disponibilizados** e os detalhes de criptografia estiverem definidos no blob do sistema operacional, anexe os discos usando o script abaixo.

    Se o keyvault/chave/segredos de origem estiverem disponíveis, o script acima não precisará ser executado.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **VMs gerenciadas e não criptografadas** ‒ para as VMs não criptografadas gerenciadas, anexe os discos gerenciados restaurados. Para obter informações detalhadas, confira [Anexar um disco de dados a uma VM do Windows usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **VMs gerenciadas e criptografadas com o Azure AD (apenas BEK)** ‒ para VMs criptografadas gerenciadas com o Azure AD (criptografadas usando apenas BEK), anexe os discos gerenciados restaurados. Para obter informações detalhadas, confira [Anexar um disco de dados a uma VM do Windows usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **VMs gerenciadas e criptografadas com o Azure AD (BEK e KEK)** ‒ para VMs criptografadas gerenciadas com o Azure AD (criptografadas usando BEK e KEK), anexe os discos gerenciados restaurados. Para obter informações detalhadas, confira [Anexar um disco de dados a uma VM do Windows usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **VMs gerenciadas e criptografadas sem o AD do Azure (somente Bek)** – para VMs criptografadas e gerenciadas sem o Azure AD (criptografado usando apenas Bek), se o **keyvault/segredo de origem não estiver disponível** , restaure os segredos para o Key Vault usando o procedimento em [restaurar uma máquina virtual não criptografada de um ponto de recuperação de backup do Azure](backup-azure-restore-key-secret.md). Em seguida, execute os scripts a seguir para definir detalhes de criptografia no disco do sistema operacional restaurado (essa etapa não é necessária para um disco de dados). O $dekurl pode ser buscado do keyVault restaurado.

    O script a seguir precisa ser executado somente quando o keyvault/segredo de origem não estiver disponível.  

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Depois que os segredos estiverem disponíveis e os detalhes da criptografia forem definidos no disco do sistema operacional, para anexar os discos gerenciados restaurados, confira [Anexar um disco de dados a uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

    * **VMs gerenciadas e criptografadas sem o Azure AD (Bek e Kek)** – para VMs criptografadas e gerenciadas sem o Azure AD (criptografado usando Bek & Kek), se o **keyvault de origem/chave/segredo não estiverem disponíveis** , restaure a chave e os segredos para o Key Vault usando o procedimento em [restaurar uma máquina virtual não criptografada de um ponto de recuperação do backup](backup-azure-restore-key-secret.md) Em seguida, execute os scripts a seguir para definir detalhes de criptografia no disco do sistema operacional restaurado (essa etapa não é necessária para discos de dados). O $dekurl e $kekurl podem ser buscados do keyVault restaurado.

    O script a seguir precisa ser executado somente quando o keyvault/chave/segredo de origem não estiver disponível.

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Depois que a chave/segredos estiverem disponíveis e os detalhes da criptografia forem definidos no disco do sistema operacional, para anexar os discos gerenciados restaurados, confira [Anexar um disco de dados a uma VM do Windows com o PowerShell](../virtual-machines/windows/attach-disk-ps.md).

5. Defina as configurações de Rede.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Crie a máquina virtual.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Enviar por push extensão ADE.
   Se as extensões de ADE não forem enviadas por push, os discos de dados serão marcados como não criptografados, portanto, é obrigatório que as etapas abaixo sejam executadas:

   * **Para VM com Azure AD** – Use o seguinte comando para habilitar manualmente a criptografia dos discos de dados  

     **Somente BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK e KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Para VM sem Azure AD** – Use o seguinte comando para habilitar manualmente a criptografia dos discos de dados.

     Se durante a execução do comando solicitar AADClientID, você precisará atualizar seu Azure PowerShell.

     **Somente BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK e KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Certifique-se de excluir manualmente os arquivos JASON criados como parte do processo de disco de restauração de VM criptografado.

## <a name="restore-files-from-an-azure-vm-backup"></a>Restaurar arquivos de um backup de VM do Azure

Além de restauração de discos, você também pode restaurar arquivos individuais de um backup de VM do Azure. A funcionalidade de restauração de arquivos fornece acesso a todos os arquivos em um ponto de recuperação. Gerencie os arquivos via File Explorer como faria para arquivos normais.

As etapas básicas para restaurar um arquivo de um backup de VM do Azure são:

* Selecione a VM
* Escolha um ponto de recuperação
* Monte os discos do ponto de recuperação
* Copie os arquivos necessários
* Desmonte o disco

### <a name="select-the-vm-when-restoring-the-vm"></a>Selecionar a VM (ao restaurar a VM)

Para obter o objeto do PowerShell que identifica o item correto de backup, comece do contêiner no cofre e desça progressivamente na hierarquia de objetos. Para selecionar o contêiner que representa a VM, use o cmdlet [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) e redirecione-o para o cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) .

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-the-vm"></a>Escolha um ponto de recuperação (ao restaurar a VM)

Use o cmdlet [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) para listar todos os pontos de recuperação para o item de backup. Em seguida, escolha o ponto de recuperação a ser restaurado. Se você não tiver certeza de qual ponto de recuperação usar, é uma boa prática escolher o ponto RecoveryPointType = AppConsistent mais recente na lista.

No script a seguir, a variável, **$RP**, é uma matriz de pontos de recuperação para o item de backup selecionado, dos últimos sete dias. A matriz é classificada em ordem inversa de tempo com o último ponto de recuperação no índice 0. Use a indexação de matriz padrão do PowerShell para selecionar o ponto de recuperação. No exemplo, $rp[0] seleciona o último ponto de recuperação.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

A saída deverá ser semelhante ao seguinte exemplo:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Monte os discos do ponto de recuperação

Use o cmdlet [Get-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) para obter o script para montar todos os discos do ponto de recuperação.

> [!NOTE]
> Os discos são montados como discos conectados do iSCSI no computador em que o script é executado. A montagem ocorre imediatamente e você não incorre em nenhuma cobrança.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

A saída deverá ser semelhante ao seguinte exemplo:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Execute o script no computador em que deseja recuperar os arquivos. Para executar o script, você deve inserir a senha fornecida. Depois que os discos estiverem conectados, use o Windows File Explorer para procurar os novos volumes e arquivos. Para obter mais informações, consulte o artigo de backup, [recuperar arquivos do backup de máquina virtual do Azure](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Desmonte os discos

Depois que os arquivos necessários forem copiados, use [Disable-AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) para desmontar os discos. Certifique-se de desmontar os discos para que o acesso aos arquivos do ponto de recuperação é removido.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Próximas etapas

Se você preferir usar o PowerShell para interagir com os recursos do Azure, confira o artigo do PowerShell, [Implantar e gerenciar Backup do Windows Server](backup-client-automation.md). Se você gerencia backups do DPM, consulte o artigo [Implantar e gerenciar o backup do DPM](backup-dpm-automation.md).
