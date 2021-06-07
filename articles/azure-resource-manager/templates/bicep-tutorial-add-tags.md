---
title: Tutorial – adicionar marcas a recursos em um arquivo Bicep do Azure Resource Manager
description: Adicione marcas aos recursos que você implanta em seus arquivos Bicep. As marcas permitem que você organize logicamente os recursos.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ea5e078eb692d002b3f86cd43663dd042d692611
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632588"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>Tutorial: Adicionar marcas a arquivos Bicep do Azure Resource Manager

Neste tutorial, você aprende a adicionar marcas a recursos em seus arquivos Bicep. As [marcas](../management/tag-resources.md) ajudam você a organizar logicamente seus recursos. Os valores de tag aparecem em relatórios de custo. Este tutorial leva **8 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre Modelos de início rápido](bicep-tutorial-quickstart-template.md), mas isso não é obrigatório.

É necessário ter o Visual Studio Code com a extensão Bicep e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Examinar o arquivo Bicep

O arquivo Bicep anterior implantou uma conta de armazenamento, um plano do Serviço de Aplicativo e um aplicativo Web.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

Após implantar esses recursos, você pode precisar controlar custos e localizar recursos que pertencem a uma categoria. Você pode adicionar marcas para ajudar a resolver esses problemas.

## <a name="add-tags"></a>Adicionar marcas

Marque recursos para adicionar valores que ajudam você a identificar seu uso. Por exemplo, você pode adicionar marcas que listam o ambiente e o projeto. Você pode adicionar marcas que identificam um centro de custo ou a equipe que tem a propriedade do recurso. Adicione valores que façam sentido para sua organização.

O exemplo a seguir mostra as alterações no arquivo Bicep. Copie o arquivo inteiro e substitua o arquivo Bicep pelo conteúdo dele.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>Implantar o arquivo Bicep

É hora de implantar o arquivo Bicep e examinar os resultados.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável `bicepFile` como o caminho para o arquivo Bicep, conforme mostrado no [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar esse cmdlet de implantação, você precisa ter a [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
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
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Se a implantação falhar, use a opção `verbose` para obter informações sobre os recursos que estão sendo criados. Use a opção `debug` para obter mais informações de depuração.

## <a name="verify-deployment"></a>Verificar implantação

Você pode verificar a implantação explorando o grupo de recursos no portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos no qual você fez a implantação.
1. Selecione um dos recursos, como o recurso da conta de armazenamento. Você verá que agora ele tem marcas.

   ![Mostrar marcas](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, o ideal será limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou marcas aos recursos. No próximo tutorial, você aprenderá a usar os arquivos de parâmetro para simplificar a passagem de valores para a implantação.

> [!div class="nextstepaction"]
> [Usar arquivo de parâmetro](bicep-tutorial-use-parameter-file.md)
