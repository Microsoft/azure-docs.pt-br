---
title: Tutorial – Exportar modelo JSON do portal do Azure para desenvolvimento de Bicep
description: Saiba como usar um modelo JSON exportado para concluir o desenvolvimento de Bicep.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3bc7ed4ada4f7810e9864778c7f76a0573c9dc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632489"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>Tutorial: Usar modelo JSON exportado do portal do Azure

Nesta série de tutoriais, você criou um arquivo Bicep para implantar uma conta de armazenamento do Azure. Nos próximos dois tutoriais, você adicionará um *Plano do Serviço de Aplicativo* e um *website*. Em vez de criar modelos do zero, você aprenderá a exportar modelos JSON do portal do Azure e a usar modelos de exemplo dos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). Você personalizará esses modelos para seu uso. Este tutorial se concentra na exportação de modelos e na personalização do resultado para o arquivo Bicep. Esse procedimento demora cerca de **14 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre saídas](bicep-tutorial-add-outputs.md), não sendo, porém, necessário.

É necessário ter o Visual Studio Code com a extensão Bicep e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Examinar o arquivo Bicep

No final do tutorial anterior, o arquivo Bicep tinha o seguinte conteúdo:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

Esse arquivo Bicep funciona bem para a implantação de contas de armazenamento, mas talvez você queira adicionar mais recursos a ele. Exporte um modelo JSON de um recurso existente para obter rapidamente o JSON desse recurso. Em seguida, converta o JSON em Bicep para adicioná-lo ao seu arquivo Bicep.

## <a name="create-app-service-plan"></a>Criar plano de Serviço de Aplicativo

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso**.
1. Em **Pesquisar no Marketplace**, insira **Plano do Serviço de Aplicativo** e, em seguida, selecione **Plano do Serviço de Aplicativo**.  Não selecione **Plano de Serviço de Aplicativo (clássico)**
1. Selecione **Criar**.
1. Digite:

    - **Assinatura**: selecione sua assinatura do Azure.
    - **Grupo de Recursos**: Selecione **Criar** e, em seguida, especifique um nome. Forneça um nome de grupo de recursos diferente daquele que você está usando nesta série de tutoriais.
    - **Nome**: insira um nome para o Plano do Serviço de Aplicativo.
    - **Sistema Operacional**: selecione **Linux**.
    - **Região**: selecione uma localização do Azure. Por exemplo, **Centro dos EUA**.
    - **Tipo de preço**: para reduzir os custos, altere o SKU para **Básico B1** (em Desenvolvimento/Teste).

    ![Portal do modelo de exportação do modelo do Resource Manager](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. Selecione **Examinar e criar**.
1. Selecione **Criar**. São necessários alguns instantes para a criação do recurso.

## <a name="export-template"></a>Exportar modelo

Atualmente, o portal do Azure dá suporte somente à exportação de modelos JSON. Há ferramentas que você pode usar para descompilar modelos JSON em arquivos Bicep.

1. Selecione **Ir para o recurso**.

    ![Ir para o recurso](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Selecione **Exportar modelo**.

    ![Modelo de exportação do modelo do Resource Manager](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   O recurso Exportar Modelo usa o estado atual de um recurso e gera um modelo para implantá-lo. A exportação de um modelo pode ser uma maneira útil de obter rapidamente o JSON de que você precisa para implantar um recurso.

1. A definição `Microsoft.Web/serverfarms` e a definição de parâmetro são as partes necessárias.

    ![Modelo exportado do modelo de exportação do modelo do Resource Manager](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > Normalmente, o modelo exportado é mais detalhado do que o esperado ao criar um modelo. Por exemplo, o objeto do SKU no modelo exportado tem cinco propriedades. Esse modelo funciona, mas você pode simplesmente usar a propriedade `name`. Comece com o modelo exportado e, em seguida, modifique-o como desejar de acordo com suas necessidades.

1. Selecione **Baixar**.  O arquivo zip baixado contém um **template.json** e um **parameters.json**. Descompacte os arquivos.
1. Navegue até **https://bicepdemo.z22.web.core.windows.net/** , selecione **Descompilar** e abra **template.json**. Você obtém o modelo em Bicep.

## <a name="revise-existing-bicep-file"></a>Revisar arquivo Bicep existente

O modelo exportado descompilado fornece a maior parte do Bicep necessário, mas você precisará personalizá-lo para o arquivo Bicep. Preste atenção especial nas diferenças em parâmetros e variáveis entre o arquivo Bicep e o arquivo Bicep exportado. Obviamente, o processo de exportação não conhece os parâmetros e as variáveis que você já definiu no arquivo Bicep.

O exemplo a seguir mostra as adições ao arquivo Bicep. Ele contém o código exportado, além de algumas alterações. Primeiro, ele altera o nome do parâmetro para que corresponda à convenção de nomenclatura. Em segundo lugar, ele usa o parâmetro de localização para a localização do Plano do Serviço de Aplicativo. Terceiro, ele remove algumas das propriedades em que o valor padrão está ok.

Copie o arquivo inteiro e substitua o arquivo Bicep pelo conteúdo dele.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>Implantar o arquivo Bicep

Use a CLI do Azure ou o Azure PowerShell para implantar um arquivo Bicep.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável `bicepFile` como o caminho para o arquivo Bicep, conforme mostrado no [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar esse cmdlet de implantação, você precisa ter a [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addappserviceplan \
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
1. O grupo de recursos contém uma conta de armazenamento e um Plano do Serviço de Aplicativo.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, o ideal será limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu a exportar um modelo JSON do portal do Azure, converter o modelo JSON em um arquivo Bicep e usar o modelo exportado para seu desenvolvimento de Bicep. Use também os modelos de Início Rápido do Azure para simplificar o desenvolvimento de Bicep.

> [!div class="nextstepaction"]
> [Usar modelos de Início Rápido do Azure](bicep-tutorial-quickstart-template.md)
