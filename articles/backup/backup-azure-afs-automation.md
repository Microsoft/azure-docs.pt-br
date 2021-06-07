---
title: Fazer backup de um compartilhamento de arquivos do Azure usando o PowerShell
description: Neste artigo, saiba como fazer backup de um compartilhamento de arquivos do Azure files usando o serviço de backup do Azure e o PowerShell.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 948931764769bc967b88e7942b7e8384b0f93dff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87077011"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>Fazer backup de um compartilhamento de arquivos do Azure usando o PowerShell

Este artigo descreve como usar Azure PowerShell para fazer backup de um compartilhamento de arquivos do Azure por meio de um cofre dos serviços de recuperação de [backup do Azure](backup-overview.md) .

Este artigo explica como:

> [!div class="checklist"]
>
> * Configure o PowerShell e registre o provedor de serviços de recuperação.
> * Crie um cofre dos Serviços de Recuperação.
> * Configure o backup para um compartilhamento de arquivos do Azure.
> * Execute um trabalho de backup.

## <a name="before-you-start"></a>Antes de começar

* [Saiba mais](backup-azure-recovery-services-vault-overview.md) sobre os cofres dos serviços de recuperação.
* Examine a referência de referência do [cmdlet](/powershell/module/az.recoveryservices) AZ. recoveryservices na biblioteca do Azure.
* Examine a seguinte hierarquia de objeto do PowerShell para serviços de recuperação:

  ![Hierarquia de objetos dos Serviços de Recuperação](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>Configurar o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Configure o PowerShell da seguinte maneira:

1. [Baixe a versão mais recente do Azure PowerShell](/powershell/azure/install-az-ps).

    > [!NOTE]
    > A versão mínima do PowerShell necessária para o backup de compartilhamentos de arquivos do Azure é AZ. Recoveryservices 2.6.0. Usar a versão mais recente ou, pelo menos, a versão mínima, ajuda a evitar problemas com scripts existentes. Instale a versão mínima usando o seguinte comando do PowerShell:
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. Encontre os cmdlets do PowerShell para o backup do Azure usando este comando:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Examine os aliases e cmdlets para o backup do Azure, Azure Site Recovery e o cofre dos serviços de recuperação. Aqui está um exemplo do que você pode ver. Não é uma lista completa de cmdlets.

    ![Listar cmdlets dos Serviços de Recuperação](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Entre em sua conta do Azure usando **Connect-AzAccount**.
5. Na página da Web que aparece, você será solicitado a inserir suas credenciais de conta.

    Como alternativa, você pode incluir suas credenciais de conta como um parâmetro no cmdlet **Connect-AzAccount** usando **-Credential**.

    Se você for um parceiro CSP trabalhando em nome de um locatário, especifique o cliente como um locatário. Use a ID de locatário ou o nome de domínio primário do locatário. Um exemplo é **Connect-AzAccount-Tenant "fabrikam.com"**.

6. Associe a assinatura que você deseja usar com a conta, pois uma conta pode ter várias assinaturas:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Se você estiver usando o backup do Azure pela primeira vez, use o cmdlet **Register-AzResourceProvider** para registrar o provedor de serviços de recuperação do Azure com sua assinatura:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Verifique se os provedores foram registrados com êxito:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. Na saída do comando, verifique se **RegistrationState** muda para **registrado**. Caso contrário, execute o cmdlet **Register-AzResourceProvider** novamente.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

O cofre dos serviços de recuperação é um recurso do Resource Manager, portanto, você deve colocá-lo em um grupo de recursos. Você pode usar um grupo de recursos existente ou pode criar um grupo de recursos usando o cmdlet **New-AzResourceGroup** . Ao criar um grupo de recursos, especifique o nome e o local para ele.

Siga estas etapas para criar um cofre dos serviços de recuperação:

1. Se você não tiver um grupo de recursos existente, crie um novo usando o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Neste exemplo, criamos um grupo de recursos na região oeste dos EUA:

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

1. Use o cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) para criar o cofre. Especifique o mesmo local para o cofre que você usou para o grupo de recursos.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

### <a name="view-the-vaults-in-a-subscription"></a>Exibir os cofres em uma assinatura

Para exibir todos os cofres na assinatura, use [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault):

```powershell
Get-AzRecoveryServicesVault
```

A saída é semelhante à seguinte. Observe que a saída fornece o grupo de recursos e o local associados.

```powershell
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

Muitos cmdlets de backup do Azure exigem o objeto de cofre dos serviços de recuperação como uma entrada, portanto, é conveniente armazenar o objeto de cofre em uma variável.

O contexto do cofre é o tipo de dados protegido no cofre. Defina-o usando [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext). Depois que o contexto é definido, ele se aplica a todos os cmdlets subsequentes.

O exemplo a seguir define o contexto do cofre para **testvault**:

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Buscar a ID do cofre

Planejamos substituir a configuração de contexto do cofre de acordo com as diretrizes de Azure PowerShell. Em vez disso, você pode armazenar ou buscar a ID do cofre e passá-la para comandos relevantes. Se você não tiver definido o contexto do cofre ou desejar especificar o comando a ser executado para um determinado cofre, passe a ID do cofre `-vaultID` para todos os comandos relevantes da seguinte maneira:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

Uma política de backup especifica o agendamento de backups e por quanto tempo os pontos de recuperação de backup devem ser mantidos.

Uma política de backup está associada a pelo menos uma política de retenção. Uma política de retenção define por quanto tempo um ponto de recuperação é mantido até ser excluído. Você pode configurar backups com retenção diária, semanal, mensal ou anual.

Aqui estão alguns cmdlets para políticas de backup:

* Exiba a retenção de política de backup padrão usando [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject).
* Exiba o agendamento da política de backup padrão usando [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject).
* Crie uma nova política de backup usando [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy). Você insere os objetos de política de retenção e agendamento como entrada.

Por padrão, uma hora de início é definida no objeto de política de agenda. Use o exemplo a seguir para alterar a hora de início para a hora de início desejada. A hora de início desejada deve estar no horário coordenado universal (UTC). O exemplo supõe que a hora de início desejada é 01:00 AM UTC para backups diários.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Você precisa fornecer a hora de início somente em múltiplos minutos. No exemplo anterior, ele pode ser apenas "01:00:00" ou "02:30:00". A hora de início não pode ser "01:15:00".

O exemplo a seguir armazena a política de agendamento e a política de retenção em variáveis. Em seguida, ele usa essas variáveis como parâmetros para uma nova política (**NewAFSPolicy**). **NewAFSPolicy** faz um backup diário e os retém por 30 dias.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A saída deverá ser semelhante a esta:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Habilitar backup

Depois de definir a política de backup, você pode habilitar a proteção para o compartilhamento de arquivos do Azure usando a política.

### <a name="retrieve-a-backup-policy"></a>Recuperar uma política de backup

Você busca o objeto de política relevante usando [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy). Use este cmdlet para exibir as políticas associadas a um tipo de carga de trabalho ou para obter uma política específica.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Recuperar uma política para um tipo de carga de trabalho

O exemplo a seguir recupera políticas para o tipo de carga de trabalho **AzureFiles**:

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

A saída deverá ser semelhante a esta:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> O fuso horário do campo **backuptime** no PowerShell está em UTC. Quando a hora de backup é mostrada no Portal do Azure, o horário é ajustado para seu fuso horário local.

#### <a name="retrieve-a-specific-policy"></a>Recuperar uma política específica

A política a seguir recupera a política de backup chamada **dailyafs**:

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>Habilitar a proteção e aplicar a política

Habilite a proteção usando [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Depois que a política estiver associada ao cofre, os backups serão disparados de acordo com o agendamento da política.

O exemplo a seguir habilita a proteção para o compartilhamento de arquivos do Azure **testAzureFileShare** na conta de armazenamento **testStorageAcct**, com a política **dailyafs**:

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

O comando aguarda até que o trabalho configure-Protection seja concluído e forneça uma saída semelhante ao exemplo a seguir:

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

Para obter mais informações sobre como obter uma lista de compartilhamentos de arquivos para uma conta de armazenamento, consulte [Este artigo](/powershell/module/az.storage/get-azstorageshare).

## <a name="important-notice-backup-item-identification"></a>Aviso importante: identificação do item de backup

Esta seção descreve uma alteração importante nos backups de compartilhamentos de arquivos do Azure em preparação para disponibilidade geral.

Ao habilitar um backup para compartilhamentos de arquivos do Azure, o usuário fornece ao cliente um nome de compartilhamento de arquivo como o nome da entidade e um item de backup é criado. O nome do item de backup é um identificador exclusivo que o serviço de backup do Azure cria. Normalmente, o identificador é um nome amigável para o usuário. Mas para lidar com o cenário de exclusão reversível, em que um compartilhamento de arquivos pode ser excluído e outro compartilhamento de arquivos pode ser criado com o mesmo nome, a identidade exclusiva de um compartilhamento de arquivos do Azure agora é uma ID.

Para saber a ID exclusiva de cada item, execute o comando **Get-AzRecoveryServicesBackupItem** com os filtros relevantes para **backupManagementType** e **workloadtype** para obter todos os itens relevantes. Em seguida, observe o campo nome no objeto/resposta do PowerShell retornado.

É recomendável que você liste itens e, em seguida, recupere seu nome exclusivo do campo nome na resposta. Use esse valor para filtrar os itens com o parâmetro *Name* . Caso contrário, use o parâmetro *FriendlyName* para recuperar o item com sua ID.

> [!IMPORTANT]
> Verifique se o PowerShell foi atualizado para a versão mínima (AZ. Recoveryservices 2.6.0) para backups de compartilhamentos de arquivos do Azure. Com essa versão, o filtro *FriendlyName* está disponível para o comando **Get-AzRecoveryServicesBackupItem** .
>
> Passe o nome do compartilhamento de arquivos do Azure para o parâmetro *FriendlyName* . Se você passar o nome do compartilhamento de arquivos para o parâmetro *Name* , essa versão lançará um aviso para passar o nome para o parâmetro *FriendlyName* .
>
> A não instalação da versão mínima pode resultar em uma falha de scripts existentes. Instale a versão mínima do PowerShell usando o seguinte comando:
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>Disparar um backup sob demanda

Use o [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) para executar um backup sob demanda para um compartilhamento de arquivos do Azure protegido:

1. Recupere a conta de armazenamento do contêiner no cofre que contém os dados de backup usando [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Para iniciar um trabalho de backup, obtenha informações sobre o compartilhamento de arquivos do Azure usando [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Execute um backup sob demanda usando [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Execute o backup sob demanda da seguinte maneira:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

O comando retorna um trabalho com uma ID a ser rastreada, conforme mostrado no exemplo a seguir:

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Os instantâneos de compartilhamento de arquivos do Azure são usados enquanto os backups são feitos. Normalmente, o trabalho é concluído no momento em que o comando retorna essa saída.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [fazer backup de arquivos do Azure no portal do Azure](backup-afs.md).
* Consulte o [script de exemplo no GitHub](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup) para usar um runbook de automação do Azure para agendar backups.
