---
title: Criar uma conta de armazenamento com criptografia de infraestrutura habilitada para criptografia dupla de dados
titleSuffix: Azure Storage
description: Os clientes que precisam de níveis mais altos de garantia de que seus dados são seguros também podem habilitar a criptografia AES de 256 bits no nível de infraestrutura de armazenamento do Azure. Quando a criptografia de infraestrutura está habilitada, os dados em uma conta de armazenamento são criptografados duas vezes com dois algoritmos de criptografia diferentes e duas chaves diferentes.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 612ba18ba71a22ad6c346b26008e688195c1d1e4
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746579"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Criar uma conta de armazenamento com criptografia de infraestrutura habilitada para criptografia dupla de dados

O armazenamento do Azure criptografa automaticamente todos os dados em uma conta de armazenamento no nível de serviço usando a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2. Os clientes que precisam de níveis mais altos de garantia de que seus dados são seguros também podem habilitar a criptografia AES de 256 bits no nível de infraestrutura de armazenamento do Azure. Quando a criptografia de infraestrutura está habilitada, os dados em uma conta de armazenamento são criptografados duas vezes &mdash; uma vez no nível de serviço e uma vez no nível de infraestrutura &mdash; com dois algoritmos de criptografia diferentes e duas chaves diferentes. A criptografia dupla de dados do armazenamento do Azure protege contra um cenário em que um dos algoritmos ou chaves de criptografia pode ser comprometido. Nesse cenário, a camada adicional de criptografia continua a proteger seus dados.

A criptografia de nível de serviço dá suporte ao uso de chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente com Azure Key Vault ou Key Vault o HSM (modelo de segurança de hardware) gerenciado (versão prévia). A criptografia no nível de infraestrutura depende de chaves gerenciadas pela Microsoft e sempre usa uma chave separada. Para obter mais informações sobre o gerenciamento de chaves com a criptografia de armazenamento do Azure, consulte [sobre gerenciamento de chaves de criptografia](storage-service-encryption.md#about-encryption-key-management).

Para criptografar os dados duplicados, você deve primeiro criar uma conta de armazenamento configurada para criptografia de infraestrutura. Este artigo descreve como criar uma conta de armazenamento que habilita a criptografia de infraestrutura.

## <a name="register-to-use-infrastructure-encryption"></a>Registrar-se para usar a criptografia de infraestrutura

Para criar uma conta de armazenamento com criptografia de infraestrutura habilitada, primeiro você deve se registrar para usar esse recurso com o Azure usando o PowerShell ou CLI do Azure.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para se registrar no PowerShell, chame o comando [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Para verificar o status do seu registro com o PowerShell, chame o comando [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Depois que o registro for aprovado, você deverá registrar novamente o provedor de recursos de armazenamento do Azure. Para registrar novamente o provedor de recursos com o PowerShell, chame o comando [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para se registrar com CLI do Azure, chame o comando [AZ Feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Para verificar o status do seu registro com CLI do Azure, chame o comando [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Depois que o registro for aprovado, você deverá registrar novamente o provedor de recursos de armazenamento do Azure. Para registrar novamente o provedor de recursos com CLI do Azure, chame o comando [AZ Provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Criar uma conta com criptografia de infraestrutura habilitada

Você deve configurar uma conta de armazenamento para usar a criptografia de infraestrutura no momento em que você criar a conta. A conta de armazenamento deve ser do tipo de uso geral v2.

A criptografia de infraestrutura não pode ser habilitada ou desabilitada após a criação da conta.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para usar o PowerShell para criar uma conta de armazenamento com a criptografia de infraestrutura habilitada, siga estas etapas:

1. Na portal do Azure, navegue até a página **contas de armazenamento** .
1. Escolha o botão **Adicionar** para adicionar uma nova conta de armazenamento v2 de uso geral.
1. Na guia **avançado** , localize criptografia de **infraestrutura** e selecione **habilitado** .
1. Selecione **examinar + criar** para concluir a criação da conta de armazenamento.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Captura de tela mostrando como habilitar a criptografia de infraestrutura ao criar a conta":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para usar o PowerShell para criar uma conta de armazenamento com a criptografia de infraestrutura habilitada, verifique se você instalou o [módulo AZ. Storage PowerShell](https://www.powershellgallery.com/packages/Az.Storage), versão 2.2.0 ou posterior. Para obter mais informações, consulte [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).

Em seguida, crie uma conta de armazenamento de uso geral v2 chamando o comando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) . Inclua a `-RequireInfrastructureEncryption` opção para habilitar a criptografia de infraestrutura.

O exemplo a seguir mostra como criar uma conta de armazenamento de uso geral v2 configurada para o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) e tem criptografia de infraestrutura habilitada para criptografia dupla de dados. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para usar CLI do Azure para criar uma conta de armazenamento que tenha a criptografia de infraestrutura habilitada, verifique se você instalou CLI do Azure versão 2.8.0 ou posterior. Para obter mais informações, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Em seguida, crie uma conta de armazenamento de uso geral v2 chamando o comando [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) e inclua o `--require-infrastructure-encryption option` para habilitar a criptografia de infraestrutura.

O exemplo a seguir mostra como criar uma conta de armazenamento de uso geral v2 configurada para o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) e tem criptografia de infraestrutura habilitada para criptografia dupla de dados. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Modelo](#tab/template)

O exemplo JSON a seguir cria uma conta de armazenamento v2 de uso geral configurada para o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) e tem criptografia de infraestrutura habilitada para criptografia dupla de dados. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Verificar se a criptografia de infraestrutura está habilitada

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para verificar se a criptografia de infraestrutura está habilitada para uma conta de armazenamento com o portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Em **configurações** , escolha **criptografia** .

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Captura de tela mostrando como habilitar a criptografia de infraestrutura ao criar a conta":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar se a criptografia de infraestrutura está habilitada para uma conta de armazenamento com o PowerShell, chame o comando [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) . Esse comando retorna um conjunto de propriedades de conta de armazenamento e seus valores. Recupere o `RequireInfrastructureEncryption` campo dentro da `Encryption` propriedade e verifique se ele está definido como `True` .

O exemplo a seguir recupera o valor da `RequireInfrastructureEncryption` propriedade. Lembre-se de substituir os valores de espaço reservado entre colchetes angulares por seus próprios valores:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar se a criptografia de infraestrutura está habilitada para uma conta de armazenamento com CLI do Azure, chame o comando [AZ Storage Account show](/cli/azure/storage/account#az-storage-account-show) . Esse comando retorna um conjunto de propriedades de conta de armazenamento e seus valores. Procure o `requireInfrastructureEncryption` campo na `encryption` propriedade e verifique se ele está definido como `true` .

O exemplo a seguir recupera o valor da `requireInfrastructureEncryption` propriedade. Lembre-se de substituir os valores de espaço reservado entre colchetes angulares por seus próprios valores:

```azurecli-interactive
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
