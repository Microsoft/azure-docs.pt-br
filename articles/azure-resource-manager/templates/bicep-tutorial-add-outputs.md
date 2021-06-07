---
title: Tutorial – adicionar saídas ao arquivo Bicep do Azure Resource Manager
description: Adicione saídas ao seu arquivo Bicep para simplificar a sintaxe.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4b7d7a1414091c516dba2c474e1681ba357b55a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594301"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>Tutorial: Adicionar saídas ao arquivo Bicep do Azure Resource Manager

Neste tutorial, você aprenderá a retornar um valor da sua implantação. Você usa saídas quando precisa de um valor de um recurso implantado. Este tutorial leva **7 minutos** para ser concluído.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre variáveis](bicep-tutorial-add-variables.md), não sendo, porém, necessário.

É necessário ter o Visual Studio Code com a extensão Bicep e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Examinar o arquivo Bicep

No final do tutorial anterior, o arquivo Bicep tinha o seguinte conteúdo:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

Ele implanta uma conta de armazenamento, mas não retorna nenhuma informação sobre a conta de armazenamento. Talvez seja necessário capturar as propriedades de um novo recurso para que elas estejam disponíveis posteriormente para referência.

## <a name="add-outputs"></a>Adicionar saídas

Você pode usar saídas para retornar valores da implantação. Por exemplo, pode ser útil obter os pontos de extremidade para sua nova conta de armazenamento.

O exemplo a seguir mostra a alteração no seu arquivo Bicep para adicionar um valor de saída. Copie o arquivo inteiro e substitua o arquivo Bicep pelo conteúdo dele.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

Há alguns itens importantes a serem observados sobre o valor de saída adicionado.

O tipo de valor retornado é definido como `object`, o que significa que ele retorna um objeto de modelo.

Para obter a propriedade `primaryEndpoints` da conta de armazenamento, use o nome simbólico da conta de armazenamento. O recurso de preenchimento automático do Visual Studio Code apresenta a você uma lista completa das propriedades:

   ![Propriedades do objeto de nome simbólico do bicep do Visual Studio Code](./media/bicep-tutorial-add-outputs/visual-studio-code-bicep-output-properties.png)

## <a name="deploy-bicep-file"></a>Implantar o arquivo Bicep

Você está pronto para implantar o arquivo Bicep e examinar o valor retornado.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável `bicepFile` como o caminho para o arquivo Bicep, conforme mostrado no [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

Na saída do comando de implantação, você verá um objeto semelhante ao seguinte exemplo somente se a saída estiver no formato JSON:

```json
{
  "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
  "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
  "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
  "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
  "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
  "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Se a implantação falhar, use a opção `verbose` para obter informações sobre os recursos que estão sendo criados. Use a opção `debug` para obter mais informações de depuração.

## <a name="review-your-work"></a>Examinar seu trabalho

Você fez muito nos últimos seis tutoriais. Vamos reservar um momento para examinar o que você fez. Você criou um arquivo Bicep com parâmetros que são fáceis de fornecer. O arquivo Bicep é reutilizável em ambientes diferentes porque permite a personalização e cria dinamicamente valores necessários. Ele também retorna informações sobre a conta de armazenamento que você pode usar em seu script.

Agora, vamos examinar o grupo de recursos e o histórico de implantação.

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos no qual você fez a implantação.
1. Dependendo das etapas que você fez, você deve ter pelo menos uma e, talvez, várias contas de armazenamento no grupo de recursos.
1. Você também deve ter várias implantações bem-sucedidas listadas no histórico. Selecione esse link.

   ![Selecione implantações](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. Você vê todas as implantações no histórico. Selecione a implantação chamada **addoutputs**.

   ![Mostrar histórico de implantação](./media/bicep-tutorial-add-outputs/show-history.png)

1. Você pode examinar as entradas.

   ![Mostrar entradas](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. Você pode examinar as saídas.

   ![Mostrar saídas](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. Você pode examinar o modelo JSON.

   ![Mostrar modelo](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, o ideal será limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou um valor retornado ao arquivo Bicep. No próximo tutorial, você aprenderá a exportar um modelo JSON e a usar partes desse modelo exportado em seu arquivo Bicep.

> [!div class="nextstepaction"]
> [Usar modelo exportado](bicep-tutorial-export-template.md)
