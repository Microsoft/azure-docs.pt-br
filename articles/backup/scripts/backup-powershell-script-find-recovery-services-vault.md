---
title: Script do PowerShell – localizar o Cofre da Conta de Armazenamento
description: Saiba como usar um script do Azure PowerShell para localizar o cofre dos Serviços de Recuperação em que sua conta de armazenamento está registrada.
ms.topic: sample
ms.date: 1/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40859c1ea05210d27fcdcf33ba9d4f961965ea22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89075691"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>O Script do PowerShell localiza o cofre dos Serviços de Recuperação em que uma Conta de Armazenamento está registrada

Esse script ajuda a localizar o cofre dos Serviços de Recuperação em que sua conta de armazenamento está registrada.

## <a name="sample-script"></a>Exemplo de script

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>Como executar o script

1. Salve o script acima em seu computador com um nome de sua escolha. Neste exemplo, nós o salvamos como *FindRegisteredStorageAccount.ps1*.
2. Execute o script fornecendo os seguintes parâmetros:

    * **-ResourceGroupName** – Grupo de Recursos da conta de armazenamento
    * **-StorageAccountName** – nome da Conta de Armazenamento
    * **-SubscriptionID** – a ID da assinatura em que a conta de armazenamento está presente.

O seguinte exemplo tenta localizar o cofre dos Serviços de Recuperação em que a conta de armazenamento *afsaccount* está registrada:

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Saída

A saída exibirá o caminho completo do cofre dos Serviços de Recuperação em que a conta de armazenamento está registrada. Aqui está uma amostra de saída:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Próximas etapas

Saiba como [Fazer backup de Compartilhamentos de Arquivos do Azure do portal do Azure](../backup-afs.md)
