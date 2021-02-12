---
title: Implantar modelos com Cloud Shell
description: Use Azure Resource Manager e Azure Cloud Shell para implantar recursos no Azure. Os recursos são definidos em um modelo de Azure Resource Manager (modelo ARM).
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: c67251a33b6197603be27086bcc6cd047e0c414b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028600"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>Implantar modelos de ARM de Azure Cloud Shell

Você pode usar [Azure cloud Shell](../../cloud-shell/overview.md) para implantar um modelo de Azure Resource Manager (modelo ARM). Você pode implantar um modelo do ARM que é armazenado remotamente ou um modelo do ARM que é armazenado na conta de armazenamento local para Cloud Shell.

Você pode implantar em qualquer escopo. Este artigo mostra a implantação em um grupo de recursos.

## <a name="deploy-remote-template"></a>Implantar modelo remoto

Para implantar um modelo externo, forneça o URI do modelo, exatamente como você faria para qualquer implantação externa. O modelo externo pode estar em um repositório GitHub ou em uma conta de armazenamento externo.

1. Abra o prompt de Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Abrir Cloud Shell":::

1. Para implantar o modelo, use os seguintes comandos:

   # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>Implantar o modelo local

Para implantar um modelo local, primeiro você deve carregar o modelo para a conta de armazenamento que está conectada à sua sessão de Cloud Shell.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione o grupo de recursos do Cloud Shell. O nome padrão é `cloud-shell-storage-<region>`.

   ![Escolha o grupo de recursos](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Selecione a conta de armazenamento do Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="Escolher conta de armazenamento":::

1. Selecione **compartilhamentos de arquivos**.

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="Selecionar compartilhamentos de arquivos":::

1. Selecione o compartilhamento de arquivos padrão para Cloud Shell. O compartilhamento de arquivos tem o formato de nome de `cs-<user>-<domain>-com-<uniqueGuid>` .

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="Compartilhamento de arquivos padrão":::

1. Adicione um novo diretório para manter seus modelos. Selecione esse diretório.

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="Adicionar diretório":::

1. Escolha **Carregar**.

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="Carregar modelo":::

1. Localizar e carregar o modelo.

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="Selecionar modelo":::

1. Abra o prompt de Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Abrir Cloud Shell":::

1. Navegue até o diretório **CloudDrive** Navegue até o diretório que você adicionou para conter os modelos.

1. Para implantar o modelo, use os seguintes comandos:

   # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre os comandos de implantação, consulte [implantar recursos com modelos ARM e CLI do Azure](deploy-cli.md) e [implantar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
- Para visualizar as alterações antes de implantar um modelo, consulte [operação de hipóteses de implantação de modelo ARM](template-deploy-what-if.md).
