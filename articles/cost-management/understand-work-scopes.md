---
title: Entenda e trabalhe com os escopos de gerenciamento de custos do Azure | Microsoft Docs
description: Este artigo ajuda você a entender os escopos de gerenciamento de recursos e cobrança disponíveis no Azure e como usar os escopos em gerenciamento de custos e APIs.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 66bad9c9c647fe87fdcf6b99a8d17f319b1ef9fc
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479977"
---
# <a name="understand-and-work-with-scopes"></a>Entender e trabalhar com escopos

Este artigo ajuda você a entender os escopos de gerenciamento de recursos e cobrança disponíveis no Azure e como usar os escopos em gerenciamento de custos e APIs.

## <a name="scopes"></a>Escopos

Um _escopo_ é um nó na hierarquia de recursos do Azure em que os usuários do Azure ad acessam e gerenciam serviços. A maioria dos recursos do Azure são criados e implantados em grupos de recursos, que fazem parte das assinaturas. A Microsoft também oferece duas hierarquias acima das assinaturas do Azure que têm funções especializadas para gerenciar dados de cobrança:
- Dados de cobrança, como pagamentos e faturas
- Serviços de nuvem, como governança de custo e política

Os escopos são onde você gerencia os dados de cobrança, têm funções específicas para pagamentos, exibem faturas e realizam o gerenciamento geral da conta. As funções de cobrança e de conta são gerenciadas separadamente das usadas para o gerenciamento de recursos, que usam o [RBAC do Azure](../role-based-access-control/overview.md). Para distinguir claramente a intenção dos escopos separados, incluindo as diferenças de controle de acesso, eles são chamados de escopos de _cobrança_ e escopos de _RBAC_, respectivamente.

## <a name="how-cost-management-uses-scopes"></a>Como o gerenciamento de custos usa escopos

O gerenciamento de custos funciona em todos os escopos acima dos recursos para permitir que as organizações gerenciem os custos no nível em que têm acesso, seja essa a conta de cobrança inteira ou um único grupo de recursos. Embora os escopos de cobrança sejam diferentes com base no seu contrato da Microsoft (tipo de assinatura), os escopos do RBAC não.

## <a name="azure-rbac-scopes"></a>Escopos de RBAC do Azure

O Azure dá suporte a três escopos para gerenciamento de recursos. Cada escopo dá suporte ao gerenciamento de acesso e governança, incluindo, mas não limitado, ao gerenciamento de custos.

