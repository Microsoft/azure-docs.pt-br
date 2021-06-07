---
title: Criar múltiplas instâncias de recursos
description: Saiba como criar um modelo do ARM (modelo do Azure Resource Manager) para criar várias instâncias de recurso do Azure.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: e669e27547633639a88674ffee499fb1d84facdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673946"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Tutorial: Criar vários instâncias de recurso com os modelos do ARM

Saiba como iterar no seu modelo do ARM (modelo do Azure Resource Manager) para criar várias instâncias de um recurso do Azure. Neste tutorial, você modificará um modelo para criar três instâncias de conta de armazenamento.

![O Azure Resource Manager cria um diagrama de várias instâncias](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Editar o modelo
> * Implantar o modelo

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Para obter um módulo do Microsoft Learn que aborda a cópia de recursos, confira [Gerenciar implantações de nuvem complexas usando recursos avançados do modelo do ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Visual Studio Code com a extensão de Ferramentas do Resource Manager. Confira [Início Rápido: Criar modelos do ARM com o Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

[Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/) é um repositório de modelos do ARM. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste início rápido é chamado [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/). O modelo define um recurso da conta de Armazenamento do Azure.

1. No Visual Studio Code, escolha **Arquivo** > **Abrir Arquivo**.
1. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Escolha **Abrir** para abrir o arquivo.
1. Há um recurso `Microsoft.Storage/storageAccounts` definido no modelo. Comparar o modelo para a [referência de modelo](/azure/templates/Microsoft.Storage/storageAccounts). É útil ter algumas noções básicas do modelo antes de personalizá-lo.
1. Escolha **Arquivo** > **Salvar como** para salvar o arquivo como _azuredeploy.json_ em seu computador local.

## <a name="edit-the-template"></a>Editar o modelo

O modelo existente cria uma conta de armazenamento. Você personaliza o modelo para criar três contas de armazenamento.

No Visual Studio Code, faça as quatro alterações a seguir:

![O Azure Resource Manager cria várias instâncias](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Adicione um elemento `copy` à definição de recurso de conta de armazenamento. No elemento `copy`, você especifica o número de iterações e uma variável para esse loop. O valor da contagem deve ser um número inteiro positivo e não pode exceder 800.
2. A função `copyIndex()` retorna a iteração atual no loop. Você pode usar o índice como o prefixo do nome. `copyIndex()`é baseado em zero. Para deslocar o valor do índice, você pode passar um valor na função `copyIndex()`. Por exemplo, `copyIndex(1)`.
3. Exclua o elemento `variables`, pois ele não será mais usado.
4. Exclua o elemento `outputs`. Ele não é mais necessário.

O modelo concluído se parece com:

```json
{
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
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

Para obter mais informações de como criar várias instâncias, confira [Iteração de recursos em modelos do ARM](./copy-resources.md)

## <a name="deploy-the-template"></a>Implantar o modelo

1. Entrar no [Azure Cloud Shell](https://shell.azure.com)

1. Escolha seu ambiente preferencial selecionando **PowerShell** ou **Bash** (para a CLI) no canto superior esquerdo. Ao alternar, é necessário reiniciar o shell.

    ![Carregar arquivo do Cloud Shell no portal do Azure](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Escolha **Carregar/fazer o download dos arquivos** e, em seguida, escolha **Carregar**. Consulte a captura de tela anterior. Selecione o arquivo que você salvou na seção anterior. Depois de carregar o arquivo, use os comandos `ls` e `cat` para verificar se o arquivo foi carregado com êxito.

1. No Cloud Shell, execute os comandos a seguir. Selecione a guia para mostrar o código do PowerShell ou o código da CLI.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

Após uma implantação de modelo bem-sucedida, você pode exibir as três contas de armazenamento criadas no grupo de recursos especificado. Compare os nomes de conta de armazenamento com a definição de nome no modelo.

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"

Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.  Você deverá ver um total de três recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar várias instâncias da conta de armazenamento. No próximo tutorial, você desenvolverá um modelo com vários recursos e vários tipos de recursos. Alguns dos recursos têm recursos dependentes.

> [!div class="nextstepaction"]
> [Criar recursos dependentes](./template-tutorial-create-templates-with-dependent-resources.md)
