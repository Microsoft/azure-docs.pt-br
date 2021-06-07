---
title: Criar uma conta que dê suporte a chaves gerenciadas pelo cliente para tabelas e filas
titleSuffix: Azure Storage
description: Saiba como criar uma conta de armazenamento que dá suporte à configuração de chaves gerenciadas pelo cliente para tabelas e filas. Use o CLI do Azure ou um modelo de Azure Resource Manager para criar uma conta de armazenamento que dependa da chave de criptografia da conta para a criptografia de armazenamento do Azure. Em seguida, você pode configurar chaves gerenciadas pelo cliente para a conta.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8150375eff98374e21d200d98c04158b07f1c243
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92789685"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Criar uma conta que dê suporte a chaves gerenciadas pelo cliente para tabelas e filas

O Armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, o armazenamento de filas e o armazenamento de tabelas usam uma chave que tem como escopo o serviço e é gerenciada pela Microsoft. Você também pode optar por usar chaves gerenciadas pelo cliente para criptografar dados de fila ou de tabela. Para usar chaves gerenciadas pelo cliente com filas e tabelas, você deve primeiro criar uma conta de armazenamento que usa uma chave de criptografia com escopo para a conta, e não para o serviço. Depois de criar uma conta que usa a chave de criptografia da conta para dados de fila e tabela, você pode configurar chaves gerenciadas pelo cliente para essa conta de armazenamento.

Este artigo descreve como criar uma conta de armazenamento que se baseia em uma chave que tem como escopo a conta. Quando a conta é criada pela primeira vez, a Microsoft usa a chave de conta para criptografar os dados na conta e a Microsoft gerencia a chave. Posteriormente, você pode configurar chaves gerenciadas pelo cliente para a conta para aproveitar esses benefícios, incluindo a capacidade de fornecer suas próprias chaves, atualizar a versão da chave, girar as chaves e revogar controles de acesso.

## <a name="about-the-feature"></a>Sobre o recurso

Para criar uma conta de armazenamento que dependa da chave de criptografia da conta para armazenamento de fila e de tabela, primeiro você deve se registrar para usar esse recurso com o Azure. Devido à capacidade limitada, lembre-se de que pode levar vários meses antes que as solicitações de acesso sejam aprovadas.

Você pode criar uma conta de armazenamento que dependa da chave de criptografia da conta para armazenamento de fila e de tabela nas seguintes regiões:

- Leste dos EUA
- Centro-Sul dos Estados Unidos
- Oeste dos EUA 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registre-se para usar a chave de criptografia da conta

