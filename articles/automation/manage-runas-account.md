---
title: Gerenciar uma conta Executar como da Automação do Azure
description: Este artigo descreve como gerenciar as contas Executar como com PowerShell ou pelo portal do Azure.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.openlocfilehash: f9e99318c526bb935d0e035fdbf59874249390da
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050932"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gerenciar uma conta Executar como da Automação do Azure

As contas Executar como na automação do Azure fornecem autenticação para gerenciar recursos no Azure Resource Manager ou no modelo de implantação clássico do Azure usando runbooks de automação e outros recursos de automação. Este artigo fornece orientação sobre como gerenciar uma conta Executar como ou executar como clássica.

Para saber mais sobre a autenticação da conta de automação do Azure e diretrizes relacionadas a cenários de automação de processos, consulte [visão geral da autenticação da conta de automação](automation-security-overview.md).

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Renovar um certificado autoassinado

O certificado autoassinado que você criou para a conta Executar como expira um ano a contar da data de criação. Antes da conta Executar como expirar, você deverá renovar o certificado. Você pode renová-lo a qualquer momento antes que ele expire.

Ao renovar o certificado autoassinado, o certificado válido atual é retido para garantir que todos os runbooks colocados na fila ou em execução ativa e autenticados com a conta Executar como não sejam afetados negativamente. O certificado permanece válido até a data de expiração.

>[!NOTE]
>Se achar que a conta Executar como foi comprometida, você poderá excluir e recriar o certificado autoassinado.

>[!NOTE]
>Se você tiver configurado a conta Executar como para usar um certificado emitido por a autoridade de certificação corporativa e usar essa opção para renovar uma opção de certificado autoassinado, o certificado da empresa será substituído por um certificado autoassinado.

Use as etapas a seguir para renovar o certificado autoassinado.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Acesse sua conta de Automação e selecione **Contas Executar como** na seção de configurações de conta.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Painel de propriedades da conta de automação.":::

1. Na página de propriedades **contas Executar como** , selecione a **conta Executar como** ou a **conta Executar como clássica** , dependendo de qual conta você precisa para renovar o certificado.

1. Na página **Propriedades** da conta selecionada, selecione **renovar certificado**.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Renove o certificado para a conta Executar como.":::

1. Enquanto o certificado está sendo renovado, você poderá acompanhar o andamento em **Notificações** no menu.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Conceder permissões de conta Executar como em outras assinaturas

A automação do Azure dá suporte ao uso de uma única conta de automação de uma assinatura e a execução de runbooks em Azure Resource Manager recursos em várias assinaturas. Esta configuração não dá suporte ao modelo de implantação clássico do Azure.

