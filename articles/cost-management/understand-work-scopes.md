---
title: Entender e trabalhar com os escopos de gerenciamento de custos do Azure | Microsoft Docs
description: Este artigo ajuda você a entender a cobrança e recurso de escopos de gerenciamento disponíveis no Azure e como usar os escopos no gerenciamento de custos e APIs.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 4e7956e8873b552fcd73c51a51f51d99f21af324
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61003002"
---
# <a name="understand-and-work-with-scopes"></a>Entender e trabalhar com escopos

Este artigo ajuda você a entender a cobrança e recurso de escopos de gerenciamento disponíveis no Azure e como usar os escopos no gerenciamento de custos e APIs.

## <a name="scopes"></a>Escopos

Um _escopo_ é um nó na hierarquia de recursos do Azure em que os usuários do AD do Azure acessar e gerenciar serviços. Recursos mais do Azure são criados e implantados em grupos de recursos, que fazem parte das assinaturas. A Microsoft também oferece duas hierarquias acima de assinaturas do Azure que têm especializado funções para gerenciar dados de cobrança:
- Dados de cobrança, como pagamentos e notas fiscais
- Serviços de nuvem, como o custo e a política de controle

Escopos são onde você gerenciar dados de cobrança, têm funções específicas a pagamentos, exibe faturas e conduzir o gerenciamento de conta geral. Funções de cobrança e conta são gerenciadas separadamente daqueles usados para gerenciamento de recursos, use [RBAC do Azure](../role-based-access-control/overview.md). Para distinguir claramente a intenção dos escopos separados, incluindo as diferenças de controle de acesso, eles são denominados _escopos de cobrança_ e _escopos RBAC_, respectivamente.

## <a name="how-cost-management-uses-scopes"></a>Como o gerenciamento de custos usa escopos

Custo gerenciamento funciona em todos os escopos de recursos para permitir que as organizações a gerenciar custos no nível no qual eles têm acesso, independentemente de ser a conta de cobrança inteira ou um único grupo de recursos acima. Embora a cobrança escopos diferem com base em seu contrato do Microsoft (tipo de assinatura), os escopos RBAC não.

## <a name="azure-rbac-scopes"></a>Escopos de RBAC do Azure

O Azure suporta três escopos para gerenciamento de recursos. Cada escopo dá suporte ao gerenciamento de acesso e governança, incluindo, mas não limitado a, o gerenciamento de custos.

