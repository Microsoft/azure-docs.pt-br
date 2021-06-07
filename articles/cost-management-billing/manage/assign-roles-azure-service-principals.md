---
title: Atribuir funções aos nomes de entidade de serviço do Enterprise Agreement do Azure
description: Este artigo ajuda você a atribuir funções a nomes da entidade de serviço usando o PowerShell e as APIs REST.
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/07/2021
ms.author: banders
ms.openlocfilehash: e7f5370e1e387947d196959fef31043ea8f4d3bd
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508513"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Atribuir funções aos nomes de entidade de serviço do Enterprise Agreement do Azure

Você pode gerenciar seu registro do EA (Enterprise Agreement) no [portal do Azure Enterprise](https://ea.azure.com/). Você pode criar funções diferentes para gerenciar sua organização, exibir custos e criar assinaturas. Este artigo ajuda a automatizar algumas dessas tarefas usando o Azure PowerShell e APIs REST com os SPNs (nomes de entidade de serviço) do Azure.

Antes de começar, verifique se você está familiarizado com os seguintes artigos:

- [Funções do Enterprise Agreement](understand-ea-roles.md)
- [Entrar com o Azure PowerShell](/powershell/azure/authenticate-azureps)
- [Como chamar APIs REST com Postman](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>Criar e autenticar sua entidade de serviço

Para automatizar ações do EA usando um SPN, você precisará criar um aplicativo do Azure AD (Azure Active Directory). Ele pode ser autenticado de maneira automatizada. Leia os artigos abaixo e crie e autentique sua entidade de serviço seguindo as etapas.

1. [Criar uma entidade de serviço](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Obter valores de ID do aplicativo e locatário para entrar](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Aqui está uma captura de tela de exemplo mostrando o registro de aplicativo.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Captura de tela mostrando como registrar um aplicativo." lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>Localizar o SPN e a ID de locatário

Você também precisa da ID do objeto do SPN e da ID do locatário do aplicativo. Você precisa das informações para operações de atribuição de permissão em seções posteriores.

Encontre a ID do locatário do aplicativo Azure AD na página de visão geral do aplicativo. Para encontrá-la no portal do Azure, navegue até o Azure Active Directory e selecione **Aplicativos empresariais**. Pesquise o aplicativo.

:::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Captura de tela mostrando um exemplo de aplicativo empresarial." lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

Selecione o aplicativo. Veja um exemplo que mostra a ID do aplicativo e a ID do objeto.

:::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="Captura de tela mostrando uma ID do aplicativo e uma ID do objeto para um aplicativo empresarial." lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

Você pode encontrar a ID do locatário na página Visão geral do Microsoft Azure AD.

:::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="Captura de tela mostrando onde exibir sua ID de locatário." lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

A ID de locatário principal também é chamada de ID principal, SPN e ID do objeto em vários locais. O valor da sua ID de locatário do Azure AD é semelhante a um GUID com o seguinte formato: `11111111-1111-1111-1111-111111111111`.

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>Permissões que podem ser atribuídas ao SPN

Para as próximas etapas, você concede permissão ao aplicativo do Azure AD para executar ações usando uma função do EA. Você pode atribuir apenas as funções a seguir ao SPN. A ID de definição de função, exatamente como mostrada, é usada posteriormente em etapas de atribuição.

| Função | Ações permitidas | ID de definição de função |
| --- | --- | --- |
| EnrollmentReader | Pode exibir o uso e os encargos em todas as contas e assinaturas. Pode exibir o saldo de Pagamento antecipado do Azure (anteriormente chamado de compromisso monetário) associado ao registro. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| DepartmentReader | Baixe os detalhes de uso do departamento administrado. Pode exibir o uso e os encargos associados ao departamento. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | Crie assinaturas no escopo de conta fornecido. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- Um leitor de registro pode ser atribuído a um SPN somente por um usuário com a função de gravador de registro.
- Um leitor de departamento pode ser atribuído a um SPN somente por um usuário que tenha a função de gravador de registro ou gravador de departamento.
- Uma função de criador de assinatura pode ser atribuída a um SPN somente por um usuário que seja o proprietário da conta de registro.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>Atribuir permissão de função da conta de registro ao SPN

Leia o artigo da API REST [Atribuições de função – Put](/rest/api/billing/2019-10-01-preview/roleassignments/put).

Ao ler o artigo, selecione **Experimentar** para começar a usar o SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Captura de tela mostrando a opção Experimentar no artigo Put." lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

Entre com sua conta no locatário que tem acesso ao registro ao qual você deseja atribuir acesso.

Forneça os parâmetros a seguir como parte da solicitação de API.

**billingAccountName**

O parâmetro é a ID da conta de cobrança. Você pode encontrá-lo no portal do Azure na página Visão geral de Gerenciamento de Custos e Cobrança.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Captura de tela mostrando a ID da conta de cobrança." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

O parâmetro é um GUID único que você precisa fornecer. Você pode gerar um GUID usando o comando [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) do PowerShell.

Ou pode usar o site do [Gerador de GUID/UUID online](https://guidgenerator.com/) para gerar um GUID único.

**api-version**

Use a versão **2019-10-01-preview**.

O corpo da solicitação tem código JSON que você precisa usar.

Use o corpo da solicitação de exemplo em [Atribuições de função – Put – Exemplos](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples).

Há três parâmetros que você precisa usar como parte do JSON.

| Parâmetro | Onde encontrá-las |
| --- | --- |
| properties.principalId | Confira [Localizar o SPN e a ID de locatário](#find-your-spn-and-tenant-id). |
| properties.principalTenantId | Confira [Localizar o SPN e a ID de locatário](#find-your-spn-and-tenant-id). |
| properties.roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e" |

O nome da conta de cobrança é o mesmo parâmetro que você usou nos parâmetros da API. É a ID de registro que você vê no portal do EA e no portal do Azure.

Observe que `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` é uma ID de definição de função de cobrança para um EnrollmentReader.

Selecione **Executar** para iniciar o comando.

:::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="Captura de tela mostrando um exemplo de atribuição de função em Experimentar do Put com informações prontas para execução." lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

Uma resposta `200 OK` mostra que o SPN foi adicionado com êxito.

Agora você pode usar o SPN (aplicativo Azure AD com a ID de objeto) para acessar APIs do EA de maneira automatizada. O SPN tem a função EnrollmentReader.

## <a name="assign-the-department-reader-role-to-the-spn"></a>Atribuir a função de leitor de departamento ao SPN

Antes de começar, leia o artigo da API REST [Atribuições de função do departamento de registro – Put](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put).

Ao ler o artigo, selecione **Experimentar**.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Captura de tela mostrando a opção Experimentar no artigo Atribuições de função do departamento de registro – Put." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Entre com sua conta no locatário que tem acesso ao registro ao qual você deseja atribuir acesso.

Forneça os parâmetros a seguir como parte da solicitação de API.

**billingAccountName**

A ID da conta de cobrança. Você pode encontrá-lo no portal do Azure na página Visão geral de Gerenciamento de Custos e Cobrança.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Captura de tela mostrando a ID da conta de cobrança." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

O parâmetro é um GUID único que você precisa fornecer. Você pode gerar um GUID usando o comando [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) do PowerShell.

Ou pode usar o site do [Gerador de GUID/UUID online](https://guidgenerator.com/) para gerar um GUID único.

**departmentName**

A ID do departamento. Você pode ver as IDs dos departamentos na portal do Azure. Navegue até Gerenciamento de Custos e Cobrança > **Departamentos**.

Para este exemplo, usamos o departamento ACE. A ID do exemplo é `84819`.

:::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Captura de tela mostrando uma ID de departamento de exemplo." lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

**api-version**

Use a versão **2019-10-01-preview**.

O corpo da solicitação tem código JSON que você precisa usar.

Use a amostra em [Atribuições de função do departamento de registro – Put](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put). Há três parâmetros que você precisa usar como parte do JSON.

| Parâmetro | Onde encontrá-las |
| --- | --- |
| properties.principalId | Confira [Localizar o SPN e a ID de locatário](#find-your-spn-and-tenant-id). |
| properties.principalTenantId | Confira [Localizar o SPN e a ID de locatário](#find-your-spn-and-tenant-id). |
| properties.roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a" |

O nome da conta de cobrança é o mesmo parâmetro que você usou nos parâmetros da API. É a ID de registro que você vê no portal do EA e no portal do Azure.

A ID de definição da função de cobrança de `db609904-a47f-4794-9be8-9bd86fbffd8a` é para um leitor de departamento.

Selecione **Executar** para iniciar o comando.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Captura de tela mostrando um exemplo da opção Experimentar do REST Atribuições de função do departamento de registro – Put, com informações de exemplo prontas para execução." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

Uma resposta `200 OK` mostra que o SPN foi adicionado com êxito.

Agora você pode usar o SPN (aplicativo Azure AD com a ID de objeto) para acessar APIs do EA de maneira automatizada. O SPN tem a função DepartmentReader.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>Atribuir a função de criador de assinatura ao SPN

Leia o artigo [Atribuições de função da conta de registro –Put](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put).

Ao ler, selecione **Experimentar** para atribuir a função de criador de assinatura ao SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Captura de tela mostrando a opção Experimentar no artigo Atribuições de função da conta de registro – Put." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Entre com sua conta no locatário que tem acesso ao registro ao qual você deseja atribuir acesso.

Forneça os parâmetros a seguir como parte da solicitação de API. Leia o artigo em [Atribuições de função da conta de registro – Put – Parâmetros de URI](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters).

**billingAccountName**

O parâmetro é a ID da conta de cobrança. Você pode encontrá-lo no portal do Azure na página Visão geral de Gerenciamento de Custos e Cobrança.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Captura de tela mostrando a ID da conta de cobrança." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

O parâmetro é um GUID único que você precisa fornecer. Você pode gerar um GUID usando o comando [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) do PowerShell.

Ou pode usar o site do [Gerador de GUID/UUID online](https://guidgenerator.com/) para gerar um GUID único.
**enrollmentAccountName**

O parâmetro é a ID da conta. Localize a ID da conta para o nome da conta no portal do Azure em Gerenciamento de Custos e Cobrança no escopo de Registro e departamento.

Para este exemplo, usamos a conta de teste GTM. A ID é `196987`.

:::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="Captura de tela mostrando a ID da conta." lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

**api-version**

Use a versão **2019-10-01-preview**.

O corpo da solicitação tem código JSON que você precisa usar.

Use a amostra em [Atribuições de função do departamento de registro – Put – Exemplos](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment).

Há três parâmetros que você precisa usar como parte do JSON.

| Parâmetro | Onde encontrá-las |
| --- | --- |
| properties.principalId | Confira [Localizar o SPN e a ID de locatário](#find-your-spn-and-tenant-id). |
| properties.principalTenantId | Confira [Localizar o SPN e a ID de locatário](#find-your-spn-and-tenant-id). |
| properties.roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71" |

O nome da conta de cobrança é o mesmo parâmetro que você usou nos parâmetros da API. É a ID de registro que você vê no portal do EA e no portal do Azure.

A ID de definição da função de cobrança de `a0bcee42-bf30-4d1b-926a-48d21664ef71` é para a função de criador da assinatura.

Selecione **Executar** para iniciar o comando.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Captura de tela mostrando a opção Experimentar no artigo Atribuições de função da conta de registro – Put" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

Uma resposta `200 OK` mostra que o SPN foi adicionado com êxito.

Agora você pode usar o SPN (aplicativo Azure AD com a ID de objeto) para acessar APIs do EA de maneira automatizada. O SPN tem a função SubscriptionCreator.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [administração do portal do EA do Azure](ea-portal-administration.md).