---
title: Azure PowerShell-habilitar a criptografia dupla em discos gerenciados em repouso
description: Habilite a criptografia dupla em repouso para os dados do disco gerenciado usando Azure PowerShell.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cfef3925152c0b94a59c662443cf202724dadc0a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549542"
---
# <a name="use-the-azure-powershell-module-to-enable-double-encryption-at-rest-for-managed-disks"></a>Usar o módulo Azure PowerShell para habilitar a criptografia dupla em repouso para discos gerenciados

O Armazenamento em Disco do Azure dá suporte à criptografia dupla em repouso para discos gerenciados. Para obter informações conceituais sobre a criptografia dupla em repouso, bem como outros tipos de criptografia de disco gerenciado, consulte a seção [criptografia dupla em repouso](../disk-encryption.md#double-encryption-at-rest) do nosso artigo sobre criptografia de disco.

## <a name="prerequisites"></a>Pré-requisitos

Instale a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps)e entre em uma conta do Azure usando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

## <a name="getting-started"></a>Introdução

1. Crie uma instância do Azure Key Vault e a chave de criptografia.

    Ao criar a instância do Key Vault, habilite a exclusão temporária e a proteção de limpeza. A exclusão temporária garante que a Key Vault mantenha uma chave excluída para determinado período de retenção (padrão de 90 dias). A proteção de limpeza garante que uma chave excluída não seja excluída permanentemente até que o período de retenção termine. Essas configurações protegem você contra a perda de dados devido à exclusão acidental. Essas configurações são obrigatórias ao usar um Key Vault para criptografar discos gerenciados.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westus2"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  Crie um DiskEncryptionSet com EncryptionType definido como EncryptionAtRestWithPlatformAndCustomerKeys. Use a versão de API **2020-05-01** no modelo de Azure Resource Manager (ARM). 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" `
    -diskEncryptionSetName $diskEncryptionSetName `
    -keyVaultId $keyVault.ResourceId `
    -keyVaultKeyUrl $key.Key.Kid `
    -encryptionType "EncryptionAtRestWithPlatformAndCustomerKeys" `
    -region $LocationName
    ```

1. Conceda o acesso ao recurso DiskEncryptionSet para o Key Vault.

    > [!NOTE]
    > Pode levar alguns minutos para o Azure criar a identidade do DiskEncryptionSet no Azure Active Directory. Se você receber um erro como "não é possível encontrar o objeto Active Directory" ao executar o comando a seguir, aguarde alguns minutos e tente novamente.

    ```powershell  
    $des=Get-AzDiskEncryptionSet -name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
    ```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou e configurou esses recursos, você pode usá-los para proteger seus discos gerenciados. Os links a seguir contêm scripts de exemplo, cada um com um cenário respectivo, que você pode usar para proteger seus discos gerenciados.

- [Azure PowerShell-habilitar chaves gerenciadas pelo cliente com discos gerenciados por criptografia do lado do servidor](disks-enable-customer-managed-keys-powershell.md)
- [Exemplos de modelo do Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
