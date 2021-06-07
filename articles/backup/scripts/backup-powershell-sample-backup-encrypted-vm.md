---
title: Amostra de script do PowerShell – Fazer backup de uma VM do Azure
description: Neste artigo, saiba como usar uma amostra de script do Azure PowerShell para fazer backup de uma máquina virtual do Azure.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 73dc119e8db34aed04ce8926bfa85f557027c8e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97967303"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Fazer backup de uma máquina virtual do Azure criptografada com o PowerShell

Esse script cria um cofre dos Serviços de Recuperação com GRS (armazenamento com redundância geográfica) para uma máquina virtual do Azure criptografada. A política de proteção padrão é aplicada ao cofre. A política gera um backup diário para a máquina virtual e retém cada backup por 365 dias. O script também dispara o ponto de recuperação inicial para a máquina virtual e mantém esse ponto de recuperação por 30 dias.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Cria um cofre dos Serviços de Recuperação para armazenar backups. |
| [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Define propriedades de armazenamento de backup no cofre dos Serviços de Recuperação. |
| [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Cria a política de proteção usando a política de agendamento e a política de retenção no cofre dos Serviços de Recuperação. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Define as permissões no Key Vault para conceder à entidade de serviço o acesso às chaves de criptografia. |
| [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Permite o backup de um item com uma política de proteção de Backup especificada. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Modifica uma política de proteção de Backup existente. |
| [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Inicia um backup de um item de Backup do Azure protegido que não está vinculado à agenda de backup. |
| [Wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Aguarda até que um trabalho do Backup do Azure seja concluído. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/new-azureps-module-az).
