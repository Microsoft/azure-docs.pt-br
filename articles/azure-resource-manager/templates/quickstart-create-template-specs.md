---
title: Criar e implantar especificação de modelo
description: Saiba como criar uma especificação usando um modelo do ARM. Implante a especificação de modelo em um grupo de recursos em sua assinatura.
author: tfitzmac
ms.date: 12/14/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 28987486726f5a88d20efe9fe8a766e536062c2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889953"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Início Rápido: Criar e implantar especificação de modelo (versão prévia)

Este guia de início rápido mostra como empacotar um modelo do ARM (modelo do Azure Resource Manager) em uma [especificação de modelo](template-specs.md). Em seguida, você implantará essa especificação de modelo. A especificação de modelo contém um modelo do ARM que implanta uma conta de armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> As especificações de modelo estão atualmente em versão prévia. Para usá-lo com o Azure PowerShell, você precisa instalar a [versão 5.0.0 ou posterior](/powershell/azure/install-az-ps). Para usá-lo com a CLI do Azure, use a [versão 2.14.2 ou posterior](/cli/azure/install-azure-cli).

## <a name="create-template"></a>Criar modelo

Você criará uma especificação de modelo com base em um modelo local. Copie o modelo a seguir e salve-o localmente em um arquivo chamado **azuredeploy.json**. Este início rápido assume que você salvou no caminho **c:\Templates\azuredeploy.json**, mas é possível usar qualquer caminho.

:::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>Criar especificação de modelo

A especificação de modelo é um tipo de recurso chamado `Microsoft.Resources/templateSpecs`. Para criar uma especificação de modelo, use o PowerShell, a CLI do Azure, o portal ou um modelo do ARM.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie um grupo de recursos para conter a especificação de modelo.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Crie a especificação de modelo nesse grupo de recursos. Nomeie a nova especificação de modelo **storageSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Crie um grupo de recursos para conter a especificação de modelo.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Crie a especificação de modelo nesse grupo de recursos. Nomeie a nova especificação de modelo **storageSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procure **especificações de modelo**. Selecione **Especificações de modelo** nas opções disponíveis.

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="procurar especificações do modelo":::

1. Selecione **Importar modelo**.

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="importar modelo":::

1. Selecione o ícone de pasta.

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="abrir pasta":::

1. Procure o modelo local que você salvou e selecione-o. Selecione **Abrir**.
1. Selecione **Importar**.

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="selecionar opção de importação":::

1. Forneça os seguintes valores:

    - **Nome**: insira um nome para a especificação de modelo.  Por exemplo, **storageSpec**
    - **Assinatura**: selecione uma assinatura do Azure usada para criar a especificação de modelo.
    - **Grupo de recursos**: selecione **Criar** e insira um novo nome do grupo de recursos.  Por exemplo, **templateSpecRG**.
    - **Local**: selecione um local para o grupo de recursos. Por exemplo, **Oeste dos EUA 2**.
    - **Versão**: insira uma versão para a especificação de modelo. Use **1.0**.

1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

# <a name="arm-template"></a>[Modelo do ARM](#tab/azure-resource-manager)

> [!NOTE]
> Em vez de usar um modelo do ARM, recomendamos que você use o PowerShell ou a CLI para criar a especificação de modelo. Essas ferramentas convertem automaticamente os modelos vinculados em artefatos conectados ao modelo principal. Ao usar um modelo do ARM para criar a especificação de modelo, você precisará adicionar manualmente esses modelos vinculados como artefatos, o que pode ser complicado.

1. Quando você usa um modelo do ARM para criar a especificação de modelo, o modelo é incorporado na definição de recursos. Há algumas alterações que você precisará fazer no modelo local. Copie o modelo a seguir e salve-o localmente como **azuredeploy.json**.

    > [!NOTE]
    > No modelo inserido, todas as [expressões do modelo](template-expressions.md) devem ser precedidas por um segundo colchete à esquerda. Use `"[[` em vez de `"[`. As matrizes JSON ainda usam apenas um colchete à esquerda.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Use a CLI do Azure ou o PowerShell para criar um grupo de recursos.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Implante seu modelo com a CLI do Azure ou o PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Implantar especificação de modelo

