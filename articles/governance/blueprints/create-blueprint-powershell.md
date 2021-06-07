---
title: 'Início Rápido: Criar um blueprint com o PowerShell'
description: Neste início rápido, você usará o Azure Blueprints para criar, definir e implantar artefatos usando o PowerShell.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-api
ms.openlocfilehash: 29c43da0c1467c74ddc85a447a0cf9addb574c7e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538901"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Início Rápido: Definir e atribuir um Azure Blueprint com o PowerShell

Aprender a criar e atribuir blueprints permite definir padrões comuns para desenvolver configurações reutilizáveis e que podem ser implantadas rapidamente com base em modelos do ARM (modelos do Azure Resource Manager), políticas, segurança e muito mais. Neste tutorial, você aprenderá a usar o Blueprint do Azure para executar algumas das tarefas comuns relacionadas à criação, publicação e atribuição de um blueprint dentro de sua organização, como:

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.
- Se ainda não estiver instalado, siga as instruções em [Adicionar o módulo Az.Blueprint](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) para instalar e validar o módulo **Az.Blueprint** na Galeria do PowerShell.
- Se você ainda não usou o Azure Blueprints, registre o provedor de recursos por meio do Azure PowerShell com `Register-AzResourceProvider -ProviderNamespace Microsoft.Blueprint`.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Criar um plano gráfico

A primeira etapa na definição de um modelo padrão para conformidade é compor um blueprint a partir dos recursos disponíveis. Vamos criar um blueprint chamado 'MyBlueprint' para configurar as atribuições de função e política para a assinatura. Em seguida, vamos adicionar um grupo de recursos, um modelo do ARM e uma atribuição de função ao grupo de recursos.

> [!NOTE]
> Usando o PowerShell, o objeto _blueprint_ é criado primeiro. Para cada _artefato_ com parâmetros a ser adicionado, os parâmetros precisam ser definidos com antecedência no _blueprint_ inicial.

