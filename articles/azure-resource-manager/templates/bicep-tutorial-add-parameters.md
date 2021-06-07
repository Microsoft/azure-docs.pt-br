---
title: Tutorial – adicionar parâmetros a arquivo Bicep do Azure Resource Manager
description: Adicione parâmetros ao seu arquivo Bicep para torná-lo reutilizável.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 00df2ffc6272011127c5a1eb0c1e302011f8de5f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632774"
---
# <a name="tutorial-add-parameters-to-azure-resource-manager-bicep-file"></a>Tutorial: Adicionar parâmetros a arquivo Bicep do Azure Resource Manager

No [tutorial anterior](bicep-tutorial-create-first-bicep.md), você aprendeu a implantar uma conta de armazenamento. Neste tutorial, você aprenderá a aprimorar o arquivo Bicep adicionando parâmetros. Este tutorial demora cerca de **14 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua a [criação do primeiro arquivo Bicep](bicep-tutorial-create-first-bicep.md), mas ela não é necessária.

É necessário ter o Visual Studio Code com a extensão Bicep e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Examinar o arquivo Bicep

No final do tutorial anterior, o Bicep tinha a seguinte aparência:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.bicep":::

Talvez você tenha notado que há um problema com o arquivo Bicep. O nome da conta de armazenamento está embutido em código. Você só pode usar o arquivo Bicep para implantar a mesma conta de armazenamento todas as vezes. Para implantar uma conta de armazenamento com um nome diferente, você precisaria criar um arquivo Bicep, o que obviamente não é um jeito prático de automatizar suas implantações.

## <a name="make-bicep-file-reusable"></a>Tornar o arquivo Bicep reutilizável

Para tornar o arquivo Bicep reutilizável, vamos adicionar um parâmetro que você pode usar para passar um nome de conta de armazenamento. O arquivo Bicep a seguir mostra o que foi alterado em seu arquivo. O parâmetro `storageName` é identificado como uma cadeia de caracteres. O comprimento máximo é definido como 24 caracteres para evitar qualquer nome muito longo.

Copie o arquivo inteiro e substitua-o pelo conteúdo a seguir.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.bicep" range="1-15" highlight="1-3,6":::

Observe que os parâmetros podem ser referenciados diretamente usando os nomes deles no Bicep, em comparação com a exigência de [parameters('storageName')] no modelo JSON do ARM.

## <a name="deploy-bicep-file"></a>Implantar o arquivo Bicep

Vamos implantar o arquivo Bicep. O exemplo a seguir implanta o arquivo Bicep com a CLI do Azure ou o PowerShell. Observe que você fornece o nome da conta de armazenamento como um dos valores no comando de implantação. Para o nome da conta de armazenamento, forneça o mesmo nome usado no tutorial anterior.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável `bicepFile` como o caminho para o arquivo Bicep, conforme mostrado no [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar esse cmdlet de implantação, você precisa ter a [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Compreender atualizações de recurso

Na seção anterior, você implantou uma conta de armazenamento com o mesmo nome que você tinha criado anteriormente. Você pode estar se perguntando como o recurso é afetado pela reimplantação.

Se o recurso já existir e nenhuma alteração for detectada nas propriedades, nenhuma ação será executada. Se o recurso já existir e uma propriedade tiver sido alterada, o recurso será atualizado. Se o recurso não existir, ele será criado.

Essa maneira de lidar com atualizações significa que o arquivo Bicep pode incluir todos os recursos de que você precisa para uma solução do Azure. Você pode reimplantar o arquivo Bicep com segurança e ter a certeza de que os recursos são alterados ou criados somente quando necessário. Por exemplo, se tiver adicionado arquivos à sua conta de armazenamento, você poderá reimplantar a conta de armazenamento sem perder esses arquivos.

## <a name="customize-by-environment"></a>Personalizar por ambiente

Os parâmetros permitem que você personalize a implantação fornecendo valores que são personalizados para determinado ambiente. Por exemplo, você poderá passar valores diferentes dependendo de o ambiente no qual você está implantando ser de desenvolvimento, teste ou produção.

O arquivo Bicep anterior sempre implantava uma conta de armazenamento **Standard_LRS**. Talvez você queira a flexibilidade de implantar SKUs diferentes dependendo do ambiente. O exemplo a seguir realça as alterações para adicionar um parâmetro para o SKU. Copie todo o arquivo e cole-o em seu arquivo Bicep.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep" range="1-27" highlight="5-15,21":::

O parâmetro `storageSKU` tem um valor padrão. Esse valor é usado quando um valor não é especificado durante a implantação. Ele também tem uma lista de valores permitidos. Esses valores correspondem aos valores necessários para criar uma conta de armazenamento. Não é interessante que os usuários do seu arquivo Bicep passem SKUs que não funcionam.

## <a name="redeploy-bicep-file"></a>Reimplantar o arquivo Bicep

Você está pronto para implantar novamente. Já que o SKU padrão é definido como **Standard_LRS**, você não precisa fornecer um valor para esse parâmetro.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Se a implantação falhar, use a opção `verbose` para obter informações sobre os recursos que estão sendo criados. Use a opção `debug` para obter mais informações de depuração.

Para ver a flexibilidade do seu arquivo Bicep, vamos implantá-lo novamente. Dessa vez, defina o parâmetro do SKU como **Standard_GRS**. Você pode passar um novo nome para criar uma conta de armazenamento diferente ou usar o mesmo nome para atualizar sua conta de armazenamento existente. As duas opções funcionam.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Por fim, vamos executar mais um teste e ver o que acontece quando você passa um SKU que não é um dos valores permitidos. Nesse caso, testamos o cenário em que um usuário do seu arquivo Bicep pensa que **basic** é um dos SKUs.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

O comando falha imediatamente com uma mensagem de erro que indica quais valores são permitidos. O Resource Manager identifica o erro antes de iniciar a implantação.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, o ideal será limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Você aprimorou o arquivo Bicep criado no [primeiro tutorial](bicep-tutorial-create-first-bicep.md) adicionando parâmetros. No próximo tutorial, você aprenderá sobre as funções do Bicep.

> [!div class="nextstepaction"]
> [Adicionar funções](bicep-tutorial-add-functions.md)
