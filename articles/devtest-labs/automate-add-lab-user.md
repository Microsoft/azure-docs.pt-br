---
title: Automatizar a adição de um usuário de laboratório no Azure DevTest Labs | Microsoft Docs
description: Este artigo mostra como automatizar a adição de um usuário a um laboratório no Azure DevTest Labs usando modelos de Azure Resource Manager, o PowerShell e a CLI.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: dc5522cfe694f193b9bbeeb3145808a367a62c12
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519394"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatize a adição de um usuário de laboratório a um laboratório no Azure DevTest Labs
Azure DevTest Labs permite criar rapidamente ambientes de desenvolvimento e teste de autoatendimento usando o portal do Azure. No entanto, se você tiver várias equipes e várias instâncias do DevTest Labs, a automatização do processo de criação poderá poupar tempo. Os [modelos de Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) permitem criar laboratórios, VMS de laboratório, imagens personalizadas, fórmulas e adicionar usuários de maneira automatizada. Este artigo se concentra especificamente na adição de usuários a uma instância do DevTest Labs.

Para adicionar um usuário a um laboratório, você adiciona o usuário à função de **usuário do DevTest Labs** para o laboratório. Este artigo mostra como automatizar a adição de um usuário a um laboratório usando uma das seguintes maneiras:

- Modelos do Azure Resource Manager
- Cmdlets do Azure PowerShell 
- CLI do Azure.

## <a name="use-azure-resource-manager-templates"></a>Usar modelos do Gerenciador de Recursos do Azure
O modelo do Resource Manager de exemplo a seguir especifica um usuário a ser adicionado à função de **usuário do DevTest Labs** de um laboratório. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Se você estiver atribuindo a função no mesmo modelo que está criando o laboratório, lembre-se de adicionar uma dependência entre o recurso de atribuição de função e o laboratório. Para obter mais informações, consulte [definindo dependências no artigo Azure Resource Manager modelos](../azure-resource-manager/templates/define-resource-dependency.md) .

### <a name="role-assignment-resource-information"></a>Informações de recurso de atribuição de função
O recurso de atribuição de função precisa especificar o tipo e o nome.

A primeira coisa a ser observada é que o tipo do recurso não é `Microsoft.Authorization/roleAssignments` como seria para um grupo de recursos.  Em vez disso, o tipo de recurso segue o padrão `{provider-namespace}/{resource-type}/providers/roleAssignments` . Nesse caso, o tipo de recurso será `Microsoft.DevTestLab/labs/providers/roleAssignments` .

O nome da atribuição de função em si precisa ser globalmente exclusivo.  O nome da atribuição usa o padrão `{labName}/Microsoft.Authorization/{newGuid}` . O `newGuid` é um valor de parâmetro para o modelo. Ele garante que o nome da atribuição de função seja exclusivo. Como não há nenhuma função de modelo para criar GUIDs, você precisa gerar um GUID por conta própria usando qualquer ferramenta de gerador de GUID.  

No modelo, o nome para a atribuição de função é definido pela `fullDevTestLabUserRoleName` variável. A linha exata do modelo é:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Propriedades do recurso de atribuição de função
Uma atribuição de função em si define três propriedades. Ele precisa do `roleDefinitionId` , `principalId` , e `scope` .

### <a name="role-definition"></a>Definição de função
A ID de definição de função é o identificador de cadeia de caracteres para a definição de função existente. A ID da função está no formato `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}` . 

A ID da assinatura é obtida usando a `subscription().subscriptionId` função de modelo.  

Você precisa obter a definição de função para a `DevTest Labs User` função interna. Para obter o GUID da função de [usuário do DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) , você pode usar a [API REST de atribuições de função](/rest/api/authorization/roleassignments) ou o cmdlet [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) .

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

A ID da função é definida na seção de variáveis e nomeada `devTestLabUserRoleId` . No modelo, a ID da função é definida como: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>ID da entidade de segurança
ID da entidade de segurança é a ID de objeto do Active Directory usuário, grupo ou entidade de serviço que você deseja adicionar como um usuário do laboratório ao laboratório. O modelo usa o `ObjectId` como um parâmetro.

