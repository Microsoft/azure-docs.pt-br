---
title: Implantar o modelo com segurança com o token SAS
description: Implante recursos no Azure com um modelo de Azure Resource Manager que é protegido por um token SAS. Mostra Azure PowerShell e CLI do Azure.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 8b35e82da8ebca98ec9fe1fb7441612bf61fb142
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88855665"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Implantar modelo de ARM privado com token SAS

Quando seu modelo de Azure Resource Manager (modelo ARM) está localizado em uma conta de armazenamento, você pode restringir o acesso ao modelo para evitar expô-lo publicamente. Você acessa um modelo protegido criando um token de SAS (assinatura de acesso compartilhado) para o modelo e fornecendo esse token durante a implantação. Este artigo explica como usar Azure PowerShell ou CLI do Azure para implantar um modelo com um token SAS.

> [!IMPORTANT]
> Em vez de proteger seu modelo com um token SAS, considere o uso de [especificações de modelo](template-specs.md). Com as especificações de modelo, você pode compartilhar seus modelos com outros usuários em sua organização e gerenciar o acesso aos modelos por meio do RBAC do Azure.

## <a name="create-storage-account-with-secured-container"></a>Criar conta de armazenamento com contêiner protegido

O script a seguir cria uma conta de armazenamento e um contêiner com acesso público desativado.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Carregar modelo para conta de armazenamento

Agora, você está pronto para carregar seu modelo na conta de armazenamento. Forneça o caminho para o modelo que você deseja usar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Forneça um token SAS durante a implantação

Para implantar um modelo privado em uma conta de armazenamento, gere um token SAS e inclua-o no URI para o modelo. Defina a hora de vencimento de forma a permitir que haja tempo suficiente para concluir a implantação.

> [!IMPORTANT]
> O blob que contém o modelo fica acessível somente para o proprietário da conta. No entanto, quando você cria um token SAS para o blob, o blob fica acessível para qualquer pessoa com o URI. Se outro usuário interceptar o URI, esse usuário será capaz de acessar o modelo. Um token SAS é uma boa maneira de limitar o acesso aos seus modelos, mas você não deve incluir dados confidenciais como senhas diretamente no modelo.
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

O exemplo a seguir funciona com o ambiente bash no Cloud Shell. Outros ambientes podem exigir uma sintaxe diferente para criar o tempo de expiração para o token SAS.

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Para ver um exemplo de como usar um token SAS com modelos vinculados, consulte [Usando modelos vinculados com o Azure Resource Manager](linked-templates.md).


## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução à implantação de modelos, consulte [implantar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
* Para definir os parâmetros no modelo, consulte [Criando modelos](template-syntax.md#parameters).
