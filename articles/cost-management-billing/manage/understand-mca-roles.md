---
title: Funções de cobrança para Contrato de Cliente da Microsoft – Azure
description: Saiba mais sobre as funções de cobrança para contas de cobrança no Azure para Contratos de Cliente da Microsoft.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 12/17/2020
ms.author: banders
ms.openlocfilehash: 511c0ecdef45a882bcc3a427d5ec602b3ebabc66
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673824"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Entender as funções administrativas do Contrato de Cliente da Microsoft no Azure

Para gerenciar sua conta de cobrança para um Contrato de Cliente da Microsoft, use as funções descritas nas seções a seguir. Essas funções existem além das funções internas que o Azure tem para controlar o acesso aos recursos. Para obter mais informações, veja [Funções internas do Azure](../../role-based-access-control/built-in-roles.md).

Este artigo aplica-se a uma conta de cobrança para um Contrato de Cliente da Microsoft. Verifique se você tem acesso a um Contrato de Cliente da Microsoft.

## <a name="billing-role-definitions"></a>Definições da função de cobrança

A tabela a seguir descreve as funções de cobrança usadas para gerenciar sua conta de cobrança, os perfis de cobrança e as seções da fatura.

|Função|Descrição|
|---|---|
|Proprietário da conta de cobrança|Gerencia tudo para a conta de cobrança|
|Colaborador da conta de cobrança|Gerencia tudo, exceto as permissões na conta de cobrança|
|Leitor da conta de cobrança|Exibição somente leitura de tudo na conta de cobrança|
|Proprietário do perfil de cobrança|Gerencia tudo para o perfil de cobrança|
|Colaborador do perfil de cobrança|Gerencia tudo, exceto as permissões no perfil de cobrança|
|Leitor do perfil de cobrança|Exibição somente leitura de tudo no perfil de cobrança|
|Gerenciador de faturas|Veja as faturas do perfil de cobrança|
|Proprietário da seção da fatura|Gerencia tudo na seção da fatura|
|Colaborador da seção da fatura|Gerencia tudo, exceto as permissões na seção da fatura|
|Leitor da seção da fatura|Exibição somente leitura de tudo na seção da fatura|
|Criador de assinatura do Azure|Cria assinaturas do Azure|

## <a name="billing-account-roles-and-tasks"></a>Funções e tarefas da conta de cobrança

