---
title: Tutorial – Implantar o modelo do Azure Resource Manager local
description: Saiba como implantar um modelo do ARM (modelo do Azure Resource Manager) por meio do computador local
ms.date: 02/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: d8d54acfa345994edcc401170e70495b3826bfdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384224"
---
# <a name="tutorial-deploy-a-local-arm-template"></a>Tutorial: Implantar um modelo do ARM local

Saiba como implantar um modelo do ARM (modelo do Azure Resource Manager) por meio do computador local. Esse procedimento demora cerca de **8 minutos** para ser concluído.

Este tutorial é o primeiro de uma série. À medida que avança na série, você modulariza o modelo criando um modelo vinculado, armazena o modelo vinculado em uma conta de armazenamento e protege-o usando o token SAS, além de aprender a criar um pipeline de DevOps para implantar modelos. Esta série se concentra na implantação de modelo. Se desejar aprender sobre o desenvolvimento de modelos, confira os [tutoriais de iniciantes](./template-tutorial-create-first-template.md).

## <a name="get-tools"></a>Obter ferramentas

Vamos começar verificando se você tem as ferramentas necessárias para implantar modelos.

### <a name="command-line-deployment"></a>Implantação de linha de comando

Você precisa do Azure PowerShell ou da CLI do Azure para implantar o modelo. Para obter as instruções de instalação, confira:

- [Instale o Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows)
- [Instalar a CLI do Azure no Linux](/cli/azure/install-azure-cli-linux)
- [Instalar a CLI do Azure no macOS](/cli/azure/install-azure-cli-macos)

Depois de instalar o Azure PowerShell ou a CLI do Azure, é preciso que você entre pela primeira vez. Para obter ajuda, confira [Entrar – PowerShell](/powershell/azure/install-az-ps#sign-in) ou [Entrar – CLI do Azure](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Editor (opcional)

Modelos são arquivos JSON. Para examinar/editar modelos, você precisa de um bom editor de JSON. Recomendamos o Visual Studio Code com a extensão Ferramentas do Resource Manager. Se precisar instalar essas ferramentas, confira [Guia de Início Rápido: Criar modelos do ARM com o Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="review-template"></a>Examinar modelo

O modelo implanta uma conta de armazenamento, um plano do serviço de aplicativo e um aplicativo Web. Se estiver interessado em criar o modelo, acompanhe o [tutorial sobre Modelos de início rápido](template-tutorial-quickstart-template.md). No entanto, isso não é necessário para concluir este tutorial.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres, usar números e apenas letras minúsculas. O nome deve ser exclusivo. No modelo, o nome da conta de armazenamento é o nome do projeto com **store** acrescentado e o nome do projeto deve ter entre 3 e 11 caracteres. Portanto, o nome do projeto deve atender aos requisitos de nome da conta de armazenamento e ter menos de 11 caracteres.

Salve uma cópia do modelo no computador local com a extensão _.json_, por exemplo, _azuredeploy.json_. Você implantará esse modelo posteriormente no tutorial.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Para começar a trabalhar com o Azure PowerShell/a CLI do Azure para implantar um modelo, entre com suas credenciais do Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az login
```

---

Se tiver várias assinaturas do Azure, selecione a que deseja usar. Substitua `[SubscriptionID/SubscriptionName]` e os colchetes `[]` pelas informações da sua assinatura:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Criar grupo de recursos

Ao implantar um modelo, você especificará um grupo de recursos que conterá os recursos. Antes de executar o comando de implantação, crie o grupo de recursos com a CLI do Azure ou o Azure PowerShell. Selecione as guias na seção de código a seguir para escolher entre o Azure PowerShell e a CLI do Azure. Os exemplos de CLI neste artigo são escritos para o shell do Bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Implantar modelo

Use uma ou ambas as opções de implantação para implantar o modelo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Para saber mais sobre como implantar o modelo usando o Azure PowerShell, confira [Implantar recursos com modelos do ARM e o Azure PowerShell](./deploy-powershell.md).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Para saber mais sobre como implantar o modelo usando a CLI do Azure, confira [Implantar recursos com modelos do ARM e a CLI do Azure](./deploy-cli.md).

---

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe os recursos que você implantou excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu a implantar um modelo local. No próximo tutorial, separe o modelo em um modelo principal e um modelo vinculado e saiba como armazenar e proteger este último.

> [!div class="nextstepaction"]
> [Implantar um modelo vinculado](./deployment-tutorial-linked-template.md)
