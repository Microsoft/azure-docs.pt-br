---
title: Entender a cobrança de funções administrativas para contratos de clientes da Microsoft - Azure
description: Saiba mais sobre as funções de cobrança para contas no Azure de cobrança para contratos de clientes da Microsoft.
author: amberbhargava
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 12e492fee95192d0af49ff7b6a5be3144398fef8
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490435"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Entender as funções administrativas do contrato de cliente da Microsoft no Azure

Para gerenciar sua conta de cobrança para um contrato de cliente da Microsoft, use as funções descritas nas seções a seguir. Essas funções são além das funções internas que o Azure tem para controlar o acesso aos recursos. Para obter mais informações, consulte [Funções internas dos recursos do Azure](../role-based-access-control/built-in-roles.md).

Este artigo se aplicam a uma conta de cobrança para um contrato de cliente da Microsoft. Verifique se você tem acesso a um contrato de cliente da Microsoft.

## <a name="billing-role-definitions"></a>Definições de função de cobrança

A tabela a seguir descreve as funções de cobrança você usa para gerenciar sua conta de cobrança, perfis de cobrança e seções de nota fiscal.

|Role|DESCRIÇÃO|
|---|---|
|Proprietário da conta de cobrança|Gerenciar tudo para a conta de cobrança|
|Colaborador da conta de cobrança|Gerenciar tudo, exceto permissões na conta de cobrança|
|Leitor de conta de cobrança|Exibição somente leitura de tudo na conta de cobrança|
|Proprietário do perfil de cobrança|Gerenciar tudo para o perfil de cobrança|
|Colaborador de perfil de cobrança|Gerenciar tudo, exceto as permissões no perfil de cobrança|
|Leitor de perfil de cobrança|Exibição somente leitura de tudo no perfil de cobrança|
|Gerenciador de faturas|Exibir e pagar notas fiscais para perfil de cobrança|
|Proprietário da seção de nota fiscal|Gerenciar todos os itens na seção de nota fiscal|
|Colaborador da seção de nota fiscal|Gerenciar tudo, exceto as permissões na seção de nota fiscal|
|Leitor de seção de nota fiscal|Exibição somente leitura de tudo o que na seção de nota fiscal|
|Criador da assinatura do Azure|Criar assinaturas do Azure|

## <a name="billing-account-roles-and-tasks"></a>Tarefas e funções da conta de cobrança

