---
title: Monitorar alterações de delegação no seu locatário de gerenciamento
description: Saiba como monitorar a atividade de delegação de locatários do cliente para seu locatário de gerenciamento.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: 9fdf47df4ac37fec44cf53b565b7fe1411540793
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089403"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Monitorar alterações de delegação no seu locatário de gerenciamento

Como um provedor de serviços, talvez você queira estar atento quando os grupos de recursos ou assinaturas de clientes são delegados para seu locatário por meio [do Azure Lighthouse](../overview.md)ou quando os recursos previamente delegados são removidos.

No locatário de gerenciamento, o [log de atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md) rastreia a atividade de delegação no nível do locatário. Essa atividade registrada inclui todas as delegações adicionadas ou removidas de todos os locatários do cliente.

Este tópico explica as permissões necessárias para monitorar a atividade de delegação para seu locatário (em todos os seus clientes). Ele também inclui um script de exemplo que mostra um método para consultar e relatar esses dados.

> [!IMPORTANT]
> Todas essas etapas devem ser executadas em seu locatário de gerenciamento, em vez de em qualquer locatário do cliente.
>
> Embora possamos nos referimos a provedores de serviços e clientes neste tópico, as [empresas que gerenciam vários locatários](../concepts/enterprise.md) podem usar os mesmos processos.

## <a name="enable-access-to-tenant-level-data"></a>Habilitar o acesso a dados no nível do locatário

Para acessar dados do log de atividades no nível do locatário, uma conta deve ser atribuída à função interna do [leitor de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-reader) do Azure no escopo raiz (/). Essa atribuição deve ser executada por um usuário que tenha a função de administrador global com acesso elevado adicional.

### <a name="elevate-access-for-a-global-administrator-account"></a>Elevar o acesso para uma conta de administrador global

Para atribuir uma função no escopo raiz (/), você precisará ter a função de administrador global com acesso elevado. Esse acesso elevado deve ser adicionado somente quando você precisa fazer a atribuição de função e, em seguida, removido quando terminar.

Para obter instruções detalhadas sobre como adicionar e remover a elevação, consulte [elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](../../role-based-access-control/elevate-access-global-admin.md).

Depois de elevar seu acesso, sua conta terá a função de administrador de acesso do usuário no Azure no escopo raiz. Essa atribuição de função permite exibir todos os recursos e atribuir acesso em qualquer assinatura ou grupo de gerenciamento no diretório, bem como fazer atribuições de função no escopo raiz.

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>Atribuir a função leitor de monitoramento ao escopo raiz

Depois de ter elevado o acesso, você pode atribuir as permissões apropriadas a uma conta para que ela possa consultar dados do log de atividades no nível do locatário. Essa conta precisará ter a função interna do [leitor de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-reader) do Azure atribuída no escopo raiz do seu locatário de gerenciamento.

