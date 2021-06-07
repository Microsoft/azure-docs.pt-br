---
title: Corrigir recursos sem conformidade
description: Este guia o orienta pela correção de recursos que não estão em conformidade com as políticas no Azure Policy.
ms.date: 02/17/2021
ms.topic: how-to
ms.openlocfilehash: e567bedf48393a36215c1ac3f3d11f467ae7badd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742221"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Corrigir recursos que não estão em conformidade com o Azure Policy

Recursos que não estão em conformidade com uma política de **deployIfNotExists** ou **modify** podem ser colocados em um estado de conformidade por meio de **Correção**. A correção é realizada instruindo Azure Policy a executar o efeito de **deployIfNotExists** ou as **operações de modificação** da política atribuída em seus recursos e assinaturas existentes, seja essa atribuição para um grupo de gerenciamento, uma assinatura, um grupo de recursos ou um recurso individual. Este artigo mostra as etapas necessárias para entender e realizar a correção com o Azure Policy.

## <a name="how-remediation-security-works"></a>Como funciona a correção de segurança

Quando o Azure Policy executa o modelo na definição de política **deployIfNotExists**, ele faz isso usando uma [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md).
O Azure Policy cria uma identidade gerenciada para cada atribuição, mas precisa ter detalhes sobre quais funções devem receber a identidade gerenciada. Se a identidade gerenciada estiver sem funções, um erro será exibido durante a atribuição da política ou de uma iniciativa. Quando o portal é usado, o Azure Policy concederá automaticamente a identidade gerenciada às funções listadas quando a atribuição for iniciada. Ao usar o SDK, as funções devem ser concedidas manualmente à identidade gerenciada. O _local_ da identidade gerenciada não afeta sua operação com Azure Policy.

:::image type="content" source="../media/remediate-resources/missing-role.png" alt-text="Captura de tela de uma política deployIfNotExists que não tem uma permissão definida na identidade gerenciada." border="false":::

