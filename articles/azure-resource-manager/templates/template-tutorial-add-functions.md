---
title: Tutorial – adicionar funções de modelo
description: Adicione funções de modelo ao modelo do ARM (modelo do Azure Resource Manager) para construir valores.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 52b5bd0650b3a069adc3ef7f101c48a4674deaab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97107100"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Tutorial: Adicionar funções de modelo ao modelo do ARM

Neste tutorial, você aprenderá a adicionar [funções de modelo](template-functions.md) ao modelo do ARM (modelo do Azure Resource Manager). Você usa essas funções para construir valores dinamicamente. Além dessas funções de modelo fornecidas pelo sistema, você também pode criar [funções definidas pelo usuário](./template-user-defined-functions.md). Este tutorial leva **7 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre parâmetros](template-tutorial-add-parameters.md), mas isso não é obrigatório.

É necessário ter o Visual Studio Code com a extensão Ferramentas do Resource Manager e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Examinar modelo

No final do tutorial anterior, o modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

A localização da conta de armazenamento é embutida em código para **Leste dos EUA**. No entanto, talvez seja necessário implantar a conta de armazenamento em outras regiões. Você está enfrentando novamente um problema de falta de flexibilidade no modelo. Você pode adicionar um parâmetro para a localização, mas seria ótimo se o valor padrão dele fizesse mais sentido do que apenas um valor embutido em código.

## <a name="use-function"></a>Usar uma função

Se você completou o tutorial anterior nesta série, você já usou uma função. Quando adicionou `"[parameters('storageName')]"`, você usou a função [parameters](template-functions-deployment.md#parameters). Os colchetes indicam que a sintaxe dentro dos colchetes é uma [expressão de modelo](template-expressions.md). O Resource Manager resolve a sintaxe, em vez de tratá-la como um valor literal.

As funções adicionam flexibilidade ao modelo ao obter valores dinamicamente durante a implantação. Neste tutorial, você usa uma função para obter a localização do grupo de recursos que você está usando para implantação.

O exemplo a seguir realça as alterações para adicionar um parâmetro chamado `location`. O valor padrão do parâmetro chama a função [resourceGroup](template-functions-resource.md#resourcegroup). Essa função retorna um objeto com informações sobre o grupo de recursos que está sendo usado para implantação. Uma das propriedades no objeto é uma propriedade de localização. Quando você usa o valor padrão, a localização da conta de armazenamento é a mesma que a do grupo de recursos. Os recursos dentro de um grupo de recursos não precisam compartilhar a mesma localização. Você também pode fornecer uma localização diferente quando necessário.

Copie o arquivo inteiro e substitua o modelo pelo conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Implantar modelo

Nos tutoriais anteriores, você criou uma conta de armazenamento no Leste dos EUA, mas o grupo de recursos foi criado em EUA Central. Para este tutorial, sua conta de armazenamento é criada na mesma região que o grupo de recursos. Use o valor padrão para localização, para que você não precise fornecer esse valor de parâmetro. Você precisa fornecer um novo nome para a conta de armazenamento, porque está criando uma conta de armazenamento em uma localização diferente. Por exemplo, use **store2** como o prefixo em vez de **store1**.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável `templateFile` como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

Neste tutorial, você usou uma função ao definir o valor padrão para um parâmetro. Nesta série de tutoriais, você continuará usando funções. Ao final da série, você adicionará funções a cada seção do modelo.

> [!div class="nextstepaction"]
> [Adicionar variáveis](template-tutorial-add-variables.md)