Uma conta de cobrança permite que você gerencie a cobrança de sua organização. Use a conta de cobrança para organizar custos, monitorar encargos e faturas e controlar o acesso de cobrança para a sua organização. Para obter mais informações, confira [Entender a conta de cobrança](../understand/mca-overview.md#your-billing-account).

As tabelas a seguir mostram a função que você precisa para concluir tarefas no contexto da conta de cobrança.

### <a name="manage-billing-account-permissions-and-properties"></a>Gerencia permissões e propriedades da conta de cobrança

|Tarefa|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança|
|---|---|---|---|
|Exibir as permissões existentes para a conta de cobrança|✔|✔|✔|
|Concede a outros usuários as permissões para exibir e gerenciar a conta de cobrança|✔|✘|✘|
|Exibir propriedades da conta de cobrança, como nome da empresa, endereço, entre outros|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Gerencia perfis de cobrança da conta de cobrança

|Tarefa|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança|
|---|---|---|---|
|Exibir todos os perfis de cobrança na conta|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Gerencia faturas da conta de cobrança

|Tarefa|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança|
|---|---|---|---|
|Exibir todas as faturas na conta|✔|✔|✔|
|Baixar faturas, arquivos de uso e encargos do Azure, planilhas de preços e documentos de imposto na conta|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Gerencia seções da fatura da conta de cobrança

|Tarefa|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança|
|---|---|---|---|
|Exibir todas as seções da fatura na conta|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Gerencia transações da conta de cobrança

|Tarefa|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança|
|---|---|---|---|
|Exibir todas as transações de cobrança da conta|✔|✔|✔|
|Exibir todos os produtos comprados na conta|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Gerencia assinaturas da conta de cobrança

|Tarefa|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança|
|---|---|---|---|
|Exibir todas as assinaturas do Azure na conta de cobrança|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Funções e tarefas do perfil de cobrança

Um perfil de cobrança permite que você gerencie suas faturas e suas formas de pagamento. Uma fatura mensal é gerada para as assinaturas do Azure e outros produtos adquiridos com o uso do perfil de cobrança. Você usa as formas de pagamentos para pagar a fatura. Para obter mais informações, confira [Entender os perfis de cobrança](../understand/mca-overview.md#billing-profiles).

As tabelas a seguir mostram a função que você precisa para concluir tarefas no contexto do perfil de cobrança.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Gerencia permissões, propriedades e políticas do perfil de cobrança

|Tarefa|Proprietário do perfil de cobrança|Colaborador do perfil de cobrança|Leitor do perfil de cobrança|Gerenciador de faturas|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir as permissões existentes para o perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|
|Concede a outros usuários as permissões para exibir e gerenciar o perfil de cobrança|✔|✘|✘|✘|✘|✘|✘|
|Exibir propriedades do perfil de cobrança como número da OC, preferência de fatura por email, entre outros|✔|✔|✔|✔|✔|✔|✔|
|Atualizar propriedades do perfil de cobrança |✔|✔|✘|✘|✘|✘|✘|
|Exibir as políticas aplicadas no perfil de cobrança, como habilitar compras de reserva do Azure, habilitar compras do Azure Marketplace, entre outros|✔|✔|✔|✔|✔|✔|✔|
|Aplicar políticas no perfil de cobrança |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Gerencia faturas do perfil de cobrança

|Tarefa|Proprietário do perfil de cobrança|Colaborador do perfil de cobrança|Leitor do perfil de cobrança|Gerenciador de faturas|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir todas as faturas do perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|
|Baixar faturas, arquivos de uso e encargos do Azure, planilhas de preços e documentos de imposto no perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Gerencia seções da fatura do perfil de cobrança

|Tarefa|Proprietário do perfil de cobrança|Colaborador do perfil de cobrança|Leitor do perfil de cobrança|Gerenciador de faturas|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir todas as seções da fatura do perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|
|Criar uma seção da fatura para o perfil de cobrança|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Gerencia transações do perfil de cobrança

|Tarefa|Proprietário do perfil de cobrança|Colaborador do perfil de cobrança|Leitor do perfil de cobrança|Gerenciador de faturas|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir todas as transações de cobrança do perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Gerencia formas de pagamento do perfil de cobrança

|Tarefa|Proprietário do perfil de cobrança|Colaborador do perfil de cobrança|Leitor do perfil de cobrança|Gerenciador de faturas|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir formas de pagamento do perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|
|Acompanha o saldo de créditos Azure do perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Gerencia assinaturas do perfil de cobrança

|Tarefa|Proprietário do perfil de cobrança|Colaborador do perfil de cobrança|Leitor do perfil de cobrança|Gerenciador de faturas|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir todas as assinaturas do Azure no perfil de cobrança|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Funções e tarefas da seção da fatura

Uma seção da fatura permite que você organize os custos em sua fatura. Você pode criar uma seção para organizar os custos por departamento, ambiente de desenvolvimento ou conforme as necessidades de sua organização. Conceda a outros usuários a permissão para criar assinaturas do Azure na seção. Todas as compras e todos os encargos de uso das assinaturas são então mostrados na seção da fatura. Para obter mais informações, confira [Entender a seção da fatura](../understand/mca-overview.md#invoice-sections).

As tabelas a seguir mostram a função que você precisa para concluir tarefas no contexto das seções da fatura.

### <a name="manage-invoice-section-permissions-and-properties"></a>Gerencia permissões e propriedades da seção da fatura

|Tarefas|Proprietário da seção da fatura|Colaborador da seção da fatura|Leitor da seção da fatura|Criador de assinatura do Azure|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança |
|---|---|---|---|---|---|---|---|
|Exibir todas as permissões na seção da fatura|✔|✔|✔|✔|✔|✔|✔|
|Concede a outros usuários as permissões para exibir e gerenciar a seção da fatura|✔|✘|✘|✘|✘|✘|✘|
|Exibir propriedades da seção da fatura|✔|✔|✔|✔|✔|✔|✔|
|Atualizar propriedades da seção da fatura|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Gerencia produtos da seção da fatura

|Tarefas|Proprietário da seção da fatura|Colaborador da seção da fatura|Leitor da seção da fatura|Criador de assinatura do Azure|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir todos os produtos comprados na seção da fatura|✔|✔|✔|✘|✔|✔|✔|
|Gerencia a cobrança de produtos da seção da fatura, como cancelar, desligar renovação automática, entre outros|✔|✔|✘|✘|✘|✘|✘|
|Alterar a seção da fatura para os produtos|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Gerenciar as assinaturas da seção da fatura

|Tarefas|Proprietário da seção da fatura|Colaborador da seção da fatura|Leitor da seção da fatura|Criador de assinatura do Azure|Proprietário da conta de cobrança|Colaborador da conta de cobrança|Leitor da conta de cobrança
|---|---|---|---|---|---|---|---|
|Exibir todas as assinaturas do Azure da seção da fatura|✔|✔|✔|✘|✔|✔|✔|
|Alterar a seção da fatura para as assinaturas|✔|✔|✘|✘|✘|✘|✘|
|Solicitar a propriedade de cobrança das assinaturas de usuários em outras contas de cobrança|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Funções e tarefas da cobrança de assinatura

A tabela a seguir mostra a função que você precisa para concluir tarefas no contexto de uma assinatura.

|Tarefas|Proprietário da seção da fatura|Colaborador da seção da fatura|Leitor da seção da fatura|Criador de assinatura do Azure|
|---|---|---|---|---|
|Cria assinaturas do Azure|✔|✔|✘|✔|
|Atualizar o centro de custo na assinatura|✔|✔|✘|✘|
|Altera a seção da fatura na assinatura|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Gerencia as funções de cobrança no portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. Selecione **Controle de acesso (IAM)** em um escopo, como conta de cobrança, perfil de cobrança ou seção da fatura, no qual deseja fornecer acesso.

4. A página Controle de acesso (IAM) lista os usuários e os grupos que são atribuídos a cada função nesse escopo.

   ![Captura de tela que mostra a lista de administradores da conta de cobrança](./media/understand-mca-roles/billing-list-admins.png)

5. Para fornecer acesso a um usuário, selecione **Adicionar** na parte superior da página. Na lista suspensa Função, selecione uma função. Insira o endereço de email do usuário a quem você deseja permitir acesso. Selecione **Salvar** para atribuir a função.

   ![Captura de tela que mostra a adição de um administrador a uma conta de cobrança](./media/understand-mca-roles/billing-add-admin.png)

6. Para remover o acesso de um usuário, selecione o usuário com a atribuição de função que você deseja remover. Selecione Remover.

   ![Captura de tela que mostra a remoção de um administrador de uma conta de cobrança](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte
Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para saber mais sobre a sua conta de cobrança:

- [Introdução à conta de cobrança para o Contrato de Cliente da Microsoft](../understand/mca-overview.md)
- [Criar uma assinatura do Azure em sua conta de cobrança para o Contrato de Cliente da Microsoft](create-subscription.md)
