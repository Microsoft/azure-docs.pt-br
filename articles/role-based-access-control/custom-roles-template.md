---
title: Criar ou atualizar funções personalizadas do Azure usando um modelo de Azure Resource Manager – RBAC do Azure
description: Saiba como criar ou atualizar funções personalizadas do Azure usando um modelo de Azure Resource Manager (modelo ARM) e controle de acesso baseado em função do Azure (RBAC do Azure).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: 0626a9e36d05ac9cb51f62652dbe6f3133bbc6d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095905"
---
# <a name="create-or-update-azure-custom-roles-using-an-arm-template"></a>Criar ou atualizar funções personalizadas do Azure usando um modelo ARM

Se as [funções internas do Azure](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias [funções personalizadas](custom-roles.md). Este artigo descreve como criar ou atualizar uma função personalizada usando um modelo de Azure Resource Manager (modelo ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Para criar uma função personalizada, especifique um nome de função, permissões e onde a função pode ser usada. Neste artigo, você cria uma função chamada _de função personalizada-leitor RG_ com permissões de recurso que podem ser atribuídas em um escopo de assinatura ou inferior.

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsubscription-deployments%2Fcreate-role-def%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma função personalizada, você deve ter:

- Permissões para criar funções personalizadas, como [proprietário](built-in-roles.md#owner) ou [administrador de acesso do usuário](built-in-roles.md#user-access-administrator).

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste artigo é dos [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/create-role-def). O modelo tem quatro parâmetros e uma seção de recursos. Os quatro parâmetros são:

- Matriz de ações com um valor padrão de `["Microsoft.Resources/subscriptions/resourceGroups/read"]` .
- Matriz de `notActions` com um valor padrão vazio.
- Nome da função com um valor padrão de `Custom Role - RG Reader` .
- Descrição da função com um valor padrão de `Subscription Level Deployment of a Role Definition` .

O escopo onde essa função personalizada pode ser atribuída é definido como a assinatura atual.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

O recurso definido no modelo inclui:

- [Microsoft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

## <a name="deploy-the-template"></a>Implantar o modelo

Siga estas etapas para implantar o modelo anterior.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Abra o Azure Cloud Shell para PowerShell.

1. Copie e cole o script a seguir no Cloud Shell.

    ```azurepowershell-interactive
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"
    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Insira um local para a implantação, como `centralus` .

1. Insira uma lista de ações para a função personalizada como uma lista separada por vírgulas, como `Microsoft.Resources/resources/read,Microsoft.Resources/subscriptions/resourceGroups/read` .

1. Se necessário, pressione ENTER para executar o `New-AzDeployment` comando.

    O comando [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) implanta o modelo para criar a função personalizada.

    Será exibida uma saída semelhante à seguinte:

    ```azurepowershell-interactive
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions

    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0

    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Siga estas etapas para verificar se a função personalizada foi criada.

1. Execute o comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) para listar a função personalizada.

    ```azurepowershell-interactive
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    Será exibida uma saída semelhante à seguinte:

    ```azurepowershell-interactive
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. Na portal do Azure, abra sua assinatura.

1. No menu à esquerda, selecione **controle de acesso (iam)**.

1. Selecione a guia **funções** .

1. Defina a lista de **tipos** como **CustomRole**.

1. Verifique se a função **personalizada do leitor de RG** está listada.

   ![Nova função personalizada no portal do Azure](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Semelhante à criação de uma função personalizada, você pode atualizar uma função personalizada existente usando um modelo. Para atualizar uma função personalizada, você deve especificar a função que deseja atualizar.

Aqui estão as alterações que você precisaria fazer no modelo de início rápido anterior para atualizar a função personalizada.

- Inclua a ID da função como um parâmetro.
    ```json
        ...
        "roleDefName": {
          "type": "string",
          "metadata": {
            "description": "ID of the role definition"
          }
        ...
    ```

- Inclua o parâmetro de ID de função na definição de função.

    ```json
      ...
      "resources": [
        {
          "type": "Microsoft.Authorization/roleDefinitions",
          "apiVersion": "2018-07-01",
          "name": "[parameters('roleDefName')]",
          "properties": {
            ...
    ```

Aqui está um exemplo de como implantar o modelo.

```azurepowershell
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
[string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
$actions = $actions.Split(',')
$roleDefName = Read-Host -Prompt "Enter the role ID to update"
$templateFile = "rg-reader-update.json"
New-AzDeployment -Location $location -TemplateFile $templateFile -actions $actions -roleDefName $roleDefName
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a função personalizada, siga estas etapas.

1. Execute o comando a seguir para remover a função personalizada.

    ```azurepowershell-interactive
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Digite **Y** para confirmar que você deseja remover a função personalizada.

## <a name="next-steps"></a>Próximas etapas

- [Entender as definições de função do Azure](role-definitions.md)
- [Início rápido: atribuir uma função do Azure usando um modelo de Azure Resource Manager](quickstart-role-assignments-template.md)
- [Documentação do modelo ARM](../azure-resource-manager/templates/index.yml)