Você pode obter o ObjectId usando os cmdlets do PowerShell [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup ou [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) . Esses cmdlets retornam uma única lista de Active Directory objetos que têm uma propriedade de ID, que é a ID de objeto de que você precisa. O exemplo a seguir mostra como obter a ID de objeto de um único usuário em uma empresa.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Você também pode usar os cmdlets do Azure Active Directory PowerShell que incluem [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)e [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Escopo
Escopo Especifica o recurso ou grupo de recursos para o qual a atribuição de função deve ser aplicada. Para recursos, o escopo está no formato: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}` . O modelo usa a `subscription().subscriptionId` função para preencher a `subscription-id` parte e a `resourceGroup().name` função de modelo para preencher a `resource-group-name` parte. O uso dessas funções significa que o laboratório ao qual você está atribuindo uma função deve existir na assinatura atual e no mesmo grupo de recursos no qual a implantação do modelo é feita. A última parte, `resource-name` , é o nome do laboratório. Esse valor é recebido por meio do parâmetro de modelo neste exemplo. 

O escopo da função no modelo: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Implantação do modelo
Primeiro, crie um arquivo de parâmetro (por exemplo: azuredeploy.parameters.jsem) que passe valores para parâmetros no modelo do Resource Manager. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Em seguida, use o cmdlet [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) do PowerShell para implantar o modelo do Resource Manager. O comando de exemplo a seguir atribui uma pessoa, um grupo ou uma entidade de serviço à função de usuário do DevTest Labs para um laboratório.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

É importante observar que o nome da implantação do grupo e o GUID de atribuição de função precisam ser exclusivos. Se você tentar implantar uma atribuição de recurso com um GUID não exclusivo, receberá um `RoleAssignmentUpdateNotPermitted` erro.

Se você planeja usar o modelo várias vezes para adicionar vários objetos Active Directory à função de usuário do DevTest Labs para seu laboratório, considere o uso de objetos dinâmicos no comando do PowerShell. O exemplo a seguir usa o cmdlet [New-GUID](/powershell/module/Microsoft.PowerShell.Utility/New-Guid) para especificar o nome da implantação do grupo de recursos e o GUID de atribuição de função dinamicamente.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure
Conforme discutido na introdução, você cria uma nova atribuição de função do Azure para adicionar um usuário à função de **usuário do DevTest Labs** para o laboratório. No PowerShell, você faz isso usando o cmdlet [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) . Esse cmdlet tem muitos parâmetros opcionais para permitir a flexibilidade. O `ObjectId` , `SigninName` , ou `ServicePrincipalName` pode ser especificado como o objeto que recebe permissões.  

Aqui está um exemplo Azure PowerShell comando que adiciona um usuário à função de usuário do DevTest Labs no laboratório especificado.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Para especificar o recurso ao qual as permissões estão sendo concedidas podem ser especificadas por uma combinação de `ResourceName` , `ResourceType` `ResourceGroup` ou pelo `scope` parâmetro. Qualquer combinação de parâmetros é usada, forneça informações suficientes ao cmdlet para identificar exclusivamente o objeto de Active Directory (usuário, grupo ou entidade de serviço), escopo (grupo de recursos ou recurso) e definição de função.

## <a name="use-azure-command-line-interface-cli"></a>Usar a CLI (interface de linha de comando) do Azure
No CLI do Azure, a adição de um usuário de laboratórios a um laboratório é feita usando o `az role assignment create` comando. Para obter mais informações sobre CLI do Azure cmdlets, consulte [Adicionar ou remover atribuições de função do Azure usando CLI do Azure](../role-based-access-control/role-assignments-cli.md).

O objeto que está sendo concedido acesso pode ser especificado pelos `objectId` parâmetros, `signInName` , `spn` . O laboratório ao qual o objeto está sendo concedido acesso pode ser identificado pela `scope` URL ou uma combinação dos `resource-name` `resource-type` parâmetros, e `resource-group` .

O exemplo a seguir CLI do Azure mostra como adicionar uma pessoa à função de usuário do DevTest Labs para o laboratório especificado.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Criar e gerenciar máquinas virtuais com DevTest Labs usando a CLI do Azure](devtest-lab-vmcli.md)
- [Criar uma máquina virtual com o DevTest Labs usando o Azure PowerShell](devtest-lab-vm-powershell.md)
- [Usar ferramentas de linha de comando para iniciar e parar Azure DevTest Labs máquinas virtuais](use-command-line-start-stop-virtual-machines.md)