Para se registrar para usar a chave de criptografia da conta com o armazenamento de fila ou de tabela, use o PowerShell ou o CLI do Azure.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para se registrar no PowerShell, chame o comando [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para se registrar com CLI do Azure, chame o comando [AZ Feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

### <a name="check-the-status-of-your-registration"></a>Verificar o status do seu registro

Para verificar o status do seu registro para armazenamento de fila ou de tabela, use o PowerShell ou CLI do Azure.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar o status do seu registro com o PowerShell, chame o comando [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar o status do seu registro com CLI do Azure, chame o comando [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Registrar novamente o provedor de recursos de armazenamento do Azure

Depois que o registro for aprovado, você deverá registrar novamente o provedor de recursos de armazenamento do Azure. Use o PowerShell ou CLI do Azure para registrar novamente o provedor de recursos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para registrar novamente o provedor de recursos com o PowerShell, chame o comando [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para registrar novamente o provedor de recursos com CLI do Azure, chame o comando [AZ Provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Criar uma conta que usa a chave de criptografia da conta

Você deve configurar uma nova conta de armazenamento para usar a chave de criptografia da conta para filas e tabelas no momento em que você cria a conta de armazenamento. O escopo da chave de criptografia não pode ser alterado depois que a conta é criada.

A conta de armazenamento deve ser do tipo de uso geral v2. Você pode criar a conta de armazenamento e configurá-la para contar com a chave de criptografia da conta usando o CLI do Azure ou um modelo de Azure Resource Manager.

> [!NOTE]
> Somente o armazenamento de fila e de tabela pode ser configurado opcionalmente para criptografar dados com a chave de criptografia da conta quando a conta de armazenamento é criada. O armazenamento de BLOBs e os arquivos do Azure sempre usam a chave de criptografia da conta para criptografar dados.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para usar o PowerShell para criar uma conta de armazenamento que dependa da chave de criptografia da conta, verifique se você instalou o módulo Azure PowerShell, versão 3.4.0 ou posterior. Para obter mais informações, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

Em seguida, crie uma conta de armazenamento de uso geral v2 chamando o comando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) , com os parâmetros apropriados:

- Inclua a `-EncryptionKeyTypeForQueue` opção e defina seu valor como `Account` para usar a chave de criptografia da conta para criptografar dados no armazenamento de filas.
- Inclua a `-EncryptionKeyTypeForTable` opção e defina seu valor como `Account` para usar a chave de criptografia da conta para criptografar dados no armazenamento de tabelas.

O exemplo a seguir mostra como criar uma conta de armazenamento v2 de uso geral configurada para o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) e que usa a chave de criptografia da conta para criptografar dados para o armazenamento de fila e de tabela. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para usar CLI do Azure para criar uma conta de armazenamento que dependa da chave de criptografia da conta, verifique se você instalou CLI do Azure versão 2.0.80 ou posterior. Para obter mais informações, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Em seguida, crie uma conta de armazenamento de uso geral v2 chamando o comando [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) , com os parâmetros apropriados:

- Inclua a `--encryption-key-type-for-queue` opção e defina seu valor como `Account` para usar a chave de criptografia da conta para criptografar dados no armazenamento de filas.
- Inclua a `--encryption-key-type-for-table` opção e defina seu valor como `Account` para usar a chave de criptografia da conta para criptografar dados no armazenamento de tabelas.

O exemplo a seguir mostra como criar uma conta de armazenamento v2 de uso geral configurada para o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) e que usa a chave de criptografia da conta para criptografar dados para o armazenamento de fila e de tabela. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Modelo](#tab/template)

O exemplo de JSON a seguir cria uma conta de armazenamento v2 de uso geral configurada para o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) e que usa a chave de criptografia da conta para criptografar dados para o armazenamento de fila e de tabela. Lembre-se de substituir os valores de espaço reservado entre colchetes angulares por seus próprios valores:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Depois de criar uma conta que dependa da chave de criptografia da conta, você pode configurar chaves gerenciadas pelo cliente que são armazenadas em Azure Key Vault ou em Key Vault modelo de segurança de hardware gerenciado (HSM) (versão prévia). Para saber como armazenar chaves gerenciadas pelo cliente em um cofre de chaves, consulte [Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault](customer-managed-keys-configure-key-vault.md). Para saber como armazenar chaves gerenciadas pelo cliente em um HSM gerenciado, consulte [Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault HSM gerenciado (versão prévia)](customer-managed-keys-configure-key-vault-hsm.md).

## <a name="verify-the-account-encryption-key"></a>Verificar a chave de criptografia da conta

Para verificar se um serviço em uma conta de armazenamento está usando a chave de criptografia da conta, chame o comando CLI do Azure [AZ Storage Account](/cli/azure/storage/account#az-storage-account-show) . Esse comando retorna um conjunto de propriedades de conta de armazenamento e seus valores. Procure o `keyType` campo de cada serviço na propriedade de criptografia e verifique se ele está definido como `Account` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar se um serviço em uma conta de armazenamento está usando a chave de criptografia da conta, chame o comando [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) . Esse comando retorna um conjunto de propriedades de conta de armazenamento e seus valores. Procure o `KeyType` campo de cada serviço dentro da `Encryption` propriedade e verifique se ele está definido como `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar se um serviço em uma conta de armazenamento está usando a chave de criptografia da conta, chame o comando [AZ Storage Account show](/cli/azure/storage/account#az-storage-account-show) . Esse comando retorna um conjunto de propriedades de conta de armazenamento e seus valores. Procure o `keyType` campo de cada serviço na propriedade de criptografia e verifique se ele está definido como `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="next-steps"></a>Próximas etapas

- [Criptografia do Armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [Chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure](customer-managed-keys-overview.md)
- [O que é Azure Key Vault](../../key-vault/general/overview.md)?