---
title: Como trabalhar com seus grupos de gerenciamento – Governança do Azure
description: Saiba como visualizar, manter, atualizar e excluir sua hierarquia de grupos de gerenciamento.
ms.date: 01/15/2021
ms.topic: conceptual
ms.openlocfilehash: 05e78d66c29e500842d14a6eeb563c4569ecf0bd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596490"
---
# <a name="manage-your-resources-with-management-groups"></a>Gerenciar seus recursos com grupos de gerenciamento

Se sua organização tiver muitas assinaturas, talvez seja necessária uma maneira de gerenciar com eficiência o acesso, as políticas e a conformidade dessas assinaturas. Os grupos de gerenciamento do Azure fornecem um nível de escopo acima das assinaturas. Você organiza assinaturas em contêineres chamados "grupos de gerenciamento" e aplica as condições de governança aos grupos de gerenciamento. Todas as assinaturas dentro de um grupo de gerenciamento herdam automaticamente as condições aplicadas ao grupo de gerenciamento.

Os grupos de gerenciamento fornecem gerenciamento de nível empresarial em larga escala, independentemente do tipo de assinaturas que você possa ter. Para saber mais sobre grupos de gerenciamento, consulte [organizar seus recursos com grupos de gerenciamento do Azure](./overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> Os tokens de usuário e o cache do grupo de gerenciamento do Azure Resource Manager duram 30 minutos antes que a atualização deles seja forçada. Depois da realização de qualquer ação, como mover um grupo de gerenciamento ou uma assinatura, pode levar até 30 minutos para que ela entre em vigor. Para ver as atualizações mais cedo, você precisa atualizar seu token atualizando o navegador, entrando e saindo ou solicitando um novo token.  

## <a name="change-the-name-of-a-management-group"></a>Alterar o nome de um grupo de gerenciamento

Você pode alterar o nome do grupo de gerenciamento usando o portal, o PowerShell ou a CLI do Azure.

### <a name="change-the-name-in-the-portal"></a>Alterar o nome no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento que gostaria de renomear.

1. Selecione os **detalhes**.

1. Selecione a opção **Renomear grupo** na parte superior da página.

   :::image type="content" source="./media/detail_action_small.png" alt-text="Captura de tela da barra de ação e do botão ' Renomear Grupo ' na página do grupo de gerenciamento." border="false":::

1. Quando o menu for aberto, digite o novo nome que gostaria de exibir.

   :::image type="content" source="./media/rename_context.png" alt-text="Captura de tela da janela Renomear Grupo e opções para renomear um grupo de gerenciamento." border="false":::

1. Clique em **Salvar**.

### <a name="change-the-name-in-powershell"></a>Alterar o nome no PowerShell

Para atualizar o nome de exibição, use **Update-AzManagementGroup**. Por exemplo, para alterar um nome de exibição dos grupos de gerenciamento de "Contoso IT" para "Contoso Group", execute o seguinte comando:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Alterar o nome na CLI do Azure

Para a CLI do Azure, use o comando de atualização.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Excluir um grupo de gerenciamento

Para a exclusão de um grupo de gerenciamento, os seguintes requisitos deverão ser atendidos:

1. Não existem grupos de gerenciamento filhos ou assinaturas no grupo de gerenciamento. Para mover uma assinatura ou grupo de gerenciamento para outro grupo de gerenciamento, consulte [movendo grupos de gerenciamento e assinaturas na hierarquia](#moving-management-groups-and-subscriptions).

1. Você precisa de permissões de gravação no grupo de gerenciamento ("Proprietário", ou "Colaborador" ou "Colaborador do Grupo de Gerenciamento"). Para ver quais permissões você tem, selecione o grupo de gerenciamento e, em seguida, selecione **IAM**. Para saber mais sobre as funções do Azure, consulte  
   [Controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md).

### <a name="delete-in-the-portal"></a>Excluir no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento que gostaria de excluir.

1. Selecione os **detalhes**.

1. Selecione **Excluir**

   :::image type="content" source="./media/delete.png" alt-text="Captura de tela da página do grupo de gerenciamento com o botão ' excluir ' realçado." border="false":::

   > [!TIP]
   > Se o ícone estiver desativado, passar o seletor de mouse sobre o ícone mostrará o motivo.

1. Há uma janela que abre confirmando que deseja excluir o grupo de gerenciamento.

   :::image type="content" source="./media/delete_confirm.png" alt-text="Captura de tela da caixa de diálogo de confirmação ' excluir grupo ' para excluir um grupo de gerenciamento." border="false":::

1. Selecione **Sim** na barra superior.

### <a name="delete-in-powershell"></a>Excluir no PowerShell

Use o comando **Remove-AzManagementGroup** dentro do PowerShell para excluir grupos de gerenciamento.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Exclusão na CLI do Azure

Com a CLI do Azure, use o comando az account management-group delete.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Exibir grupos de gerenciamento

Você pode exibir qualquer grupo de gerenciamento no qual tenha uma função direta ou herdada do Azure.  

### <a name="view-in-the-portal"></a>Exibir no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. A página de hierarquia do grupo de gerenciamento será carregada. É nessa página que você pode explorar todos os grupos de gerenciamento e assinaturas aos quais você tem acesso. Selecionar o nome do grupo leva você para um nível inferior na hierarquia. A navegação funciona da mesma forma que um explorador de arquivos.

1. Para ver os detalhes do grupo de gerenciamento, selecione o link **(detalhes)** ao lado do título do grupo de gerenciamento. Se esse link não estiver disponível, você não tem permissões para exibir esse grupo de gerenciamento.

   :::image type="content" source="./media/main.png" alt-text="Captura de tela da página Grupos de gerenciamento mostrando assinaturas e grupos de gerenciamento filho." border="false":::

### <a name="view-in-powershell"></a>Exibir no PowerShell

Você pode usar o comando Get-AzManagementGroup para recuperar todos os grupos. Confira os módulos [AZ.Resources](/powershell/module/az.resources/Get-AzManagementGroup) para obter a lista completa de comandos GET do PowerShell do grupo de gerenciamento.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Para obter informações de um único grupo de gerenciamento, use o parâmetro -GroupName

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Para retornar um grupo de gerenciamento específico e todos os níveis da hierarquia abaixo dele, use os parâmetros **-Expand** e **-Recurse**.  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Exibir na CLI do Azure

Você pode usar o comando list para recuperar todos os grupos.  

```azurecli-interactive
az account management-group list
```

Para obter informações de um único grupo de gerenciamento, use o comando show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Para retornar um grupo de gerenciamento específico e todos os níveis da hierarquia abaixo dele, use os parâmetros **-Expand** e **-Recurse**.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Como mover grupos de gerenciamento e assinaturas   

Um motivo para criar um grupo de gerenciamento é agrupar assinaturas. Somente grupos de gerenciamento e assinaturas podem ser tornados filhos de outro grupo de gerenciamento. Uma assinatura movida para um grupo de gerenciamento herda todos os acessos e políticas de usuário do grupo de gerenciamento pai

Ao mover um grupo de gerenciamento ou uma assinatura para ser um filho de outro grupo de gerenciamento, três regras precisam ser avaliadas como true.

Se você estiver executando a ação de mover, precisará de: 

- Permissões de gravação de Atribuição de função e gravação de grupo de gerenciamento na no grupo de gerenciamento ou assinatura filho.
  - **Proprietário** de exemplo de função interna
- Acesso de gravação do grupo de gerenciamento no grupo de gerenciamento pai alvo.
  - Exemplo de função interna: **Proprietário**, **Colaborador**, **Colaborador do Grupo de Gerenciamento**
- Acesso de gravação do grupo de gerenciamento no grupo de gerenciamento pai existente.
  - Exemplo de função interna: **Proprietário**, **Colaborador**, **Colaborador do Grupo de Gerenciamento**

**Exceção**: se o grupo de gerenciamento pai existente ou alvo for o grupo de gerenciamento raiz, os requisitos de permissões não se aplicarão. Como o grupo de gerenciamento raiz é o ponto de aterrissagem padrão para todos os novos grupos de gerenciamento e assinaturas, não é preciso ter permissões para mover um item.

Se a função de proprietário na assinatura for herdada do grupo de gerenciamento atual, seus destinos de movimentação serão limitados. Você só pode mover a assinatura para outro grupo de gerenciamento no qual você tem a função de Proprietário. Não é possível mover a assinatura para um grupo de gerenciamento no qual você é apenas um colaborador, pois você perderia a propriedade da assinatura. Se você estiver diretamente atribuído à função de proprietário da assinatura, poderá movê-la para qualquer grupo de gerenciamento no qual você seja um colaborador.

Para ver quais permissões você tem no portal do Azure, selecione o grupo de gerenciamento e, em seguida, selecione **IAM**. Para saber mais sobre as funções do Azure, consulte [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md).

## <a name="move-subscriptions"></a>Mover assinaturas 

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Adicionar uma assinatura existente a um grupo de gerenciamento no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento o qual planeja que seja o pai.

1. Na parte superior da página, selecione **Adicionar assinatura**.

1. Selecione a assinatura na lista com a ID correta.

   :::image type="content" source="./media/add_context_sub.png" alt-text="Captura de tela das opções ' Adicionar assinatura ' para selecionar uma assinatura existente a ser adicionada a um grupo de gerenciamento." border="false":::

1. Selecione "Salvar".

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Remover uma assinatura de um grupo de gerenciamento no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento que você está planejando que é o pai atual.  

1. Selecione a elipse no final da linha da assinatura na lista que você deseja mover.

   :::image type="content" source="./media/move_small.png" alt-text="Captura de tela do menu alternativo de uma assinatura para selecionar a opção ' mover '." border="false":::

1. Selecione **Mover**.

1. No menu aberto, selecione o **Grupo de gerenciamento pai**.

   :::image type="content" source="./media/move_small_context.png" alt-text="Captura de tela da janela ' mover ' e opções para mover uma assinatura para um grupo de gerenciamento diferente." border="false":::

1. Clique em **Salvar**.

### <a name="move-subscriptions-in-powershell"></a>Mover assinaturas no PowerShell

Para mover uma assinatura no PowerShell, use o comando New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Para remover o vínculo entre a assinatura e o grupo de gerenciamento, use o comando Remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Mover assinaturas na CLI do Azure

Para mover uma assinatura na CLI, use o comando add.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Para remover a assinatura do grupo de gerenciamento, use o comando subscription remove.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-arm-template"></a>Mover assinaturas no modelo ARM

Para mover uma assinatura em um modelo de Azure Resource Manager (modelo ARM), use o modelo a seguir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMgId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the management group that you want to move the subscription to."
            }
        },
        "subscriptionId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the existing subscription to move."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "type": "Microsoft.Management/managementGroups/subscriptions",
            "apiVersion": "2020-05-01",
            "name": "[concat(parameters('targetMgId'), '/', parameters('subscriptionId'))]",
            "properties": {
            }
        }
    ],
    "outputs": {}
}
```

## <a name="move-management-groups"></a>Mover grupos de gerenciamento 

### <a name="move-management-groups-in-the-portal"></a>Mover grupos de gerenciamento no portal

1. Faça logon no [Portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Grupos de gerenciamento**.

1. Selecione o grupo de gerenciamento o qual planeja que seja o pai.

1. Na parte superior da página, selecione **Adicionar grupo de gerenciamento**.

1. No menu que será aberto, selecione se deseja usar um grupo de gerenciamento novo ou existente.

   - A seleção de um novo criará um novo grupo de gerenciamento.
   - A seleção de um existente apresentará uma lista suspensa de todos os grupos de gerenciamento que você pode mover para esse grupo de gerenciamento.  

   :::image type="content" source="./media/add_context_MG.png" alt-text="Captura de tela das opções &quot;Adicionar grupo de gerenciamento&quot; para criar um grupo de gerenciamento." border="false":::

1. Clique em **Salvar**.

### <a name="move-management-groups-in-powershell"></a>Mover grupos de gerenciamento no PowerShell

Use o comando Update-AzManagementGroup no PowerShell para mover um grupo de gerenciamento em um grupo diferente.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Mover grupos de gerenciamento na CLI do Azure

Use o comando update para mover um grupo de gerenciamento com a CLI do Azure.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Auditar grupos de gerenciamento usando logs de atividades

Os grupos de gerenciamento são compatíveis com o [Log de atividades do Azure](../../azure-monitor/essentials/platform-logs-overview.md). Você pode pesquisar todos os eventos que ocorrem para um grupo de gerenciamento no mesmo local central que os outros recursos do Azure. Por exemplo, você pode ver todas as alterações de atribuições de função ou de política feitas em um grupo de gerenciamento específico.

:::image type="content" source="./media/al-mg.png" alt-text="Captura de tela de Logs de Atividades e operações relacionados ao grupo de gerenciamento selecionado." border="false":::

Ao analisar a consulta em grupos de gerenciamento fora do portal do Azure, o escopo de destino dos grupos de gerenciamento é semelhante a **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="referencing-management-groups-from-other-resource-providers"></a>Fazer referência a grupos de gerenciamento de outros provedores de recursos

Ao fazer referência a grupos de gerenciamento de ações de outros provedores de recursos, use o caminho a seguir como escopo. Esse caminho é utilizado ao usar PowerShell, CLI do Azure e APIs REST.  

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

Um exemplo de como usar esse caminho é ao atribuir uma nova função a um grupo de gerenciamento no PowerShell:

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

O mesmo caminho de escopo é usado ao recuperar uma definição de política em um grupo de gerenciamento.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2019-09-01
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre grupos de gerenciamento, consulte:

- [Criar grupos de gerenciamento para organizar recursos do Azure](./create-management-group-portal.md)
- [Como alterar, excluir ou gerenciar seus grupos de gerenciamento](./manage.md)
- [Analisar grupos de gerenciamento no Módulo de Recursos do Azure PowerShell](/powershell/module/az.resources#resources)
- [Revisar grupos de gerenciamento na API REST](/rest/api/resources/managementgroups)
- [Revisar grupos de gerenciamento na CLI do Azure](/cli/azure/account/management-group)