- [**Grupos de gerenciamento** ](../governance/management-groups/index.md) -contêineres hierárquicos, até oito níveis, para organizar as assinaturas do Azure.

    Tipo de recurso: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Assinaturas** -contêineres de principais recursos do Azure.

    Tipo de recurso: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Grupos de recursos** ](../azure-resource-manager/resource-group-overview.md#resource-groups) -agrupamentos lógicos de recursos relacionados a uma solução do Azure que compartilham o mesmo ciclo de vida. Por exemplo os recursos que são implantados e excluídos juntos.

    Tipo de recurso: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Grupos de gerenciamento permitem que você organiza assinaturas em uma hierarquia. Por exemplo, você pode criar uma hierarquia de organização lógica usando grupos de gerenciamento. Em seguida, oferecem assinaturas de equipes para cargas de trabalho de desenvolvimento/teste e produção. E, em seguida, criar grupos de recursos nas assinaturas para gerenciar cada componente ou subsistema.

Criando uma hierarquia organizacional permite que o custo e a conformidade da política acumulação organizacional. Em seguida, cada líder pode exibir e analisar os custos atuais. E, em seguida, eles podem criar orçamentos para moderar padrões ruins de gastos e otimizar os custos com as recomendações do Assistente do nível mais baixo.

Conceder acesso para exibir os custos e, opcionalmente, gerenciar a configuração de custo, como os orçamentos e exportações, é executada em escopos de governança usando RBAC do Azure. Você usa o RBAC do Azure para conceder aos usuários do AD do Azure e grupos de acesso para executar um conjunto predefinido de ações que são definidas em uma função em um escopo específico e abaixo. Por exemplo, uma função atribuída a um escopo de grupo de gerenciamento também concede as mesmas permissões a grupos de recursos e assinaturas aninhadas.

Gerenciamento de custos suporta as seguintes funções internas para cada um dos escopos seguintes:

- [**Proprietário** ](../role-based-access-control/built-in-roles.md#owner) – pode exibir os custos e gerencie tudo, incluindo a configuração de custo.
- [**Colaborador** ](../role-based-access-control/built-in-roles.md#contributor) – pode exibir os custos e gerencie tudo, incluindo a configuração de custo, mas excluindo o controle de acesso.
- [**Leitor** ](../role-based-access-control/built-in-roles.md#reader) – pode exibir tudo, incluindo dados de custo e a configuração, mas não é possível fazer alterações.
- [**Colaborador de gerenciamento de custos** ](../role-based-access-control/built-in-roles.md#cost-management-contributor) – pode exibir os custos e gerenciar a configuração de custo.
- [**Leitor do gerenciamento de custos** ](../role-based-access-control/built-in-roles.md#cost-management-reader) – podem exibir dados de custo e a configuração.

Colaborador de gerenciamento de custo é a função de privilégio mínimo recomendada. Ele permite que as pessoas acesso criar e gerenciar orçamentos e exporta mais efetivamente monitorar e informar sobre os custos. Colaboradores de gerenciamento de custo também pode exigir a funções adicionais para dar suporte a cenários de gerenciamento de custo de ponta a ponta. Considere os seguintes cenários:

- **Agir quando os orçamentos são excedidos** – colaboradores do gerenciamento de custo também precisam de acesso para criar e/ou gerenciar grupos de ação para reagir automaticamente a excedentes. Considere conceder [Colaborador de monitoramento](../role-based-access-control/built-in-roles.md#monitoring-contributor) para um grupo de recursos que contém o grupo de ação para usar quando os limites do orçamento forem excedidos. Automatizar ações específicas exige funções adicionais para os serviços específicos usados, como a automação e o Azure Functions.
- **Agendamento de exportação de dados de custo** – colaboradores do gerenciamento de custo também precisam de acesso para gerenciar contas de armazenamento para agendar uma exportação para copiar dados em uma conta de armazenamento. Considere conceder [colaborador da conta de armazenamento](../role-based-access-control/built-in-roles.md#storage-account-contributor) para um grupo de recursos que contém o armazenamento de conta em que os dados de custo é exportada.
- **Exibindo recomendações de economia de custo** – os colaboradores e leitores de gerenciamento de custo não tem acesso às recomendações por padrão. O acesso às recomendações requer acesso de leitura a recursos individuais. Considere conceder [Reader](../role-based-access-control/built-in-roles.md#reader) ou um [específicos do serviço de função](../role-based-access-control/built-in-roles.md#built-in-role-descriptions).

## <a name="enterprise-agreement-scopes"></a>Escopos de Enterprise Agreement

Contas de cobrança do Enterprise Agreement (EA), também chamadas de registros, têm os seguintes escopos:

- [**Conta de cobrança** ](../billing/billing-view-all-accounts.md) -representa um registro de EA. As faturas são geradas neste escopo. Compras que não estão em uso, como o Marketplace e reservas, só estão disponíveis neste escopo. Elas não são representadas em departamentos ou contas de registro.

    Tipo de recurso: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Departamento** - opcional de agrupamento de contas de registro.

    Tipo de recurso: `Billing/billingAccounts/departments`

- **Conta de registro** -representa um único proprietário da conta. Não dá suporte a concessão de acesso a várias pessoas.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Embora os escopos de governança são associados a um único diretório, não são escopos de cobrança EA. Uma conta de cobrança EA pode ter assinaturas em qualquer número de diretórios do Azure AD.

Escopos de cobrança EA dão suporte a funções a seguir:

- **Administrador corporativo** – pode gerenciar o acesso e as configurações de conta de cobrança, pode exibir todos os custos e pode gerenciar a configuração de custo. Por exemplo, orçamentos e exporta. Na função, o escopo de cobrança de EA é o mesmo que [função de RBAC do Azure de Colaborador de gerenciamento de custo](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Usuário somente leitura do Enterprise** – pode exibir as configurações de conta de cobrança, dados de custo e configuração de custo. Por exemplo, orçamentos e exporta. Na função, o escopo de cobrança de EA é igual a [função de RBAC do Azure de leitor de gerenciamento de custo](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Administrador de departamento** – pode gerenciar configurações de departamento, como o Centro de custo e pode acessar, exibir todos os custos e gerenciar a configuração de custo. Por exemplo, orçamentos e exporta.  O **encargos de exibição do acelerador de desenvolvimento** configuração da conta de cobrança deve ser habilitado para administradores de departamento e usuários de somente leitura para ver os custos. Se **encargos de exibição do acelerador de desenvolvimento** é desabilitada, os usuários do departamento não é possível ver os custos em qualquer nível, mesmo que sejam um proprietário de conta ou assinatura.
- **Usuário somente leitura do departamento** – pode exibir as configurações de departamento, dados de custo e configuração de custo. Por exemplo, orçamentos e exporta. Se **encargos de exibição do acelerador de desenvolvimento** é desabilitada, os usuários do departamento não é possível ver os custos em qualquer nível, mesmo que sejam um proprietário de conta ou assinatura.
- **Proprietário da conta** – pode gerenciar configurações de conta de registro (como o Centro de custo), exibir todos os custos e gerenciar a configuração de custo (por exemplo, os orçamentos e exportações) para a conta de registro. O **encargos de exibição do sol** configuração da conta de cobrança deve ser habilitado para os proprietários e usuários do RBAC para ver os custos.

Usuários de conta de cobrança de EA não tem acesso direto às notas fiscais. As faturas estão disponíveis em um sistema de licenciamento por volume externo.

As assinaturas do Azure são aninhadas sob contas de registro. Os usuários de cobrança têm acesso aos dados de custo para as assinaturas e grupos de recursos que estão sob seus respectivos escopos. Eles não têm acesso para ver ou gerenciar recursos no portal do Azure. Os usuários de cobrança podem exibir custos, navegando até **gerenciamento de custos + cobrança** na lista do portal do Azure de serviços. Em seguida, eles podem filtrar os custos para os grupos de recursos que eles precisam para relatar e assinaturas específicas.

Os usuários de cobrança não tem acesso a grupos de gerenciamento, porque eles não se enquadram explicitamente em uma conta de cobrança específica. Acesso deve ser concedido explicitamente a grupos de gerenciamento. Os custos de Rollup de todas as assinaturas de grupos de gerenciamento. No entanto, eles incluem apenas com base no uso de compras. Elas não incluem compras, como as reservas e ofertas do Marketplace de terceiros. Para exibir esses custos, use a conta de cobrança EA.

## <a name="individual-agreement-pay-as-you-go-scopes"></a>Escopos de contrato individuais (pré-pago)

Assinaturas pré-pagas (PAYG), incluindo tipos relacionados como gratuita ou de avaliação e ofertas de desenvolvimento/teste, não tem um escopo explícito de conta de cobrança. Em vez disso, cada assinatura tem um proprietário de conta ou o administrador da conta, como o proprietário da conta do EA.

- [**Conta de cobrança** ](../billing/billing-view-all-accounts.md) -representa um único proprietário da conta para uma ou mais assinaturas do Azure. Ele atualmente não dá suporte a concessão de acesso a várias pessoas ou acesso a modos de exibição de custo agregado.

    Tipo de recurso: Não aplicável

Os administradores de conta de assinatura PAYG podem exibir e gerenciar dados de cobrança, como notas fiscais e pagamentos, do [Centro de contas do Azure](https://account.azure.com/subscriptions). No entanto, eles não podem exibir dados de custo ou gerenciar recursos no portal do Azure. Para conceder acesso para o administrador da conta, use as funções de gerenciamento de custos mencionadas anteriormente.

Ao contrário do EA, os administradores de conta de assinatura PAYG podem ver suas notas fiscais no portal do Azure. Tenha em mente que as funções de leitor de gerenciamento de custo e colaborador de gerenciamento de custo não fornecem acesso a faturas. Para obter mais informações, consulte [como conceder acesso a faturas PAYG](../billing/billing-manage-access.md#give-access-to-billing).

## <a name="customer-agreement-scopes"></a>Escopos de contrato do cliente

Contas de cobrança do contrato de cliente da Microsoft têm os seguintes escopos:

- **Conta de cobrança** -representa um contrato do cliente para vários produtos e serviços Microsoft. Contas de cobrança de contrato do cliente não são funcionalmente o mesmo que registros EA. Registros EA são mais intimamente alinhados aos perfis de cobrança.

    Tipo de recurso: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Perfil de cobrança** -define as assinaturas que estão incluídas em uma nota fiscal. Perfis de cobrança são o equivalente funcional de um registro de EA, já que é o escopo que as faturas são geradas em. Da mesma forma, as compras que não estão em uso (como Marketplace e reservas) só estão disponíveis neste escopo. Eles não estão incluídos nas seções de nota fiscal.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Seção de nota fiscal** -representa um grupo de assinaturas em uma nota fiscal ou o perfil de cobrança. Seções de fatura são como os departamentos — várias pessoas podem ter acesso a uma seção de nota fiscal.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/invoiceSections`

Ao contrário de EA de cobrança escopos, contas de cobrança do contrato de cliente _são_ associado a um único diretório e não podem ter assinaturas em vários diretórios do AD do Azure.

Escopos de cobrança de contrato de cliente dão suporte a funções a seguir:

- **Proprietário** – pode gerenciar o acesso e as configurações de cobrança, exibir todos os custos e gerenciar a configuração de custo. Por exemplo, orçamentos e exporta. Na função, esse escopo de cobrança do contrato de cliente é igual a [função de RBAC do Azure de Colaborador de gerenciamento de custo](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Colaborador** – pode gerenciar as configurações de cobrança, exceto o acesso, exibir todos os custos e gerenciar a configuração de custo. Por exemplo, orçamentos e exporta. Na função, esse escopo de cobrança do contrato de cliente é igual a [função de RBAC do Azure de Colaborador de gerenciamento de custo](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Leitor** – pode exibir as configurações de cobrança, dados de custo e configuração de custo. Por exemplo, orçamentos e exporta. Na função, esse escopo de cobrança do contrato de cliente é igual a [função de RBAC do Azure de leitor de gerenciamento de custo](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Gerenciador de faturas** – pode exibir e pagar notas fiscais e podem exibir dados e a configuração de custo. Por exemplo, orçamentos e exporta. Na função, esse escopo de cobrança do contrato de cliente é igual a [função de RBAC do Azure de leitor de gerenciamento de custo](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Criador da assinatura do Azure** – pode criar assinaturas do Azure, exibir os custos e gerenciar a configuração de custo. Por exemplo, orçamentos e exporta. Na função, esse escopo de cobrança do contrato do cliente é o mesmo que a função de proprietário de conta de registro de EA.

As assinaturas do Azure são aninhadas em seções de nota fiscal, como como eles estão sob contas de registro de EA. Os usuários de cobrança têm acesso aos dados de custo para as assinaturas e grupos de recursos que estão sob seus respectivos escopos. No entanto, eles não têm acesso para ver ou gerenciar recursos no portal do Azure. Os usuários de cobrança podem exibir custos, navegando até **gerenciamento de custos + cobrança** na lista do portal do Azure de serviços. Em seguida, filtre os custos para os grupos de recursos que eles precisam para relatar e assinaturas específicas.

Os usuários de cobrança não tem acesso a grupos de gerenciamento porque não estão explicitamente sob a conta de cobrança. No entanto, quando os grupos de gerenciamento são habilitados para a organização, todos os custos de assinatura são acumuladas para a conta de cobrança e ao grupo de gerenciamento raiz porque eles são restritos em um único diretório. Grupos de gerenciamento incluem apenas compras que estão em uso. Compras como as reservas e ofertas do Marketplace de terceiros não são incluídas nos grupos de gerenciamento. Portanto, o grupo de gerenciamento raiz e a conta de cobrança pode relatar outros totais. Para exibir esses custos, use a conta de cobrança ou o respectivo perfil de cobrança.

## <a name="cloud-solution-provider-csp-scopes"></a>Escopos de provedor de solução (CSP) de nuvem

Parceiros de solução CSP (provedor) de nuvem não são suportados atualmente no gerenciamento de custos. Em vez disso, você pode usar [Partner Center](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Alternar entre escopos no gerenciamento de custos

Todos os modos de exibição de gerenciamento de custos no portal do Azure incluem uma **escopo** com conteúdo malicioso no canto superior esquerdo do modo de exibição. Usá-lo para alterar rapidamente o escopo. Clique o **escopo** com conteúdo malicioso para abrir o seletor de escopo. Ele mostra as contas de cobrança, o grupo de gerenciamento raiz e todas as assinaturas que não estão aninhadas sob o grupo de gerenciamento raiz. Para selecionar um escopo, clique em segundo plano para realçá-lo e, em seguida, clique em **selecionar** na parte inferior. Para fazer drill-in para escopos aninhados, como grupos de recursos em uma assinatura, clique no link do nome de escopo. Para selecionar o escopo pai em qualquer nível aninhado, clique em **Selecione esta &lt;escopo&gt;**  na parte superior do seletor de escopo.

## <a name="identify-the-resource-id-for-a-scope"></a>Identificar a ID de recurso para um escopo

Ao trabalhar com APIs de gerenciamento de custo, sabendo que o escopo é crítico. Use as informações a seguir para criar o URI do escopo apropriado para as APIs de gerenciamento de custo.

### <a name="billing-accounts"></a>Contas de cobrança

1. Abra o portal do Azure e, em seguida, navegue até **gerenciamento de custos + cobrança** na lista de serviços.
2. Selecione **propriedades** no menu de conta de cobrança.
3. Copie a ID de conta cobrança.
4. O escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Perfis de cobrança

1. Abra o portal do Azure e, em seguida, navegue até **gerenciamento de custos + cobrança** na lista de serviços.
2. Selecione **perfis de cobrança** no menu de conta de cobrança.
3. Clique no nome do perfil de cobrança desejado.
4. Selecione **propriedades** no menu de perfil de cobrança.
5. Copie a conta de cobrança e as IDs de perfil de cobrança.
6. O escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Seções de fatura

1. Abra o portal do Azure e, em seguida, navegue até **gerenciamento de custos + cobrança** na lista de serviços.
2. Selecione **seções de nota fiscal** no menu de conta de cobrança.
3. Clique no nome da seção de nota fiscal desejada.
4. Selecione **propriedades** no menu da seção de nota fiscal.
5. Copie a conta de cobrança e seção IDs de nota fiscal.
6. O escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Departamentos de EA

1. Abra o portal do Azure e, em seguida, navegue até **gerenciamento de custos + cobrança** na lista de serviços.
2. Selecione **departamentos** no menu de conta de cobrança.
3. Clique no nome do departamento desejado.
4. Selecione **propriedades** no menu de departamento.
5. Copie as IDs de conta e o departamento de cobrança.
6. O escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Conta de registro de EA

1. Abra o portal do Azure e navegue até **gerenciamento de custos + cobrança** na lista de serviços.
2. Selecione **contas de registro** no menu de conta de cobrança.
3. Clique no nome da conta do registro desejada.
4. Selecione **propriedades** no menu de conta de registro.
5. Copie a conta de cobrança e conta de IDs de registro.
6. O escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Grupo de gerenciamento

1. Abra o portal do Azure e navegue até **grupos de gerenciamento** na lista de serviços.
2. Navegue até o grupo de gerenciamento desejado.
3. Copie a ID do grupo de gerenciamento da tabela.
4. O escopo é: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Assinatura

1. Abra o portal do Azure e navegue até **assinaturas** na lista de serviços.
2. Copie a ID da assinatura da tabela.
3. O escopo é: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Grupos de recursos

1. Abra o portal do Azure e navegue até **grupos de recursos** na lista de serviços.
2. Clique no nome do grupo de recursos desejado.
3. Selecione **propriedades** no menu do grupo de recursos.
4. Copie o valor de campo de ID do recurso.
5. O escopo é: `"/subscriptions/{id}/resourceGroups/{name}"`

Gerenciamento de custos é suportado atualmente no [Azure Global](https://management.azure.com) e [do Azure governamental](https://management.usgovcloudapi.net). Para obter mais informações sobre o Azure governamental, consulte [pontos de extremidade Global do Azure e a API do governo](../azure-government/documentation-government-developer-guide.md#endpoint-mapping)_._

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).
