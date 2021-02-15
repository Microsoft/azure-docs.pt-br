---
title: Entender e trabalhar com os escopos do Gerenciamento de Custos do Azure
description: Este artigo ajuda você a entender os escopos de gerenciamento de recursos e de cobrança disponíveis no Azure e como usar os escopos no Gerenciamento de Custos e nas APIs.
author: bandersmsft
ms.author: banders
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 729444b1d1ccf55f34e54a4b59508131458c472b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054797"
---
# <a name="understand-and-work-with-scopes"></a>Entender e trabalhar com escopos

Este artigo ajuda você a entender os escopos de gerenciamento de recursos e de cobrança disponíveis no Azure e como usar os escopos no Gerenciamento de Custos e nas APIs.

## <a name="scopes"></a>Escopos

Um _escopo_ é um nó na hierarquia de recursos do Azure em que os usuários do Azure AD acessam e gerenciam serviços. A maioria dos recursos do Azure são criados e implantados em grupos de recursos, que fazem parte de assinaturas. A Microsoft também oferece duas hierarquias acima das assinaturas do Azure que têm funções especializadas para gerenciar dados de cobrança:
- Dados de cobrança, como pagamentos e faturas
- Serviços de nuvem, como governança de políticas e custos

Os escopos são o local em que você gerencia os dados de cobrança, têm funções específicas para pagamentos, exibem faturas e realizam o gerenciamento geral da conta. As funções de cobrança e de conta são gerenciadas separadamente das funções usadas para o gerenciamento de recursos, que usam o [Azure RBAC](../../role-based-access-control/overview.md). Para distinguir claramente a intenção dos escopos separados, incluindo as diferenças de controle de acesso, eles são chamados de _escopos do orçamento_ e _escopos do Azure RBAC_, respectivamente.

