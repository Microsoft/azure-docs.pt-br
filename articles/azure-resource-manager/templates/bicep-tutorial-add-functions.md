---
title: Tutorial – Adicionar funções aos arquivos Bicep do Azure Resource Manager
description: Adicione funções aos arquivos Bicep para construir valores.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: b909beb0cce9ad04ba00068ee25247520dcff47d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102633148"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Tutorial: Adicionar funções ao arquivo Bicep do Azure Resource Manager

Neste tutorial, você aprende como adicionar [funções de modelo](template-functions.md) ao arquivo Bicep. Você usa essas funções para construir valores dinamicamente. Além dessas funções de modelo fornecidas pelo sistema, você também pode criar [funções definidas pelo usuário](./template-user-defined-functions.md). Este tutorial leva **7 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre parâmetros](bicep-tutorial-add-parameters.md), mas isso não é obrigatório.

É necessário ter o Visual Studio Code com a extensão Bicep e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Examinar o arquivo Bicep

No final do tutorial anterior, o arquivo Bicep tinha o seguinte conteúdo:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

A localização da conta de armazenamento é embutida em código para **Leste dos EUA**. No entanto, talvez seja necessário implantar a conta de armazenamento em outras regiões. Você está enfrentando novamente um problema de falta de flexibilidade do arquivo Bicep. Você pode adicionar um parâmetro para a localização, mas seria ótimo se o valor padrão dele fizesse mais sentido do que apenas um valor embutido em código.

## <a name="use-function"></a>Usar uma função

As funções adicionam flexibilidade ao arquivo Bicep obtendo valores de maneira dinâmica durante a implantação. Neste tutorial, você usa uma função para obter a localização do grupo de recursos que você está usando para implantação.

O exemplo a seguir realça as alterações para adicionar um parâmetro chamado `location`. O valor padrão do parâmetro chama a função [resourceGroup](template-functions-resource.md#resourcegroup). Essa função retorna um objeto com informações sobre o grupo de recursos que está sendo usado para implantação. Uma das propriedades no objeto é uma propriedade de localização. Quando você usa o valor padrão, a localização da conta de armazenamento é a mesma que a do grupo de recursos. Os recursos dentro de um grupo de recursos não precisam compartilhar a mesma localização. Você também pode fornecer uma localização diferente quando necessário.

Copie o arquivo inteiro e substitua o arquivo Bicep pelo conteúdo dele.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Implantar o arquivo Bicep

Nos tutoriais anteriores, você criou uma conta de armazenamento no Leste dos EUA, mas o grupo de recursos foi criado em EUA Central. Para este tutorial, sua conta de armazenamento é criada na mesma região que o grupo de recursos. Use o valor padrão para localização, para que você não precise fornecer esse valor de parâmetro. Você precisa fornecer um novo nome para a conta de armazenamento, porque está criando uma conta de armazenamento em uma localização diferente. Por exemplo, use **store2** como o prefixo em vez de **store1**.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável `bicepFile` como o caminho para o arquivo Bicep, conforme mostrado no [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar esse cmdlet de implantação, você precisa ter a [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Se a implantação falhar, use a opção `verbose` para obter informações sobre os recursos que estão sendo criados. Use a opção `debug` para obter mais informações de depuração.

## <a name="verify-deployment"></a>Verificar implantação

Você pode verificar a implantação explorando o grupo de recursos no portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos no qual você fez a implantação.
1. Você verá que um recurso de conta de armazenamento foi implantado e tem a mesma localização que o grupo de recursos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, o ideal será limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou uma função ao definir o valor padrão para um parâmetro. Nesta série de tutoriais, você continuará usando funções. Ao final da série, você adicionará funções a cada seção do arquivo Bicep.

> [!div class="nextstepaction"]
> [Adicionar variáveis](bicep-tutorial-add-variables.md)
