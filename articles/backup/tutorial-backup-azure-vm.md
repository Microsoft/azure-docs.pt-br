---
title: Fazer backup de várias VMs do Azure com o PowerShell
description: Este tutorial oferece detalhes de como fazer backups de várias VMs do Azure para um cofre dos Serviços de Recuperação usando o Microsoft Azure PowerShell.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 7cbe2cca37ce237409042e40b4a60311aed2446c
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273999"
---
# <a name="back-up-azure-vms-with-powershell"></a>Fazer backup de VMs do Azure com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial descreve como implantar um cofre dos Serviços de Recuperação do [Backup do Azure](backup-overview.md) para fazer backup de várias VMs do Azure usando o PowerShell.  

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Crie um cofre dos Serviços de Recuperação e defina o contexto do cofre.
> * Definir uma política de backup
> * Aplicar a política de backup para proteger várias máquinas virtuais
> * Gatilho de um trabalho de backup sob demanda para as máquinas virtuais protegidas. Antes de você poder fazer backup (ou proteger) uma máquina virtual, você deve concluir os [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o ambiente para proteger suas VMs. 

> [!IMPORTANT]
> Este tutorial presume que você já criou um grupo de recursos e uma máquina virtual do Azure.


## <a name="log-in-and-register"></a>Fazer logon e registrar


1. Faça logon na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

    ```powershell
    Connect-AzAccount
    ```
2. Na primeira vez que você usar o Backup do Azure, deverá registrar o provedor de serviços de recuperação do Azure em sua assinatura com [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Se você já tiver registrado, ignore esta etapa.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um [cofre dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) é um contêiner lógico que armazena dados de backup para recursos protegidos, como VMs do Azure. Quando um trabalho de backup executa, ele cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Você pode usar um desses pontos de recuperação para restaurar dados para um determinado ponto no tempo.


- Neste tutorial, você pode criar o cofre no mesmo grupo de recursos e local da VM que você deseja fazer backup.
- O Backup do Azure manipula automaticamente o armazenamento para os dados de backup. Por padrão, o cofre usa [GRS (Armazenamento com Redundância Geográfica)](../storage/common/storage-redundancy-grs.md). A redundância geográfica garante que os dados de backup sejam replicados para uma região do Azure secundária, a centenas de quilômetros de distância da região primária.

Crie um cofre conforme a seguir:

1. Use o [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)para criar o cofre. Especifique o nome do grupo de recursos e o local da VM que você deseja fazer backup.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```
2. Muitos cmdlets do Backup do Azure exigem o objeto do cofre dos Serviços de Recuperação como entrada. Por esse motivo, pode ser útil armazenar o objeto do cofre dos Serviços de Recuperação de backup em uma variável.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```
    
3. Defina o contexto do cofre com [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   - O contexto do cofre é o tipo de dados protegido no cofre.
   - Depois que o contexto é definido, ele se aplica a todos os cmdlets subsequentes

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Fazer backup de VMs do Azure

Os backups são executados de acordo com o agendamento especificado na política de backup. Quando você cria um cofre dos Serviços de Recuperação, ele vem com proteção e políticas de retenção padrão.

- A política de proteção padrão dispara um trabalho de backup uma vez por dia em um horário especificado.
- A política de retenção padrão retém o ponto de recuperação diário por 30 dias. 

Para habilitar e fazer backup da VM do Azure neste tutorial, vamos fazer o seguinte:

1. Especifique um contêiner no cofre que contém os dados de backup com [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Cada VM para backup é um item. Para iniciar um trabalho de backup, você obtém informações sobre a VM com [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Execute um backup ad hoc com o [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem). 
    - O primeiro trabalho de backup inicial criará um ponto de recuperação completo.
    - Após o backup inicial, cada trabalho de backup criará pontos de recuperação incrementais.
    - Os pontos de recuperação incrementais são eficientes em termos de armazenamento e de tempo, já que eles transferem somente as alterações feitas desde o último backup.

Habilite e execute o backup da seguinte maneira:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>solução de problemas 

Se você enfrentar problemas ao copiar a sua máquina virtual, confira este [artigo de solução de problemas](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Excluir um cofre dos Serviços de Recuperação

Se você precisar excluir um cofre, em primeiro lugar, exclua os pontos de recuperação no cofre, e sem seguida, cancele o registro do cofre, conforme a seguir:


```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Próximas etapas

- [Revisar](backup-azure-vms-automation.md) uma explicação mais detalhada de backup e restauração de VMs do Azure com o PowerShell. 
- [Gerenciar e monitorar o backup de VM do Azure](backup-azure-manage-vms.md)
- [Restaurar máquinas virtuais do Azure](backup-azure-arm-restore-vms.md)