Para saber mais sobre escopos, assista ao vídeo [Gerenciamento de Custos configurando hierarquias](https://www.youtube.com/watch?v=n3TLRaYJ1NY). Para assistir a outros vídeos, visite o [Canal do YouTube do Gerenciamento de Custos](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

## <a name="how-cost-management-uses-scopes"></a>Como o Gerenciamento de Custos usa os escopos

O Gerenciamento de Custos funciona em todos os escopos acima dos recursos para permitir que as organizações gerenciem os custos no nível em que têm acesso, seja esse nível a conta de cobrança inteira ou um grupo de recursos. Embora os escopos do orçamento sejam diferentes de acordo com o seu contrato da Microsoft (tipo de assinatura), os escopos do Azure RBAC não são.

## <a name="azure-rbac-scopes"></a>Função do Azure RBAC

O Azure dá suporte a três escopos para gerenciamento de recursos. Cada escopo dá suporte ao gerenciamento de acesso e governança, incluindo, mas não limitado, ao gerenciamento de custos.

- [**Grupos de gerenciamento**](../../governance/management-groups/overview.md) – contêineres hierárquicos, até oito níveis, para organizar assinaturas do Azure.

    Tipo de recurso: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Assinaturas** – contêineres primários para recursos do Azure.

    Tipo de recurso: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Grupos de recursos**](../../azure-resource-manager/management/overview.md#resource-groups) – agrupamentos lógicos de recursos relacionados para uma solução do Azure que compartilham o mesmo ciclo de vida. Por exemplo, recursos que são implantados e excluídos juntos.

    Tipo de recurso: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Os grupos de gerenciamento permitem que você organize as assinaturas em uma hierarquia. Por exemplo, você pode criar uma hierarquia lógica da organização usando grupos de gerenciamento. Em seguida, forneça assinaturas para cargas de trabalho de produção e de desenvolvimento/teste às equipes. Então, crie grupos de recursos nas assinaturas para gerenciar cada subsistema ou componente.

A criação de uma hierarquia organizacional permite que a conformidade de custos e de políticas se acumule na organização. Em seguida, cada líder pode exibir e analisar os próprios custos atuais. E, em seguida, os líderes podem criar orçamentos para moderar padrões de gastos inadequados e otimizar custos com recomendações do Assistente, mesmo nos menores níveis.

A concessão de acesso para exibir custos e, opcionalmente, gerenciar a configuração de custos, como orçamentos e exportações, é feita em escopos de governança usando o Azure RBAC. Você usa o Azure RBAC para conceder a usuários e grupos do Azure AD acesso para fazer um conjunto predefinido de ações, que são definidas em uma função em um escopo específico e nos níveis inferiores. Por exemplo, uma função atribuída a um escopo de grupo de gerenciamento também concede as mesmas permissões a assinaturas e grupos de recursos aninhados.

O Gerenciamento de Custos é compatível com as seguintes funções internas para cada um dos seguintes escopos:

- [**Proprietário**](../../role-based-access-control/built-in-roles.md#owner) – pode exibir os custos e gerenciar tudo, inclusive a configuração de custos.
- [**Colaborador**](../../role-based-access-control/built-in-roles.md#contributor) – pode exibir os custos e gerenciar tudo (inclusive a configuração de custos), com exceção do controle de acesso.
- [**Leitor**](../../role-based-access-control/built-in-roles.md#reader) – Pode exibir tudo, inclusive os dados e a configuração de custos, mas não pode fazer nenhuma alteração.
- [**Colaborador do Gerenciamento de Custos**](../../role-based-access-control/built-in-roles.md#cost-management-contributor) – pode exibir custos, gerenciar a configuração de custos e exibir recomendações.
- [**Leitor do Gerenciamento de Custos**](../../role-based-access-control/built-in-roles.md#cost-management-reader) – pode exibir dados de custos, configuração de custos e recomendações.

É recomendado que a função de Colaborador do Gerenciamento de Custos seja aquela com o mínimo de privilégios. A função permite que pessoas criem e gerenciem orçamentos e exportações a fim de monitorar e gerar relatórios de custos com mais eficiência. Os Colaboradores do Gerenciamento de Custos também podem exigir funções adicionais para dar suporte a cenários complexos de gerenciamento de custos. Considere os seguintes cenário:

- **Relatório sobre o uso de recursos** – o Gerenciamento de Custos do Azure mostra o custo no portal do Azure. Ele inclui o uso, pois isso se refere ao custo nos padrões de uso completos. Esse relatório também pode mostrar os preços da API e de download, mas o ideal é também analisar as métricas de uso detalhadas no Azure Monitor para obter uma compreensão mais profunda. Considere conceder [Leitor de Monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-reader) em qualquer escopo em que você também precise relatar métricas de uso detalhadas.
- **Agir quando os orçamentos forem excedidos** – os Colaboradores do Gerenciamento de Custos também precisam de acesso para criar e gerenciar grupos de ações para reagir automaticamente a excedentes. Considere a possibilidade de conceder um [Colaborador de Monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-contributor) a um grupo de recursos que contenha o grupo de ações a ser usado quando os limites de orçamento forem excedidos. Automatizar ações específicas requer funções adicionais para os serviços específicos usados, como a Automação e o Azure Functions.
- **Agendar exportação de dados de custo** – os Colaboradores do Gerenciamento de Custos também precisam de acesso para gerenciar contas de armazenamento a fim de agendar uma exportação para copiar dados para uma conta de armazenamento. Considere a possibilidade de conceder um [Colaborador da Conta de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor) a um grupo de recursos que contém a conta de armazenamento na qual os dados de custo são exportados.
- **Exibir recomendações de economia de custos** – os Leitores do Gerenciamento de Custos e os Colaboradores do Gerenciamento de Custos têm acesso à *exibição* de recomendações de custo por padrão. No entanto, o acesso para agir segundo as recomendações de custo exige acesso a recursos individuais. Considere a possibilidade de conceder uma [função específica a um serviço](../../role-based-access-control/built-in-roles.md#all) se você quiser agir segundo uma recomendação baseada em custo.

Somente haverá suporte para grupos de gerenciamento se eles contiverem assinaturas EA (Contrato Enterprise), PAYG (pagas conforme o uso) ou internas da Microsoft. Grupos de gerenciamento com outros tipos de assinatura, como o Contrato de Cliente da Microsoft ou assinaturas do Azure Active Directory, não podem exibir custos. Se você tiver uma combinação de assinaturas, mova as assinaturas sem suporte para um braço separado da hierarquia de grupo de gerenciamento para habilitar o Gerenciamento de Custos para as assinaturas com suporte. Por exemplo, crie dois grupos de gerenciamento no grupo de gerenciamento raiz: **Azure AD** e **Minha Organização**. Mova sua assinatura do Azure AD para o grupo de gerenciamento do **Azure AD** e, em seguida, exiba e gerencie os custos usando o grupo de gerenciamento **Minha Organização**.

## <a name="enterprise-agreement-scopes"></a>Escopos do Contrato Enterprise

As contas de cobrança do EA (Contrato Enterprise), também chamadas de registros, têm os seguintes escopos:

- [**Conta de cobrança**](../manage/view-all-accounts.md) – representa um registro de EA. As faturas são geradas nesse escopo. As compras não baseadas em uso, como as do Marketplace e as reservas, estão disponíveis somente nesse escopo. Elas não são representadas em departamentos nem em contas de registro. O uso de reserva, juntamente com todos os outros usos, é aplicado a recursos individuais. O uso é acumulado em assinaturas na conta de cobrança. Para ver os custos de reserva divididos em cada recurso, mude para a exibição **Custo amortizado** na análise de custo.

    Tipo de recurso: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Departamento** – agrupamento opcional de contas de registro.

    Tipo de recurso: `Billing/billingAccounts/departments`

- **Conta de registro** – representa um proprietário da conta. Não dá suporte à concessão de acesso a várias pessoas.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Embora os escopos de governança estejam associados a um diretório, os escopos do orçamento de EA não estão. Uma conta de cobrança de EA pode ter assinaturas em qualquer número de diretórios do Azure AD.

Os escopos do orçamento de EA dão suporte às seguintes funções:

- **Admin corporativo** – pode gerenciar as configurações e o acesso da conta de cobrança, pode exibir todos os custos e pode gerenciar a configuração de custo. Por exemplo, orçamentos e exportações. Em função, o escopo do orçamento do EA é o mesmo que a [função do Azure de Colaborador do Gerenciamento de Custos](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Usuário corporativo somente leitura** – pode exibir configurações de conta de cobrança, dados de custo e configuração de custo. Por exemplo, orçamentos e exportações. Em função, o escopo do orçamento do EA é o mesmo que a [função do Azure de Leitor do Gerenciamento de Custos](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Administrador de departamento** – pode gerenciar configurações de departamento, como centro de custo, e pode acessar e exibir todos os custos e gerenciar a configuração de custo. Por exemplo, orçamentos e exportações.  A configuração **Administrador de departamento exibir encargos** da conta de cobrança deve estar habilitada para que administradores do departamento e usuários somente leitura vejam os custos. Se a opção **Administrador de departamento exibir encargos** estiver desabilitada, os usuários do departamento não poderão ver os custos em nenhum nível, mesmo se forem um proprietário de conta ou da assinatura.
- **Usuário somente leitura do departamento** – pode exibir configurações de departamento, dados de custo e configuração de custo. Por exemplo, orçamentos e exportações. Se a opção **Administrador de departamento exibir encargos** estiver desabilitada, os usuários do departamento não poderão ver os custos em nenhum nível, mesmo se forem um proprietário de conta ou da assinatura.
- **Proprietário da conta** – pode gerenciar as configurações da conta de registro (como o centro de custo), exibir todos os custos e gerenciar a configuração de custos (como orçamentos e exportações) para a conta de registro. A configuração **Exibir cobranças de AO** da conta de cobrança deve estar habilitada para que proprietários da conta e usuários Azure RBAC vejam os custos.

Os usuários da conta de cobrança do EA não têm acesso direto às faturas. As notas fiscais estão disponíveis em um sistema de licenciamento por volume externo.

As assinaturas do Azure são aninhadas sob as contas de registro. Os usuários de cobrança têm acesso aos dados de custo para as assinaturas e aos grupos de recursos que estão sob seus respectivos escopos. Eles não têm acesso para ver ou gerenciar recursos no portal do Azure. Os usuários podem exibir os custos navegando para **Gerenciamento de Custos + Cobrança** na lista de serviços do portal do Azure. Em seguida, eles podem filtrar os custos para as assinaturas específicas e os grupos de recursos sobre os quais precisam fazer um relatório.

Os usuários de cobrança não têm acesso a grupos de gerenciamento porque eles não ficam explicitamente sob uma conta de cobrança específica. O acesso deve ser concedido explicitamente a grupos de gerenciamento. Os grupos de gerenciamento acumulam custos de todas as assinaturas aninhadas. No entanto, eles incluem apenas compras baseadas em uso. Eles não incluem compras como reservas e ofertas do Marketplace de terceiros. Para exibir esses custos, use a conta de cobrança do EA.

## <a name="individual-agreement-scopes"></a>Escopos de contrato individual

As assinaturas do Azure criadas com base em ofertas individuais, como as pagas conforme o uso e os tipos relacionados, como a avaliação gratuita e as ofertas de desenvolvimento/teste, não têm um escopo de conta de cobrança explícito. Em vez disso, cada assinatura tem um proprietário de conta ou administrador de conta, como o proprietário da conta do EA.

- [**Conta de cobrança**](../manage/view-all-accounts.md) – representa um proprietário da conta para uma ou mais assinaturas do Azure. Atualmente, não dá suporte à concessão de acesso a várias pessoas ou acesso a exibições de custo agregado.

    Tipo de recurso: Não aplicável

Os administradores individuais da conta de assinatura do Azure podem ver e gerenciar dados de cobrança, como faturas e pagamentos no [portal do Azure](https://portal.azure.com) > **Assinaturas** > selecionar uma assinatura.

Ao contrário do que ocorre no EA, os administradores de contas de assinaturas do Azure individuais podem ver as respectivas notas fiscais no portal do Azure. Lembre-se de que as funções de Colaborador do Gerenciamento de Custos e de Leitor do Gerenciamento de Custos não fornecem acesso a faturas. Para obter mais informações, veja [Como permitir acesso a faturas](../manage/manage-billing-access.md#give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Escopos do Contrato de Cliente da Microsoft

As contas de cobrança do Contrato de Cliente da Microsoft têm os seguintes escopos:

- **Conta de cobrança** – representa um contrato de cliente para vários produtos e serviços da Microsoft. As contas de cobrança do Contrato de Cliente não são funcionalmente iguais a registros de EA. Os registros de EA estão mais alinhados aos perfis de cobrança.

    Tipo de recurso: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Perfil de cobrança** – define as assinaturas que são incluídas em uma fatura. Os perfis de cobrança são o equivalente funcional de um registro de EA, pois esse é o escopo em que as notas fiscais são geradas. De maneira similar, as compras não baseadas em uso, como as do Marketplace e as reservas, estão disponíveis somente nesse escopo. Elas não são incluídas em seções da fatura.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Seção de fatura** – representa um grupo de assinaturas em um perfil de cobrança ou em uma fatura. As seções de fatura são como departamentos – várias pessoas podem ter acesso a uma seção de fatura.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Cliente** – representa um grupo de assinaturas que estão associadas a um cliente específico que é integrado a um Contrato de Cliente da Microsoft por um parceiro. Esse escopo é específico de CSP (Provedores de Soluções na Nuvem).

Ao contrário dos escopos do orçamento de EA, as contas de cobrança do Contrato de Cliente _são_ associadas a um diretório e não podem ter assinaturas em vários diretórios do Azure AD.

Os escopos do orçamento do Contrato de Cliente não se aplicam a parceiros. As funções e permissões de parceiro estão documentadas em [Atribuir permissões e funções de usuários](/partner-center/permissions-overview).

Os escopos do orçamento de Contrato de Cliente dão suporte às seguintes funções:

- **Proprietário** – pode gerenciar as configurações de cobrança e o acesso, exibir todos os custos e gerenciar a configuração de custo. Por exemplo, orçamentos e exportações. Em função, o escopo do orçamento do Contrato do Cliente é o mesmo que a [função do Azure de Colaborador do Gerenciamento de Custos](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Colaborador** – pode gerenciar as configurações de cobrança (com a exceção do acesso), bem como exibir todos os custos e gerenciar a configuração de custo. Por exemplo, orçamentos e exportações. Em função, o escopo do orçamento do Contrato do Cliente é o mesmo que a [função do Azure de Colaborador do Gerenciamento de Custos](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Leitor** – pode exibir configurações de cobrança, dados de custo e configuração de custo. Por exemplo, orçamentos e exportações. Em função, o escopo do orçamento do Contrato de Cliente é o mesmo que a [função do Azure de Leitor do Gerenciamento de Custos](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Gerenciador de faturas** – pode exibir e pagar faturas e pode exibir configuração e dados de custo. Por exemplo, orçamentos e exportações. Em função, o escopo do orçamento do Contrato de Cliente é o mesmo que a [função do Azure de Leitor do Gerenciamento de Custos](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Criador de assinatura do Azure** – pode criar assinaturas do Azure, exibir custos e gerenciar a configuração de custo. Por exemplo, orçamentos e exportações. Em função, esse escopo do orçamento do Contrato de Cliente é o mesmo que a função de proprietário da conta de registro de EA.

As assinaturas do Azure são aninhadas sob seções de fatura, como se estivessem sob contas de registro de EA. Os usuários de cobrança têm acesso aos dados de custo para as assinaturas e aos grupos de recursos que estão sob seus respectivos escopos. No entanto, eles não têm acesso para ver ou gerenciar recursos no portal do Azure. Os usuários de cobrança podem exibir os custos navegando para **Gerenciamento de Custos + Cobrança** na lista de serviços do portal do Azure. Em seguida, eles podem filtrar os custos para as assinaturas específicas e os grupos de recursos sobre os quais precisam fazer um relatório.

Os usuários de cobrança não têm acesso a grupos de gerenciamento porque eles não ficam explicitamente sob a conta de cobrança. No entanto, quando os grupos de gerenciamento são habilitados para a organização, todos os custos de assinatura são acumulados na conta de cobrança e no grupo de gerenciamento raiz porque os dois são restritos a um diretório. Os grupos de gerenciamento incluem apenas compras com base no uso. Compras como reservas e ofertas do Marketplace de terceiros não estão incluídas em grupos de gerenciamento. Portanto, a conta de cobrança e o grupo de gerenciamento raiz podem relatar totais diferentes. Para exibir esses custos, use a conta de cobrança ou o respectivo perfil de cobrança.

## <a name="aws-scopes"></a>Escopos da AWS

Após a conclusão da integração da AWS, veja [preparar e configurar a integração da AWS](aws-integration-set-up-configure.md). Os seguintes escopos estão disponíveis:

- **Conta de cobrança externa** – representa um contrato de cliente com um fornecedor terceirizado. Isso é semelhante à conta de cobrança do EA.

    Tipo de recurso: `Microsoft.CostManagement/externalBillingAccounts`

- **Assinatura externa** – representa uma conta operacional do cliente com um fornecedor terceirizado. Ela é semelhante a uma assinatura do Azure.

    Tipo de recurso: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Escopos de CSP (Provedor de Soluções na Nuvem)

Os seguintes escopos são compatíveis com CSPs com clientes em um Contrato de Cliente da Microsoft:

- **Conta de cobrança** – representa um contrato de cliente para vários produtos e serviços da Microsoft. As contas de cobrança do Contrato de Cliente não são funcionalmente iguais a registros de EA. Os registros de EA estão mais alinhados aos perfis de cobrança.

    Tipo de recurso: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Perfil de cobrança** – define as assinaturas que são incluídas em uma fatura. Os perfis de cobrança são o equivalente funcional de um registro de EA, pois esse é o escopo em que as notas fiscais são geradas. De maneira similar, as compras não baseadas em uso, como as do Marketplace e as reservas, estão disponíveis somente nesse escopo.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Cliente** – representa um grupo de assinaturas que estão associadas a um cliente específico que é integrado a um Contrato de Cliente da Microsoft por um parceiro.

Somente os usuários com as funções *Administrador global* e *Agente administrativo* podem gerenciar e ver os custos de contas de cobrança, perfis de cobrança e clientes diretamente no locatário do Azure do parceiro. Para obter mais informações sobre as funções do Partner Center, confira [Atribuir funções e permissões de usuários](/partner-center/permissions-overview).

O Gerenciamento de Custos do Azure só dará suporte a clientes de parceiros CSP se os clientes tiverem um Contrato de Cliente da Microsoft. Para clientes com suporte do CSP que ainda não estão em um Contrato de Cliente da Microsoft, confira [Partner Center](/azure/cloud-solution-provider/overview/partner-center-overview).

Os grupos de gerenciamento em escopos do CSP não têm suporte do Gerenciamento de Custos. Se você tiver uma assinatura do CSP e definir o escopo para um grupo de gerenciamento na análise de custo, você verá um erro semelhante a:

`Management group <ManagementGroupName> does not have any valid subscriptions`

## <a name="switch-between-scopes-in-cost-management"></a>Alternar entre escopos no Gerenciamento de Custos

Todas as exibições do Gerenciamento de Custos na portal do Azure incluem um controle oval de seleção **Escopo** na parte superior esquerda da exibição. Use-o para alterar o escopo rapidamente. Selecione o controle oval **Escopo** para abrir o seletor de escopo. Ele mostra contas de cobrança, o grupo de gerenciamento raiz e todas as assinaturas que não estão aninhadas no grupo de gerenciamento raiz. Para selecionar um escopo, selecione a tela de fundo para realçá-lo e, em seguida, escolha **Selecionar** na parte inferior. Para se aprofundar em escopos aninhados, como grupos de recursos em uma assinatura, selecione o link do nome do escopo. Para selecionar o escopo pai em qualquer nível aninhado, escolha **Selecionar este &lt;escopo&gt;** na parte superior do seletor de escopo.

## <a name="identify-the-resource-id-for-a-scope"></a>Identificar a ID do recurso para um escopo

Ao trabalhar com APIs de Gerenciamento de Custos, conhecer o escopo é crítico. Use as informações a seguir para criar o URI de escopo adequado para APIs de Gerenciamento de Custos.

### <a name="billing-accounts"></a>Contas de cobrança

1. Abra o portal do Azure e navegue até **Gerenciamento de Custos + Cobrança** na lista de serviços.
2. Selecione **Propriedades** no menu da conta de cobrança.
3. Copie a ID da conta de cobrança.
4. Seu escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Perfis de cobrança

1. Abra o portal do Azure e navegue até **Gerenciamento de Custos + Cobrança** na lista de serviços.
2. Selecione **Perfis de cobrança** no menu da conta de cobrança.
3. Selecione o nome do perfil de cobrança.
4. Selecione **Propriedades** no menu do perfil de cobrança.
5. Copie as IDs da conta de cobrança e do perfil de cobrança.
6. Seu escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Seções da fatura

1. Abra o portal do Azure e navegue até **Gerenciamento de Custos + Cobrança** na lista de serviços.
2. Selecione **Seções da fatura** no menu da conta de cobrança.
3. Selecione o nome da seção da fatura.
4. Selecione **Propriedades** no menu da seção da fatura.
5. Copie as IDs da conta de cobrança e da seção da fatura.
6. Seu escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Departamentos de EA

1. Abra o portal do Azure e navegue até **Gerenciamento de Custos + Cobrança** na lista de serviços.
2. Selecione **Departamentos** no menu da conta de cobrança.
3. Selecione o nome do departamento.
4. Selecione **Propriedades** no menu do departamento.
5. Copie as IDs da conta de cobrança e do departamento.
6. Seu escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Conta de registro de EA

1. Abra o portal do Azure e navegue até **Gerenciamento de Custos + Cobrança** na lista de serviços.
2. Selecione **Contas de registro** no menu da conta de cobrança.
3. Selecione o nome da conta de inscrição.
4. Selecione **Propriedades** no menu da conta de registro.
5. Copie a conta de cobrança e as IDs da conta de registro.
6. Seu escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Grupo de gerenciamento

1. Abra o portal do Azure e navegue até **Grupos de gerenciamento** na lista de serviços.
2. Navegue até o grupo de gerenciamento.
3. Copie a ID do grupo de gerenciamento da tabela.
4. Seu escopo é: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Subscription

1. Abra o portal do Azure e navegue até **Assinaturas** na lista de serviços.
2. Copie a ID da assinatura da tabela.
3. Seu escopo é: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Grupos de recursos

1. Abra o portal do Azure e navegue até **Grupos de recursos** na lista de serviços.
2. Selecione o nome do grupo de recursos.
3. Selecione **Propriedades** no menu do grupo de recursos.
4. Copie o valor do campo ID do recurso.
5. Seu escopo é: `"/subscriptions/{id}/resourceGroups/{name}"`

Atualmente, o Gerenciamento de Custos é compatível com o [Azure Global](https://management.azure.com) e o [Azure Government](https://management.usgovcloudapi.net). Para obter mais informações sobre o Azure Government, veja [Pontos de extremidade de API do Azure Global e Government](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping).

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).