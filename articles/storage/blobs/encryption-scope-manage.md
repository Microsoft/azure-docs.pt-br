---
title: Criar e gerenciar escopos de criptografia
description: Saiba como criar um escopo de criptografia para isolar dados de blob no nível de contêiner ou BLOB.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: c29282637f6854248c98dff59f8fae46ad1a9d39
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640577"
---
# <a name="create-and-manage-encryption-scopes"></a>Criar e gerenciar escopos de criptografia

Os escopos de criptografia permitem gerenciar a criptografia no nível de um blob ou contêiner individual. Você pode usar escopos de criptografia para criar limites seguros entre os dados que residem na mesma conta de armazenamento, mas que pertencem a clientes diferentes. Para obter mais informações sobre escopos de criptografia, consulte [escopos de criptografia para armazenamento de BLOBs](encryption-scope-overview.md).

Este artigo mostra como criar um escopo de criptografia. Ele também mostra como especificar um escopo de criptografia quando você cria um BLOB ou contêiner.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>Criar um escopo de criptografia

Você pode criar um escopo de criptografia que é protegido com uma chave gerenciada pela Microsoft ou com uma chave gerenciada pelo cliente que é armazenada em um Azure Key Vault ou em um Azure Key Vault modelo de segurança de hardware gerenciado (HSM) (versão prévia). Para criar um escopo de criptografia com uma chave gerenciada pelo cliente, você deve primeiro criar um cofre de chaves ou HSM gerenciado e adicionar a chave que você pretende usar para o escopo. O cofre de chaves ou HSM gerenciado deve ter a proteção de limpeza habilitada e deve estar na mesma região que a conta de armazenamento.

Um escopo de criptografia é habilitado automaticamente quando você o cria. Depois de criar o escopo de criptografia, você pode especificá-lo ao criar um blob. Você também pode especificar um escopo de criptografia padrão ao criar um contêiner, que se aplica automaticamente a todos os BLOBs no contêiner.

# <a name="portal"></a>[Portal](#tab/portal)

Para criar um escopo de criptografia no portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Selecione a configuração de **criptografia** .
1. Selecione a guia **escopos de criptografia** .
1. Clique no botão **Adicionar** para adicionar um novo escopo de criptografia.
1. No painel **criar escopo de criptografia** , insira um nome para o novo escopo.
1. Selecione o tipo desejado de suporte à chave de criptografia, **chaves gerenciadas pela Microsoft** ou **chaves gerenciadas pelo cliente**.
    - Se você selecionou **chaves gerenciadas pela Microsoft**, clique em **criar** para criar o escopo de criptografia.
    - Se você selecionou **chaves gerenciadas pelo cliente**, selecione uma assinatura e especifique um cofre de chaves ou um HSM gerenciado e uma chave para usar para esse escopo de criptografia, conforme mostrado na imagem a seguir.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Captura de tela mostrando como criar o escopo de criptografia no portal do Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar um escopo de criptografia com o PowerShell, instale o módulo [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) PowerShell, versão 3.4.0 ou posterior.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Criar um escopo de criptografia protegido por chaves gerenciadas pela Microsoft

Para criar um novo escopo de criptografia protegido por chaves gerenciadas pela Microsoft, chame o comando **New-AzStorageEncryptionScope** com o `-StorageEncryption` parâmetro.

Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Criar um escopo de criptografia protegido por chaves gerenciadas pelo cliente

Para criar um novo escopo de criptografia protegido por chaves gerenciadas pelo cliente armazenadas em um cofre de chaves ou HSM gerenciado, primeiro configure as chaves gerenciadas pelo cliente para a conta de armazenamento. Você deve atribuir uma identidade gerenciada à conta de armazenamento e, em seguida, usar a identidade gerenciada para configurar a política de acesso para o cofre de chaves ou HSM gerenciado para que a conta de armazenamento tenha permissões para acessá-la.

Para configurar chaves gerenciadas pelo cliente para uso com um escopo de criptografia, a proteção de limpeza deve ser habilitada no cofre de chaves ou HSM gerenciado. O cofre de chaves ou HSM gerenciado deve estar na mesma região que a conta de armazenamento.

Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri>"
$scopeName2 = "customer2scope"

# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Em seguida, chame o comando **New-AzStorageEncryptionScope** com o `-KeyvaultEncryption` parâmetro e especifique o URI da chave. A inclusão da versão de chave no URI de chave é opcional. Se você omitir a versão de chave, o escopo de criptografia usará automaticamente a versão de chave mais recente. Se você incluir a versão de chave, deverá atualizar a versão de chave manualmente para usar uma versão diferente.

Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para criar um escopo de criptografia com CLI do Azure, primeiro instale CLI do Azure versão 2.20.0 ou posterior.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Criar um escopo de criptografia protegido por chaves gerenciadas pela Microsoft

Para criar um novo escopo de criptografia que é protegido por chaves gerenciadas pela Microsoft, chame o comando [AZ Storage Account Encryption-escopo Create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) , especificando o `--key-source` parâmetro como `Microsoft.Storage` . Lembre-se de substituir os valores dos espaços reservados por seus próprios valores:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Criar um escopo de criptografia protegido por chaves gerenciadas pelo cliente

Para criar um novo escopo de criptografia que é protegido por chaves gerenciadas pela Microsoft, chame o comando [AZ Storage Account Encryption-escopo Create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) , especificando o `--key-source` parâmetro como `Microsoft.Storage` . Lembre-se de substituir os valores dos espaços reservados por seus próprios valores:

Para criar um novo escopo de criptografia protegido por chaves gerenciadas pelo cliente em um cofre de chaves ou HSM gerenciado, primeiro configure as chaves gerenciadas pelo cliente para a conta de armazenamento. Você deve atribuir uma identidade gerenciada à conta de armazenamento e, em seguida, usar a identidade gerenciada para configurar a política de acesso para o cofre de chaves para que a conta de armazenamento tenha permissões para acessá-la. Para obter mais informações, confira [Chaves gerenciadas pelo cliente para criptografia do Armazenamento do Azure](../common/customer-managed-keys-overview.md).

Para configurar chaves gerenciadas pelo cliente para uso com um escopo de criptografia, a proteção de limpeza deve ser habilitada no cofre de chaves ou HSM gerenciado. O cofre de chaves ou HSM gerenciado deve estar na mesma região que a conta de armazenamento.

Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

Em seguida, chame o comando **AZ Storage Account Encryption-escopo Create** com o `--key-uri` parâmetro e especifique o URI da chave. A inclusão da versão de chave no URI de chave é opcional. Se você omitir a versão de chave, o escopo de criptografia usará automaticamente a versão de chave mais recente. Se você incluir a versão de chave, deverá atualizar a versão de chave manualmente para usar uma versão diferente.

Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Para saber como configurar a criptografia de armazenamento do Azure com chaves gerenciadas pelo cliente em um cofre de chaves ou HSM gerenciado, consulte os seguintes artigos:

- [Configurar a criptografia com as chaves gerenciadas pelo cliente armazenadas no Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md)
- [Configure a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault HSM gerenciado (versão prévia)](../common/customer-managed-keys-configure-key-vault-hsm.md).

## <a name="list-encryption-scopes-for-storage-account"></a>Listar escopos de criptografia para a conta de armazenamento

# <a name="portal"></a>[Portal](#tab/portal)

Para exibir os escopos de criptografia para uma conta de armazenamento no portal do Azure, navegue até a configuração **escopos de criptografia** para a conta de armazenamento. Nesse painel, você pode habilitar ou desabilitar um escopo de criptografia ou alterar a chave de um escopo de criptografia.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Captura de tela mostrando a lista de escopos de criptografia no portal do Azure":::

Para exibir detalhes de uma chave gerenciada pelo cliente, incluindo o URI e a versão da chave e se a versão da chave é atualizada automaticamente, siga o link na coluna **chave** .

:::image type="content" source="media/encryption-scope-manage/customer-managed-key-details-portal.png" alt-text="Captura de tela mostrando detalhes de uma chave usada com um escopo de criptografia":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para listar os escopos de criptografia disponíveis para uma conta de armazenamento com o PowerShell, chame o comando **Get-AzStorageEncryptionScope** . Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Para listar todos os escopos de criptografia em um grupo de recursos por conta de armazenamento, use a sintaxe do pipeline:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para listar os escopos de criptografia disponíveis para uma conta de armazenamento com CLI do Azure, chame o comando [AZ Storage Account Encryption-escopo List](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) . Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Criar um contêiner com um escopo de criptografia padrão

