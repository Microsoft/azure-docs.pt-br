---
title: Implantação de modelo What-If
description: Determine quais alterações ocorrerão para seus recursos antes de implantar um modelo de Azure Resource Manager.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: tomfitz
ms.openlocfilehash: 8122fa5c00a61017b5f358a112c94a5299539cee
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591617"
---
# <a name="arm-template-deployment-what-if-operation"></a>Operação teste de hipóteses de implantação do modelo do ARM

Antes de implantar um modelo de Azure Resource Manager (modelo ARM), você pode visualizar as alterações que ocorrerão. Azure Resource Manager fornece a operação What-If para permitir que você veja como os recursos serão alterados se você implantar o modelo. A operação what-if não faz nenhuma alteração nos recursos existentes. Em vez disso, ela prevê as alterações se o modelo especificado é implantado.

Você pode usar a operação What-If com as operações Azure PowerShell, CLI do Azure ou API REST. O What-If tem suporte para implantações de grupo de recursos, de assinatura, de grupo de gerenciamento e de nível de locatário.

## <a name="install-azure-powershell-module"></a>Instalar o módulo do Azure PowerShell

Para usar What-If no PowerShell, você deve ter **a versão 4,2 ou posterior do módulo AZ**.

Para instalar o módulo, use:

```powershell
Install-Module -Name Az -Force
```

