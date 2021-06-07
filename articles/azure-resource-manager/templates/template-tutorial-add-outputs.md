---
title: Tutorial – adicionar saídas ao modelo
description: Adicione saídas ao modelo do ARM (modelo do Azure Resource Manager) para simplificar a sintaxe.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 7e022822f1306a5351cef43498f00a0ac0f2ef63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101707278"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>Tutorial: Adicionar saídas ao seu modelo do ARM

Neste tutorial, você aprenderá a retornar um valor por meio do modelo do ARM (modelo do Azure Resource Manager). Você usa saídas quando precisa de um valor de um recurso implantado. Este tutorial leva **7 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre variáveis](template-tutorial-add-variables.md), não sendo, porém, necessário.

É necessário ter o Visual Studio Code com a extensão Ferramentas do Resource Manager e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Examinar modelo

No final do tutorial anterior, o modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Ele implanta uma conta de armazenamento, mas não retorna nenhuma informação sobre a conta de armazenamento. Talvez seja necessário capturar as propriedades de um novo recurso para que elas estejam disponíveis posteriormente para referência.

## <a name="add-outputs"></a>Adicionar saídas

Você pode usar saídas para retornar valores do modelo. Por exemplo, pode ser útil obter os pontos de extremidade para sua nova conta de armazenamento.

O exemplo a seguir realça a alteração em seu modelo para adicionar um valor de saída. Copie o arquivo inteiro e substitua o modelo pelo conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

Há alguns itens importantes a serem observados sobre o valor de saída adicionado.

O tipo de valor retornado é definido como `object`, o que significa que ele retorna um objeto JSON.

Ele usa a função [reference](template-functions-resource.md#reference) para obter o estado do runtime da conta de armazenamento. Para obter o estado do runtime de um recurso, você passa o nome ou a ID de um recurso. Nesse caso, você usa a mesma variável que usou para criar o nome da conta de armazenamento.

Por fim, ela retorna a propriedade `primaryEndpoints` da conta de armazenamento.

## <a name="deploy-template"></a>Implantar modelo

Você está pronto para implantar o modelo e examinar o valor retornado.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável `templateFile` como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

Você fez muito nos últimos seis tutoriais. Vamos reservar um momento para examinar o que você fez. Você criou um modelo com parâmetros que são fáceis de fornecer. O modelo é reutilizável em ambientes diferentes porque permite a personalização e cria dinamicamente valores necessários. Ele também retorna informações sobre a conta de armazenamento que você pode usar em seu script.

Agora, vamos examinar o grupo de recursos e o histórico de implantação.

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos no qual você fez a implantação.
1. Dependendo das etapas que você fez, você deve ter pelo menos uma e, talvez, várias contas de armazenamento no grupo de recursos.
1. Você também deve ter várias implantações bem-sucedidas listadas no histórico. Selecione esse link.

   ![Selecione implantações](./media/template-tutorial-add-outputs/select-deployments.png)

1. Você vê todas as implantações no histórico. Selecione a implantação chamada **addoutputs**.

   ![Mostrar histórico de implantação](./media/template-tutorial-add-outputs/show-history.png)

1. Você pode examinar as entradas.

   ![Mostrar entradas](./media/template-tutorial-add-outputs/show-inputs.png)

1. Você pode examinar as saídas.

   ![Mostrar saídas](./media/template-tutorial-add-outputs/show-outputs.png)

1. Você pode examinar o modelo.

   ![Mostrar modelo](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, o ideal será limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou um valor retornado ao modelo. No próximo tutorial, você aprenderá a exportar um modelo e a usar partes desse modelo exportado em seu modelo.

> [!div class="nextstepaction"]
> [Usar modelo exportado](template-tutorial-export-template.md)