- [**Grupos de gerenciamento**](../governance/management-groups/index.md) -contêineres hierárquicos, até oito níveis, para organizar assinaturas do Azure.

    Tipo de recurso: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Assinaturas** -contêineres primários para recursos do Azure.

    Tipo de recurso: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Grupos de recursos**](../azure-resource-manager/resource-group-overview.md#resource-groups) -agrupamentos lógicos de recursos relacionados para uma solução do Azure que compartilham o mesmo ciclo de vida. Por exemplo, recursos que são implantados e excluídos juntos.

    Tipo de recurso: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Os grupos de gerenciamento permitem que você organize as assinaturas em uma hierarquia. Por exemplo, você pode criar uma hierarquia lógica da organização usando grupos de gerenciamento. Em seguida, forneça assinaturas de equipes para cargas de trabalho de produção e desenvolvimento/teste. E, em seguida, criar grupos de recursos nas assinaturas para gerenciar cada subsistema ou componente.

A criação de uma hierarquia organizacional permite a acumulação de conformidade de custos e políticas de forma organizacional. Em seguida, cada líder pode exibir e analisar seus custos atuais. E, em seguida, eles podem criar orçamentos para moderar padrões de gastos inadequados e otimizar custos com recomendações do Advisor no nível mais baixo.

Conceder acesso para exibir custos e, opcionalmente, gerenciar a configuração de custos, como orçamentos e exportações, é executado em escopos de governança usando o RBAC do Azure. Você usa o Azure RBAC para conceder a usuários e grupos do Azure AD acesso para executar um conjunto predefinido de ações que são definidas em uma função em um escopo específico e abaixo. Por exemplo, uma função atribuída a um escopo de grupo de gerenciamento também concede as mesmas permissões a assinaturas aninhadas e grupos de recursos.

O gerenciamento de custos dá suporte às seguintes funções internas para cada um dos seguintes escopos:

- [**Proprietário**](../role-based-access-control/built-in-roles.md#owner) – pode exibir custos e gerenciar tudo, incluindo a configuração de custos.
- [**Colaborador**](../role-based-access-control/built-in-roles.md#contributor) – pode exibir custos e gerenciar tudo, incluindo a configuração de custos, mas excluindo o controle de acesso.
- [**Leitor**](../role-based-access-control/built-in-roles.md#reader) – pode exibir tudo, incluindo dados de custo e configuração, mas não pode fazer nenhuma alteração.
- [**Colaborador de gerenciamento de custos**](../role-based-access-control/built-in-roles.md#cost-management-contributor) – pode exibir custos, gerenciar a configuração de custos e exibir recomendações.
- [**Leitor de gerenciamento de custos**](../role-based-access-control/built-in-roles.md#cost-management-reader) – pode exibir dados de custo, configuração de custo e exibir recomendações.

Colaborador de gerenciamento de custo é a função de privilégio mínimo recomendada. Ele permite que as pessoas acessem criar e gerenciar orçamentos e exportações para monitorar e relatar com mais eficiência os custos. Os colaboradores de gerenciamento de custos também podem exigir funções adicionais para dar suporte a cenários de gerenciamento de custos de ponta a ponta. Considere os seguintes cenários:

- **Agir quando os orçamentos forem** excedidos – os colaboradores de gerenciamento de custos também precisam de acesso para criar e/ou gerenciar grupos de ações para reagir automaticamente a excedentes. Considere a concessão de [colaborador de monitoramento](../role-based-access-control/built-in-roles.md#monitoring-contributor) a um grupo de recursos que contenha o grupo de ações a ser usado quando os limites de orçamento forem excedidos. Automatizar ações específicas requer funções adicionais para os serviços específicos usados, como automação e Azure Functions.
- **Agendar a exportação de dados de custo – os** colaboradores de gerenciamento de custos também precisam de acesso para gerenciar contas de armazenamento para agendar uma exportação para copiar dados em uma conta de armazenamento. Considere conceder ao [colaborador da conta de armazenamento](../role-based-access-control/built-in-roles.md#storage-account-contributor) um grupo de recursos que contenha a conta de armazenamento na qual os dados de custo são exportados.
- **Exibindo recomendações de economia de** custos – os leitores de gerenciamento de custos e os colaboradores de gerenciamento de custos têm acesso para *Exibir* recomendações de custo por padrão. No entanto, o acesso ao Act sobre as recomendações de custo exige acesso a recursos individuais. Considere a concessão de uma [função específica do serviço](../role-based-access-control/built-in-roles.md#built-in-role-descriptions) se você quiser agir em uma recomendação baseada em custo.

## <a name="enterprise-agreement-scopes"></a>Escopos de Enterprise Agreement

As contas de cobrança do Enterprise Agreement (EA), também chamadas de registros, têm os seguintes escopos:

- [**Conta de cobrança**](../billing/billing-view-all-accounts.md) – representa um registro de ea. As notas fiscais são geradas nesse escopo. As compras que não são baseadas em uso, como Marketplace e reservas, estão disponíveis somente nesse escopo. Eles não são representados em departamentos ou contas de registro.

    Tipo de recurso:`Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Departamento** -agrupamento opcional de contas de registro.

    Tipo de recurso:`Billing/billingAccounts/departments`

- **Conta de registro** -representa um proprietário de conta única. Não dá suporte à concessão de acesso a várias pessoas.

    Tipo de recurso:`Microsoft.Billing/billingAccounts/enrollmentAccounts`

Embora os escopos de governança estejam associados a um único diretório, os escopos de cobrança EA não são. Uma conta de cobrança de EA pode ter assinaturas em qualquer número de diretórios do Azure AD.

Os escopos de cobrança de EA dão suporte às seguintes funções:

- **Administrador corporativo** – pode gerenciar as configurações de conta de cobrança e o acesso, pode exibir todos os custos e pode gerenciar a configuração de custo. Por exemplo, orçamentos e exportações. Na função, o escopo de cobrança EA é o mesmo que a [função RBAC do Azure do colaborador de gerenciamento de custo](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Usuário somente leitura corporativo** – pode exibir configurações de conta de cobrança, dados de custo e configuração de custo. Por exemplo, orçamentos e exportações. Na função, o escopo de cobrança EA é o mesmo que a [função RBAC do Azure do leitor de gerenciamento de custos](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Administrador do departamento** – pode gerenciar configurações de departamento, como centro de custo, e pode acessar, exibir todos os custos e gerenciar a configuração de custo. Por exemplo, orçamentos e exportações.  A configuração da conta de cobrança de encargos de **exibição** do dos deve ser habilitada para administradores de departamento e usuários somente leitura para ver os custos. Se os encargos **da exibição da** do forem desabilitados, os usuários do departamento não poderão ver os custos em nenhum nível, mesmo se forem um proprietário de conta ou assinatura.
- **Usuário somente leitura do departamento** – pode exibir configurações de departamento, dados de custo e configuração de custo. Por exemplo, orçamentos e exportações. Se os encargos **da exibição da** do forem desabilitados, os usuários do departamento não poderão ver os custos em nenhum nível, mesmo se forem um proprietário de conta ou assinatura.
- **Proprietário da conta** – pode gerenciar as configurações da conta de registro (como o centro de custo), exibir todos os custos e gerenciar a configuração de custos (como orçamentos e exportações) para a conta de registro. A configuração da conta de cobrança de encargos da **exibição** do ao deve ser habilitada para proprietários da conta e usuários do RBAC para ver os custos.

Os usuários da conta de cobrança EA não têm acesso direto às faturas. As notas fiscais estão disponíveis em um sistema de licenciamento por volume externo.

As assinaturas do Azure são aninhadas em contas de registro. Os usuários de cobrança têm acesso aos dados de custo para as assinaturas e os grupos de recursos que estão sob seus respectivos escopos. Eles não têm acesso para ver ou gerenciar recursos no portal do Azure. Os usuários de cobrança podem exibir os custos navegando até **Gerenciamento de custos + cobrança** na lista portal do Azure de serviços. Em seguida, eles podem filtrar os custos para as assinaturas específicas e os grupos de recursos necessários para o relatório.

Os usuários de cobrança não têm acesso aos grupos de gerenciamento porque não caem explicitamente sob uma conta de cobrança específica. O acesso deve ser concedido explicitamente aos grupos de gerenciamento. Os grupos de gerenciamento acumulam custos de todas as assinaturas aninhadas. No entanto, eles incluem apenas compras baseadas em uso. Eles não incluem compras como reservas e ofertas do Marketplace de terceiros. Para exibir esses custos, use a conta de cobrança de EA.

## <a name="individual-agreement-scopes"></a>Escopos de contrato individual

As assinaturas do Azure criadas a partir de ofertas individuais, como o pré-pago e os tipos relacionados, como avaliação gratuita e ofertas de desenvolvimento/teste, não têm um escopo de conta de cobrança explícito. Em vez disso, cada assinatura tem um proprietário de conta ou administrador de conta, como o proprietário da conta EA.

- [**Conta de cobrança**](../billing/billing-view-all-accounts.md) – representa um proprietário de conta única para uma ou mais assinaturas do Azure. Atualmente, ele não dá suporte à concessão de acesso a várias pessoas ou acesso a exibições de custo agregado.

    Tipo de recurso: Não aplicável

Os administradores individuais da conta de assinatura do Azure podem exibir e gerenciar dados de cobrança, como faturas e pagamentos, do [centro de contas do Azure](https://account.azure.com/subscriptions). No entanto, eles não podem exibir dados de custo ou gerenciar recursos no portal do Azure. Para conceder acesso ao administrador da conta, use as funções de gerenciamento de custos mencionadas anteriormente.

Ao contrário de EA, os administradores de conta de assinatura individuais do Azure podem ver suas notas fiscais no portal do Azure. Lembre-se de que as funções de colaborador de gerenciamento de custo e de leitor de gerenciamento de custos não fornecem acesso a faturas. Para obter mais informações, consulte [como conceder acesso a faturas](../billing/billing-manage-access.md##give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Escopos do contrato de cliente da Microsoft

As contas de cobrança do contrato de cliente da Microsoft têm os seguintes escopos:

- **Conta de cobrança** – representa um contrato de cliente para vários produtos e serviços da Microsoft. As contas de cobrança do contrato do cliente não são funcionalmente iguais a registros de EA. Os registros de EA estão mais bem alinhados aos perfis de cobrança.

    Tipo de recurso:`Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Perfil de cobrança** – define as assinaturas que são incluídas em uma fatura. Os perfis de cobrança são o equivalente funcional de um registro de EA, pois esse é o escopo em que as notas fiscais são geradas. Da mesma forma, as compras que não são baseadas em uso (como Marketplace e reservas) estão disponíveis somente nesse escopo. Eles não são incluídos nas seções da fatura.

    Tipo de recurso:`Microsoft.Billing/billingAccounts/billingProfiles`

- **Seção fatura** – representa um grupo de assinaturas em uma nota fiscal ou perfil de cobrança. As seções de fatura são como departamentos – várias pessoas podem ter acesso a uma seção de faturas.

    Tipo de recurso:`Microsoft.Billing/billingAccounts/invoiceSections`

Ao contrário dos escopos de cobrança EA, as contas de cobrança do contrato do cliente _são_ associadas a um único diretório e não podem ter assinaturas em vários diretórios do Azure AD.

Os escopos de cobrança do contrato do cliente dão suporte às seguintes funções:

- **Proprietário** – pode gerenciar as configurações de cobrança e o acesso, exibir todos os custos e gerenciar a configuração de custo. Por exemplo, orçamentos e exportações. Na função, esse escopo de cobrança do contrato do cliente é o mesmo que a [função RBAC do Azure do colaborador de gerenciamento de custo](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Colaborador** – pode gerenciar as configurações de cobrança, exceto o acesso, exibir todos os custos e gerenciar a configuração de custo. Por exemplo, orçamentos e exportações. Na função, esse escopo de cobrança do contrato do cliente é o mesmo que a [função RBAC do Azure do colaborador de gerenciamento de custo](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Leitor** – pode exibir configurações de cobrança, dados de custo e configuração de custo. Por exemplo, orçamentos e exportações. Na função, esse escopo de cobrança do contrato do cliente é o mesmo que a [função RBAC do Azure do leitor de gerenciamento de custos](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Gerenciador** de faturas – pode exibir e pagar faturas e pode exibir dados de custo e configuração. Por exemplo, orçamentos e exportações. Na função, esse escopo de cobrança do contrato do cliente é o mesmo que a [função RBAC do Azure do leitor de gerenciamento de custos](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Criador de assinatura do Azure** – pode criar assinaturas do Azure, exibir custos e gerenciar a configuração de custo. Por exemplo, orçamentos e exportações. Na função, esse escopo de cobrança do contrato do cliente é o mesmo que a função de proprietário da conta de registro EA.

As assinaturas do Azure são aninhadas sob seções de faturas, como se estivessem em contas de registro de EA. Os usuários de cobrança têm acesso aos dados de custo para as assinaturas e os grupos de recursos que estão sob seus respectivos escopos. No entanto, eles não têm acesso para ver ou gerenciar recursos no portal do Azure. Os usuários de cobrança podem exibir os custos navegando até **Gerenciamento de custos + cobrança** na lista portal do Azure de serviços. Em seguida, filtre os custos para as assinaturas e grupos de recursos específicos que precisam ser relatados.

Os usuários de cobrança não têm acesso aos grupos de gerenciamento porque não se enquadram explicitamente na conta de cobrança. No entanto, quando os grupos de gerenciamento são habilitados para a organização, todos os custos de assinatura são acumulados na conta de cobrança e no grupo de gerenciamento raiz porque são restritos a um único diretório. Os grupos de gerenciamento incluem apenas compras com base no uso. Compras como reservas e ofertas do Marketplace de terceiros não estão incluídas em grupos de gerenciamento. Portanto, a conta de cobrança e o grupo de gerenciamento raiz podem relatar totais diferentes. Para exibir esses custos, use a conta de cobrança ou o respectivo perfil de cobrança.

## <a name="aws-scopes"></a>Escopos AWS

Após a conclusão da integração do AWS, consulte Configurar [e configurar a integração do AWS](aws-integration-set-up-configure.md). Os seguintes escopos estão disponíveis:

- **Conta de cobrança externa** – representa um contrato de cliente com um fornecedor terceirizado. Isso é semelhante à conta de cobrança de EA.

    Tipo de recurso:`Microsoft.CostManagement/externalBillingAccounts`
    
- **Assinatura externa** – representa uma conta operacional do cliente com um fornecedor de terceiros. Isso é semelhante a uma assinatura do Azure.

    Tipo de recurso:`Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Escopos do CSP (provedor de soluções na nuvem)

Os parceiros do CSP (provedor de soluções na nuvem) não têm suporte no gerenciamento de custos atualmente. Em vez disso, você pode usar o [Partner Center](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Alternar entre escopos no gerenciamento de custos

Todas as exibições de gerenciamento de custos na portal do Azure incluem uma seleção de **escopo** para a parte superior esquerda da exibição. Use-o para alterar o escopo rapidamente. Clique no **escopo** Pill para abrir o seletor de escopo. Ele mostra contas de cobrança, o grupo de gerenciamento raiz e todas as assinaturas que não estão aninhadas no grupo de gerenciamento raiz. Para selecionar um escopo, clique no plano de fundo para realçá-lo e, em seguida, clique em **selecionar** na parte inferior. Para fazer drill-in em escopos aninhados, como grupos de recursos em uma assinatura, clique no link nome do escopo. Para selecionar o escopo pai em qualquer nível aninhado, clique em **selecionar&gt; este &lt;escopo** na parte superior do seletor de escopo.

## <a name="identify-the-resource-id-for-a-scope"></a>Identificar a ID do recurso para um escopo

Ao trabalhar com APIs de gerenciamento de custos, saber que o escopo é crítico. Use as informações a seguir para criar o URI de escopo adequado para APIs de gerenciamento de custos.

### <a name="billing-accounts"></a>Contas de cobrança

1. Abra o portal do Azure e navegue até **Gerenciamento de custos + cobrança** na lista de serviços.
2. Selecione **Propriedades** no menu conta de cobrança.
3. Copie a ID da conta de cobrança.
4. Seu escopo é:`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Perfis de cobrança

1. Abra o portal do Azure e navegue até **Gerenciamento de custos + cobrança** na lista de serviços.
2. Selecione **perfis de cobrança** no menu conta de cobrança.
3. Clique no nome do perfil de cobrança desejado.
4. Selecione **Propriedades** no menu perfil de cobrança.
5. Copie a conta de cobrança e as IDs do perfil de cobrança.
6. Seu escopo é:`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Seções de fatura

1. Abra o portal do Azure e navegue até **Gerenciamento de custos + cobrança** na lista de serviços.
2. Selecione **seções de fatura** no menu da conta de cobrança.
3. Clique no nome da seção nota fiscal desejada.
4. Selecione **Propriedades** no menu da seção fatura.
5. Copie a conta de cobrança e IDs de seção de fatura.
6. Seu escopo é:`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Departamentos de EA

1. Abra o portal do Azure e navegue até **Gerenciamento de custos + cobrança** na lista de serviços.
2. Selecione **departamentos** no menu conta de cobrança.
3. Clique no nome do departamento desejado.
4. Selecione **Propriedades** no menu do departamento.
5. Copie a conta de cobrança e as IDs de departamento.
6. Seu escopo é:`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Conta de registro EA

1. Abra o portal do Azure e navegue até **Gerenciamento de custos + cobrança** na lista de serviços.
2. Selecione **contas de registro** no menu conta de cobrança.
3. Clique no nome da conta de registro desejada.
4. Selecione **Propriedades** no menu conta de registro.
5. Copie a conta de cobrança e as IDs da conta de registro.
6. Seu escopo é:`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Grupo de gerenciamento

1. Abra o portal do Azure e navegue até **grupos de gerenciamento** na lista de serviços.
2. Navegue até o grupo de gerenciamento desejado.
3. Copie a ID do grupo de gerenciamento da tabela.
4. Seu escopo é:`"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Assinatura

1. Abra o portal do Azure e navegue até **assinaturas** na lista de serviços.
2. Copie a ID da assinatura da tabela.
3. Seu escopo é:`"/subscriptions/{id}"`

### <a name="resource-groups"></a>Grupos de recursos

1. Abra o portal do Azure e navegue até **grupos de recursos** na lista de serviços.
2. Clique no nome do grupo de recursos desejado.
3. Selecione **Propriedades** no menu grupo de recursos.
4. Copie o valor do campo ID do recurso.
5. Seu escopo é:`"/subscriptions/{id}/resourceGroups/{name}"`

Atualmente, o gerenciamento de custos tem suporte no [Azure global](https://management.azure.com) e no [Azure governamental](https://management.usgovcloudapi.net). Para obter mais informações sobre o Azure governamental, consulte [pontos de extremidade de API global e governamental do Azure](../azure-government/documentation-government-developer-guide.md#endpoint-mapping) _._

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).
