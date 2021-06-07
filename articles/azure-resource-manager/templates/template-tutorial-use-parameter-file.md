---
title: Tutorial – usar um arquivo de parâmetro para implantar o modelo
description: Use arquivos de parâmetro que contenham os valores a serem usados para implantar seu modelo do ARM (modelo do Azure Resource Manager).
author: mumian
ms.date: 09/10/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: b6257161017afc9dab692c43fcc64e5d961a90ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368419"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Tutorial: Usar arquivos de parâmetro para implantar seu modelo do ARM

Neste tutorial, você aprenderá a usar os [arquivos de parâmetro](parameter-files.md) para armazenar os valores que você passa durante a implantação. Nos tutoriais anteriores, você usou parâmetros embutidos com seu comando de implantação. Essa abordagem funcionou para testar seu modelo do ARM (Azure Resource Manager), mas, ao automatizar implantações, pode ser mais fácil passar um conjunto de valores para seu ambiente. Os arquivos de parâmetro tornam mais fácil empacotar valores de parâmetro para um ambiente específico. Neste tutorial, você criará arquivos de parâmetro para ambientes de desenvolvimento e produção. Esse procedimento demora cerca de **12 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre marcas](template-tutorial-add-tags.md), mas isso não é obrigatório.

É necessário ter o Visual Studio Code com a extensão Ferramentas do Resource Manager e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Examinar modelo

Seu modelo tem muitos parâmetros que você pode fornecer durante a implantação. No final do tutorial anterior, seu modelo tinha a seguinte aparência:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Esse modelo funciona bem, mas agora convém gerenciar facilmente os parâmetros que você passa para o modelo.

## <a name="add-parameter-files"></a>Adicionar arquivos de parâmetro

Os arquivos de parâmetro são arquivos JSON com uma estrutura semelhante ao seu modelo. No arquivo, você fornece os valores de parâmetro que você deseja passar durante a implantação.

No arquivo de parâmetros, forneça valores para os parâmetros em seu modelo. O nome de cada parâmetro nesse arquivo deve corresponder ao nome de um parâmetro em seu modelo. O nome não diferencia maiúsculas de minúsculas, mas, para ver com mais facilidade os valores correspondentes, recomendamos que você faça a correspondência com o uso de maiúsculas e minúsculas do modelo.

Você não precisa fornecer um valor para cada parâmetro. Se um parâmetro não especificado tiver um valor padrão, esse valor será usado durante a implantação. Se um parâmetro não tiver um valor padrão e não for especificado no arquivo de parâmetros, será solicitado que você forneça um valor durante a implantação.

Não é possível especificar um nome de parâmetro no arquivo de parâmetros que não corresponda a um nome de parâmetro no modelo. Você receberá um erro quando forem fornecidos parâmetros desconhecidos.

No Visual Studio Code, crie um arquivo com o conteúdo a seguir. Salve o arquivo com o nome _azuredeploy.parameters.dev.json_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Esse arquivo é seu arquivo de parâmetro para o ambiente de desenvolvimento. Observe que ele usa **Standard_LRS** para a conta de armazenamento, nomeia recursos com um prefixo **dev** e define a marca `Environment` como **Desenvolvimento**.

Novamente, crie um arquivo com o conteúdo a seguir. Salve o arquivo com o nome _azuredeploy.parameters.prod.json_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Esse arquivo é seu arquivo de parâmetro para o ambiente de produção. Observe que ele usa **Standard_GRS** para a conta de armazenamento, nomeia recursos com um prefixo **contoso** e define a marca `Environment` como **Produção**. Em um ambiente de produção real, também seria conveniente usar um serviço de aplicativo com um SKU que não seja gratuito, mas continuaremos usando esse SKU para este tutorial.

## <a name="deploy-template"></a>Implantar modelo

Use a CLI do Azure ou o Azure PowerShell para implantar o modelo.

Como um teste final do seu modelo, vamos criar dois novos grupos de recursos. Um para o ambiente de desenvolvimento e outro para o ambiente de produção.

Para as variáveis de modelo e parâmetro, substitua `{path-to-the-template-file}`, `{path-to-azuredeploy.parameters.dev.json}`, `{path-to-azuredeploy.parameters.prod.json}` e as chaves `{}` pelos caminhos de modelo e arquivo de parâmetro.

Primeiro, implantaremos no ambiente de desenvolvimento.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

Agora, implantaremos no ambiente de produção.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Se a implantação falhar, use a opção `verbose` para obter informações sobre os recursos que estão sendo criados. Use a opção `debug` para obter mais informações de depuração.

## <a name="verify-deployment"></a>Verificar implantação

Você pode verificar a implantação explorando os grupos de recursos do portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **Grupos de recursos**.
1. Você verá os dois novos grupos de recursos que você implantou neste tutorial.
1. Selecione grupo de recursos e exiba os recursos implantados. Observe que eles correspondem aos valores especificados no seu arquivo de parâmetro para esse ambiente.

## <a name="clean-up-resources"></a>Limpar os recursos

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos. Se tiver concluído essa série, você terá três grupos de recursos para excluir: **myResourceGroup**, **myResourceGroupDev** e **myResourceGroupProd**.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Parabéns, você concluiu esta introdução à implantação de modelos no Azure. Informe-nos se você tiver comentários e sugestões na seção de comentários. Obrigado!

A próxima série de tutoriais detalhará mais a implantação de modelos.

> [!div class="nextstepaction"]
> [☺Implantar um modelo local](./deployment-tutorial-local-template.md)
