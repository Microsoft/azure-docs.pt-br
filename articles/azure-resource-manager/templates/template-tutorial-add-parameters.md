---
title: Tutorial – adicionar parâmetros ao modelo
description: Adicione parâmetros ao modelo do ARM (modelo do Azure Resource Manager) para torná-lo reutilizável.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: e983f8499cbeaf400a8da6f48d7f6c8b75c4795a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97107055"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Tutorial: Adicionar parâmetros ao modelo do ARM

No [tutorial anterior](template-tutorial-add-resource.md), você aprendeu como adicionar uma conta de armazenamento ao modelo e implantá-lo. Neste tutorial, você aprenderá a aprimorar o modelo do ARM (modelo do Azure Resource Manager) adicionando parâmetros. Este tutorial demora cerca de **14 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre recursos](template-tutorial-add-resource.md), não sendo, porém, necessário.

É necessário ter o Visual Studio Code com a extensão Ferramentas do Resource Manager e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Examinar modelo

No final do tutorial anterior, o modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Talvez você tenha notado que há um problema com este modelo. O nome da conta de armazenamento está embutido em código. Você só pode usar esse modelo para implantar a mesma conta de armazenamento a cada vez. Para implantar uma conta de armazenamento com um nome diferente, você precisaria criar um novo modelo, o que obviamente não é uma maneira prática de automatizar suas implantações.

## <a name="make-template-reusable"></a>Tornar o modelo reutilizável

Para tornar seu modelo reutilizável, adicionaremos um parâmetro que você pode usar para passar um nome de conta de armazenamento. O JSON realçado no exemplo a seguir mostra o que mudou no modelo. O parâmetro `storageName` é identificado como uma cadeia de caracteres. O comprimento máximo é definido como 24 caracteres para evitar qualquer nome muito longo.

Copie o arquivo inteiro e substitua o modelo pelo conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Implantar modelo

Vamos implantar o modelo. O exemplo a seguir implanta o modelo com a CLI do Azure ou o PowerShell. Observe que você fornece o nome da conta de armazenamento como um dos valores no comando de implantação. Para o nome da conta de armazenamento, forneça o mesmo nome usado no tutorial anterior.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável `templateFile` como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Compreender atualizações de recurso

Na seção anterior, você implantou uma conta de armazenamento com o mesmo nome que você tinha criado anteriormente. Você pode estar se perguntando como o recurso é afetado pela reimplantação.

Se o recurso já existir e nenhuma alteração for detectada nas propriedades, nenhuma ação será executada. Se o recurso já existir e uma propriedade tiver sido alterada, o recurso será atualizado. Se o recurso não existir, ele será criado.

Essa maneira de lidar com atualizações significa que seu modelo pode incluir todos os recursos que você precisa para uma solução do Azure. Você pode reimplantar o modelo com segurança e ter a certeza de que os recursos são alterados ou criados somente quando necessário. Por exemplo, se tiver adicionado arquivos à sua conta de armazenamento, você poderá reimplantar a conta de armazenamento sem perder esses arquivos.

## <a name="customize-by-environment"></a>Personalizar por ambiente

Os parâmetros permitem que você personalize a implantação fornecendo valores que são personalizados para determinado ambiente. Por exemplo, você poderá passar valores diferentes dependendo de o ambiente no qual você está implantando ser de desenvolvimento, teste ou produção.

O modelo anterior sempre implantava uma conta de armazenamento **Standard_LRS**. Talvez você queira a flexibilidade de implantar SKUs diferentes dependendo do ambiente. O exemplo a seguir realça as alterações para adicionar um parâmetro para o SKU. Copie todo o arquivo e cole-o em seu modelo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

O parâmetro `storageSKU` tem um valor padrão. Esse valor é usado quando um valor não é especificado durante a implantação. Ele também tem uma lista de valores permitidos. Esses valores correspondem aos valores necessários para criar uma conta de armazenamento. Você não quer que os usuários do seu modelo transmitam SKUs que não funcionam.

## <a name="redeploy-template"></a>Reimplantar o modelo

Você está pronto para implantar novamente. Já que o SKU padrão é definido como **Standard_LRS**, você não precisa fornecer um valor para esse parâmetro.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Se a implantação falhar, use a opção `verbose` para obter informações sobre os recursos que estão sendo criados. Use a opção `debug` para obter mais informações de depuração.

Para ver a flexibilidade do modelo, vamos implantá-lo novamente. Dessa vez, defina o parâmetro do SKU como **Standard_GRS**. Você pode passar um novo nome para criar uma conta de armazenamento diferente ou usar o mesmo nome para atualizar sua conta de armazenamento existente. As duas opções funcionam.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Por fim, vamos executar mais um teste e ver o que acontece quando você passa um SKU que não é um dos valores permitidos. Nesse caso, testamos o cenário em que um usuário do modelo pensa que **basic** é um dos SKUs.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

Pela adição de parâmetros, você melhorou o modelo criado no [primeiro tutorial](template-tutorial-create-first-template.md). No próximo tutorial, você aprenderá sobre as funções de modelo.

> [!div class="nextstepaction"]
> [Adicionar funções de modelo](template-tutorial-add-functions.md)
