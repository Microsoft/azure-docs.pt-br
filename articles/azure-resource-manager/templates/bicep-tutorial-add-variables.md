---
title: Tutorial – adicionar variável ao arquivo Bicep do Azure Resource Manager
description: Adicione variáveis ao seu arquivo Bicep para simplificar a sintaxe.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: da2755c1f2c0f9fa891fe1a99b1fed21f64492c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632468"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>Tutorial: Adicionar variáveis ao arquivo Bicep do Azure Resource Manager

Neste tutorial, você aprende a adicionar uma variável ao arquivo Bicep. As variáveis simplificam os arquivos Bicep, permitindo que você grave uma expressão uma vez e a reutilize em todo o arquivo Bicep. Este tutorial leva **7 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre as funções](bicep-tutorial-add-functions.md), mas isso não é obrigatório.

É necessário ter o Visual Studio Code com a extensão Bicep e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Examinar o arquivo Bicep

No final do tutorial anterior, o arquivo Bicep tinha o seguinte conteúdo:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

O parâmetro para o nome da conta de armazenamento é difícil de usar porque você precisa fornecer um nome exclusivo. Se você concluiu os tutoriais anteriores nesta série, provavelmente você está cansado de adivinhar um nome exclusivo. Você resolve esse problema adicionando uma variável que constrói um nome exclusivo para a conta de armazenamento.

## <a name="use-variable"></a>Usar uma variável

O exemplo a seguir mostra as alterações para adicionar uma variável ao arquivo Bicep que cria um nome exclusivo de conta de armazenamento. Copie o arquivo inteiro e substitua o arquivo Bicep pelo conteúdo dele.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

Observe que ele inclui uma variável chamada `uniqueStorageName`. Essa variável usa três funções para construir um valor de cadeia de caracteres.

Você está familiarizado com a função [resourceGroup](template-functions-resource.md#resourcegroup). Nesse caso, você obtém a propriedade `id` em vez da propriedade `location`, conforme mostrado no tutorial anterior. A propriedade `id` retorna o identificador completo do grupo de recursos, incluindo a ID da assinatura e o nome do grupo de recursos.

A função [uniqueString](template-functions-string.md#uniquestring) cria um valor de hash de 13 caracteres. O valor retornado é determinado pelos parâmetros que você passa. Para este tutorial, você usa a ID do grupo de recursos como a entrada para o valor de hash. Isso significa que você pode implantar esse arquivo Bicep em grupos de recursos diferentes e obter um valor de cadeia de caracteres exclusivo diferente. No entanto, você obterá o mesmo valor se implantar para o mesmo grupo de recursos.

O Bicep dá suporte a uma sintaxe de [interpolação de cadeia de caracteres](https://en.wikipedia.org/wiki/String_interpolation#). Para essa variável, ela usa a cadeia de caracteres do parâmetro e a da função `uniqueString` e as combina em uma cadeia de caracteres.

O parâmetro `storagePrefix` permite que você passe um prefixo que ajuda a identificar contas de armazenamento. Você pode criar sua própria convenção de nomenclatura que facilite a identificação das contas de armazenamento após a implantação de uma longa lista de recursos.

Por fim, observe que o nome do armazenamento agora está definido como a variável em vez de um parâmetro.

## <a name="deploy-bicep-file"></a>Implantar o arquivo Bicep

Vamos implantar o arquivo Bicep. A implantação desse arquivo Bicep é mais fácil do que a dos anteriores, pois você fornece apenas o prefixo para o nome do armazenamento.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável `bicepFile` como o caminho para o arquivo Bicep, conforme mostrado no [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar esse cmdlet de implantação, você precisa ter a [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Se a implantação falhar, use a opção `verbose` para obter informações sobre os recursos que estão sendo criados. Use a opção `debug` para obter mais informações de depuração.

## <a name="verify-deployment"></a>Verificar implantação

Você pode verificar a implantação explorando o grupo de recursos no portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos no qual você fez a implantação.
1. Você verá que um recurso de conta de armazenamento foi implantado. O nome da conta de armazenamento é **store** mais uma cadeia de caracteres aleatórios.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, o ideal será limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou uma variável que cria um nome exclusivo para uma conta de armazenamento. No próximo tutorial, você retorna um valor da conta de armazenamento implantada.

> [!div class="nextstepaction"]
> [Adicionar saídas](bicep-tutorial-add-outputs.md)