1. Crie o objeto _blueprint_ original. O parâmetro **BlueprintFile** usa um arquivo JSON que inclui propriedades sobre o blueprint, grupos de recursos que devem ser criados e todos os parâmetros no nível do blueprint. Os parâmetros são definidos durante a atribuição e usados pelos artefatos adicionados nas etapas posteriores.

   - Arquivo JSON – blueprint.json

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "defaultValue": "Standard_LRS",
                     "allowedValues": [
                         "Standard_LRS",
                         "Standard_GRS",
                         "Standard_ZRS",
                         "Premium_LRS"
                     ],
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Use o nome de arquivo _blueprint.json_ ao criar suas definições de blueprint programaticamente.
     > Esse nome de arquivo é usado ao chamar [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     Por padrão, o objeto blueprint é criado na assinatura padrão. Para especificar o grupo de gerenciamento, use o parâmetro **ManagementGroupId**. Para especificar a assinatura, use o parâmetro **SubscriptionId**.

1. Adicione a atribuição de função na assinatura. O **ArtifactFile** define o _tipo_ de artefato, as propriedades se alinham com o identificador da definição de função e as identidades de entidade de segurança são passadas como uma matriz de valores. No exemplo a seguir, as identidades de entidade de segurança concedidas à função especificada são configuradas para um parâmetro que é definido durante a atribuição do blueprint. Este exemplo usa a função interna _Colaborador_ com um GUID igual a `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - Arquivo JSON – \artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Adicione a atribuição de política na assinatura. O **ArtifactFile** define o _tipo_ de artefato, as propriedades que se alinham com uma definição de iniciativa ou política e configura a atribuição de política para usar os parâmetros de blueprint definidos na configuração durante a atribuição do blueprint. Este exemplo usa a política interna _Aplicar a marca e seu valor padrão a grupos de recursos_ com um GUID igual a `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Arquivo JSON – \artifacts\policyTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Adicione outra atribuição de política para a marca Armazenamento (reutilizando o parâmetro _storageAccountType_) na assinatura. Este artefato de atribuição de política adicional demonstra que um parâmetro definido no blueprint pode ser usado por mais de um artefato. No exemplo, o **storageAccountType** é usado para definir uma marca no grupo de recursos. Esse valor fornece informações sobre a conta de armazenamento que será criada na próxima etapa. Este exemplo usa a política interna _Aplicar a marca e seu valor padrão a grupos de recursos_ com um GUID igual a `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Arquivo JSON – \artifacts\policyStorageTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Adicione o modelo sob o grupo de recursos. O **TemplateFile** de um modelo do ARM inclui o componente JSON normal do modelo. O modelo também reutiliza os parâmetros de blueprint **storageAccountType**, **tagName** e **tagValue** transmitindo-os para o modelo. Os parâmetros de blueprint são disponibilizados para o modelo usando o parâmetro **TemplateParameterFile** e, dentro do modelo JSON, o par chave-valor é usado para injetar o valor. Os nomes dos parâmetros de blueprint e de modelo podem ser iguais.

   - Arquivo de modelo do ARM JSON – \artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - Arquivo de parâmetro de modelo de ARM JSON – \artifacts\templateStorageParams.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "value": "[parameters('storageAccountType')]"
             },
             "tagNameFromBP": {
                 "value": "[parameters('tagName')]"
             },
             "tagValueFromBP": {
                 "value": "[parameters('tagValue')]"
             }
         }
     }
     ```

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Adicione atribuição de função sob o grupo de recursos. Semelhante à entrada de atribuição de função anterior, o exemplo abaixo usa o identificador da definição para a função **Proprietário** e fornece a ela um parâmetro diferente do blueprint. Este exemplo usa a função interna _Proprietário_ com um GUID igual a `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - Arquivo JSON – \artifacts\roleOwner.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Publicar um modelo

Agora que os artefatos foram adicionados ao blueprint, é hora de publicá-lo. A publicação disponibiliza a atribuição a uma assinatura.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

O valor de `{BlueprintVersion}` é uma cadeia de caracteres de letras, números e hifens (sem espaços ou outros caracteres especiais) com um comprimento máximo de 20 caracteres. Use algo exclusivo e informativo, como **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Atribuir um modelo

Depois que um blueprint é publicado usando o PowerShell, ele pode ser atribuído a uma assinatura. Atribua o blueprint que você criou a uma das assinaturas em sua hierarquia do grupo de gerenciamento. Se o blueprint for salvo em uma assinatura, ele só poderá ser atribuído a essa assinatura. O parâmetro **Blueprint** especifica o blueprint a ser atribuído. Para fornecer parâmetros de nome, localização, identidade, bloqueio e blueprint, use os parâmetros correspondentes do PowerShell no cmdlet `New-AzBlueprintAssignment` ou forneça-os no arquivo JSON do parâmetro **AssignmentFile**.

1. Execute a implantação do blueprint atribuindo-o uma assinatura. Como os parâmetros **contributors** e **owners** exigem que uma matriz de objectIds das entidades de segurança seja concedida à atribuição de função, use a [API do Graph do Azure Active Directory](/graph/migrate-azure-ad-graph-planning-checklist) para coletar as objectIds a serem usadas no **AssignmentFile** de seus usuários, grupos ou entidades de serviço.

   - Arquivo JSON – blueprintAssignment.json

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - Comando do PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Identidade gerenciada atribuída pelo usuário

     Uma atribuição de blueprint também pode usar uma [identidade gerenciada atribuída por usuário](../../active-directory/managed-identities-azure-resources/overview.md).
     Nesse caso, a parte **identidade** do arquivo da atribuição JSON é alterada da seguinte maneira. Substitua `{tenantId}`, `{subscriptionId}`, `{yourRG}` e `{userIdentity}` pelo tenantId, o subscriptionId, o nome do grupo de recursos e o nome da identidade gerenciada atribuída por usuário, respectivamente.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     A **identidade gerenciada atribuída por usuário** pode estar em qualquer assinatura e grupo de recursos aos quais o usuário que atribui o blueprint tem acesso.

     > [!IMPORTANT]
     > O Azure Blueprints não gerencia a identidade gerenciada atribuída por usuário. Os usuários são responsáveis por atribuir funções e permissões suficientes ou a atribuição de blueprint falhará.

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="unassign-a-blueprint"></a>Cancelar a atribuição de um blueprint

Você pode remover um blueprint de uma assinatura. A remoção geralmente é feita quando os recursos de artefato não são mais necessários. Quando um blueprint é removido, os artefatos atribuídos como parte desse blueprint são deixados para trás. Para remover uma atribuição de blueprint, use o cmdlet `Remove-AzBlueprintAssignment`:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou, atribuiu e removeu um blueprint com o PowerShell. Para saber mais sobre o Azure Blueprints, prossiga para o artigo de ciclo de vida do blueprint.

> [!div class="nextstepaction"]
> [Saiba mais sobre o ciclo de vida do blueprint](./concepts/lifecycle.md)