> [!IMPORTANT]
> Nos cenários a seguir, a identidade gerenciada da atribuição deve ser [concedida ao acesso manualmente](#manually-configure-the-managed-identity) ou a implantação de correção falhará:
>
> - Se a atribuição for criada por meio do SDK
> - Se um recurso modificado por **deployIfNotExists** ou **Modify** estiver fora do escopo da atribuição de política
> - Se o modelo acessa Propriedades em recursos fora do escopo da atribuição de política

## <a name="configure-policy-definition"></a>Configurar a definição de política

A primeira etapa é definir as funções de que **deployIfNotExists** e **modify** precisam na definição de política para implantar com êxito o conteúdo do modelo incluído. Na propriedade **detalhes**, adicione uma propriedade **roleDefinitionIds**. Essa propriedade é uma matriz de cadeias de caracteres que correspondem a funções no ambiente. Para ver um exemplo completo, veja [exemplo de deployIfNotExists](../concepts/effects.md#deployifnotexists-example) ou [exemplos de modify](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

A propriedade **roleDefinitionIds** usa todo o identificador de recurso e não adota o **roleName** abreviado da função. Para obter a ID para a função 'Colaborador' em seu ambiente, use o seguinte código:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Configurar manualmente a identidade gerenciada

Quando criar uma atribuição usando o portal, o Azure Policy gera a identidade gerenciada e a concede às funções definidas em **roleDefinitionIds**. Nas seguintes condições, as etapas usadas para criar a identidade gerenciada e atribuir permissões a ela precisam ser feitas manualmente:

- Ao usar o SDK (como o Azure PowerShell)
- Quando um recurso fora do escopo de atribuição é modificado pelo modelo
- Quando um recurso fora do escopo de atribuição é lido pelo modelo

### <a name="create-managed-identity-with-powershell"></a>Criar identidade gerenciada com o PowerShell

Para criar uma identidade gerenciada durante a atribuição da política, o **Local** deve ser definido e **AssignIdentity** deve ser usada. O exemplo a seguir obtém a definição da política interna **Implantar Transparent Data Encryption do Banco de Dados SQL**, define o grupo de recursos de destino e, em seguida, cria a atribuição.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

A variável `$assignment` agora contém a ID da entidade de segurança referente à identidade gerenciada, juntamente com os valores padrão retornados durante a criação de uma atribuição de política. Ela pode ser acessada por meio de `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Conceder funções definidas com o PowerShell

A nova identidade gerenciada deve concluir a replicação por meio do Azure Active Directory antes que possam ser concedidas a ela as funções necessárias. Após a conclusão da replicação, o exemplo a seguir itera a definição de política em `$policyDef` para **roleDefinitionIds** e usa [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para conceder as funções à nova identidade gerenciada.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Conceder funções definidas por meio do portal

Há duas maneiras de conceder à identidade gerenciada de uma atribuição as funções definidas usando o portal, usando o **controle de acesso (iam)** ou editando a atribuição de política ou iniciativa e selecionando **salvar**.

Para adicionar uma função à identidade gerenciada da atribuição, siga estas etapas:

1. Inicie o serviço do Azure Policy no portal do Azure selecionando **Todos os serviços** e, em seguida, pesquisando e selecionando **Política**.

1. Selecione **Atribuições** no lado esquerdo da página de Política do Azure.

1. Localize a atribuição que tem uma identidade gerenciada e selecione o nome.

1. Localize a propriedade **ID de Atribuição** na página de edição. A ID de atribuição será algo como:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   O nome da identidade gerenciada é a última parte da ID do recurso de atribuição, que é `2802056bfc094dfb95d4d7a5` neste exemplo. Copie essa parte da ID do recurso da atribuição.

1. Navegue até o recurso ou os recursos do contêiner pai (grupo de recursos, assinatura, grupo de gerenciamento) que precisa na definição de função adicionada manualmente.

1. Selecione o link **controle de acesso (iam)** na página recursos e, em seguida, selecione **+ Adicionar atribuição de função** na parte superior da página controle de acesso.

1. Selecione a função apropriada que corresponde a **roleDefinitionIds** da definição de política.
   Deixe **Atribuir acesso** definido como o padrão de 'Usuário, grupo ou aplicativo do Azure AD'. Na caixa **Selecionar**, cole ou digite a parte da ID do recurso de atribuição localizada anteriormente. Quando a pesquisa for concluída, selecione o objeto com o mesmo nome para selecionar ID e selecione **salvar**.

## <a name="create-a-remediation-task"></a>Criar uma tarefa de correção

### <a name="create-a-remediation-task-through-portal"></a>Criação de uma tarefa de correção através do portal

Durante a avaliação, a atribuição de política com efeitos **deployIfNotExists** ou **Modify** determina se há recursos ou assinaturas sem conformidade. Quando são encontrados recursos não compatíveis ou assinaturas, os detalhes são fornecidos na página **correção** . Junto com a lista de políticas que têm recursos ou assinaturas sem conformidade é a opção de disparar uma **tarefa de correção**.
Essa opção é a que cria uma implantação usando o modelo **deployIfNotExists** ou as operações **modify**.

Para criar uma **tarefas de correção**, siga estas etapas:

1. Inicie o serviço do Azure Policy no portal do Azure selecionando **Todos os serviços** e, em seguida, pesquisando e selecionando **Política**.

   :::image type="content" source="../media/remediate-resources/search-policy.png" alt-text="Captura de tela da pesquisa de Política em Todos os Serviços." border="false":::

1. Selecione **Correção** no lado esquerdo da página do Azure Policy.

   :::image type="content" source="../media/remediate-resources/select-remediation.png" alt-text="Captura de tela do nó de correção na página de política." border="false":::

1. Todas as atribuições de política **deployIfNotExists** e **modify** com recursos sem conformidade estão incluídas na tabela de dados e na guia **Políticas a corrigir**. Selecione em uma política com recursos que não estão em conformidade. A página **Nova tarefa de correção** é aberta.

   > [!NOTE]
   > Uma maneira alternativa de abrir a página **tarefa de correção** é localizar e selecionar a política na página **conformidade** e, em seguida, selecionar o botão **criar tarefa de correção** .

1. Na página **Nova tarefa de correção**, filtre os recursos a serem corrigidos usando as reticências de **Escopo** para escolher os recursos filho nos quais a política está atribuída (incluindo até os objetos do recurso individuais). Além disso, use a lista suspensa **Locais** para filtrar ainda mais os recursos. Somente os recursos listados na tabela serão corrigidos.

   :::image type="content" source="../media/remediate-resources/select-resources.png" alt-text="Captura de tela do nó de correção e a grade de recursos a serem corrigidos." border="false":::

1. Inicie a tarefa de correção depois que os recursos tiverem sido filtrados selecionando **corrigir**. A página de conformidade de política abre na guia **Tarefas de correção** para mostrar o estado do progresso das tarefas. As implantações criadas pela tarefa de correção são iniciadas imediatamente.

   :::image type="content" source="../media/remediate-resources/task-progress.png" alt-text="Captura de tela da guia tarefas de correção e progresso das tarefas de correção existentes." border="false":::

1. Selecione na **tarefa de correção** da página de conformidade da política para obter detalhes sobre o progresso. A filtragem usada para a tarefa é mostrada junto com uma lista dos recursos que estão sendo corrigidos.

1. Na página **tarefa de correção** , clique com o botão direito do mouse em um recurso para exibir a implantação da tarefa de correção ou o recurso. No final da linha, selecione **eventos relacionados** para ver detalhes como uma mensagem de erro.

   :::image type="content" source="../media/remediate-resources/resource-task-context-menu.png" alt-text="Captura de tela do menu de contexto de um recurso na guia corrigir tarefa." border="false":::

Os recursos implantados por meio de uma **tarefa de correção** são adicionados à guia **Recursos Implantados** na página de conformidade com a política.

### <a name="create-a-remediation-task-through-azure-cli"></a>Criação de uma tarefa de correção através da CLI do Azure

Para criar uma **tarefa de correção** com a CLI do Azure, use os comandos `az policy remediation`. Substitua `{subscriptionId}` pela sua ID da assinatura e `{myAssignmentId}` pela ID da atribuição de política **deployIfNotExists** ou **modify**.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Para outros comandos e exemplos de correção, veja os comandos [az policy remediation](/cli/azure/policy/remediation).

### <a name="create-a-remediation-task-through-azure-powershell"></a>Criação de uma tarefa de correção através do Azure PowerShell

Para criar uma **tarefa de correção** com o Azure PowerShell, use os comandos `Start-AzPolicyRemediation`. Substitua `{subscriptionId}` pela sua ID da assinatura e `{myAssignmentId}` pela ID da atribuição de política **deployIfNotExists** ou **modify**.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Para obter outros cmdlets e exemplos de correção, veja o módulo [AZ.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights).

### <a name="create-a-remediation-task-during-policy-assignment-in-the-azure-portal"></a>Criação de uma tarefa de correção durante a atribuição de política no portal do Azure

Uma maneira simplificada de criar uma tarefa de correção é fazer isso no portal do Azure durante a atribuição de política. Se a definição de política a ser atribuída for um efeito de **deployIfNotExists** ou de **modificação** , o assistente na guia **correção** oferecerá uma opção _criar uma tarefa de correção_ . Se essa opção for selecionada, uma tarefa de correção será criada ao mesmo tempo que a atribuição de política.

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [Exemplos do Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas de forma programática](programmatically-create.md).
- Saiba como [obter dados de conformidade](get-compliance-data.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
