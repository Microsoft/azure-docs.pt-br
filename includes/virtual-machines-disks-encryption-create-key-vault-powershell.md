---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ebeca5ec1e3a478fdf1a62e2478cad9754c6ccd2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99808536"
---
1. Verifique se você instalou a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps) e se está conectado a uma conta do Azure com Connect-AzAccount

1. Crie uma instância do Azure Key Vault e a chave de criptografia.

    Ao criar a instância do Key Vault, habilite a exclusão temporária e a proteção de limpeza. A exclusão temporária garante que a Key Vault mantenha uma chave excluída para determinado período de retenção (padrão de 90 dias). A proteção de limpeza garante que uma chave excluída não seja excluída permanentemente até que o período de retenção termine. Essas configurações protegem você contra a perda de dados devido à exclusão acidental. Essas configurações são obrigatórias ao usar um Key Vault para criptografar discos gerenciados.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    Crie uma instância de um DiskEncryptionSet. 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    Conceda o acesso ao recurso DiskEncryptionSet para o Key Vault.

        > [!NOTE]
        > Pode levar alguns minutos para o Azure criar a identidade do DiskEncryptionSet no Azure Active Directory. Se você receber um erro como "não é possível encontrar o objeto Active Directory" ao executar o comando a seguir, aguarde alguns minutos e tente novamente.
        
        ```powershell  
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
        ```