Para implantar uma especificação de modelo, use os mesmos comandos de implantação que usaria para implantar um modelo. Transmita a ID do recurso da especificação de modelo a ser implantada.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie um grupo de recursos para conter a nova conta de armazenamento.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Obtenha a ID do recurso da especificação de modelo.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Implante a especificação de modelo.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. Você fornece parâmetros exatamente como faria para um modelo do ARM. Reimplante a especificação de modelo com um parâmetro para o tipo de conta de armazenamento.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Crie um grupo de recursos para conter a nova conta de armazenamento.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Obtenha a ID do recurso da especificação de modelo.

    ```azurecli
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Há um problema conhecido na obtenção de uma ID de especificação de modelo e na atribuição dela a uma variável no Windows PowerShell.

1. Implante a especificação de modelo.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. Você fornece parâmetros exatamente como faria para um modelo do ARM. Reimplante a especificação de modelo com um parâmetro para o tipo de conta de armazenamento.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecione a especificação de modelo que você criou.

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="selecionar as especificações de modelo":::

1. Selecione **Implantar**.

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="implantar as especificações de modelo":::

1. Forneça os seguintes valores:

    - **Assinatura**: selecione uma assinatura do Azure usada para criar o recurso.
    - **Grupo de recursos**: selecione **Criar** e insira **storageRG**.
    - **Tipo de Conta de Armazenamento**: selecione **Standard_GRS**.

1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

# <a name="arm-template"></a>[Modelo do ARM](#tab/azure-resource-manager)

1. Copie o modelo a seguir e salve-o localmente em um arquivo chamado **storage.json**.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-10-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Use a CLI do Azure ou o PowerShell a fim de criar um grupo de recursos para a conta de armazenamento.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Implante seu modelo com a CLI do Azure ou o PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Conceder acesso

Se você quiser permitir que outros usuários na sua organização implantem sua especificação de modelo, precisará conceder acesso de leitura a eles. Você pode atribuir a função Leitor a um grupo do Azure AD do grupo de recursos que contém as especificações de modelo que deseja compartilhar. Para saber mais, confira [Tutorial: Conceder a um grupo acesso aos recursos do Azure usando o Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="update-template"></a>Atualizar modelo

Vamos supor que você tenha identificado uma alteração que deseja fazer no modelo na especificação de modelo. O modelo a seguir é semelhante ao modelo anterior, exceto pelo fato de ele adicionar um prefixo ao nome da conta de armazenamento. Copie o modelo a seguir e atualize o arquivo azuredeploy.json.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>Atualizar a versão da especificação de modelo

Em vez de criar uma especificação de modelo para o modelo revisado, adicione uma versão chamada `2.0` à especificação de modelo existente. Os usuários podem escolher uma das versões para implantação.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie uma versão para a especificação de modelo.

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. Para implantar a nova versão, obtenha a ID do recurso para a versão `2.0`.

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. Implante essa versão. Forneça um prefixo para o nome da conta de armazenamento.

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Crie uma versão para a especificação de modelo.

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. Para implantar a nova versão, obtenha a ID do recurso para a versão `2.0`.

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. Implante essa versão. Forneça um prefixo para o nome da conta de armazenamento.

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na especificação de modelo, selecione **Criar versão**.

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="criar uma versão":::

1. Nomeie a nova versão `2.0` e, opcionalmente, adicione observações. Selecione **Editar modelo**.

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="nomear a nova versão":::

1. Substitua o conteúdo do modelo pelo modelo atualizado. Clique em **Examinar + Salvar**.
1. Selecione **Salvar alterações**.

1. Para implantar a nova versão, selecione **Versões**

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="listar as versões":::

1. Para a versão que deseja implantar, selecione os três pontos e **Implantar**.

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="selecionar a versão a ser implantada":::

1. Preencha os campos como fez ao implantar a versão anterior.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

# <a name="arm-template"></a>[Modelo do ARM](#tab/azure-resource-manager)

1. Novamente, você precisará fazer algumas alterações no modelo local para que ele funcione com as especificações de modelo. Copie o modelo a seguir e salve-o localmente como azuredeploy.json.

   ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "2.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Para adicionar a nova versão à especificação de modelo, implante o modelo com a CLI do Azure ou o PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

1. Copie o modelo a seguir e salve-o localmente em um arquivo chamado **storage.json**.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-10-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Implante seu modelo com a CLI do Azure ou o PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar o recurso implantado neste início rápido, exclua ambos os grupos de recursos criados.

1. No portal do Azure, selecione Grupo de recursos no menu à esquerda.

1. Insira o nome do grupo de recursos (templateSpecRG e storageRG) no campo Filtrar por nome.

1. Selecione o nome do grupo de recursos.

1. Selecione Excluir grupo de recursos no menu superior.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como criar uma especificação de modelo que inclua modelos vinculados, confira [Criar uma especificação de modelo com um modelo vinculado](template-specs-create-linked.md).
