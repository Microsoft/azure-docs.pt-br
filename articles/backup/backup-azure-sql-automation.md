---
title: Banco de BD SQL no backup de VM do Azure & restaurar por meio do PowerShell
description: Faça backup e restaure bancos de dados SQL em VMs do Azure usando o backup do Azure e o PowerShell.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 0a3467ffa3a67ac9ad593748948cea8da59e3e6b
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734531"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>Fazer backup e restaurar bancos de dados SQL em VMs do Azure com o PowerShell

Este artigo descreve como usar Azure PowerShell para fazer backup e recuperar um banco de BD SQL em uma VM do Azure usando o cofre dos serviços de recuperação de [backup do Azure](backup-overview.md) .

Este artigo explica como:

> [!div class="checklist"]
>
> * Configure o PowerShell e registre o provedor de serviços de recuperação do Azure.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure o backup para o banco de BD SQL em uma VM do Azure.
> * Execute um trabalho de backup.
> * Restaure um banco de BD SQL de backup.
> * Monitore os trabalhos de backup e restauração.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre os cofres dos serviços de recuperação.
* Leia sobre os recursos do recurso para [fazer backup de bancos de inicialização do SQL em VMs do Azure](backup-azure-sql-database.md#before-you-start).
* Examine a hierarquia de objetos do PowerShell para serviços de recuperação.

### <a name="recovery-services-object-hierarchy"></a>Hierarquia de objetos dos Serviços de Recuperação

A hierarquia de objetos é resumida no diagrama a seguir.

![Hierarquia de objetos dos Serviços de Recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Examine a referência de [referência do cmdlet](/powershell/module/az.recoveryservices) **AZ. recoveryservices** na biblioteca do Azure.

### <a name="set-up-and-install"></a>Configurar e instalar

Configure o PowerShell da seguinte maneira:

1. [Baixe a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps). A versão mínima necessária é 1.5.0.

2. Localize os cmdlets do PowerShell de backup do Azure com este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Examine os aliases e os cmdlets para o backup do Azure e o cofre dos serviços de recuperação. Aqui está um exemplo do que você pode ver. Não é uma lista completa de cmdlets.

    ![Listar cmdlets dos Serviços de Recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Entre em sua conta do Azure com **Connect-AzAccount**.
5. Na página da Web que aparece, você será solicitado a inserir suas credenciais de conta.

    * Como alternativa, você pode incluir suas credenciais de conta como um parâmetro no cmdlet **Connect-AzAccount** com **-Credential**.
    * Se você for um parceiro CSP trabalhando para um locatário, especifique o cliente como um locatário, usando seu nome de domínio de locatárioid ou de locatário primário. Um exemplo é **Connect-AzAccount -Tenant** fabrikam.com.

6. Associe a assinatura que você deseja usar com a conta, pois uma conta pode ter várias assinaturas.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se você estiver usando um Backup do Azure pela primeira vez, use o cmdlet **Register-AzResourceProvider** para registrar o provedor dos Serviços de Recuperação do Azure com sua assinatura.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verifique se os provedores foram registrados com êxito:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Na saída do comando, verifique se **RegistrationState** muda para **registrado**. Caso contrário, execute o cmdlet **Register-AzResourceProvider** novamente.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Siga estas etapas para criar um cofre dos Serviços de Recuperação:

O cofre dos Serviços de Recuperação é um recurso do Resource Manager e, portanto, você precisará colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um grupo de recursos com o cmdlet **New-AzResourceGroup**. Ao criar um grupo de recursos, especifique o nome e o local.

1. Um cofre é colocado em um grupo de recursos. Se você não tiver um grupo de recursos existente, crie um novo com o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Neste exemplo, criamos um novo grupo de recursos na região oeste dos EUA.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Use o cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) para criar o cofre. Especifique o mesmo local para o cofre usado para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Especifique o tipo de redundância a ser usado para o armazenamento do cofre.

    * Você pode usar o armazenamento com redundância [local](../storage/common/storage-redundancy.md#locally-redundant-storage), armazenamento com redundância [geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) ou [armazenamento com redundância de zona](../storage/common/storage-redundancy.md#zone-redundant-storage) .
    * O exemplo a seguir define a opção **-BackupStorageRedundancy** para o cmd [set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) para **testvault** definido como **georedundante**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Para exibir todos os cofres da assinatura, use [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault).

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante à seguinte. O grupo de recursos e o local associados são fornecidos.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>Definir o contexto do cofre

Armazene o objeto de cofre em uma variável e defina o contexto do cofre.

* Muitos cmdlets de backup do Azure exigem o objeto de cofre dos serviços de recuperação como uma entrada, portanto, é conveniente armazenar o objeto de cofre em uma variável.
* O contexto do cofre é o tipo de dados protegido no cofre. Defina-o com [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). Depois que o contexto é definido, ele se aplica a todos os cmdlets subsequentes.

O exemplo a seguir define o contexto de cofre de **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Buscar a ID do cofre

Planejamos substituir a configuração de contexto do cofre de acordo com as diretrizes de Azure PowerShell. Em vez disso, você pode armazenar ou buscar a ID do cofre e passá-la para comandos relevantes, da seguinte maneira:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

Uma política de backup especifica o agendamento de backups e por quanto tempo os pontos de recuperação de backup devem ser mantidos:

* Uma política de backup está associada a pelo menos uma política de retenção. Uma política de retenção define por quanto tempo um ponto de recuperação é mantido até ser excluído.
* Exiba a retenção de política de backup padrão usando [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Exiba o agendamento da política de backup padrão usando [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject).
* Use o cmdlet [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) para criar uma nova política de backup. Você insere os objetos de política de retenção e agendamento.

Por padrão, uma hora de início é definida no objeto de política de agenda. Use o exemplo a seguir para alterar a hora de início para a hora de início desejada. A hora de início desejada também deve estar em UTC. O exemplo a seguir pressupõe que a hora de início desejada seja 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Você precisa fornecer a hora de início apenas em 30 minutos. No exemplo acima, ele pode ser apenas "01:00:00" ou "02:30:00". A hora de início não pode ser "01:15:00".

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. Em seguida, ele usa essas variáveis como parâmetros para uma nova política (**NewSQLPolicy**). O **NewSQLPolicy** usa um backup "completo" diário, o retém por 180 dias e faz um backup de log a cada 2 horas

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A saída é semelhante à seguinte.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Habilitar backup

### <a name="registering-the-sql-vm"></a>Registrando a VM do SQL

Para backups de VM do Azure e compartilhamentos de arquivos do Azure, o serviço de backup pode se conectar a esses Azure Resource Manager recursos e buscar os detalhes relevantes. Como o SQL é um aplicativo em uma VM do Azure, o serviço de backup precisa de permissão para acessar o aplicativo e buscar os detalhes necessários. Para fazer isso, você precisa *' Registrar '* a VM do Azure que contém o aplicativo SQL com um cofre dos serviços de recuperação. Depois de registrar uma VM do SQL com um cofre, você pode proteger os bancos de os SQL somente para esse cofre. Use o cmdlet do PowerShell [Register-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) para registrar a VM.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

O comando retornará um ' contêiner de backup ' desse recurso e o status será ' Registered '

> [!NOTE]
> Se o parâmetro Force não for fornecido, você será solicitado a confirmar com um texto ' deseja desabilitar a proteção para este contêiner '. Ignore este texto e diga "Y" para confirmar. Esse é um problema conhecido e estamos trabalhando para remover o texto e o requisito para o parâmetro Force.

### <a name="fetching-sql-dbs"></a>Buscando bancos de os SQL

Quando o registro for concluído, o serviço de backup poderá listar todos os componentes do SQL disponíveis na VM. Para exibir todos os componentes do SQL que ainda serão copiados para este cofre, use o cmdlet do PowerShell [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem)

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

A saída mostrará todos os componentes do SQL desprotegidos em todas as VMs do SQL registradas neste cofre com o tipo de item e servername. Você pode filtrar ainda mais uma determinada VM do SQL passando o parâmetro '-container ' ou usar a combinação de ' name ' e ' servername ' juntamente com o sinalizador ItemType para chegar a um item SQL exclusivo.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Configurar o backup

Agora que temos o BD SQL necessário e a política com a qual precisa ser feito backup, podemos usar o cmdlet [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) para configurar o backup para esse banco de SQL DB.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

O comando aguarda até que o backup de configuração seja concluído e retorna a saída a seguir.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Buscando novos bancos de os SQL

Depois que o computador for registrado, o serviço de backup buscará os detalhes dos bancos de computadores disponíveis. Se as instâncias do SQL ou bancos de serviços SQL forem adicionadas ao computador registrado posteriormente, você precisará disparar manualmente o serviço de backup para executar uma nova ' consulta ' para obter **todos** os bancos de computadores desprotegidos (incluindo os recém-adicionados) novamente. Use o cmdlet do PowerShell [Initialize-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/initialize-azrecoveryservicesbackupprotectableitem) na VM do SQL para executar uma nova consulta. O comando aguarda até que a operação seja concluída. Posteriormente, use o cmdlet [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) do PowerShell para obter a lista de componentes SQL desprotegidos mais recentes.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

Depois que os itens protegidos relevantes forem buscados, habilite os backups conforme instruído na [seção acima](#configuring-backup).
Se não quiser detectar manualmente novos bancos de usuários, eles poderão optar pela autoproteção, conforme explicado [abaixo](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Habilitar autoproteção

Você pode configurar o backup para que todos os bancos de dado adicionados ao futuro sejam automaticamente protegidos com uma determinada política. Para habilitar a autoproteção, use o cmdlet [Enable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupautoprotection) do PowerShell.

Como a instrução é fazer backup de todos os bancos de dado futuros, a operação é feita em um nível SQLInstance.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Depois que a tentativa de autoproteção é determinada, a consulta no computador para buscar bancos de dados recém-adicionados ocorre como uma tarefa em segundo plano agendada a cada 8 horas.

## <a name="restore-sql-dbs"></a>Restaurar bancos de os SQL

O backup do Azure pode restaurar SQL Server bancos de dados que estão em execução em VMs do Azure da seguinte maneira:

* Restaurar para uma data ou hora específica (para o segundo) usando backups de log de transações. O backup do Azure determina automaticamente o backup diferencial completo apropriado e a cadeia de backups de log que são necessários para restaurar com base na hora selecionada.
* Restaure um backup completo ou diferencial específico para restaurar para um ponto de recuperação específico.

Verifique os pré-requisitos mencionados [aqui](restore-sql-database-azure-vm.md#restore-prerequisites) antes de restaurar bancos de SQL.

Primeiro, busque o banco de BD SQL do backup relevante usando o cmdlet [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) do PowerShell.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>Buscar o tempo de restauração relevante

Conforme descrito acima, você pode restaurar o banco de BD SQL de backup para uma cópia completa/diferencial **ou** para um ponto no tempo de log.

#### <a name="fetch-distinct-recovery-points"></a>Buscar pontos de recuperação distintos

Use [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) para buscar pontos de recuperação distintos (completos/diferenciais) para um banco de BD SQL de backup.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

A saída é semelhante ao exemplo a seguir

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Use o filtro ' RecoveryPointId ' ou um filtro de matriz para buscar o ponto de recuperação relevante.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Buscar ponto de recuperação pontual

Se você quiser restaurar o banco de dado para um determinado ponto no tempo, use o cmdlet do PowerShell [Get-AzRecoveryServicesBackupRecoveryLogChain](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain) . O cmdlet retorna uma lista de datas que representam os horários de início e término de uma cadeia de logs contínua e não quebrada para esse item de backup do SQL. O ponto no tempo desejado deve estar dentro desse intervalo.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -VaultId $targetVault.ID
```

A saída será semelhante ao exemplo a seguir.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

A saída acima significa que você pode restaurar para qualquer ponto no tempo entre a hora de início e a hora de término exibidas. Os horários estão em UTC. Construa qualquer ponto no tempo no PowerShell que esteja dentro do intervalo mostrado acima.

> [!NOTE]
> Quando um ponto no tempo de log é selecionado para restauração, você não precisa especificar o ponto de partida, ou seja, o backup completo do qual o banco de BD é restaurado. O serviço de backup do Azure cuidará de todo o plano de recuperação, ou seja, o backup completo a ser escolhido, quais backups de log serão aplicados e assim por diante.

### <a name="determine-recovery-configuration"></a>Determinar configuração de recuperação

No caso de uma restauração do banco de BD SQL, há suporte para os seguintes cenários de restauração.

* Substituindo o banco de dados SQL de backup por um de outro ponto de recuperação-OriginalWorkloadRestore
* Restaurando o banco de BD SQL como um novo DB na mesma instância SQL-AlternateWorkloadRestore
* Restaurando o banco de BD SQL como um novo DB em outra instância do SQL em outra VM do SQL-AlternateWorkloadRestore
* Restaurando o banco de BD SQL como arquivos. bak-RestoreAsFiles

Depois de buscar o ponto de recuperação relevante (diferenciado ou ponto no tempo de log), use o cmdlet do PowerShell [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) para buscar o objeto de configuração de recuperação de acordo com o plano de recuperação desejado.

#### <a name="original-workload-restore"></a>Restauração da carga de trabalho original

Para substituir o banco de dados de backup por data do ponto de recuperação, basta especificar o sinalizador correto e o ponto de recuperação relevante, conforme mostrado no (s) exemplo (es) a seguir.

##### <a name="original-restore-with-distinct-recovery-point"></a>Restauração original com ponto de recuperação distinto

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Restauração original com o ponto de log-in-time

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Restauração de carga de trabalho alternativa

> [!IMPORTANT]
> Um banco de BD SQL de backup pode ser restaurado como um novo BD somente para outro SQLInstance, em uma VM do Azure ' registrada ' para este cofre.

Conforme descrito acima, se o SQLInstance de destino estiver dentro de outra VM do Azure, verifique se ele está [registrado nesse cofre](#registering-the-sql-vm) e se o SQLInstance relevante aparece como um item de proteção.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Em seguida, basta passar o ponto de recuperação relevante, a instância SQL de destino com o sinalizador à direita, conforme mostrado abaixo.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Restauração alternativa com ponto de recuperação distinto

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>Restauração alternativa com o ponto de log-in-time

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Restaurar como arquivos

Para restaurar os dados de backup como arquivos. bak, em vez de em um banco de dados, escolha a opção **restaurar como arquivos** . O banco de BD SQL com backup pode ser restaurado para qualquer VM de destino registrada nesse cofre.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Restaurar como arquivos com um ponto de recuperação distinto

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Restaurar como arquivos com o ponto de log no tempo do último completo

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Restaurar como arquivos com o ponto de log no tempo a partir de um completo especificado

Se você quiser fornecer um total específico que deve ser usado para restauração, use o seguinte comando:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

O objeto de configuração de ponto de recuperação final obtido do cmdlet [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) do PowerShell tem todas as informações relevantes para Restore e é conforme mostrado abaixo.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

Você pode editar os campos nome do BD restaurado, OverwriteWLIfpresent, norecoverymode e targetPhysicalPath. Obtenha mais detalhes para os caminhos de arquivo de destino, conforme mostrado abaixo.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Defina as propriedades relevantes do PowerShell como valores de cadeia de caracteres, conforme mostrado abaixo.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> Certifique-se de que o objeto de configuração de recuperação final tenha todos os valores necessários e adequados, uma vez que a operação de restauração será baseada no objeto de configuração.

### <a name="restore-with-relevant-configuration"></a>Restaurar com configuração relevante

Depois que o objeto de configuração de recuperação relevante for obtido e verificado, use o cmdlet do PowerShell [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) para iniciar o processo de restauração.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

A operação de restauração retorna um trabalho a ser acompanhado.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>Gerenciar backups do SQL

### <a name="on-demand-backup"></a>Backup sob demanda

Depois que o backup tiver sido habilitado para um BD, você também poderá disparar um backup sob demanda para o BD usando o cmdlet do PowerShell [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) . O exemplo a seguir dispara um backup completo em um banco de BD SQL com compactação habilitada e o backup completo deve ser retido por 60 dias.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

O comando backup sob demanda retorna um trabalho a ser acompanhado.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Se a saída for perdida ou se você quiser obter a ID de trabalho relevante, [obtenha a lista de trabalhos](#track-azure-backup-jobs) do serviço de backup do Azure e acompanhe-o e seus detalhes.

### <a name="change-policy-for-backup-items"></a>Alterar política para itens de backup

Você pode alterar a política do item de backup de *Policy1* para *Policy2*. Para alternar políticas para um item de backup, busque a política relevante e faça backup do item e use o comando [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) com o item de backup como o parâmetro.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

O comando aguarda até que o backup de configuração seja concluído e retorna a saída a seguir.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="edit-an-existing-backup-policy"></a>Editar uma política de backup existente

Para editar uma política existente, use o comando [set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) .

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -SchedulePolicy $SchPol -RetentionPolicy $RetPol
```

Verifique os trabalhos de backup depois que algum tempo tiver passado para controlar as falhas. Se houver, você precisará corrigir os problemas. Em seguida, execute novamente o comando Edit Policy com o parâmetro **FixForInconsistentItems** para tentar editar a política novamente em todos os itens de backup para os quais a operação falhou anteriormente.

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -FixForInconsistentItems
```

### <a name="re-register-sql-vms"></a>Registrar novamente as VMs do SQL

> [!WARNING]
> Certifique-se de ler este [documento](backup-sql-server-azure-troubleshoot.md#re-registration-failures) para entender os sintomas de falha e as causas antes de tentar o novo registro

Para disparar o novo registro da VM do SQL, busque o contêiner de backup relevante e passe-o para o cmdlet Register.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>Parar a proteção

#### <a name="retain-data"></a>Reter dados

Se você quiser interromper a proteção, poderá usar o cmdlet [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) do PowerShell. Isso interromperá os backups agendados, mas os dados submetidos a backup até agora são mantidos para sempre.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Excluir dados de backup

Para remover completamente os dados de backup armazenados no cofre, basta adicionar o sinalizador de '-RemoveRecoveryPoints '/alternar para o [comando de proteção ' Disable '](#retain-data).

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Desabilitar proteção automática

Se a autoproteção tiver sido configurada em uma SQLInstance, você poderá desabilitá-la usando o cmdlet [Disable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) do PowerShell.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>Cancelar registro de VM do SQL

Se todos os bancos de dados de um SQL Server [não estiverem mais protegidos e não houver nenhum dado de backup](#delete-backup-data), você poderá cancelar o registro da VM do SQL nesse cofre. Somente então você pode proteger os bancos de todos em outro cofre. Use o cmdlet do PowerShell [Unregister-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) para cancelar o registro da VM do SQL.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Rastrear trabalhos de backup do Azure

É importante observar que o backup do Azure controla apenas os trabalhos disparados pelo usuário no backup do SQL. Os backups agendados (incluindo backups de log) não são visíveis no portal ou no PowerShell. No entanto, se algum trabalho agendado falhar, um [alerta de backup](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) será gerado e exibido no Portal. [Use Azure monitor](backup-azure-monitoring-use-azuremonitor.md) para acompanhar todos os trabalhos agendados e outras informações relevantes.

Os usuários podem controlar as operações disparadas por usuário/sob demanda com o JobID que é retornado na [saída](#on-demand-backup) de trabalhos assíncronos, como backup. Use o cmdlet do PowerShell [Get-AzRecoveryServicesBackupJobDetail](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjobdetail) para acompanhar o trabalho e seus detalhes.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Para obter a lista de trabalhos sob demanda e seus status do serviço de backup do Azure, use o cmdlet do PowerShell [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) . O exemplo a seguir retorna todos os trabalhos SQL em andamento.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Para cancelar um trabalho em andamento, use o cmdlet [Stop-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) do PowerShell.

## <a name="managing-sql-always-on-availability-groups"></a>Gerenciando grupos de disponibilidade do SQL Always On

Para grupos de disponibilidade do SQL Always On, certifique [-se de registrar todos os nós](#registering-the-sql-vm) do grupo de disponibilidade (AG). Quando o registro é feito para todos os nós, um objeto de grupo de disponibilidade do SQL é criado logicamente em itens protegíveis. Os bancos de dados sob o SQL AG serão listados como ' SQLDatabase '. Os nós aparecerão como instâncias autônomas e os bancos de dados SQL padrão sob eles serão listados como bancos de dados SQL também.

Por exemplo, vamos supor que um AG do SQL tem dois nós: *SQL-Server-0* e *SQL-Server-1* e 1 SQL AG DB. Depois que ambos os nós forem registrados, se você [listar os itens protegíveis](#fetching-sql-dbs), ele listará os seguintes componentes

* Um tipo de item protegido por objeto do SQL AG como sqlavailability
* Um SQL AG DB-tipo de item de proteção como SQLDatabase
* SQL-Server-0-tipo de item de proteção como SqlInstance
* SQL-Server-1-tipo de item de proteção como SqlInstance
* Quaisquer bancos de dados SQL padrão (Mestre, modelo, msdb) em SQL-Server-0-tipo de item de proteção como SQLDatabase
* Quaisquer bancos de dados SQL padrão (Mestre, modelo, msdb) em SQL-Server-1-tipo de item de proteção como SQLDatabase

SQL-Server-0, SQL-Server-1 também será listado como "AzureVMAppContainer" quando os [contêineres de backup forem listados](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).

Basta buscar o banco de dados relevante para [habilitar o backup](#configuring-backup) e os [cmdlets do PowerShell](#restore-sql-dbs) de backup e restauração [sob demanda](#on-demand-backup) são idênticos.