Uma conta de cobrança lhe permite gerenciar a cobrança para sua organização. Você pode usar conta de cobrança para organizar os custos, encargos de monitoramento e faturas e controlar o acesso de cobrança para sua organização. Para obter mais informações, consulte [entender a conta de cobrança](billing-mca-overview.md#your-billing-account).

As tabelas a seguir mostram qual função você precisa concluir tarefas no contexto da conta de cobrança.

### <a name="manage-billing-account-permissions-and-properties"></a>Gerenciar propriedades e permissões de conta de cobrança

|Tarefa|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança|
|---|---|---|---|
|Exibir as permissões existentes para a conta de cobrança|✔|✔|✔|
|Conceder a outros usuários permissões para exibir e gerenciar a conta de cobrança|✔|✘|✘|
|Exibir propriedades de conta de cobrança, como o nome da empresa, endereço e muito mais|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Gerenciar perfis de cobrança para a conta de cobrança

|Tarefa|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança|
|---|---|---|---|
|Exibir todos os perfis de cobrança na conta|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Gerenciar as notas fiscais para a conta de cobrança

|Tarefa|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança|
|---|---|---|---|
|Exibir todas as notas fiscais na conta|✔|✔|✔|
|Baixar as faturas, os arquivos de uso e os encargos do Azure, folhas de preços e documentos na conta de imposto|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Gerenciar seções de nota fiscal para a conta de cobrança

|Tarefa|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança|
|---|---|---|---|
|Exibir todas as seções de nota fiscal na conta|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Gerenciar transações para a conta de cobrança

|Tarefa|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança|
|---|---|---|---|
|Exibir todas as transações para a conta de cobrança|✔|✔|✔|
|Exibir todos os produtos comprados para a conta|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Gerenciar assinaturas para a conta de cobrança

|Tarefa|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança|
|---|---|---|---|
|Exibir todas as assinaturas do Azure na conta de cobrança|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Tarefas e funções de perfil de cobrança

Um perfil de cobrança permite que você gerencie seus métodos de pagamento e notas fiscais. Uma fatura mensal é gerada para as assinaturas do Azure e outros produtos adquiridos usando o perfil de cobrança. Você pode usar os métodos de pagamento para pagar a nota fiscal. Para obter mais informações, consulte [entender a cobrança perfis](billing-mca-overview.md#billing-profiles).

As tabelas a seguir mostram qual função você precisa concluir tarefas no contexto do perfil de cobrança.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Gerenciar políticas, propriedades e permissões de perfil de cobrança

|Tarefa|Proprietário do perfil de cobrança|Colaborador de perfil de cobrança|Leitor de perfil de cobrança|Gerenciador de faturas|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir as permissões existentes para o perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|
|Conceder a outros usuários permissões para exibir e gerenciar o perfil de cobrança|✔|✘|✘|✘|✘|✘|✘|
|Exibir propriedades de perfil de cobrança, como número PO, preferência de nota fiscal de email e muito mais|✔|✔|✔|✔|✔|✔|✔|
|Atualizar propriedades de perfil de cobrança |✔|✔|✘|✘|✘|✘|✘|
|Exibir políticas aplicadas no perfil de cobrança, como habilitar as compras de reserva do Azure, habilitar compras no marketplace do Azure e muito mais|✔|✔|✔|✔|✔|✔|✔|
|Aplicar políticas no perfil de cobrança |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Gerenciar as notas fiscais para o perfil de cobrança

|Tarefa|Proprietário do perfil de cobrança|Colaborador de perfil de cobrança|Leitor de perfil de cobrança|Gerenciador de faturas|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir todas as notas fiscais para o perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|
|Baixar as faturas, os arquivos de uso e os encargos do Azure, folhas de preços e documentos para o perfil de cobrança de imposto|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Gerenciar seções de fatura para o perfil de cobrança

|Tarefa|Proprietário do perfil de cobrança|Colaborador de perfil de cobrança|Leitor de perfil de cobrança|Gerenciador de faturas|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir todas as seções de fatura para o perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|
|Criar nova seção de fatura para o perfil de cobrança|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Gerenciar transações para o perfil de cobrança

|Tarefa|Proprietário do perfil de cobrança|Colaborador de perfil de cobrança|Leitor de perfil de cobrança|Gerenciador de faturas|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir todas as transações de cobrança para o perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Gerenciar métodos de pagamento para cobrança de perfil

|Tarefa|Proprietário do perfil de cobrança|Colaborador de perfil de cobrança|Leitor de perfil de cobrança|Gerenciador de faturas|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança
|---|---|---|---|---|---|---|---|
|Métodos de pagamento do modo de exibição para o perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|
|Acompanhar o saldo de créditos do Azure para o perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Gerenciar assinaturas para o perfil de cobrança

|Tarefa|Proprietário do perfil de cobrança|Colaborador de perfil de cobrança|Leitor de perfil de cobrança|Gerenciador de faturas|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir todas as assinaturas do Azure para o perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Tarefas e funções de seção de nota fiscal

Uma seção de nota fiscal permite que você organize os custos em sua fatura. Você pode criar uma seção para organizar seus custos por departamento, ambiente de desenvolvimento, ou com base nas necessidades da sua organização. Conceda permissão para criar assinaturas do Azure para a seção a outros. Quaisquer encargos de uso e compras para as assinaturas e em seguida, Mostrar na seção da nota fiscal. Para obter mais informações, consulte [seção de nota fiscal de compreender](billing-mca-overview.md#invoice-sections).

As tabelas a seguir mostram qual função você precisa concluir tarefas no contexto das seções de nota fiscal.

### <a name="manage-invoice-section-permissions-and-properties"></a>Gerenciar permissões de seção de nota fiscal e propriedades

|Tarefas|Proprietário da seção de nota fiscal|Colaborador da seção de nota fiscal|Leitor de seção de nota fiscal|Criador da assinatura do Azure|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança | |
|---|---|---|---|---|---|---|---|---|
|Exibir todas as permissões na seção de nota fiscal|✔|✔|✔|✔|✔|✔|✔| |
|Conceder a outros usuários permissões para exibir e gerenciar a seção de nota fiscal|✔|✘|✘|✘|✘|✘|✘| |
|Exibir propriedades de seção de nota fiscal|✔|✔|✔|✔|✔|✔|✔| |
|Atualizar propriedades de seção de nota fiscal|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Gerenciar produtos para a seção de nota fiscal

|Tarefas|Proprietário da seção de nota fiscal|Colaborador da seção de nota fiscal|Leitor de seção de nota fiscal|Criador da assinatura do Azure|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir todos os produtos comprados na seção de nota fiscal|✔|✔|✔|✘|✔|✔|✔|
|Gerenciar a cobrança para os produtos para a seção de nota fiscal, como cancelar, desative a renovação automática e muito mais|✔|✔|✘|✘|✘|✘|✘|
|Seção de nota fiscal de alteração para os produtos|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Gerenciar assinaturas para a seção de nota fiscal

|Tarefas|Proprietário da seção de nota fiscal|Colaborador da seção de nota fiscal|Leitor de seção de nota fiscal|Criador da assinatura do Azure|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor de conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir todas as assinaturas do Azure para a seção de nota fiscal|✔|✔|✔|✘|✔|✔|✔|
|Seção de nota fiscal de alteração para as assinaturas|✔|✔|✘|✘|✘|✘|✘|
|Solicitar a propriedade da cobrança de assinaturas de usuários em outras contas de cobrança|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Tarefas e funções de cobrança da assinatura

A tabela a seguir mostra qual função você precisa concluir tarefas no contexto de uma assinatura.

|Tarefas|Proprietário da seção de nota fiscal|Colaborador da seção de nota fiscal|Leitor de seção de nota fiscal|Criador da assinatura do Azure|
|---|---|---|---|---|
|Criar assinaturas do Azure|✔|✔|✘|✔|
|Centro de custo para a assinatura de atualização|✔|✔|✘|✘|
|Seção de nota fiscal de alteração para a assinatura|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Gerenciar funções de cobrança no portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **custo de gerenciamento + cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. Selecione **controle de acesso (IAM)** em um escopo como conta de cobrança, perfil de cobrança ou seção de nota fiscal, onde você deseja conceder acesso.

4. A página de acesso (IAM) do controle lista de usuários e grupos que são atribuídos a cada função para esse escopo.

   ![Captura de tela que mostra a lista de administradores para a conta de cobrança](./media/billing-understand-mca-roles/billing-list-admins.png)

5. Para conceder acesso a um usuário, selecione **adicionar** da parte superior da página. Na lista suspensa de função, selecione uma função. Insira o endereço de email do usuário a quem você deseja conceder acesso. Selecione **salvar** para atribuir a função.

   ![Captura de tela que mostra a adição de um administrador para uma conta de cobrança](./media/billing-understand-mca-roles/billing-add-admin.png)

6. Para remover o acesso para um usuário, selecione o usuário com a atribuição de função que você deseja remover. Selecione Remover.

   ![Captura de tela que mostra a remoção de um administrador de uma conta de cobrança](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte
Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para saber mais sobre sua conta de cobrança:

- [Introdução à sua conta de cobrança para o contrato de cliente da Microsoft](billing-mca-overview.md)
- [Criar uma assinatura do Azure para sua conta de cobrança para o contrato de cliente da Microsoft](billing-mca-create-subscription.md)