Para obter mais informações sobre a instalação de módulos, consulte [Install Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="install-azure-cli-module"></a>Instalar o módulo CLI do Azure

Para usar o comando what-if na CLI do Azure, você precisará ter a CLI do Azure 2.5.0 ou posterior. Se necessário, [instale a última versão da CLI do Azure](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Ver resultados

Quando você usa What-If no PowerShell ou CLI do Azure, a saída inclui resultados codificados por cor que ajudam você a ver os diferentes tipos de alterações.

![Implantação do modelo do Resource Manager e-se operação fullresourcepayload e tipos de alteração](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A saída de texto é:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> A operação What-If não pode resolver a [função de referência](template-functions-resource.md#reference). Sempre que você definir uma propriedade como uma expressão de modelo que inclui a função de referência, os relatórios What-If serão alterados pela propriedade. Esse comportamento ocorre porque o What-If compara o valor atual da propriedade (como `true` ou `false` para um valor booliano) com a expressão de modelo não resolvida. Obviamente, esses valores não serão correspondentes. Quando você implanta o modelo, a propriedade só será alterada quando a expressão do modelo for resolvida para um valor diferente.

## <a name="what-if-commands"></a>Comandos What-If

### <a name="azure-powershell"></a>Azure PowerShell

Para visualizar as alterações antes de implantar um modelo, use [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) ou [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment). Adicione o `-Whatif` parâmetro de opção ao comando de implantação.

* `New-AzResourceGroupDeployment -Whatif` para implantações de grupo de recursos
* `New-AzSubscriptionDeployment -Whatif` e `New-AzDeployment -Whatif` para implantações em nível de assinatura

Você pode usar o `-Confirm` parâmetro switch para visualizar as alterações e receber uma solicitação para continuar com a implantação.

* `New-AzResourceGroupDeployment -Confirm` para implantações de grupo de recursos
* `New-AzSubscriptionDeployment -Confirm` e `New-AzDeployment -Confirm` para implantações em nível de assinatura

Os comandos anteriores retornam um resumo de texto que você pode inspecionar manualmente. Para obter um objeto que você pode inspecionar para alterações programaticamente, use [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) ou [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult).

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` para implantações de grupo de recursos
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` ou `$results = Get-AzDeploymentWhatIfResult` para implantações de nível de assinatura

### <a name="azure-cli"></a>CLI do Azure

Para visualizar as alterações antes de implantar um modelo, use:

* [AZ Deployment Group e-se](/cli/azure/deployment/group#az-deployment-group-what-if) para implantações de grupo de recursos
* [AZ Deployment sub What-If](/cli/azure/deployment/sub#az-deployment-sub-what-if) para implantações em nível de assinatura
* [AZ Deployment mg e-se](/cli/azure/deployment/mg#az-deployment-mg-what-if) para implantações de grupo de gerenciamento
* [AZ Deployment locatário What-se](/cli/azure/deployment/tenant#az-deployment-tenant-what-if) para implantações de locatário

Você pode usar a `--confirm-with-what-if` opção (ou sua forma abreviada `-c` ) para visualizar as alterações e receber uma solicitação para continuar com a implantação. Adicione esta opção a:

* [Criar grupo de implantação AZ](/cli/azure/deployment/group#az-deployment-group-create)
* [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create).
* [criação de mg de implantação AZ](/cli/azure/deployment/mg#az-deployment-mg-create)
* [criar locatário de implantação AZ](/cli/azure/deployment/tenant#az-deployment-tenant-create)

Por exemplo, use `az deployment group create --confirm-with-what-if` ou `-c` para implantações de grupo de recursos.

Os comandos anteriores retornam um resumo de texto que você pode inspecionar manualmente. Para obter um objeto JSON que você pode inspecionar para alterações programaticamente, use a `--no-pretty-print` opção. Por exemplo, use `az deployment group what-if --no-pretty-print` para implantações de grupo de recursos.

Se você quiser retornar os resultados sem cores, abra o arquivo de [configuração CLI do Azure](/cli/azure/azure-cli-configuration) . Defina **no_color** como **Sim**.

### <a name="azure-rest-api"></a>API REST do Azure

Para a API REST, use:

* [Implantações-What If](/rest/api/resources/deployments/whatif) para implantações de grupo de recursos
* [Implantações-What If no escopo da assinatura](/rest/api/resources/deployments/whatifatsubscriptionscope) para implantações de assinatura
* [Implantações-What If no escopo do grupo de gerenciamento](/rest/api/resources/deployments/whatifatmanagementgroupscope) para implantações do grupo de gerenciamento
* [Implantações-What If no escopo do locatário](/rest/api/resources/deployments/whatifattenantscope) para implantações de locatário.

## <a name="change-types"></a>Alterar tipos

A operação What-If lista seis tipos diferentes de alterações:

- **Criar**: o recurso não existe atualmente, mas está definido no modelo. O recurso será criado.

- **Excluir**: esse tipo de alteração se aplica somente ao usar o [modo completo](deployment-modes.md) para implantação. O recurso existe, mas não está definido no modelo. Com o modo completo, o recurso será excluído. Somente os recursos que [dão suporte à exclusão de modo completo](complete-mode-deletion.md) são incluídos nesse tipo de alteração.

- **Ignorar**: o recurso existe, mas não está definido no modelo. O recurso não será implantado nem modificado.

- **NoChange**: o recurso existe e é definido no modelo. O recurso será reimplantado, mas as propriedades do recurso não serão alteradas. Esse tipo de alteração é retornado quando [ResultFormat](#result-format) é definido como `FullResourcePayloads` , que é o valor padrão.

- **Modify**: o recurso existe e é definido no modelo. O recurso será reimplantado e as propriedades do recurso serão alteradas. Esse tipo de alteração é retornado quando [ResultFormat](#result-format) é definido como `FullResourcePayloads` , que é o valor padrão.

- **Implantar**: o recurso existe e é definido no modelo. O recurso será reimplantado. As propriedades do recurso podem ou não ser alteradas. A operação retorna esse tipo de alteração quando não tem informações suficientes para determinar se as propriedades serão alteradas. Você só verá essa condição quando [ResultFormat](#result-format) estiver definido como `ResourceIdOnly` .

## <a name="result-format"></a>Formato de resultado

Você controla o nível de detalhe que é retornado sobre as alterações previstas. Você tem duas opções:

* **FullResourcePayloads** -retorna uma lista de recursos que serão alterados e detalhes sobre as propriedades que serão alteradas
* **ResourceIdOnly** -retorna uma lista de recursos que serão alterados

O valor padrão é **FullResourcePayloads**.

Para comandos de implantação do PowerShell, use o `-WhatIfResultFormat` parâmetro. Nos comandos de objeto programático, use o `ResultFormat` parâmetro.

CLI do Azure, use o `--result-format` parâmetro.
 
Os resultados a seguir mostram os dois formatos de saída diferentes:

- Cargas de recursos completas

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Somente ID do recurso

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Executar a operação What-If

### <a name="set-up-environment"></a>Configurar o ambiente

Para ver como funciona o que acontece, vamos executar alguns testes. Primeiro, implante um [modelo que cria uma rede virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Você usará essa rede virtual para testar como as alterações são relatadas por What-If.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Modificação de teste

Após a conclusão da implantação, você estará pronto para testar a operação What-If. Desta vez, você implanta um [modelo que altera a rede virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Falta uma marca original nas marcas originais, uma sub-rede foi removida e o prefixo de endereço foi alterado.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

A saída de what-if é semelhante a:

![Saída da operação What-If da implantação de modelo do Resource Manager](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A saída de texto é:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Observe na parte superior da saída que as cores são definidas para indicar o tipo de alterações.

Na parte inferior da saída, mostra que o proprietário da marca foi excluído. O prefixo de endereço mudou de 10.0.0.0/16 para 10.0.0.0/15. A sub-rede denominada subnet001 foi excluída. Lembre-se que essas alterações não foram implantadas. Você verá uma visualização das alterações que ocorrerão se você implantar o modelo.

Algumas das propriedades listadas como excluídas não serão realmente alteradas. As propriedades podem ser relatadas incorretamente como excluídas quando não estão no modelo, mas são definidas automaticamente durante a implantação como valores padrão. Esse resultado é considerado "ruído" na resposta What-If. O recurso implantado final terá os valores definidos para as propriedades. Como a operação What-If amadurece, essas propriedades serão filtradas do resultado.

## <a name="programmatically-evaluate-what-if-results"></a>Avaliar programaticamente os resultados de hipóteses

Agora, vamos avaliar programaticamente os resultados What-If definindo o comando como uma variável.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Você pode ver um resumo de cada alteração.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Confirmar exclusão

A operação What-If dá suporte ao uso do [modo de implantação](deployment-modes.md). Quando definido para o modo completo, os recursos que não estão no modelo são excluídos. O exemplo a seguir implanta um [modelo que não tem recursos definidos](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) no modo completo.

Para visualizar as alterações antes de implantar um modelo, use o parâmetro confirmar opção com o comando de implantação. Se as alterações forem as esperadas, responda que você deseja que a implantação seja concluída.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Como nenhum recurso está definido no modelo e o modo de implantação está definido como concluído, a rede virtual será excluída.

![Implantação do modelo do Resource Manager o modo de implantação de saída de operação de conclusão](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

A saída de texto é:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Você vê as alterações esperadas e pode confirmar que deseja que a implantação seja executada.

## <a name="sdks"></a>SDKs

Você pode usar a operação What-If por meio dos SDKs do Azure.

* Para Python, use [What-If](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-).

* Para Java, use a [classe DeploymentWhatIf](/java/api/com.microsoft.azure.management.resources.deploymentwhatif).

* Para .NET, use a [classe DeploymentWhatIf](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif).

## <a name="next-steps"></a>Próximas etapas

- Se você observar resultados incorretos da operação What-If, informe os problemas em [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- Para um módulo Microsoft Learn que aborda o uso do quê, consulte [Visualizar alterações e validar recursos do Azure usando o What-If e o ARM template Test Toolkit](/learn/modules/arm-template-test/).
- Para implantar modelos com Azure PowerShell, consulte [implantar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
- Para implantar modelos com CLI do Azure, consulte [implantar recursos com modelos ARM e CLI do Azure](deploy-cli.md).
- Para implantar modelos com REST, consulte [implantar recursos com modelos ARM e API REST do Gerenciador de recursos](deploy-rest.md).
