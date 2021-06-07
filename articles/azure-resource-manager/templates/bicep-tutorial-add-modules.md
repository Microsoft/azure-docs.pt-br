---
title: 'Tutorial: Adicionar módulos ao arquivo Bicep do Azure Resource Manager'
description: Use módulos para encapsular detalhes complexos da declaração de recurso bruto.
author: mumian
ms.date: 03/25/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8c7ab1038cbe62d6f15faf56796193df12b38546
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568759"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>Tutorial: Adicionar módulos ao arquivo Bicep do Azure Resource Manager

No [tutorial anterior](bicep-tutorial-use-parameter-file.md), você aprendeu a usar um arquivo de parâmetro para implantar o arquivo Bicep. Neste tutorial, você aprenderá a usar os módulos do Bicep para encapsular detalhes complexos da declaração de recursos brutos. Os módulos podem ser compartilhados e reutilizados dentro da sua solução.  Esse procedimento demora cerca de **12 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre o arquivo de parâmetro](bicep-tutorial-use-parameter-file.md), mas isso não é obrigatório.

É necessário ter o Visual Studio Code com a extensão Bicep e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Examinar o arquivo Bicep

No final do tutorial anterior, o arquivo Bicep tinha o seguinte conteúdo:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Esse arquivo Bicep funciona bem. Mas, para soluções maiores, é interessante dividir o arquivo Bicep em muitos módulos relacionados para poder compartilhar e reutilizar esses módulos. O arquivo Bicep atual implanta uma conta de armazenamento, um plano do serviço de aplicativo e um site.  Vamos separar a conta de armazenamento em um módulo.

## <a name="create-bicep-module"></a>Criar módulo Bicep

Todo arquivo Bicep pode ser consumido como um módulo, portanto, não há sintaxe específica para definir um módulo. Crie um arquivo _storage.bicep_ com o seguinte conteúdo:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

Esse módulo contém o recurso de conta de armazenamento e os parâmetros e variáveis relacionados. Os valores para o parâmetro _location_ e os parâmetros _resourceTags_ foram removidos. Esses valores serão passados do arquivo Bicep principal.

## <a name="consume-bicep-module"></a>Consumir módulo Bicep

Substitua a definição de recurso da conta de armazenamento no _azuredeploy.bicep_ existente pelo seguinte conteúdo do Bicep:

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **módulo**: palavra-chave.
- **nome simbólico** (stg): esse é um identificador do módulo.
- **arquivo de módulo**: o caminho para o módulo neste exemplo é especificado usando um caminho relativo (./storage.bicep). Todos os caminhos no Bicep devem ser especificados usando o separador de diretório de barra (/) para garantir a compilação consistente multiplataforma. A barra invertida do Windows (caractere \)) não é compatível.

Para recuperar o ponto de extremidade de armazenamento, atualize a saída em _azuredeploy.bicep_ para o seguinte Bicep:

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

O azuredeploy.bicep concluído tem o seguinte conteúdo:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>Implantar modelo

Use a CLI do Azure ou o Azure PowerShell para implantar o modelo.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável `bicepFile` como o caminho para o arquivo Bicep, conforme mostrado no [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar esse cmdlet de implantação, você precisa ter a [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
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
  --name addmodule \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
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

Parabéns, você concluiu esta introdução à implantação de arquivos Bicep no Azure. Informe-nos se você tiver comentários e sugestões na seção de comentários. Obrigado!

A próxima série de tutoriais detalhará mais a implantação de modelos.

> [!div class="nextstepaction"]
> [☺Implantar um modelo local](./deployment-tutorial-local-template.md)
