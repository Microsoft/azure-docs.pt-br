---
title: Permitir acesso para criar assinaturas do Azure Enterprise
description: Aprenda a conferir a um usuário ou entidade de serviço a capacidade de criar programaticamente assinaturas do Azure Enterprise.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.reviewer: andalmia
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: banders
ms.openlocfilehash: 039e728f6518d21ddfb9c7c359a6cf2ec743f232
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185097"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Conceder acesso para criar assinaturas do Azure Enterprise (versão prévia)

Como cliente do Azure no [EA (Enterprise Agreement)](https://azure.microsoft.com/pricing/enterprise-agreement/), você pode conceder a outro usuário ou entidade de serviço permissão para criar assinaturas cobradas de sua conta. Neste artigo, você aprenderá a usar o [RBAC (Controle de Acesso Baseado em Função) do Azure](../../role-based-access-control/role-assignments-portal.md) para compartilhar a capacidade de criar assinaturas e aprenderá a fazer auditoria das criações de assinatura. É necessário ter a função Proprietário na conta que deseja compartilhar.

> [!NOTE]
> Essa API funciona apenas com as [APIs de versão prévia para a criação de assinatura](programmatically-create-subscription-preview.md). Se você quiser usar a [versão de GA](programmatically-create-subscription-enterprise-agreement.md), use a versão da API mais recente em [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Se estiver fazendo a migração para usar as APIs mais recentes, você precisará conceder ao proprietário as permissões novamente usando [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Sua configuração anterior que usa as APIs a seguir não é convertida automaticamente para uso com APIs mais recentes.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Conceder acesso

Para [criar assinaturas em uma conta de registro](programmatically-create-subscription-enterprise-agreement.md), os usuários precisam ter a [função de Proprietário](../../role-based-access-control/built-in-roles.md#owner) do Azure RBAC nessa conta. Você pode conceder a um usuário ou grupo de usuários a função de Proprietário do Azure RBAC em uma conta de registro seguindo estas etapas:

1. Obter a ID de objeto da conta de registro à qual você deseja permitir acesso

    Para conceder a outros usuários a função de Proprietário do Azure RBAC em uma conta de registro, você precisa ser o Proprietário da Conta ou um Proprietário do Azure RBAC da conta.

    # <a name="rest"></a>[REST](#tab/rest)

    Solicitação para listar todas as contas de registro às quais você tem acesso:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure responde com uma lista de todas as contas de registro a que você tem acesso:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    Use a propriedade `principalName` para identificar a conta à qual você deseja permitir acesso de Proprietário do Azure RBAC. Copie o `name` dessa conta. Por exemplo, se você quiser permitir acesso de Proprietário do Azure RBAC à conta de registro SignUpEngineering@contoso.com, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ele é a ID de objeto da conta de registro. Cole esse valor em algum lugar, de modo que possa usá-lo na próxima etapa como `enrollmentAccountObjectId`.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Use o cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de registro às quais você tem acesso. Selecione **Experimentar** para abrir o [Azure Cloud Shell](https://shell.azure.com/). Para colar o código, clique com o botão direito do mouse na janela do shell e selecione **Colar**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    O Azure responde com uma lista das contas de registro às quais você tem acesso:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Use a propriedade `principalName` para identificar a conta à qual você deseja permitir acesso de Proprietário do Azure RBAC. Copie o `ObjectId` dessa conta. Por exemplo, se você quiser permitir acesso de Proprietário do Azure RBAC à conta de registro SignUpEngineering@contoso.com, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Cole essa ID do objeto em algum lugar, de modo que possa usá-la na próxima etapa como `enrollmentAccountObjectId`.

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    Use o comando [az billing enrollment-account list](/cli/azure/billing) para listar todas as contas de registro às quais você tem acesso. Selecione **Experimentar** para abrir o [Azure Cloud Shell](https://shell.azure.com/). Para colar o código, clique com o botão direito do mouse na janela do shell e selecione **Colar**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    O Azure responde com uma lista das contas de registro às quais você tem acesso:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    Use a propriedade `principalName` para identificar a conta à qual você deseja permitir acesso de Proprietário do Azure RBAC. Copie o `name` dessa conta. Por exemplo, se você quiser permitir acesso de Proprietário do Azure RBAC à conta de registro SignUpEngineering@contoso.com, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Ele é a ID de objeto da conta de registro. Cole esse valor em algum lugar, de modo que possa usá-lo na próxima etapa como `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Obtenha a ID de objeto do usuário ou do grupo ao qual você deseja conceder a função de Proprietário do Azure RBAC

    1. No portal do Azure, pesquise em **Azure Active Directory**.
    1. Se você quiser permitir acesso a um usuário, selecione **Usuários** no menu à esquerda. Para conceder acesso a um grupo, selecione **Grupos**.
    1. Selecione o Usuário ou o Grupo ao qual você deseja conceder a função de Proprietário do Azure RBAC.
    1. Se você selecionar um Usuário, a ID de objeto estará na página Perfil. Se você selecionar um Grupo, a ID do objeto estará na página Visão Geral. Copie o **ObjectID** selecionando o ícone à direita da caixa de texto. Cole-o em algum lugar, de modo que possa usá-lo na próxima etapa como `userObjectId`.

1. Conceder ao usuário ou ao grupo a função de Proprietário do Azure RBAC na conta de registro

    Usando os valores que você coletou nas duas primeiras etapas, conceda ao usuário ou ao grupo a função de Proprietário do Azure RBAC na conta de registro.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Execute o comando a seguir, substituindo ```<enrollmentAccountObjectId>``` pelo `name` copiado na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` pela ID de objeto que você copiou da segunda etapa.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Quando a função de Proprietário é atribuída com êxito no escopo da conta do registro, o Azure responde com informações sobre a atribuição de função:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    Execute o comando [New-AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md) a seguir, substituindo ```<enrollmentAccountObjectId>``` pelo `ObjectId` coletado na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` pela ID de objeto coletada na segunda etapa.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli-2)

    Execute o comando [az role assignment create](../../role-based-access-control/role-assignments-cli.md) a seguir, substituindo ```<enrollmentAccountObjectId>``` pelo `name` copiado na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` pela ID de objeto coletada na segunda etapa.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Depois que um usuário se tornar um Proprietário do Azure RBAC da sua conta de registro, ele poderá [criar programaticamente assinaturas](programmatically-create-subscription-enterprise-agreement.md) nela. Uma assinatura criada por um usuário delegado ainda tem o Proprietário da Conta original como Administrador de Serviço, mas ele também tem o usuário delegado como um Proprietário do Azure RBAC por padrão.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Audite quem criou assinaturas usando logs de atividade

Para controlar as assinaturas criadas por meio desta API, use o [API de Locatário do Log de Atividade](/rest/api/monitor/tenantactivitylogs). No momento não é possível usar o PowerShell, CLI ou o portal do Azure para controlar a criação da assinatura.

1. Como um administrador de locatários do locatário do AD do Azure, [eleve o acesso](../../role-based-access-control/elevate-access-global-admin.md), em seguida, atribua uma função de leitor para o usuário de auditoria sobre o escopo `/providers/microsoft.insights/eventtypes/management`. Esse acesso está disponível na função [Leitor](../../role-based-access-control/built-in-roles.md#reader), na função [Colaborador de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-contributor) ou em uma [função personalizada](../../role-based-access-control/custom-roles.md).
1. Como o usuário de auditoria, chame o [API de Locatário do Log de Atividade](/rest/api/monitor/tenantactivitylogs) para ver as atividades de criação de assinatura. Exemplo:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Para chamar convenientemente esta API da linha de comando, tente [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Próximas etapas

* Agora que o usuário ou entidade de serviço tem permissão para criar uma assinatura, use essa identidade para [criar programaticamente as assinaturas do Azure Enterprise](programmatically-create-subscription-enterprise-agreement.md).
* Para obter um exemplo sobre como criar assinaturas usando .NET, consulte [código de exemplo no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Para saber mais sobre o Azure Resource Manager, confira [Visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).
* Para saber mais sobre como gerenciar grandes números de assinaturas usando grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](../../governance/management-groups/overview.md)
* Para ver uma abrangente diretriz de práticas recomendadas para grandes organizações no controle de assinatura, consulte [scaffold enterprise do Azure - governança prescritivas de assinatura](/azure/architecture/cloud-adoption-guide/subscription-governance)