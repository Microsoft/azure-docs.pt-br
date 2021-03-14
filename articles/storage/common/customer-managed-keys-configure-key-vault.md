---
title: Configurar a criptografia com as chaves gerenciadas pelo cliente armazenadas no Azure Key Vault
titleSuffix: Azure Storage
description: Saiba como configurar a criptografia de armazenamento do Azure com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault usando o portal do Azure, o PowerShell ou o CLI do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 24fbe843986b732a04c9e356c54f3d768d6739be
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558181"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Configurar a criptografia com as chaves gerenciadas pelo cliente armazenadas no Azure Key Vault

O Armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter mais controle sobre as chaves de criptografia, você pode gerenciar suas próprias chaves. As chaves gerenciadas pelo cliente devem ser armazenadas em Azure Key Vault ou Key Vault modelo de segurança de hardware (HSM) gerenciado (versão prévia).

Este artigo mostra como configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em um cofre de chaves usando o portal do Azure, o PowerShell ou o CLI do Azure. Para saber como configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em um HSM gerenciado, consulte [Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault HSM gerenciado (versão prévia)](customer-managed-keys-configure-key-vault-hsm.md).

> [!NOTE]
> Azure Key Vault e Azure Key Vault o HSM gerenciado oferece suporte às mesmas APIs e interfaces de gerenciamento para configuração.

## <a name="configure-a-key-vault"></a>Configurar um cofre de chaves

Você pode usar um cofre de chaves novo ou existente para armazenar chaves gerenciadas pelo cliente. A conta de armazenamento e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes.

O uso de chaves gerenciadas pelo cliente com a criptografia de armazenamento do Azure exige que a exclusão reversível e a proteção de limpeza sejam habilitadas para o cofre de chaves. A exclusão reversível é habilitada por padrão quando você cria um novo cofre de chaves e não pode ser desabilitada. Você pode habilitar a proteção de limpeza ao criar o cofre de chaves ou após sua criação.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para saber como criar um cofre de chaves com o portal do Azure, consulte [início rápido: criar um cofre de chaves usando o portal do Azure](../../key-vault/general/quick-create-portal.md). Ao criar o cofre de chaves, selecione **habilitar proteção de limpeza**, conforme mostrado na imagem a seguir.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Captura de tela mostrando como habilitar a proteção de limpeza ao criar um cofre de chaves":::

Para habilitar a proteção de limpeza em um cofre de chaves existente, siga estas etapas:

1. Navegue até o cofre de chaves na portal do Azure.
1. Em **configurações**, escolha **Propriedades**.
1. Na seção **limpar proteção** , escolha **habilitar proteção de limpeza**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar um novo cofre de chaves com o PowerShell, instale a versão 2.0.0 ou posterior do módulo do PowerShell [AZ. keyvault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) . Em seguida, chame [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) para criar um novo cofre de chaves. Com a versão 2.0.0 e posterior do módulo AZ. keyvault, a exclusão reversível é habilitada por padrão quando você cria um novo cofre de chaves.

O exemplo a seguir cria um novo cofre de chaves com a exclusão reversível e a proteção de limpeza habilitada. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Para saber como habilitar a proteção de limpeza em um cofre de chaves existente com o PowerShell, consulte [como usar a exclusão reversível com o PowerShell](../../key-vault/general/key-vault-recovery.md).

Em seguida, atribua uma identidade gerenciada atribuída pelo sistema à sua conta de armazenamento. Você usará essa identidade gerenciada para conceder permissões de conta de armazenamento para acessar o cofre de chaves. Para obter mais informações sobre identidades gerenciadas atribuídas pelo sistema, consulte [o que são identidades gerenciadas para recursos do Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

Para atribuir uma identidade gerenciada usando o PowerShell, chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount):

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Por fim, configure a política de acesso para o cofre de chaves para que a conta de armazenamento tenha permissões para acessá-la. Nesta etapa, você usará a identidade gerenciada que você atribuiu anteriormente à conta de armazenamento.

