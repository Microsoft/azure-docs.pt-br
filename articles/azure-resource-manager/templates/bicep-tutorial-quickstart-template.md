---
title: Tutorial – Usar modelos de início rápido para o desenvolvimento de Bicep no Azure Resource Manager
description: Saiba como usar os modelos de Início Rápido do Azure para concluir o desenvolvimento de Bicep.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: cf655885e01fe6bca99a82c82d6bbbc4c1a080b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632418"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>Tutorial – Usar modelos de início rápido do Azure para o desenvolvimento de Bicep no Azure Resource Manager

[Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/) é um repositório de modelos JSON provenientes de contribuições da comunidade. Você pode usar os modelos de exemplo em seu desenvolvimento de Bicep. Neste tutorial, você encontrará uma definição de recurso de site, a descompilará para Bicep e a adicionará ao arquivo Bicep. Esse procedimento demora cerca de **12 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre modelos exportados](bicep-tutorial-export-template.md), mas isso não é obrigatório.

É necessário ter o Visual Studio Code com a extensão Bicep e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Examinar o arquivo Bicep

No final do tutorial anterior, o arquivo Bicep tinha o seguinte conteúdo:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

Esse arquivo Bicep funciona para implantar contas de armazenamento e planos do Serviço de Aplicativo, mas talvez você queira adicionar um site a ele. Você pode usar modelos predefinidos para descobrir rapidamente o JSON necessário para implantar um recurso. Antes que os modelos de início rápido do Azure oferecessem exemplos de Bicep, você podia usar ferramentas de conversão para converter modelos JSON em arquivos Bicep.

## <a name="find-template"></a>Localizar modelo

Atualmente, os modelos de início rápido do Azure fornecem apenas modelos JSON. Há ferramentas que você pode usar para descompilar modelos JSON em modelos Bicep.

1. Abra [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/)
1. Em **Pesquisar**, insira _implantar aplicativo Web Linux_.
1. Selecione o bloco com o título **Implantar um aplicativo Web Linux básico**. Se você tiver problemas para encontrá-lo, aqui está o [link direto](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Selecione **Procurar no GitHub**.
1. Selecione _azuredeploy.json_. Esse é o modelo que você pode usar.
1. Selecione **Bruto** e faça uma cópia da URL.
1. Navegue até **https://bicepdemo.z22.web.core.windows.net/** , selecione **Descompilar** e forneça a URL do modelo bruto.
1. Examine o modelo do Bicep. Em particular, procure o recurso `Microsoft.Web/sites`.

    ![Site de início rápido do modelo do Resource Manager](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    Há alguns recursos importantes do Bicep a serem observados nesse novo recurso se você trabalhou em modelos JSON.

    Em modelos JSON do ARM, você precisa especificar manualmente as dependências de recursos com a propriedade _dependsOn_. O recurso do site depende do recurso do plano do serviço de aplicativo. Com o Bicep, se você referenciar qualquer propriedade do recurso usando o nome simbólico, a propriedade dependsOn será adicionada automaticamente.

    Você pode referenciar facilmente a ID do recurso com base no nome simbólico do plano do serviço de aplicativo (appServicePlanName.id), que será convertido na função resourceId(...) no modelo JSON compilado.

## <a name="revise-existing-bicep-file"></a>Revisar arquivo Bicep existente

Mescle o modelo de início rápido descompilado com o arquivo Bicep existente. Assim como fez no tutorial anterior, atualize o nome simbólico do recurso e o nome do recurso de maneira que correspondam à sua convenção de nomenclatura.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

O nome do aplicativo Web precisa ser exclusivo no Azure. Para evitar a existência de nomes duplicados, a variável `webAppPortalName` foi atualizada por meio de `var webAppPortalName_var = '${webAppName}-webapp'` to `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` .

## <a name="deploy-bicep-file"></a>Implantar o arquivo Bicep

Use a CLI do Azure ou o Azure PowerShell para implantar um modelo do Bicep.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável **bicepFile** como o caminho para o arquivo Bicep, conforme mostrado no [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar esse cmdlet de implantação, você precisa ter a [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Se a implantação falhar, use a opção `verbose` para obter informações sobre os recursos que estão sendo criados. Use a opção `debug` para obter mais informações de depuração.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, o ideal será limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu a usar um modelo de início rápido para o desenvolvimento de Bicep. No próximo tutorial, você adicionará marcas aos recursos.

> [!div class="nextstepaction"]
> [Adicionar marcas](bicep-tutorial-add-tags.md)