Você atribui a entidade de serviço da conta Executar como a função de [colaborador](../role-based-access-control/built-in-roles.md#contributor) na outra assinatura ou permissões mais restritivas. Para obter mais informações, consulte [controle de acesso baseado em função](automation-role-based-access-control.md) na automação do Azure. Para atribuir a conta Executar como à função na outra assinatura, a conta de usuário que executa essa tarefa precisa ser um membro da função de **proprietário** nessa assinatura.

> [!NOTE]
> Essa configuração dá suporte apenas a várias assinaturas de uma organização usando um locatário do Azure AD comum.

Antes de conceder as permissões da conta Executar como, você precisa primeiro anotar o nome de exibição da entidade de serviço a ser atribuída.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Na sua conta de Automação, selecione **Contas Executar como** em **Configurações da Conta**.
1. Selecione **Conta Executar como do Azure**.
1. Copie ou anote o valor para **nome de exibição** na página da **conta Executar como do Azure** .

Para obter etapas detalhadas sobre como adicionar atribuições de função, confira os artigos a seguir, dependendo do método que você deseja usar.

* [Adicionar a atribuição de função do Azure da portal do Azure](../role-based-access-control/role-assignments-portal.md)
* [Adicionar a atribuição de função do Azure usando Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Adicionar a atribuição de função do Azure usando o CLI do Azure](../role-based-access-control/role-assignments-cli.md)
* [Adicionar a atribuição de função do Azure usando a API REST](..//role-based-access-control/role-assignments-rest.md)

Depois de atribuir a conta Executar como à função, em seu runbook, especifique `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` para definir o contexto de assinatura a ser usado. Para obter mais informações, consulte [set-AzContext](/powershell/module/az.accounts/set-azcontext).

## <a name="limit-run-as-account-permissions"></a>Limitar as permissões da conta Executar como

Para controlar o direcionamento de automação contra recursos no Azure, você pode executar o script de[Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8). Esse script altera a entidade de serviço da conta Executar como existente para criar e usar uma definição de função personalizada. A função tem permissões para todos os recursos, exceto [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Depois de executar o script **Update-AutomationRunAsAccountRoleAssignments.ps1**, os runbooks que acessam Key Vault por meio do uso de contas Executar como não funcionam mais. Antes de executar o script, você deve examinar os runbooks em sua conta para chamadas para Azure Key Vault. Para habilitar o acesso a Key Vault de runbooks de automação do Azure, você deve [adicionar a conta Executar como a permissões de Key Vault](#add-permissions-to-key-vault).

Se precisar restringir ainda mais o que a entidade de serviço executar como pode fazer, você pode adicionar outros tipos de recursos ao `NotActions` elemento da definição de função personalizada. O exemplo a seguir restringe o acesso a `Microsoft.Compute/*`. Se você adicionar esse tipo de recurso a `NotActions` para a definição de função, a função não poderá acessar nenhum recurso de computação. Para obter mais informações sobre definições de função, confira [Noções básicas sobre definições de função para recursos do Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Você pode determinar se a entidade de serviço usada pela conta Executar como atribuiu a função **colaborador** ou uma personalizada.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Acesse sua conta de Automação e selecione **Contas Executar como** na seção de configurações de conta.
1. Selecione **Conta Executar como do Azure**.
1. Selecione **Função** para localizar a definição de função que está sendo usada.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Verifique a função da conta Executar como." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Você também pode determinar a definição de função usada pelas contas Executar como para várias assinaturas ou contas de automação. Faça isso usando o script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) no Galeria do PowerShell.

### <a name="add-permissions-to-key-vault"></a>Adicionar permissões a um Key Vault

Você pode permitir que a Automação do Azure verifique se Key Vault e sua entidade de serviço da conta Executar como estão usando uma definição de função personalizada. Você deve:

* Conceder permissões a um Key Vault.
* Definir a política de acesso.

Você pode usar o script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) no Galeria do PowerShell para conceder permissões de conta Executar como para Key Vault. Consulte [atribuir uma política de acesso Key Vault](../key-vault/general/assign-access-policy-powershell.md) para obter mais detalhes sobre como definir permissões em Key Vault.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Resolver problemas de configuração incorreta para contas Executar como

Alguns itens de configuração necessários para que a conta Executar como ou Executar como Clássica funcione corretamente podem foram excluídos ou criados incorretamente durante a instalação inicial. As possíveis instâncias de configuração incorreta incluem:

* Ativo de certificado
* Ativo de conexão
* A Conta Executar como foi removida da função de Colaborador
* Entidade de serviço ou aplicativo no Azure AD

Para outras instâncias de uma configuração incorreta, a conta de Automação detecta as alterações e exibe o status *Incompleto* na página de propriedades Contas Executar como para a conta.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Configuração da conta Executar como incompleta.":::

Quando você selecionar a conta Executar como, o painel Propriedades da conta exibirá a seguinte mensagem de erro:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Você pode resolver rapidamente esses problemas de conta Executar como [excluindo](delete-run-as-account.md) e [recriando](create-run-as-account.md) a conta Executar como.

## <a name="next-steps"></a>Próximas etapas

* [Objetos de aplicativo e de entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md).
* [Visão geral sobre certificados para os Serviços de Nuvem do Azure](../cloud-services/cloud-services-certs-create.md).
* Para criar ou recriar uma conta Executar como, consulte [criar uma conta Executar como](create-run-as-account.md).
* Se você não precisar mais usar uma conta Executar como, consulte [excluir uma conta Executar como](delete-run-as-account.md).