Para definir a política de acesso para o cofre de chaves, chame [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy):

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar um novo cofre de chaves usando CLI do Azure, chame [AZ keyvault Create](/cli/azure/keyvault#az-keyvault-create). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Para saber como habilitar a proteção de limpeza em um cofre de chaves existente com CLI do Azure, consulte [como usar a exclusão reversível com a CLI](../../key-vault/general/key-vault-recovery.md).

Em seguida, atribua uma identidade gerenciada atribuída pelo sistema à conta de armazenamento. Você usará essa identidade gerenciada para conceder permissões de conta de armazenamento para acessar o cofre de chaves. Para obter mais informações sobre identidades gerenciadas atribuídas pelo sistema, consulte [o que são identidades gerenciadas para recursos do Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

Para atribuir uma identidade gerenciada usando CLI do Azure, chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update):

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Por fim, configure a política de acesso para o cofre de chaves para que a conta de armazenamento tenha permissões para acessá-la. Nesta etapa, você usará a identidade gerenciada que você atribuiu anteriormente à conta de armazenamento.

Para definir a política de acesso para o cofre de chaves, chame [AZ keyvault Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy):

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>Adicionar uma chave

Em seguida, adicione uma chave no cofre de chaves.

A criptografia de armazenamento do Azure dá suporte às chaves RSA e RSA-HSM de tamanhos 2048, 3072 e 4096. Para obter mais informações sobre chaves, consulte [sobre chaves](../../key-vault/keys/about-keys.md).

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para saber como adicionar uma chave com o portal do Azure, consulte [início rápido: definir e recuperar uma chave de Azure Key Vault usando o portal do Azure](../../key-vault/keys/quick-create-portal.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para adicionar uma chave com o PowerShell, chame [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para adicionar uma chave com CLI do Azure, chame [AZ keyvault Key Create](/cli/azure/keyvault/key#az-keyvault-key-create). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar a criptografia com chaves gerenciadas pelo cliente

Em seguida, configure sua conta de armazenamento do Azure para usar chaves gerenciadas pelo cliente com Azure Key Vault e, em seguida, especifique a chave a ser associada à conta de armazenamento.

Ao configurar a criptografia com chaves gerenciadas pelo cliente, você pode optar por atualizar automaticamente a versão de chave usada para a criptografia de armazenamento do Azure sempre que uma nova versão estiver disponível no cofre de chaves associado. Como alternativa, você pode especificar explicitamente uma versão de chave a ser usada para criptografia até que a versão da chave seja atualizada manualmente.

> [!NOTE]
> Para girar uma chave, crie uma nova versão da chave em Azure Key Vault. O armazenamento do Azure não manipula a rotação da chave no Azure Key Vault, portanto, você precisará girar a chave manualmente ou criar uma função para girá-la em um agendamento.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Configurar a criptografia para atualização automática de versões de chave

O armazenamento do Azure pode atualizar automaticamente a chave gerenciada pelo cliente que é usada para criptografia para usar a versão de chave mais recente. Quando a chave gerenciada pelo cliente é girada no Azure Key Vault, o armazenamento do Azure começará automaticamente a usar a versão mais recente da chave para criptografia.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para configurar chaves gerenciadas pelo cliente com a atualização automática da versão de chave no portal do Azure, siga estas etapas:

1. Navegue para sua conta de armazenamento.
1. Na folha **Configurações** da conta de armazenamento, clique em **Criptografia**. Por padrão, o gerenciamento de chaves é definido como **chaves gerenciadas da Microsoft**, conforme mostrado na imagem a seguir.

    ![Captura de tela do portal mostrando a opção de criptografia](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Selecione a opção **chaves gerenciadas pelo cliente** .
1. Escolha a opção **Selecionar do Cofre de chaves**.
1. Selecione **selecionar um cofre de chaves e uma chave**.
1. Selecione o cofre de chaves que contém a chave que você deseja usar.
1. Selecione a chave no cofre de chaves.

   ![Captura de tela mostrando como selecionar o cofre de chaves e a chave](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Salve suas alterações.

Depois de especificar a chave, o portal do Azure indica que a atualização automática da versão de chave está habilitada e exibe a versão de chave atualmente em uso para criptografia.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Captura de tela mostrando a atualização automática da versão de chave habilitada":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para configurar chaves gerenciadas pelo cliente com a atualização automática da versão de chave com o PowerShell, instale o módulo [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) , versão 2.0.0 ou posterior.

Para atualizar automaticamente a versão de chave de uma chave gerenciada pelo cliente, omita a versão da chave ao configurar a criptografia com chaves gerenciadas pelo cliente para a conta de armazenamento. Chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as configurações de criptografia da conta de armazenamento, conforme mostrado no exemplo a seguir, e inclua a opção **-KeyvaultEncryption** para habilitar chaves gerenciadas pelo cliente para a conta de armazenamento.

Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar chaves gerenciadas pelo cliente com a atualização automática da versão de chave com CLI do Azure, instale [CLI do Azure versão 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) ou posterior. Para obter mais informações, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Para atualizar automaticamente a versão de chave de uma chave gerenciada pelo cliente, omita a versão da chave ao configurar a criptografia com chaves gerenciadas pelo cliente para a conta de armazenamento. Chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) para atualizar as configurações de criptografia da conta de armazenamento, conforme mostrado no exemplo a seguir. Inclua o `--encryption-key-source` parâmetro e defina-o como `Microsoft.Keyvault` para habilitar chaves gerenciadas pelo cliente para a conta.

Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Configurar a criptografia para atualização manual de versões de chave

Se você preferir atualizar manualmente a versão da chave, especifique explicitamente a versão no momento em que configurar a criptografia com chaves gerenciadas pelo cliente. Nesse caso, o armazenamento do Azure não atualizará automaticamente a versão de chave quando uma nova versão for criada no cofre de chaves. Para usar uma nova versão de chave, você deve atualizar manualmente a versão usada para a criptografia de armazenamento do Azure.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para configurar chaves gerenciadas pelo cliente com a atualização manual da versão de chave no portal do Azure, especifique o URI de chave, incluindo a versão. Para especificar uma chave como um URI, siga estas etapas:

1. Para localizar o URI de chave no portal do Azure, navegue até o cofre de chaves e selecione a configuração **chaves** . Selecione a chave desejada e clique na chave para exibir suas versões. Selecione uma versão de chave para exibir as configurações dessa versão.
1. Copie o valor do campo **identificador de chave** , que fornece o URI.

    ![Captura de tela mostrando o URI da chave do Key Vault](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. Nas configurações de **chave de criptografia** para sua conta de armazenamento, escolha a opção **inserir URI de chave** .
1. Cole o URI que você copiou no campo **URI da chave** . Omita a versão de chave do URI para habilitar a atualização automática da versão de chave.

   ![Captura de tela mostrando como inserir o URI da chave](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Especifique a assinatura que contém o cofre de chaves.
1. Salve suas alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para configurar chaves gerenciadas pelo cliente com a atualização manual da versão de chave, forneça explicitamente a versão de chave ao configurar a criptografia para a conta de armazenamento. Chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as configurações de criptografia da conta de armazenamento, conforme mostrado no exemplo a seguir, e inclua a opção **-KeyvaultEncryption** para habilitar chaves gerenciadas pelo cliente para a conta de armazenamento.

Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Ao atualizar manualmente a versão da chave, você precisará atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão. Primeiro, chame [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) para obter a versão mais recente da chave. Em seguida, chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão da chave, conforme mostrado no exemplo anterior.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar chaves gerenciadas pelo cliente com a atualização manual da versão de chave, forneça explicitamente a versão de chave ao configurar a criptografia para a conta de armazenamento. Chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) para atualizar as configurações de criptografia da conta de armazenamento, conforme mostrado no exemplo a seguir. Inclua o `--encryption-key-source` parâmetro e defina-o como `Microsoft.Keyvault` para habilitar chaves gerenciadas pelo cliente para a conta.

Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

Ao atualizar manualmente a versão da chave, você precisará atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão. Primeiro, consulte o URI do cofre de chaves chamando [AZ keyvault show](/cli/azure/keyvault#az-keyvault-show)e para a versão de chave chamando [AZ keyvault Key List-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Em seguida, chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) para atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão da chave, conforme mostrado no exemplo anterior.

---

## <a name="change-the-key"></a>Alterar a chave

Você pode alterar a chave que está usando para a criptografia de armazenamento do Azure a qualquer momento.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para alterar a chave com o portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento e exiba as configurações de **criptografia** .
1. Selecione o cofre de chaves e escolha uma nova chave.
1. Salve suas alterações.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para alterar a chave com o PowerShell, chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) conforme mostrado em [Configurar criptografia com chaves gerenciadas pelo cliente](#configure-encryption-with-customer-managed-keys) e forneça o novo nome e versão da chave. Se a nova chave estiver em um cofre de chaves diferente, você também deverá atualizar o URI do cofre de chaves.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para alterar a chave com CLI do Azure, chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) conforme mostrado em [Configurar a criptografia com chaves gerenciadas pelo cliente](#configure-encryption-with-customer-managed-keys) e forneça o novo nome e versão da chave. Se a nova chave estiver em um cofre de chaves diferente, você também deverá atualizar o URI do cofre de chaves.

---

## <a name="revoke-customer-managed-keys"></a>Revogar chaves gerenciadas pelo cliente

A revogação de uma chave gerenciada pelo cliente remove a associação entre a conta de armazenamento e o cofre de chaves.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para revogar chaves gerenciadas pelo cliente com o portal do Azure, desabilite a chave, conforme descrito em [desabilitar chaves gerenciadas pelo cliente](#disable-customer-managed-keys).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Você pode revogar chaves gerenciadas pelo cliente removendo a política de acesso do cofre de chaves. Para revogar uma chave gerenciada pelo cliente com o PowerShell, chame o comando [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) , conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode revogar chaves gerenciadas pelo cliente removendo a política de acesso do cofre de chaves. Para revogar uma chave gerenciada pelo cliente com CLI do Azure, chame o comando [AZ keyvault Delete-Policy](/cli/azure/keyvault#az-keyvault-delete-policy) , conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Desabilitar chaves gerenciadas pelo cliente

Quando você desabilita chaves gerenciadas pelo cliente, sua conta de armazenamento é novamente criptografada com chaves gerenciadas pela Microsoft.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para desabilitar as chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento e exiba as configurações de **criptografia** .
1. Desmarque a caixa de seleção ao lado da configuração **usar sua própria chave** .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para desabilitar chaves gerenciadas pelo cliente com o PowerShell, chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) com a `-StorageEncryption` opção, conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para desabilitar as chaves gerenciadas pelo cliente com CLI do Azure, chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) e defina como `--encryption-key-source parameter` `Microsoft.Storage` , conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Próximas etapas

- [Criptografia do Armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [Chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure](customer-managed-keys-overview.md)
- [Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault HSM gerenciado (versão prévia)](customer-managed-keys-configure-key-vault-hsm.md)
