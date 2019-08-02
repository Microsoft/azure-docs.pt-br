---
title: Conceder acesso para criar assinaturas do Azure Enterprise | Microsoft Docs
description: Aprenda a conferir a um usuário ou entidade de serviço a capacidade de criar programaticamente assinaturas do Azure Enterprise.
services: azure-resource-manager
author: jureid
manager: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: a7ed7dffd27b51c1314c4293820dc33be4d7e8e0
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206636"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Conceder acesso para criar assinaturas do Azure Enterprise (versão prévia)

Como cliente do Azure no [EA (Enterprise Agreement)](https://azure.microsoft.com/pricing/enterprise-agreement/), você pode conceder a outro usuário ou entidade de serviço permissão para criar assinaturas cobradas de sua conta. Neste artigo, você aprenderá a usar o [RBAC (Controle de Acesso Baseado em Função)](../active-directory/role-based-access-control-configure.md) para compartilhar a capacidade de criar assinaturas, e aprenderá a fazer auditoria das criações de assinatura. É necessário ter a função Proprietário na conta que deseja compartilhar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Conceder acesso

Para [criar assinaturas de uma conta de registro](programmatically-create-subscription.md), os usuários devem ter o [função de proprietário de RBAC](../role-based-access-control/built-in-roles.md#owner) nessa conta. Você pode conceder um usuário ou um grupo de usuários a função de proprietário de RBAC em uma conta de registro seguindo estas etapas:

1. Obter a ID de objeto da conta do registro que você deseja conceder acesso a

    Para conceder a outras pessoas a função de proprietário de RBAC em uma conta de registro, você deve ser o proprietário da conta ou um proprietário de RBAC da conta.

    # <a name="resttabrest"></a>[REST](#tab/rest)

    Solicitação para listar todas as contas de registro que você tem acesso a:

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

    Use o `principalName` propriedade para identificar a conta que você deseja conceder acesso de proprietário de RBAC a. Copie o `name` dessa conta. Por exemplo, se você quisesse conceder acesso de proprietário de RBAC para o SignUpEngineering@contoso.com conta de registro, você poderia copiar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Esta é a ID de objeto da conta do registro. Cole esse valor em algum lugar para que você pode usá-lo na próxima etapa como `enrollmentAccountObjectId`.

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    Use o cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de registro às quais você tem acesso. Selecione **Experimente** para abrir [Azure Cloud Shell](https://shell.azure.com/). Para colar o código, clique com botão direito do windows shell e selecione **cole**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    O Azure responde com uma lista de contas de registro que você tem acesso a:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Use o `principalName` propriedade para identificar a conta que você deseja conceder acesso de proprietário de RBAC a. Copie o `ObjectId` dessa conta. Por exemplo, se você quisesse conceder acesso de proprietário de RBAC para o SignUpEngineering@contoso.com conta de registro, você poderia copiar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Cole a ID de objeto em algum lugar para que você pode usá-lo na próxima etapa, como o `enrollmentAccountObjectId`.

    # <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

    Use o comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) para listar todas as contas de registro às quais você tem acesso. Selecione **Experimente** para abrir [Azure Cloud Shell](https://shell.azure.com/). Para colar o código, clique com botão direito do windows shell e selecione **cole**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    O Azure responde com uma lista de contas de registro que você tem acesso a:

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

    Use o `principalName` propriedade para identificar a conta que você deseja conceder acesso de proprietário de RBAC a. Copie o `name` dessa conta. Por exemplo, se você quisesse conceder acesso de proprietário de RBAC para o SignUpEngineering@contoso.com conta de registro, você poderia copiar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Esta é a ID de objeto da conta do registro. Cole esse valor em algum lugar para que você pode usá-lo na próxima etapa como `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Obter ID de objeto do usuário ou grupo que deseja fornecer à função de proprietário de RBAC para

    1. No portal do Azure, pesquise **Azure Active Directory**.
    1. Se você deseja conceder acesso a um usuário, clique em **usuários** no menu à esquerda. Se você deseja conceder acesso a um grupo, clique em **grupos**.
    1. Selecione o usuário ou grupo que você deseja fornecer à função de proprietário de RBAC para.
    1. Se você tiver selecionado um usuário, você encontrará a ID de objeto na página de perfil. Se você selecionou um grupo, a ID de objeto será na página de visão geral. Cópia de **ObjectID** clicando no ícone à direita da caixa de texto. Cole esse em algum lugar para que você pode usá-lo na próxima etapa como `userObjectId`.

1. Conceda ao usuário ou grupo a função de proprietário de RBAC na conta de registro

    Usando os valores coletados nas duas primeiras etapas, conceda ao usuário ou grupo a função de proprietário de RBAC na conta de registro.

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    Execute o seguinte comando, substituindo ```<enrollmentAccountObjectId>``` com o `name` copiadas na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` com a ID de objeto que você copiou na segunda etapa.

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

    # <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

    Execute o seguinte [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) comando, substituindo ```<enrollmentAccountObjectId>``` com o `ObjectId` coletados na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` com o objeto ID coletados na segunda etapa.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[CLI do Azure](#tab/azure-cli-2)

    Execute o seguinte [criar atribuição de função az](../active-directory/role-based-access-control-manage-access-azure-cli.md) comando, substituindo ```<enrollmentAccountObjectId>``` com o `name` copiadas na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Substitua ```<userObjectId>``` com o objeto ID coletados na segunda etapa.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Depois que um usuário se torna um proprietário de RBAC para sua conta do registro, eles podem [criar programaticamente assinaturas](programmatically-create-subscription.md) sob ele. Uma assinatura criada por um usuário delegado ainda tem o proprietário da conta original como administrador de serviço, mas ele também tem o usuário delegado como um proprietário de RBAC por padrão.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Audite quem criou assinaturas usando logs de atividade

Para controlar as assinaturas criadas por meio desta API, use o [API de Locatário do Log de Atividade](/rest/api/monitor/tenantactivitylogs). No momento não é possível usar o PowerShell, CLI ou o portal do Azure para controlar a criação da assinatura.

1. Como um administrador de locatários do locatário do AD do Azure, [eleve o acesso](../active-directory/role-based-access-control-tenant-admin-access.md), em seguida, atribua uma função de leitor para o usuário de auditoria sobre o escopo `/providers/microsoft.insights/eventtypes/management`.
1. Como o usuário de auditoria, chame o [API de Locatário do Log de Atividade](/rest/api/monitor/tenantactivitylogs) para ver as atividades de criação de assinatura. Exemplo:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Para chamar convenientemente esta API da linha de comando, tente [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Próximas etapas

* Agora que o usuário ou entidade de serviço tem permissão para criar uma assinatura, use essa identidade para [criar programaticamente as assinaturas do Azure Enterprise](programmatically-create-subscription.md).
* Para obter um exemplo sobre como criar assinaturas usando .NET, consulte [código de exemplo no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Para saber mais sobre o Azure Resource Manager, confira [Visão geral do Azure Resource Manager](resource-group-overview.md).
* Para saber mais sobre como gerenciar grandes números de assinaturas usando grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](management-groups-overview.md)
* Para ver uma abrangente diretriz de práticas recomendadas para grandes organizações no controle de assinatura, consulte [scaffold enterprise do Azure - governança prescritivas de assinatura](/azure/architecture/cloud-adoption-guide/subscription-governance)
