---
title: Configurar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure da CLI do Azure
description: Saiba como usar a CLI do Azure para configurar as chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure. Chaves gerenciadas pelo cliente permitem que você criar, girar, desabilitar e revogar os controles de acesso.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/24/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 925b69e064e260a78a102a068f052ad7d396c380
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357071"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Configurar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure da CLI do Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando a CLI do Azure.

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade para a conta de armazenamento

Para habilitar as chaves gerenciadas pelo cliente para sua conta de armazenamento, primeiro atribua uma identidade gerenciada atribuído pelo sistema para a conta de armazenamento. Você usará essa identidade gerenciada para conceder as permissões de conta de armazenamento para acessar o Cofre de chaves.

Para atribuir uma identidade gerenciada usando a CLI do Azure, chame [atualização de conta de armazenamento az](/cli/azure/storage/account#az-storage-account-update). Lembre-se de substituir os valores de espaço reservado entre colchetes pelos seus próprios valores.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Para obter mais informações sobre a configuração atribuída pelo sistema de identidades gerenciadas com a CLI do Azure, consulte [configurar gerenciadas identidades para recursos do Azure em uma VM do Azure usando a CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Criar um novo cofre de chaves

O Cofre de chaves que você usa para armazenar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure deve ter duas configurações de proteção de chave habilitadas, **exclusão reversível** e **não limpar**. Para criar um novo cofre de chaves usando o PowerShell ou CLI do Azure com essas configurações habilitadas, execute os comandos a seguir. Lembre-se de substituir os valores de espaço reservado entre colchetes pelos seus próprios valores. 

Para criar um novo cofre de chaves usando a CLI do Azure, chame [az creata](/cli/azure/keyvault#az-keyvault-create). Lembre-se de substituir os valores de espaço reservado entre colchetes pelos seus próprios valores.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurar a política de acesso do Cofre de chaves

Em seguida, configure a política de acesso para o Cofre de chaves para que a conta de armazenamento tem permissões para acessá-lo. Nesta etapa, você usará a identidade gerenciada atribuídos previamente a conta de armazenamento.

Para definir a política de acesso para o Cofre de chaves, chame [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Lembre-se de substituir os valores de espaço reservado entre colchetes pelos seus próprios valores.

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
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Criar uma nova chave

Em seguida, crie uma chave no cofre de chaves. Para criar uma chave, chame [criar chave de keyvault az](/cli/azure/keyvault/key#az-keyvault-key-create). Lembre-se de substituir os valores de espaço reservado entre colchetes pelos seus próprios valores.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar a criptografia com chaves gerenciadas pelo cliente

Por padrão, a criptografia de armazenamento do Azure usa chaves gerenciadas pela Microsoft. Configurar sua conta de armazenamento do Azure para chaves gerenciadas pelo cliente e especifique a chave para associar a conta de armazenamento.

Para atualizar as configurações de criptografia da conta de armazenamento, chame [atualização de conta de armazenamento az](/cli/azure/storage/account#az-storage-account-update). Este exemplo também consulta para o URI de Cofre de chaves e a versão da chave, ambos os valores que são necessárias para associar a chave com a conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre colchetes pelos seus próprios valores.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Atualizar a versão da chave

Quando você cria uma nova versão de uma chave, você precisará atualizar a conta de armazenamento para usar a nova versão. Primeiro, consultar o URI do Cofre de chaves, chamando [show do az keyvault](/cli/azure/keyvault#az-keyvault-show)e para a versão da chave chamando [versões lista az keyvault chave](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Em seguida, chame [atualização de conta de armazenamento az](/cli/azure/storage/account#az-storage-account-update) para atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão da chave, como mostrado na seção anterior.

## <a name="next-steps"></a>Próximas etapas

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md) 
- [O que é o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
