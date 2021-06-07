---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 98922829e83f84078c3d8cadae15844dba194c93
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800121"
---
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

*Se você já tiver um grupo de recursos, poderá pular para [Criar um cofre de chaves](#create-a-key-vault).*

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

Crie um grupo de recursos usando o comando [az group create](/cli/azure/group#az_group_create) da CLI do Azure, o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) do Azure PowerShell ou por meio do [portal do Azure](https://portal.azure.com).

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Criar um cofre de chave

*Se você já tiver um cofre de chaves, poderá pular para [definir políticas de acesso avançado do cofre de chaves](#set-key-vault-advanced-access-policies).*

Crie um cofre de chaves usando o comando [az keyvault create](/cli/azure/keyvault#az_keyvault_create) da CLI do Azure, o comando [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) do Azure PowerShell, o [portal do Azure](https://portal.azure.com) ou um [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> O cofre de chaves e as VMs devem estar na mesma assinatura. Além disso, para garantir que os segredos de criptografia não ultrapassem os limites regionais, o Azure Disk Encryption exige que o Key Vault e as VMs estejam localizados na mesma região. Crie e use um Key Vault que esteja na mesma assinatura e na mesma região que as VMs a ser criptografadas. 

Cada Key Vault deve ter um nome exclusivo. Substitua <seu-nome-de cofre-de-chaves-exclusivo> pelo nome do seu cofre de chaves nos exemplos a seguir.

### <a name="azure-cli"></a>CLI do Azure

Ao criar um cofre de chaves usando a CLI do Azure, adicione o sinalizador "--enabled-for-disk-encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Ao criar um cofre de chaves usando o Azure PowerShell, adicione o sinalizador "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Modelo do Resource Manager

Você também pode criar um cofre de chaves usando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. No modelo de início rápido do Azure, clique em **Implantar no Azure**.
2. Selecione a assinatura, o grupo de recursos, a localização do grupo de recursos, o nome do Key Vault, a ID do Objeto, os termos legais e o contrato e clique em **Comprar**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Definir as políticas de acesso avançado do cofre de chaves

A plataforma Azure precisa acessar as chaves de criptografia ou os segredos no cofre de chaves para disponibilizá-los para a máquina virtual para inicialização e descriptografar os volumes. 

Se você não habilitou o cofre de chaves para criptografia de disco, implantação ou implantação de modelo no momento da criação (conforme demonstrado na etapa anterior), você deve atualizar as políticas de acesso avançado dele.  

### <a name="azure-cli"></a>CLI do Azure

Use [atualização de keyvault az](/cli/azure/keyvault#az_keyvault_update) para habilitar a criptografia de disco para o Cofre de chaves. 

 - **Habilite o cofre de chaves para criptografia de disco:** habilitado para criptografia de disco é necessária. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Ative o cofre de chaves para implantação, se necessário:** Permite que o provedor de recursos Microsoft.Compute recupere segredos desse cofre de chaves quando esse cofre de chaves é mencionado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Habilite o cofre de chaves para implantação de modelo, se necessário:** permitem que o Gerenciador de recursos para recuperar segredos do cofre.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Use o cmdlet de cofre de chaves [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) do PowerShell para habilitar a criptografia de disco para o cofre da chaves.

  - **Ative o Key Vault para criptografia de disco:** EnabledForDiskEncryption é necessário para a criptografia do Azure Disk.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Ative o cofre de chaves para implantação, se necessário:** Permite que o provedor de recursos Microsoft.Compute recupere segredos desse cofre de chaves quando esse cofre de chaves é mencionado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Ative o cofre de chaves para implantação de modelos, se necessário:** Permite que o Azure Resource Manager obtenha segredos desse cofre de chaves quando esse cofre de chaves for mencionado em uma implantação de modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Portal do Azure

1. Selecione o cofre de chaves, vá para **Políticas de Acesso** e **Clique para mostrar as políticas de acesso avançado**.
2. Selecione a caixa rotulada **habilitar o acesso ao Azure Disk Encryption para criptografia de volume**.
3. Selecione **habilitar o acesso às máquinas virtuais do Azure para implantação** e/ou **habilitar acesso ao Azure Resource Manager para implantação de modelo**, se necessário. 
4. Clique em **Salvar**.

    ![Cofre de chaves do Azure, políticas de acesso avançadas](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Configurar uma KEK (chave de criptografia)

Se você quiser usar uma chave de criptografia (KEK) para uma camada adicional de segurança para chaves de criptografia, adicione uma KEK ao seu cofre de chaves. Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para agrupar os segredos de criptografia antes de gravar no Key Vault.

Você pode gerar uma nova KEK usando o comando [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create) da CLI do Azure, o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) do Azure PowerShell ou o [portal do Azure](https://portal.azure.com/). Você deve gerar uma chave do tipo RSA. O Azure Disk Encryption ainda não é compatível com o uso de chaves de Curva Elíptica.

Em vez disso, você pode importar uma KEK do HSM de gerenciamento de chaves local. Para obter mais informações, consulte [documentação do cofre de chaves](../articles/key-vault/keys/hsm-protected-keys.md).

As URLs de KEK do cofre de chaves devem ser submetidas ao controle de versão. O Azure impõe essa restrição de controle de versão. Para um segredo válido e URLs KEK, confira os seguintes exemplos:

* Exemplo de uma URL secreta válida: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Exemplo de uma URL de KEK válida: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

o Azure Disk Encryption não dá suporte à especificação de números de portas como parte de segredos do cofre de chaves e URLs KEK. Para exemplos de URLs do cofre de chaves não suportados e suportados, consulte os seguintes exemplos:

  * URL aceitável do cofre de chaves: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL inaceitável do cofre de chaves: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>CLI do Azure

Use o comando [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create) da CLI do Azure para gerar uma nova KEK e armazená-la no cofre de chaves.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

Em vez disso, você pode importar uma chave privada usando o comando [az keyvault key import](/cli/azure/keyvault/key#az_keyvault_key_import) da CLI do Azure:

Em ambos os casos, você fornecerá o nome da KEK ao parâmetro KEK [az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable) da CLI do Azure. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Use o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) do Azure PowerShell para gerar uma nova KEK e armazená-la no cofre de chaves.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Em vez disso, você pode importar uma chave privada usando o comando [az keyvault key import](/cli/azure/keyvault/key#az_keyvault_key_import) do Azure PowerShell.

Em ambos os casos, você fornecerá a ID do cofre de chaves da KEK e a URL da KEK para os parâmetros [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) -KeyEncryptionKeyVaultId e -KeyEncryptionKeyUrl do Azure PowerShell. Observe que este exemplo pressupõe que você esteja usando o mesmo cofre de chaves para a chave de criptografia de disco e a KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```