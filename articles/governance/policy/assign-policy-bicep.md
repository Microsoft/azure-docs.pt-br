---
title: 'Guia de início rápido: Nova atribuição de política com o arquivo Bicep (versão prévia)'
description: Neste guia de início rápido, você usará um arquivo Bicep (versão prévia) para criar uma atribuição de política a fim de identificar os recursos fora de conformidade.
ms.date: 04/01/2021
ms.topic: quickstart
ms.custom: subject-bicepqs
ms.openlocfilehash: 17460f9a06d7225d50933608645ea8aea2f5e8f6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223849"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-bicep-file"></a>Guia de início rápido: Criar uma atribuição de política para identificar os recursos fora de conformidade usando um arquivo Bicep

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos.
Este guia de início rápido descreve o processo de uso de um arquivo [Bicep (versão prévia)](https://github.com/Azure/bicep) compilado em um modelo do ARM (Azure Resource Manager) para criar uma atribuição de política a fim de identificar as máquinas virtuais que não estão usando discos gerenciados. No final deste processo, você identificará com êxito quais máquinas virtuais não estão usando discos gerenciados. Eles _não estão em conformidade_ com a atribuição da política.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo é aberto no portal do Azure.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Botão para implantar o modelo do ARM para atribuir um Azure Policy ao Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Pré-requisitos:

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- Versão `0.3` ou superior do Bicep instalada. Se você ainda não tem a CLI do Bicep ou precisa atualizá-la, confira [Instalar o Bicep (versão prévia)](../../azure-resource-manager/templates/bicep-install.md).

## <a name="review-the-bicep-file"></a>Examinar o arquivo Bicep

Neste guia de início rápido, você criará uma atribuição de política e atribuirá uma definição de política interna chamada _Auditar VMs que não usam discos gerenciados_ (`06a78e20-9358-41c9-923c-fb736d382a4d`). Para ver uma lista parcial das políticas internas disponíveis, confira [Exemplos do Azure Policy](./samples/index.md).

Crie o seguinte arquivo Bicep como `assignment.bicep`:

```bicep
param policyAssignmentName string = 'audit-vm-manageddisks'
param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'

resource assignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
    name: policyAssignmentName
    properties: {
        scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
        policyDefinitionId: policyDefinitionID
    }
}

output assignmentId string = assignment.id
```

O recurso definido no arquivo é:

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>Implantar o modelo

> [!NOTE]
> o serviço Azure Policy é gratuito. Para saber mais, confira [Visão geral do Azure Policy](./overview.md).

Depois que a CLI do Bicep for instalada e o arquivo for criado, implante o arquivo Bicep com:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name PolicyDeployment `
  -ResourceGroupName PolicyGroup `
  -TemplateFile assignment.bicep
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name PolicyDeployment \
  --resource-group PolicyGroup \
  --template-file assignment.bicep
```

---

Alguns recursos adicionais:

- Para encontrar mais modelos de exemplo, confira o [Modelo de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Para ver a referência de modelo, acesse [Referência de modelo do Azure](/azure/templates/microsoft.authorization/allversions).
- Para saber como desenvolver modelos do ARM, confira a [documentação do Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Para conhecer a implantação de nível de assinatura, confira [Create resource groups and resources at the subscription level](../../azure-resource-manager/templates/deploy-to-subscription.md) (Criar grupos de recursos e recursos no nível da assinatura).

## <a name="validate-the-deployment"></a>Validar a implantação

Selecione **Conformidade** no lado esquerdo da página. Em seguida, localize as _VMs de auditoria que não usam a atribuição de política de discos gerenciados_ que você criou.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Captura de tela dos detalhes de conformidade na página Conformidade com a Política." border="false":::

Se houver recursos sem conformidade com essa nova atribuição, eles aparecerão em **Recursos sem conformidade**.

Para saber mais, confira [How compliance works](./how-to/get-compliance-data.md#how-compliance-works) (Como a conformidade funciona).

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a atribuição criada, siga estas etapas:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página e localize as _VMs de auditoria que não usam a atribuição  de política de discos gerenciados_ que você criou.

1. Clique com o botão direito do mouse na atribuição de política _Auditar VMs que não usam discos gerenciados_ e selecione **Excluir atribuição**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Captura de tela do uso do menu de contexto para excluir uma atribuição da página Conformidade." border="false":::

1. Exclua o arquivo `assignment.bicep`.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você atribuiu uma definição de política interna a um escopo e avaliou seu relatório de conformidade. A definição de política valida que todos os recursos no escopo estão em conformidade e identifica quais não estão.

Para saber mais sobre a atribuição de políticas para validar que novos recursos estejam em conformidade, continue com o tutorial para:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./tutorials/create-and-manage.md)