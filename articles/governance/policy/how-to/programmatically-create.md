---
title: Cria políticas de maneira programática
description: Este artigo orienta você na criação e gerenciamento por meio de programação de políticas no Azure Policy com a CLI do Azure, o Azure PowerShell e a API REST.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 4c3a503e8d48573441093879511490608437b1e1
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033856"
---
# <a name="programmatically-create-policies"></a>Cria políticas de maneira programática

Este artigo orienta você na criação e gerenciamento por meio de programação de políticas. As definições do Azure Policy aplicam diferentes regras e efeitos em seus recursos. A imposição garante que os recursos permaneçam em conformidade com seus padrões empresariais e contratos de nível de serviço.

Para obter informações sobre a conformidade, veja [como obter dados de conformidade](get-compliance-data.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se os seguintes pré-requisitos são cumpridos:

1. Caso ainda não tenha feito, instale o [ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta que envia solicitações HTTP para APIs baseadas no Azure Resource Manager.

1. Atualize seu módulo do Azure PowerShell para a versão mais recente. Confira [Instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps) para obter informações detalhadas. Para obter mais informações sobre a versão mais recente, consulte o [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Registre o provedor de recursos do Azure Policy Insights usando o Azure PowerShell para validar que sua assinatura funciona com o provedor de recursos. Para registrar um provedor de recursos, você precisa ter permissão para executar a operação de ação de registro para o provedor de recursos. Esta operação está incluída nas funções de Colaborador e de Proprietário. Execute o seguinte comando para registrar o provedor de recursos:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Para saber mais sobre como registrar e exibir provedores de recursos, consulte [Provedores de recursos e tipos](../../../azure-resource-manager/management/resource-providers-and-types.md).

1. Se ainda não fez, instale a CLI do Azure. Você pode obter a versão mais recente em [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Criar e atribuir uma definição de política

A primeira etapa para obter melhor visibilidade de seus recursos é criar e atribuir políticas sobre seus recursos. A próxima etapa é aprender a criar e atribuir uma política por meio de programação. O exemplo de política realiza auditoria nas contas de armazenamento que estão abertas para todas as redes públicas usando PowerShell, CLI do Azure e solicitações HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Criar e atribuir uma definição de política com o PowerShell

1. Use o snippet JSON a seguir para criar um arquivo JSON com o nome AuditStorageAccounts.json.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Para obter mais informações sobre a criação de uma definição de política, consulte [Estrutura da definição do Azure Policy](../concepts/definition-structure.md).

1. Execute o seguinte comando para criar uma definição de política usando os arquivos AuditStorageAccounts.json.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   O comando cria uma definição de política denominada _Auditar Contas de Armazenamento Abertas para Redes Públicas_.
   Para obter mais informações sobre outros parâmetros que você pode usar, confira [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   Quando chamado sem parâmetros de local, `New-AzPolicyDefinition` é padronizado para salvar a definição de política na assinatura selecionada do contexto de sessões. Para salvar a definição para um local diferente, use os seguintes parâmetros:

   - **SubscriptionId** – salvar em uma assinatura diferente. Exige um valor de _GUID_.
   - **ManagementGroupName** – salvar em um grupo de gerenciamento. Exige um valor de _cadeia de caracteres_.

1. Depois de criar sua definição de política, você pode criar uma atribuição de política executando os seguintes comandos:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Substitua _ContosoRG_ pelo nome do grupo de recursos desejado.

   O parâmetro **scope** em `New-AzPolicyAssignment` funciona com grupos de gerenciamento, assinaturas, grupos de recursos ou com um recurso único. O parâmetro usa um caminho de recurso completo, que a propriedade **ResourceId** em `Get-AzResourceGroup` retorna. O padrão para **Escopo** para cada contêiner é como segue. Substitua `{rName}`, `{rgName}`, `{subId}` e `{mgName}` pelo nome de recurso, nome do grupo de recursos, ID da assinatura e nome do grupo de gerenciamento, respectivamente.
   `{rType}` deve ser substituído pelo **tipo de recurso**, como `Microsoft.Compute/virtualMachines` para uma VM.

   - Recurso: `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Grupo de recursos – `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Assinatura – `/subscriptions/{subId}`
   - Grupo de gerenciamento – `/providers/Microsoft.Management/managementGroups/{mgName}`

Para obter mais informações sobre como gerenciar políticas de recursos usando o módulo do PowerShell do Gerenciador de recursos, consulte [AZ. Resources](/powershell/module/az.resources/#policy).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Criar e atribuir uma definição de política usando ARMClient

Use o procedimento a seguir para criar uma definição de política.

1. Copie o snippet JSON a seguir para criar um arquivo JSON. Você chamará o arquivo na próxima etapa.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Crie a definição de política usando uma das seguintes chamadas:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>
   ```

   Substitua o {subscriptionId} anterior pela ID da sua assinatura ou {managementGroupId} pela ID do seu [grupo de gerenciamento](../../management-groups/overview.md).

   Para obter mais informações sobre a estrutura da consulta, veja [Definições do Azure Policy – Criação ou atualização](/rest/api/resources/policydefinitions/createorupdate) e [Definições de políticas – Criação ou atualização em grupo de gerenciamento](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Use o procedimento a seguir para criar uma atribuição de política e atribuir a definição de política no nível do grupo de recursos.

1. Copie o snippet JSON a seguir para criar um arquivo de atribuição de política JSON. Substitua as informações de exemplo nos símbolos &lt;&gt; pelos seus próprios valores.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Crie a atribuição de política usando a seguinte chamada:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2019-09-01" @<path to Assignment JSON file>
   ```

   Substitua as informações de exemplo nos símbolos &lt;&gt; pelos seus próprios valores.

   Para obter mais informações sobre como fazer chamadas HTTP para a API REST, consulte [Recursos da API REST do Azure](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Criar e atribuir uma definição de política com a CLI do Azure

Para criar uma definição de política, use o procedimento a seguir:

1. Copie o snippet JSON a seguir para criar um arquivo de atribuição de política JSON.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Para obter mais informações sobre a criação de uma definição de política, consulte [Estrutura da definição do Azure Policy](../concepts/definition-structure.md).

1. Execute o seguinte comando para criar uma definição de política:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   O comando cria uma definição de política denominada _Auditar Contas de Armazenamento Abertas para Redes Públicas_.
   Para saber mais sobre outros parâmetros que você pode usar, confira [Criar definição de política do az](/cli/azure/policy/definition#az_policy_definition_create).

   Quando chamado sem parâmetros de local, `az policy definition creation` é padronizado para salvar a definição de política na assinatura selecionada do contexto de sessões. Para salvar a definição para um local diferente, use os seguintes parâmetros:

   - **subscription** – Salvar em uma assinatura diferente. Requer um valor de _GUID_ para a ID da assinatura ou um valor de _cadeia de caracteres_ para o nome da assinatura.
   - **management-group** – Salvar em um grupo de gerenciamento. Exige um valor de _cadeia de caracteres_.

1. Use o comando a seguir para criar uma atribuição de política. Substitua as informações de exemplo nos símbolos &lt;&gt; pelos seus próprios valores.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   O parâmetro **scope** em `az policy assignment create` funciona com grupos de gerenciamento, assinaturas, grupos de recursos ou com um recurso único. O parâmetro usa um caminho de recurso completo. O padrão para **scope** em cada contêiner é descrito a seguir. Substitua `{rName}`, `{rgName}`, `{subId}` e `{mgName}` pelo nome de recurso, nome do grupo de recursos, ID da assinatura e nome do grupo de gerenciamento, respectivamente. `{rType}` deve ser substituído pelo **tipo de recurso**, como `Microsoft.Compute/virtualMachines` para uma VM.

   - Recurso: `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Grupo de recursos – `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Assinatura – `/subscriptions/{subID}`
   - Grupo de gerenciamento – `/providers/Microsoft.Management/managementGroups/{mgName}`

Você pode obter a ID de definição do Azure Policy usando o seguinte do comando do PowerShell:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

A ID de definição de política para a definição de política que você criou deve se assemelhar ao exemplo a seguir:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Para obter mais informações sobre como gerenciar políticas de recursos com a CLI do Azure, consulte [Políticas de Recursos da CLI do Azure](/cli/azure/policy).

## <a name="next-steps"></a>Próximas etapas

Examine os seguintes artigos para obter mais informações sobre os comandos e consultas neste artigo.

- [Recursos da API REST do Azure](/rest/api/resources/)
- [Módulos do Azure PowerShell](/powershell/module/az.resources/#policy)
- [Comandos de Política da CLI do Azure](/cli/azure/policy)
- [Referência da API REST do provedor de recursos do Azure Policy Insights](/rest/api/policy-insights)
- [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
