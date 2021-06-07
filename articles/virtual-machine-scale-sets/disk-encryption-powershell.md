---
title: Criptografar discos para conjuntos de dimensionamento do Azure com Azure PowerShell
description: Saiba como usar o Azure PowerShell para criptografar as instâncias de VM e discos conectados em conjuntos de escala de máquinas virtuais do Windows
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 59b015232fcfe2fd98e1b9f28c2eb4fa86606049
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519564"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Criptografar o sistema operacional e OS discos de dados anexados em um conjunto de dimensionamento de máquinas virtuais com Azure PowerShell

O módulo do Azure PowerShell é usado para criar e gerenciar recursos do Azure da linha de comando do PowerShell ou em scripts.  Este artigo mostra como usar Azure PowerShell para criar e criptografar um conjunto de dimensionamento de máquinas virtuais. Para obter mais informações sobre como aplicar Azure Disk Encryption a um conjunto de dimensionamento de máquinas virtuais, consulte [Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Criar um Azure Key Vault habilitado para criptografia de disco

O Cofre de Chaves do Azure pode armazenar chaves, segredos ou senhas que permitem implementá-los de forma segura em seus aplicativos e serviços. Chaves e criptográficas são armazenadas no Cofre de chaves do Azure usando a proteção de software ou você pode importar ou gerar as chaves em Módulos de segurança de Hardware (HSMs) certificados para padrões de nível 2 de FIPS 140-2. Essas chaves criptográficas são usadas para criptografar e descriptografar os discos virtuais conectados à sua VM. Você mantém o controle dessas chaves criptográficas e pode auditar seu uso.

Crie um Key Vault com [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Para permitir que o Key Vault seja usado para criptografia de disco, defina o parâmetro *EnabledForDiskEncryption*. O exemplo a seguir também define variáveis para o nome do grupo de recursos, o nome do Key Vault e o local. Forneça seu próprio nome exclusivo do Key Vault:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Usar um Key Vault existente

Essa etapa só é necessária se você tiver um Key Vault existente que você deseje usar com a criptografia de disco. Ignore esta etapa se você tiver criado um Key Vault na seção anterior.

Você pode habilitar um Key Vault existente na mesma assinatura e região do conjunto de dimensionamento para a criptografia de disco com [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Defina o nome do seu Key Vault existente na variável *$vaultName* da seguinte maneira:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Criar um conjunto de escala

Primeiro, defina o nome de usuário e a senha de um administrador para as instâncias de VM com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora crie um conjunto de dimensionamento de máquinas virtuais com [New-AzVmss](/powershell/module/az.compute/new-azvmss). Para distribuir o tráfego para as instâncias de VM individuais, um balanceador de carga também é criado. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, além de permitir o tráfego de área de trabalho remota na porta TCP 3389 e comunicação remota do PowerShell na porta TCP 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Habilitar criptografia

Para criptografar as instâncias de VM em um conjunto de dimensionamento, primeiro obtenha algumas informações sobre a ID de recursos e o URI do Key Vault com [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Essas variáveis são usadas para iniciar o processo de criptografia com [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Quando solicitado, digite *y* para continuar o processo de criptografia de disco nas instâncias de VM do conjunto de dimensionamento.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Habilitar criptografia usando KEK para encapsular a chave

Também é possível usar uma Chave de Criptografia de Chave para aumentar a segurança ao criptografar o conjunto de dimensionamento de máquinas virtuais.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  A sintaxe para o valor do parâmetro disk-encryption-keyvault é a cadeia de caracteres completa do identificador:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> A sintaxe para o valor do parâmetro key-encryption-key é o URI completo para o KEK, como em:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Verificar o andamento da criptografia

Para verificar o status da criptografia de disco, use [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Quando instâncias de VM são criptografadas, o código *EncryptionSummary* relata *ProvisioningState/succeeded* conforme mostrado no seguinte exemplo de saída:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Desabilitar criptografia

Se você não quiser mais usar discos de instâncias de VM criptografadas, poderá desabilitar a criptografia com [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) da seguinte maneira:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Próximas etapas

- Neste artigo, você usou o Azure PowerShell para criptografar um conjunto de dimensionamento de máquinas virtuais. Você também pode usar os modelos [CLI do Azure](disk-encryption-cli.md) ou [Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Caso queira aplicar Azure Disk Encryption depois que outra extensão for provisionada, você poderá usar o [sequenciamento de extensão](virtual-machine-scale-sets-extension-sequencing.md).