> [!IMPORTANT]
> A concessão de uma atribuição de função no escopo raiz significa que as mesmas permissões serão aplicadas a todos os recursos no locatário. Como esse é um nível amplo de acesso, talvez você queira [atribuir essa função a uma conta de entidade de serviço e usar essa conta para consultar dados](#use-a-service-principal-account-to-query-the-activity-log). Você também pode atribuir a função leitor de monitoramento no escopo raiz a usuários individuais ou a grupos de usuários para que eles possam [Exibir informações de delegação diretamente no portal do Azure](#view-delegation-changes-in-the-azure-portal). Se você fizer isso, lembre-se de que esse é um nível amplo de acesso que deve ser limitado ao menor número de usuários possível.

Use um dos métodos a seguir para fazer a atribuição de escopo raiz.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ObjectId <objectId> 
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Remover acesso elevado para a conta de administrador global

Depois de atribuir a função leitor de monitoramento ao escopo raiz à conta desejada, certifique-se de [remover o acesso elevado](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) para a conta de administrador global, pois esse nível de acesso não será mais necessário.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Exibir alterações de delegação no portal do Azure

Os usuários que receberam a função leitor de monitoramento no escopo raiz podem exibir as alterações de delegação diretamente no portal do Azure.

1. Navegue até a página **meus clientes** e, em seguida, selecione **log de atividades** no menu de navegação à esquerda.
1. Verifique se a **atividade de diretório** está selecionada no filtro próximo à parte superior da tela.

Uma lista de alterações de delegação será exibida. Você pode selecionar **Editar colunas** para mostrar ou ocultar o **status**, **a categoria de evento**, a **hora**, o carimbo de **data/hora**, a **assinatura**, o **evento iniciado por**, o **grupo de recursos**, o tipo de **recurso** e os valores de **recurso** .

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Captura de tela de alterações de delegação no portal do Azure.":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>Usar uma conta de entidade de serviço para consultar o log de atividades

Como a função leitor de monitoramento no escopo raiz é um nível tão amplo de acesso, talvez você queira atribuir a função a uma conta de entidade de serviço e usar essa conta para consultar dados usando o script abaixo.

> [!IMPORTANT]
> Atualmente, os locatários com uma grande quantidade de atividade de delegação podem encontrar erros ao consultar esses dados.

Ao usar uma conta de entidade de serviço para consultar o log de atividades, recomendamos as seguintes práticas recomendadas:

- [Crie uma nova conta de entidade de serviço](../../active-directory/develop/howto-create-service-principal-portal.md) para ser usada somente para essa função, em vez de atribuir essa função a uma entidade de serviço existente usada para outra automação.
- Certifique-se de que essa entidade de serviço não tenha acesso a nenhum recurso de cliente delegado.
- [Use um certificado para autenticar](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) e [armazená-lo com segurança no Azure Key Vault](../../key-vault/general/security-overview.md).
- Limite os usuários que têm acesso ao Act em nome da entidade de serviço.

Depois de criar uma nova conta de entidade de serviço com acesso de leitor de monitoramento ao escopo raiz do seu locatário de gerenciamento, você poderá usá-la para consultar e relatar a atividade de delegação em seu locatário.

[Este script de Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) pode ser usado para consultar o último dia de atividade e relatórios sobre quaisquer delegações adicionadas ou removidas (ou tentativas que não foram bem-sucedidas). Ele consulta os dados do [log de atividades do locatário](/rest/api/monitor/TenantActivityLogs/List) e, em seguida, constrói os seguintes valores para relatar as delegações adicionadas ou removidas:

- **DelegatedResourceId**: a ID da assinatura delegada ou grupo de recursos
- **CustomerTenantId**: a ID de locatário do cliente
- **CustomerSubscriptionId**: a ID de assinatura que foi delegada ou que contém o grupo de recursos que foi delegado
- **CustomerDelegationStatus**: a alteração de status para o recurso delegado (Succeeded ou Failed)
- **EventTimeStamp**: a data e a hora em que a alteração de delegação foi registrada

Ao consultar esses dados, tenha em mente:

- Se vários grupos de recursos forem delegados em uma única implantação, as entradas separadas serão retornadas para cada grupo de recursos.
- As alterações feitas em uma delegação anterior (como atualizar a estrutura de permissão) serão registradas como uma delegação adicionada.
- Como mencionado acima, uma conta deve ter a função interna do Azure leitor de monitoramento no escopo raiz (/) para acessar os dados no nível do locatário.
- Você pode usar esses dados em seus próprios fluxos de trabalho e relatórios. Por exemplo, você pode usar a [API do coletor de dados http (visualização pública)](../../azure-monitor/platform/data-collector-api.md) para registrar dados em Azure monitor de um cliente de API REST e, em seguida, usar [grupos de ação](../../azure-monitor/platform/action-groups.md) para criar notificações ou alertas.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="next-steps"></a>Próximas etapas

- Saiba como integrar clientes ao [Azure Lighthouse](../concepts/azure-delegated-resource-management.md).
- Saiba mais sobre [Azure monitor](../../azure-monitor/index.yml) e o [log de atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md).
- Examine a pasta de trabalho de exemplo [logs de atividades por domínio](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) para saber como exibir os logs de atividades do Azure entre assinaturas com uma opção para filtrá-los por nome de domínio.