Ao criar um contêiner, você pode especificar um escopo de criptografia padrão. Os BLOBs nesse contêiner usarão esse escopo por padrão.

Um blob individual pode ser criado com seu próprio escopo de criptografia, a menos que o contêiner esteja configurado para exigir que todos os BLOBs usem o escopo padrão. Para obter mais informações, consulte [escopos de criptografia para contêineres e blobs](encryption-scope-overview.md#encryption-scopes-for-containers-and-blobs).

# <a name="portal"></a>[Portal](#tab/portal)

Para criar um contêiner com um escopo de criptografia padrão no portal do Azure, primeiro crie o escopo de criptografia conforme descrito em [criar um escopo de criptografia](#create-an-encryption-scope). Em seguida, siga estas etapas para criar o contêiner:

1. Navegue até a lista de contêineres em sua conta de armazenamento e selecione o botão **Adicionar** para criar um novo contêiner.
1. Expanda as configurações **avançadas** no painel **novo contêiner** .
1. Na lista suspensa **escopo de criptografia** , selecione o escopo de criptografia padrão para o contêiner.
1. Para exigir que todos os BLOBs no contêiner usem o escopo de criptografia padrão, marque a caixa de seleção para **usar esse escopo de criptografia para todos os BLOBs no contêiner**. Se essa caixa de seleção estiver selecionada, um blob individual no contêiner não poderá substituir o escopo de criptografia padrão.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Captura de tela mostrando contêiner com escopo de criptografia padrão":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar um contêiner com um escopo de criptografia padrão com o PowerShell, chame o comando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) , especificando o escopo para o `-DefaultEncryptionScope` parâmetro. Para forçar todos os BLOBs em um contêiner a usar o escopo padrão do contêiner, defina o `-PreventEncryptionScopeOverride` parâmetro como `true` .

```powershell
$containerName1 = "container1"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a container with a default encryption scope that cannot be overridden.
New-AzStorageContainer -Name $containerName1 `
    -Context $ctx `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para criar um contêiner com um escopo de criptografia padrão com CLI do Azure, chame o comando [AZ Storage container Create](/cli/azure/storage/container#az-storage-container-create) , especificando o escopo para o `--default-encryption-scope` parâmetro. Para forçar todos os BLOBs em um contêiner a usar o escopo padrão do contêiner, defina o `--prevent-encryption-scope-override` parâmetro como `true` .

O exemplo a seguir usa sua conta do Azure AD para autorizar a operação para criar o contêiner. Você também pode usar a chave de acesso da conta. Para obter mais informações, confira [Autorizar o acesso a dados de blob ou de filas com a CLI do Azure](./authorize-data-operations-cli.md).

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

Se um cliente tentar especificar um escopo ao carregar um blob em um contêiner que tenha um escopo de criptografia padrão e o contêiner estiver configurado para impedir que os BLOBs substituam o escopo padrão, a operação falhará com uma mensagem indicando que a solicitação é proibida pela política de criptografia do contêiner.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Carregar um blob com um escopo de criptografia

Ao carregar um blob, você pode especificar um escopo de criptografia para esse BLOB ou usar o escopo de criptografia padrão para o contêiner, se um tiver sido especificado.

# <a name="portal"></a>[Portal](#tab/portal)

Para carregar um blob com um escopo de criptografia por meio do portal do Azure, primeiro crie o escopo de criptografia conforme descrito em [criar um escopo de criptografia](#create-an-encryption-scope). Em seguida, siga estas etapas para criar o blob:

1. Navegue até o contêiner para o qual você deseja carregar o blob.
1. Selecione o botão **carregar** e localize o blob a ser carregado.
1. Expanda as configurações **avançadas** no painel **carregar blob** .
1. Localize a seção suspensa **escopo de criptografia** . Por padrão, o blob é criado com o escopo de criptografia padrão para o contêiner, se um tiver sido especificado. Se o contêiner exigir que os BLOBs usem o escopo de criptografia padrão, esta seção será desabilitada.
1. Para especificar um escopo diferente para o blob que você está carregando, selecione **escolher um escopo existente** e, em seguida, selecione o escopo desejado na lista suspensa.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Captura de tela mostrando como carregar um blob com um escopo de criptografia":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para carregar um blob com um escopo de criptografia por meio do PowerShell, chame o comando [set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) e forneça o escopo de criptografia para o blob.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx

# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx `
    -Container $containerName2 `
    -File $localSrcFile `
    -Blob "helloworld.txt" `
    -BlobType Block `
    -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para carregar um blob com um escopo de criptografia via CLI do Azure, chame o comando [AZ Storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload) e forneça o escopo de criptografia para o blob.

Se você estiver usando Azure Cloud Shell, siga as etapas descritas em [carregar um blob](storage-quickstart-blobs-cli.md#upload-a-blob) para criar um arquivo no diretório raiz. Em seguida, você pode carregar esse arquivo em um BLOB usando o exemplo a seguir.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>Alterar a chave de criptografia para um escopo

Para alterar a chave que protege um escopo de criptografia de uma chave gerenciada pela Microsoft para uma chave gerenciada pelo cliente, primeiro verifique se você habilitou as chaves gerenciadas pelo cliente com Azure Key Vault ou Key Vault HSM para a conta de armazenamento. Para obter mais informações, consulte [Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md) ou [Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas no Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md).

# <a name="portal"></a>[Portal](#tab/portal)

Para alterar a chave que protege um escopo no portal do Azure, siga estas etapas:

1. Navegue até a guia **escopos de criptografia** para exibir a lista de escopos de criptografia para a conta de armazenamento.
1. Selecione o botão **mais** ao lado do escopo que você deseja modificar.
1. No painel **Editar escopo de criptografia** , você pode alterar o tipo de criptografia da chave gerenciada pela Microsoft para a chave gerenciada pelo cliente ou vice-versa.
1. Para selecionar uma nova chave gerenciada pelo cliente, selecione **usar uma nova chave** e especifique o cofre de chaves, a chave e a versão da chave.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para alterar a chave que protege um escopo de criptografia de uma chave gerenciada pelo cliente para uma chave gerenciada pela Microsoft com o PowerShell, chame o comando **Update-AzStorageEncryptionScope** e passe o `-StorageEncryption` parâmetro:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Em seguida, chame o comando **Update-AzStorageEncryptionScope** e passe os `-KeyUri` `-KeyvaultEncryption` parâmetros e:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para alterar a chave que protege um escopo de criptografia de uma chave gerenciada pelo cliente para uma chave gerenciada pela Microsoft com CLI do Azure, chame o comando [AZ Storage Account Encryption-Scope Update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) e passe o `--key-source` parâmetro com o valor `Microsoft.Storage` :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Em seguida, chame o comando **AZ Storage Account Encryption-Scope Update** , passe o `--key-uri` parâmetro e passe o `--key-source` parâmetro com o valor `Microsoft.KeyVault` :

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Desabilitar um escopo de criptografia

Quando um escopo de criptografia estiver desabilitado, você não será mais cobrado por ele. Desabilite os escopos de criptografia que não são necessários para evitar encargos desnecessários. Para obter mais informações, consulte [Criptografia do Armazenamento do Azure para dados em repouso](../common/storage-service-encryption.md).

# <a name="portal"></a>[Portal](#tab/portal)

Para desabilitar um escopo de criptografia no portal do Azure, navegue até a configuração **escopos de criptografia** para a conta de armazenamento, selecione o escopo de criptografia desejado e selecione **desabilitar**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para desabilitar um escopo de criptografia com o PowerShell, chame o comando Update-AzStorageEncryptionScope e inclua o `-State` parâmetro com um valor de `disabled` , conforme mostrado no exemplo a seguir. Para reabilitar um escopo de criptografia, chame o mesmo comando com o `-State` parâmetro definido como `enabled` . Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Para desabilitar um escopo de criptografia com CLI do Azure, chame o comando [AZ Storage Account Encryption-Scope Update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) e inclua o `--state` parâmetro com um valor de `Disabled` , conforme mostrado no exemplo a seguir. Para reabilitar um escopo de criptografia, chame o mesmo comando com o `--state` parâmetro definido como `Enabled` . Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

> [!IMPORTANT]
> Não é possível excluir um escopo de criptografia. Para evitar custos inesperados, certifique-se de desabilitar os escopos de criptografia que não são necessários no momento.

---

## <a name="next-steps"></a>Próximas etapas

- [Criptografia do Armazenamento do Azure para dados em repouso](../common/storage-service-encryption.md)
- [Escopos de criptografia para armazenamento de BLOBs](encryption-scope-overview.md)
- [Chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure](../common/customer-managed-keys-overview.md)