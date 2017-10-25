---
title: Fazer backup de VMs do Azure no Azure em escala | Microsoft Docs
description: "Este tutorial oferece detalhes de como fazer backups de várias máquinas virtuais do Azure para um Cofre dos Serviços de Recuperação."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "backup de máquina virtual, fazer backup de máquina virtual, backup e recuperação de desastre"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: 
ms.openlocfilehash: db4e1392acaeb2431d29a851113b7bc5a6dc1e9d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Fazer backup de máquinas virtuais do Azure no Azure em escala

Este tutorial oferece detalhes de como fazer o backup de máquinas virtuais do Azure para um Cofre dos Serviços de Recuperação. A maior parte do trabalho para fazer backup de máquinas virtuais é a preparação. Antes de fazer backup (ou proteger) uma máquina virtual, você deverá atender aos [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o ambiente para proteger suas VMs. 

> [!IMPORTANT]
> Este tutorial presume que você já criou um grupo de recursos e uma máquina virtual do Azure.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um [Cofre de Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) é um contêiner que mantém os pontos de recuperação para os itens que estão tendo o backup realizado. Um Cofre de Serviços de Recuperação é um recurso do Azure que pode ser implantado e gerenciado como parte de um grupo de recursos do Azure. Nesse tutorial, você criará um Cofre de Serviços de Recuperação no mesmo grupo de recursos que a máquina virtual sendo protegida.


Na primeira vez que usar o Backup do Azure, você deverá registrar o provedor de Serviços de Recuperação do Azure com sua assinatura. Se já tiver registrado o provedor com sua assinatura, vá para a próxima etapa.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

Crie um Cofre de Serviços de Recuperação com **New-AzureRmRecoveryServicesVault**. Certifique-se de especificar o nome do grupo de recursos e o local usado ao configurar a máquina virtual da qual deseja fazer backup. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Muitos cmdlets do Backup do Azure exigem o objeto do cofre dos Serviços de Recuperação como entrada. Por esse motivo, pode ser útil armazenar o objeto do cofre dos Serviços de Recuperação de backup em uma variável. Em seguida, use **Set-AzureRmRecoveryServicesBackupProperties** para definir a opção **-BackupStorageRedundancy** como [GRS (Armazenamento com Redundância Geográfica)](../storage/common/storage-redundancy.md#geo-redundant-storage). 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Fazer backup de máquinas virtuais do Azure

Antes de executar o backup inicial, você deve definir o contexto do cofre. O contexto do cofre é o tipo de dados protegido no cofre. Quando você cria um cofre dos Serviços de Recuperação, ele vem com proteção e políticas de retenção padrão. A política de proteção padrão dispara um trabalho de backup diariamente em um horário especificado. A política de retenção padrão retém o ponto de recuperação diário por 30 dias. Para este tutorial, aceite a política padrão. 

Use **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** para definir o contexto do cofre. Depois que o contexto de cofre é definido, ele se aplica a todos os cmdlets subsequentes. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

Você pode usar **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** para disparar o trabalho de backup. O trabalho de backup cria um ponto de recuperação. Se esse é for backup inicial, o ponto de recuperação será um backup completo. Os backups posteriores criam uma cópia incremental.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>Excluir o Cofre de Serviços de Recuperação

Para excluir um Cofre de Serviços de Recuperação, você deve primeiro excluir os pontos de recuperação no cofre e, em seguida, cancelar o registro do cofre. Os comandos a seguir detalham essas etapas. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>Solucionar erros
Se você enfrentar problemas ao realizar o backup de sua máquina virtual, consulte o [artigo sobre solução de problemas de backup de máquinas virtuais do Azure](backup-azure-vms-troubleshoot.md) para obter ajuda.

## <a name="next-steps"></a>Próximas etapas
Agora que você protegeu suas máquinas virtuais, consulte os seguintes artigos para aprender sobre as tarefas de gerenciamento e como restaurar as máquinas virtuais de um ponto de recuperação.

* Para modificar a política de backup, consulte [Usar os cmdlets AzureRM.RecoveryServices.Backup para fazer backup de máquinas virtuais](backup-azure-vms-automation.md#create-a-protection-policy).
* [Gerenciar e monitorar suas máquinas virtuais](backup-azure-manage-vms.md)
* [Restaurar máquinas virtuais](backup-azure-arm-restore-vms.md)
