---
title: Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault HSM gerenciado (versão prévia)
titleSuffix: Azure Storage
description: Saiba como configurar a criptografia de armazenamento do Azure com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault HSM gerenciado (versão prévia) usando CLI do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9be9272a898ad48f3553d4c5e48952e1fcdde81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218631"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault HSM gerenciado (versão prévia)

O Armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter mais controle sobre as chaves de criptografia, você pode gerenciar suas próprias chaves. As chaves gerenciadas pelo cliente devem ser armazenadas em Azure Key Vault ou Key Vault modelo de segurança de hardware (HSM) gerenciado (versão prévia). Um HSM gerenciado Azure Key Vault é um HSM validado pelo FIPS 140-2 nível 3.

Este artigo mostra como configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em um HSM gerenciado usando o CLI do Azure. Para saber como configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em um cofre de chaves, consulte [Configurar a criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault](customer-managed-keys-configure-key-vault.md).

> [!IMPORTANT]
>
> A criptografia com chaves gerenciadas pelo cliente armazenadas em Azure Key Vault HSM gerenciado está atualmente em versão **prévia**. Consulte os [termos de uso complementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.
>
> Azure Key Vault e Azure Key Vault o HSM gerenciado oferece suporte às mesmas APIs e interfaces de gerenciamento para configuração.

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade à conta de armazenamento

Primeiro, atribua uma identidade gerenciada atribuída pelo sistema à conta de armazenamento. Você usará essa identidade gerenciada para conceder permissões de conta de armazenamento para acessar o HSM gerenciado. Para obter mais informações sobre identidades gerenciadas atribuídas pelo sistema, consulte [o que são identidades gerenciadas para recursos do Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

Para atribuir uma identidade gerenciada usando CLI do Azure, chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Atribuir uma função à conta de armazenamento para acessar o HSM gerenciado

Em seguida, atribua a função de **criptografia do serviço de criptografia HSM gerenciado** à identidade gerenciada da conta de armazenamento para que a conta de armazenamento tenha permissões para o HSM gerenciado. A Microsoft recomenda que você atribua o escopo da atribuição de função ao nível da chave individual para conceder o mínimo possível de privilégios à identidade gerenciada.

Para criar a atribuição de função para a conta de armazenamento, chame [AZ Key Vault role Assignment Create](/cli/azure/role/assignment#az_role_assignment_create). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Configurar a criptografia com uma chave no HSM gerenciado

Por fim, configure a criptografia de armazenamento do Azure com chaves gerenciadas pelo cliente para usar uma chave armazenada no HSM gerenciado. Os tipos de chave com suporte incluem chaves RSA-HSM de tamanhos 2048, 3072 e 4096. Instale o CLI do Azure 2.12.0 ou posterior para configurar a criptografia para usar uma chave gerenciada pelo cliente em um HSM gerenciado. Para obter mais informações, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Para atualizar automaticamente a versão de chave de uma chave gerenciada pelo cliente, omita a versão da chave ao configurar a criptografia com chaves gerenciadas pelo cliente para a conta de armazenamento. Chame [AZ Storage Account Update](/cli/azure/storage/account#az_storage_account_update) para atualizar as configurações de criptografia da conta de armazenamento, conforme mostrado no exemplo a seguir. Inclua o `--encryption-key-source parameter` e defina-o como `Microsoft.Keyvault` para habilitar chaves gerenciadas pelo cliente para a conta. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Para atualizar manualmente a versão de uma chave gerenciada pelo cliente, inclua a versão da chave ao configurar a criptografia para a conta de armazenamento:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Ao atualizar manualmente a versão da chave, você precisará atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão. Primeiro, consulte o URI do cofre de chaves chamando [AZ keyvault show](/cli/azure/keyvault#az-keyvault-show)e para a versão de chave chamando [AZ keyvault Key List-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Em seguida, chame [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) para atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão da chave, conforme mostrado no exemplo anterior.

## <a name="next-steps"></a>Próximas etapas

- [Criptografia do Armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [Chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure](customer-managed-keys-overview.md)
