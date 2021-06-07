---
title: Tutorial – usar modelos de início rápido
description: Saiba como usar os modelos de Início Rápido do Azure para concluir o desenvolvimento do modelo.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 46b32ae7aeb971c9391a69e3ca3d01f669774248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106896"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Tutorial: Usar modelos de Início Rápido do Azure

[Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/) é um repositório de modelos provenientes de contribuições da comunidade. Você pode usar os modelos de exemplo em seu desenvolvimento de modelo. Neste tutorial, você encontrará uma definição de recurso de site e a adicionará ao seu próprio modelo. Esse procedimento demora cerca de **12 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre modelos exportados](template-tutorial-export-template.md), mas isso não é obrigatório.

É necessário ter o Visual Studio Code com a extensão Ferramentas do Resource Manager e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Examinar modelo

No final do tutorial anterior, o modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Esse modelo funciona para implantar contas de armazenamento e planos do Serviço de Aplicativo, mas talvez você queira adicionar um site a ele. Você pode usar modelos predefinidos para descobrir rapidamente o JSON necessário para implantar um recurso.

## <a name="find-template"></a>Localizar modelo

1. Abra [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/)
1. Em **Pesquisar**, insira _implantar aplicativo Web Linux_.
1. Selecione o bloco com o título **Implantar um aplicativo Web Linux básico**. Se você tiver problemas para encontrá-lo, aqui está o [link direto](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Selecione **Procurar no GitHub**.
1. Selecione _azuredeploy.json_.
1. Examine o modelo. Em particular, procure o recurso `Microsoft.Web/sites`.

    ![Site de início rápido do modelo do Resource Manager](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Revisar o modelo existente

Mescle o modelo de início rápido com o modelo existente:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

O nome do aplicativo Web precisa ser exclusivo no Azure. Para evitar a existência de nomes duplicados, a variável `webAppPortalName` foi atualizada por meio de `"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"` to `"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"` .

Adicione uma vírgula no final da definição de `Microsoft.Web/serverfarms` para separar a definição do recurso da definição de `Microsoft.Web/sites`.

Há alguns recursos importantes a serem observados nesse novo recurso.

Você notará que ele tem um elemento chamado `dependsOn` definido como o plano do serviço de aplicativo. Essa configuração é necessária porque o Plano do Serviço de Aplicativo deve existir antes que o aplicativo Web seja criado. O elemento `dependsOn` informa ao Resource Manager como ordenar os recursos para implantação.

A propriedade `serverFarmId` usa a função [resourceId](template-functions-resource.md#resourceid). Essa função obtém o identificador exclusivo de um recurso. Nesse caso, ela obtém o identificador exclusivo para o plano do Serviço de Aplicativo. O aplicativo Web está associado a um plano específico do Serviço de Aplicativo.

## <a name="deploy-template"></a>Implantar modelo

Use a CLI do Azure ou o Azure PowerShell para implantar um modelo.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável **templateFile** como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
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
  --template-file $templateFile \
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

Você aprendeu a usar um modelo de início rápido para o desenvolvimento de modelo. No próximo tutorial, você adicionará marcas aos recursos.

> [!div class="nextstepaction"]
> [Adicionar marcas](template-tutorial-add-tags.md)
