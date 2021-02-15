---
title: Configurar cobrança para o Contrato de Cliente da Microsoft – Azure
description: Saiba como configurar sua conta de cobrança para um Contrato de Cliente da Microsoft. Confira os pré-requisitos para a instalação e exiba os recursos adicionais disponíveis.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/20/2020
ms.author: banders
ms.openlocfilehash: 7e930ca938b2531fb001c327132f79f703b2ea74
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316404"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Configurar sua conta de cobrança para um Contrato de Cliente da Microsoft

Caso seu registro direto do Contrato Enterprise tenha expirado ou esteja prestes a expirar, assine um Contrato de Cliente da Microsoft para renovar seu registro. Este artigo descreve as alterações em sua cobrança existente após a configuração e orienta você na configuração de sua nova conta de cobrança. Atualmente, Contratos Enterprise indiretos em expiração não podem ser renovados com um Contrato de Cliente da Microsoft.

A renovação inclui as seguintes etapas:

1. Aceitar o novo Contrato de Cliente da Microsoft. Trabalhar com seu representante de campo da Microsoft para entender os detalhes e aceitar o novo contrato.
2. Configurar a conta de cobrança criada para o novo Contrato de Cliente da Microsoft.

Para configurar a conta de cobrança, você precisará fazer a transição da cobrança de assinaturas do Azure do registro do Contrato Enterprise para a nova conta. A configuração não afeta os serviços do Azure que estão em execução em suas assinaturas. No entanto, ela altera a maneira como você gerenciará a cobrança de suas assinaturas.

- Em vez do [Portal do EA](https://ea.azure.com), você gerenciará seus serviços e sua cobrança do Azure no [portal do Azure](https://portal.azure.com).
- Você obterá uma fatura mensal digital de seus encargos. Exiba e analise a fatura na página Gerenciamento de Custos do Azure + Cobrança.
- Em vez de departamentos e da conta no registro do Contrato Enterprise, você usará a estrutura e os escopos do orçamento da nova conta para gerenciar e organizar sua cobrança.

Antes de iniciar a configuração, recomendamos que você faça as seguintes ações:

- **Entender sua nova conta de cobrança**
  - Sua nova conta simplifica a cobrança para sua organização. [Obtenha uma visão geral rápida de sua nova conta de cobrança](../understand/mca-overview.md)
- **Verificar seu acesso para concluir a configuração**
  - Somente os usuários com determinadas permissões administrativas podem concluir a configuração. Verifique se você tem o [acesso necessário para concluir a configuração](#access-required-to-complete-the-setup).
- **Entender as alterações em sua hierarquia de cobrança**
  - Sua nova conta de cobrança é organizada de maneira diferente de seu registro do Contrato Enterprise. [Entender as alterações em sua hierarquia de cobrança na nova conta](#understand-changes-to-your-billing-hierarchy).
- **Entender as alterações no acesso de administradores de cobrança**
  - Os administradores do registro do Contrato Enterprise obtêm acesso aos escopos do orçamento na nova conta.[Entenda as alterações no acesso deles](#changes-to-billing-administrator-access).
- **Exibir os recursos do Contrato Enterprise que foram substituídos pela nova conta**
  - Exiba os recursos do registro do Contrato Enterprise que foram substituídos por recursos na nova conta.
- **Exibir as respostas às perguntas mais comuns**
  - Exiba [informações adicionais](#additional-information) para saber mais sobre a configuração.

## <a name="access-required-to-complete-the-setup"></a>Acesso necessário para concluir a configuração

Para concluir a configuração, você precisará do seguinte acesso:

- Proprietário do perfil de cobrança que foi criado no momento da assinatura do Contrato de Cliente da Microsoft. Para saber mais sobre os perfis de cobrança, confira [Entender os perfis de cobrança](../understand/mca-overview.md#billing-profiles).

- Administrador corporativo no registro renovado.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Se você não é um administrador corporativo no registro

Solicite aos administradores corporativos do registro a conclusão da configuração de sua conta de cobrança.

1. Entre no portal do Azure usando o link no email enviado a você no momento de sua assinatura do Contrato de Cliente da Microsoft.

2. Caso não tenha o email, entre usando o link a seguir. Substitua `<enrollmentNumber>` pelo número de registro do Contrato Enterprise que foi renovado.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. Selecione os administradores corporativos para os quais deseja enviar a solicitação.

   ![Captura de tela que mostra o convite aos administradores corporativos](./media/mca-setup-account/ea-mca-invite-admins.png)

4. Selecione **Enviar solicitação** .

   Os administradores receberão um email com instruções para concluir a configuração.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Se você não é um proprietário do perfil de cobrança

O usuário de sua organização que assinou o Contrato de Cliente da Microsoft é adicionado como o proprietário no perfil de cobrança. Solicite ao usuário que o adicione como um proprietário, de modo que você possa concluir a configuração.

## <a name="understand-changes-to-your-billing-hierarchy"></a>Entender as alterações em sua hierarquia de cobrança

Sua nova conta de cobrança simplifica a cobrança para sua organização, fornecendo funcionalidades avançadas de cobrança e gerenciamento de custos. O diagrama a seguir explica como a cobrança é organizada na nova conta de cobrança.

![Imagem da hierarquia do EA-MCA pós-transição](./media/mca-setup-account/mca-post-transition-hierarchy.png)

1. Você usará a conta de cobrança para gerenciar a cobrança de seu Contrato de Cliente da Microsoft. Os administradores corporativos se tornam proprietários da conta de cobrança. Para saber mais sobre a conta de cobrança, confira [Entender a conta de cobrança](../understand/mca-overview.md#your-billing-account).
2. Você usará o perfil de cobrança para gerenciar a cobrança de sua organização, de maneira semelhante ao registro do Contrato Enterprise. Os administradores corporativos se tornam proprietários do perfil de cobrança. Para saber mais sobre os perfis de cobrança, confira [Entender os perfis de cobrança](../understand/mca-overview.md#billing-profiles).
3. Você usará uma seção da fatura para organizar os custos conforme suas necessidades, de maneira semelhante aos departamentos no registro do Contrato Enterprise. O departamento se torna as seções da fatura e os administradores do departamento se tornam proprietários das respectivas seções da fatura. Para saber mais sobre as seções da fatura, confira [Entender as seções da fatura](../understand/mca-overview.md#invoice-sections).
4. Não há suporte na nova conta de cobrança para as contas que foram criadas no Contrato Enterprise. As assinaturas da conta pertencem à respectiva seção da fatura de seus departamentos. Os proprietários da conta podem criar e gerenciar assinaturas para as seções da fatura.

## <a name="changes-to-billing-administrator-access"></a>Alterações no acesso de administrador de cobrança

Dependendo do acesso, os administradores de cobrança no registro do Contrato Enterprise obtêm acesso aos escopos do orçamento na nova conta. A seguinte tabela explica a alteração no acesso durante a configuração:

| Função existente | Função pós-transição |
| --- | --- |
| **Administrador corporativo (somente leitura = Não)** | **– Proprietário da conta de cobrança** </br> Gerencia tudo na conta de cobrança </br> **– Proprietário do perfil de cobrança** </br> Gerencia tudo no perfil de cobrança </br> **– Proprietário da seção da fatura em todas as seções da fatura** </br> Gerencia tudo nas seções da fatura |
| **Administrador corporativo (somente leitura = Sim)** | **– Leitor da conta de cobrança** </br> Exibição somente leitura de tudo na conta de cobrança</br> **- Leitor do perfil de cobrança** </br> Exibição somente leitura de tudo no perfil de cobrança</br>**- Leitor da seção da fatura em todas as seções da fatura**</br> Exibição somente leitura de tudo nas seções da fatura|
| **Administrador do departamento (somente leitura = Não)** |**- Proprietário da seção da fatura na seção da fatura criada para seu respectivo departamento** </br>Gerencia tudo na seção da fatura|
| **Administrador do departamento (somente leitura = Sim)**|**- Leitor da seção da fatura na seção da fatura criada para seu respectivo departamento**</br> Exibição somente leitura de tudo na seção da fatura|
| **Proprietário da conta** | **- Criador de assinatura do Azure na seção da fatura criada para seu respectivo departamento** </br>  Cria assinaturas do Azure na seção da fatura|

Um locatário do Azure AD (Active Directory) é selecionado para a nova conta de cobrança durante a aceitação do Contrato de Cliente da Microsoft. Se um locatário não existir para sua organização, ele será criado. O locatário representa sua organização no Azure Active Directory. Os administradores de locatários globais em sua organização usam o locatário para gerenciar o acesso de aplicativos e dados em sua organização.

Sua nova conta só dá suporte aos usuários do locatário que foi selecionado no momento da assinatura do Contrato de Cliente da Microsoft. Se os usuários com permissão administrativa no Contrato Enterprise fizerem parte do locatário, eles terão acesso à nova conta de cobrança durante a configuração. Se eles não fizerem parte do locatário, não poderão acessar a nova conta de cobrança, a menos que você os convide.

Quando você convida os usuários, eles são adicionados ao locatário como usuários convidados e obtêm acesso à conta de cobrança. Para convidar os usuários, o acesso para convidado precisa ser ativado para o locatário. Para obter mais informações, confira [Controlar o acesso para convidado no Azure Active Directory](/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Se o acesso para convidado estiver desligado, contate os administradores globais de seu locatário para ativá-lo. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>Exibir os recursos substituídos

Os recursos do Contrato Enterprise a seguir foram substituídos por novos recursos na conta de cobrança de um Contrato de Cliente da Microsoft.

### <a name="enterprise-agreement-accounts"></a>Contas do Contrato Enterprise

Não há suporte na nova conta de cobrança para as contas que foram criadas no registro do Contrato Enterprise. As assinaturas da conta pertencem à seção da fatura criada para seus respectivos departamentos. Os proprietários da conta se tornam criadores de assinatura do Azure e podem criar e gerenciar assinaturas nas seções da fatura.

### <a name="notification-contacts"></a>Contatos de notificação

Os contatos de notificação recebem comunicações por email sobre o Contrato Enterprise do Azure. Não há suporte para eles na nova conta de cobrança. Emails sobre faturas e créditos Azure são enviados aos usuários que têm acesso aos perfis de cobrança em sua conta de cobrança.

### <a name="spending-quotas"></a>Cotas de gastos

As cotas de gastos definidas para os departamentos no registro do Contrato Enterprise foram substituídas por orçamentos na nova conta de cobrança. Um orçamento é criado para cada cota de gastos definida nos departamentos de seu registro. Para obter mais informações sobre orçamentos, confira [Tutorial: Criar e gerenciar orçamentos do Azure](../costs/tutorial-acm-create-budgets.md).

### <a name="cost-centers"></a>Centros de custo

O centro de custo definido nas assinaturas do Azure no registro do Contrato Enterprise é transferido para a nova conta de cobrança. No entanto, não há suporte para os centros de custo de departamentos e contas do Contrato Enterprise.

## <a name="additional-information"></a>Informações adicionais

As seções a seguir fornecem informações adicionais sobre como configurar sua conta de cobrança.

### <a name="no-service-downtime"></a>Não há tempo de inatividade do serviço

Os serviços do Azure em sua assinatura continuam sendo executados sem nenhuma interrupção. Apenas fazemos a transição do relacionamento de cobrança para suas assinaturas do Azure. Não haverá impacto em recursos, grupos de recursos ou grupos de gerenciamento existentes.

### <a name="user-access-to-azure-resources"></a>Acesso do usuário aos recursos do Azure

O acesso aos recursos do Azure que foi definido usando o Azure RBAC (controle de acesso baseado em função do Azure) não é afetado durante a transição.

### <a name="azure-reservations"></a>Reservas do Azure

As Reservas do Azure no registro do Contrato Enterprise são migradas para sua nova conta de cobrança. Durante a transição, não haverá alterações nos descontos de reserva que são aplicados às suas assinaturas.

### <a name="azure-marketplace-products"></a>Produtos do Azure Marketplace

Todos os produtos do Azure Marketplace em seu registro do Contrato Enterprise são migrados junto com as assinaturas. Não haverá nenhuma alteração no acesso ao serviço dos produtos do Marketplace durante a transição.

### <a name="support-plan"></a>Plano de suporte

Os benefícios de suporte não são transferidos como parte da transição. Compre um novo plano de suporte para obter os benefícios das assinaturas do Azure em sua nova conta de cobrança.

### <a name="past-charges-and-balance"></a>Encargos e saldo anteriores

Os encargos e o saldo de créditos anteriores à transição podem ser exibidos no registro do Contrato Enterprise por meio do portal do Azure. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Quando a configuração deve ser concluída?

Conclua a configuração de sua conta de cobrança antes da expiração do registro do Contrato Enterprise. Caso o registro expire, os serviços em suas assinaturas do Azure ainda continuarão em execução sem interrupções. No entanto, você será cobrado pelos serviços com base nas taxas pagas conforme o uso.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Alterações no registro do Contrato Enterprise após a configuração

As assinaturas do Azure criadas para o registro do Contrato Enterprise após a transição podem ser migradas manualmente para a nova conta de cobrança. Para obter mais informações, confira [Obter a propriedade de cobrança das assinaturas do Azure de outros usuários](mca-request-billing-ownership.md). Para mover as Reservas do Azure adquiridas após a transição, [contate o Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Forneça também aos usuários o acesso à conta de cobrança após a transição. Para obter mais informações, confira [Gerenciar funções de cobrança no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Reverter a transição

A transição não pode ser revertida. Depois que a cobrança de suas assinaturas do Azure é transferida para a nova conta de cobrança, não é possível revertê-la para o registro do Contrato Enterprise.

### <a name="closing-your-browser-during-setup"></a>Como fechar o navegador durante a configuração

Antes de selecionar **Iniciar transição** , feche o navegador. Volte à configuração usando o link que você obteve no email e inicie a transição. Se você fechar o navegador após o início da transição, a transição continuará sendo executada. Volte à página de status da transição para monitorar o status mais recente da transição. Você receberá um email quando a transição for concluída.

## <a name="complete-the-setup-in-the-azure-portal"></a>Concluir a configuração no portal do Azure

Para concluir a configuração, você precisa ter acesso à nova conta de cobrança e ao registro do Contrato Enterprise. Para obter mais informações, confira [Acesso necessário para concluir a configuração de sua conta de cobrança](#access-required-to-complete-the-setup).

1. Entre no portal do Azure usando o link no email enviado a você no momento de sua assinatura do Contrato de Cliente da Microsoft.

2. Caso não tenha o email, entre usando o link a seguir. Substitua `<enrollmentNumber>` pelo número de registro do Contrato Enterprise que foi renovado.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. Selecione **Iniciar transição** na última etapa da configuração. Depois de selecionar Iniciar transição:

    ![Captura de tela que mostra o assistente de configuração](./media/mca-setup-account/ea-mca-set-up-wizard.png)

    - Uma hierarquia de cobrança correspondente à hierarquia do Contrato Enterprise é criada na nova conta de cobrança. Para obter mais informações, confira [Entender as alterações em sua hierarquia de cobrança](#understand-changes-to-your-billing-hierarchy).
    - Os administradores de seu registro do Contrato Enterprise obtêm acesso à nova conta de cobrança, de modo que continuem a gerenciar a cobrança de sua organização.
    - A cobrança de suas assinaturas do Azure é transferida para a nova conta. **Não haverá nenhum impacto nos serviços do Azure durante essa transição. Eles continuarão sendo executados sem nenhuma interrupção** .
    - Caso você tenha Reservas do Azure, elas serão migradas para sua nova conta de cobrança com os mesmos benefícios e o mesmo prazo.

4. Monitore o status da transição na página **Status da transição** .

   ![Captura de tela que mostra o status da transição](./media/mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-setup"></a>Validar a configuração da conta de cobrança

 Valide o seguinte para garantir que sua nova conta de cobrança seja configurada corretamente:

### <a name="azure-subscriptions"></a>Assinaturas do Azure

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança** .

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/mca-setup-account/search-cmb.png)

3. Selecione a conta de cobrança. A conta de cobrança será do tipo **Contrato de Cliente da Microsoft** .

4. Selecione **Assinaturas do Azure** no lado esquerdo.

   ![Captura de tela que mostra a lista de assinaturas](./media/mca-setup-account/mca-subscriptions-post-transition.png)

As assinaturas do Azure que são transferidas do registro do Contrato Enterprise para a nova conta de cobrança são exibidas na página de assinaturas do Azure. Se acreditar que alguma assinatura esteja ausente, faça a transição manual da cobrança da assinatura no portal do Azure. Para obter mais informações, confira [Obter a propriedade de cobrança das assinaturas do Azure de outros usuários](mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Reservas do Azure

As reservas do Azure no registro do Contrato Enterprise serão migradas para sua nova conta de cobrança com os mesmos benefícios e o mesmo prazo. As transações concluídas antes da transição não serão exibidas em sua nova conta de cobrança. No entanto, verifique se os benefícios das reservas são aplicados às suas assinaturas visitando a [página das reservas do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

### <a name="access-of-enterprise-administrators-on-the-billing-account"></a>Acesso de administradores corporativos na conta de cobrança

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança** .

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/mca-setup-account/search-cmb.png)

3. Selecione a conta de cobrança de seu **Contrato de Cliente da Microsoft** .

4. Selecione **Controle de acesso (IAM)** no lado esquerdo.

   ![Captura de tela que mostra o acesso de administradores corporativos listados como proprietários da conta de cobrança após a transição.](./media/mca-setup-account/mca-ea-admins-ba-access-post-transition.png)

Os administradores corporativos são listados como proprietários da conta de cobrança, enquanto os administradores corporativos com permissões somente leitura são listados como leitores da conta de cobrança. Se acreditar que o acesso de um administrador corporativo está ausente, forneça acesso a ele no portal do Azure. Para obter mais informações, confira [Gerenciar funções de cobrança no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Acesso de administradores corporativos no perfil de cobrança

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança** .

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/mca-setup-account/search-cmb.png)

3. Selecione o perfil de cobrança criado para o seu registro. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança. Na conta de cobrança, selecione Perfis de cobrança e, em seguida, o perfil de cobrança.

4. Selecione **Controle de acesso (IAM)** no lado esquerdo.

   ![Captura de tela que mostra o acesso de administradores corporativos listados como proprietários do perfil de cobrança após a transição.](./media/mca-setup-account/mca-ea-admins-bp-access-post-transition.png)

Os administradores corporativos são listados como proprietários do perfil de cobrança, enquanto os administradores corporativos com permissões somente leitura são listados como leitores do perfil de cobrança. Se acreditar que o acesso de um administrador corporativo está ausente, forneça acesso a ele no portal do Azure. Para obter mais informações, confira [Gerenciar funções de cobrança no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Acesso de administradores corporativos, administradores do departamento e proprietários da conta nas seções da fatura

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança** .

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/mca-setup-account/search-cmb.png).

3. Selecione uma seção da fatura. As seções da fatura têm o mesmo nome de seus respectivos departamentos nos registros do Contrato Enterprise. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança. Na conta de cobrança, selecione **Perfis de cobrança** e, em seguida, **Seções da fatura** . Na lista de seções da fatura, selecione uma seção da fatura.

   ![Captura de tela que mostra a lista de seções da fatura após a transição](./media/mca-setup-account/mca-invoice-sections-post-transition.png)

4. Selecione **Controle de acesso (IAM)** no lado esquerdo.

    ![Captura de tela que mostra o acesso de administradores do departamento e da conta após a transição](./media/mca-setup-account/mca-department-account-admins-access-post-transition.png)

Os administradores corporativos e os administradores do departamento são listados como proprietários da seção da fatura ou leitores da seção da fatura, enquanto os proprietários da conta no departamento são listados como criadores de assinatura do Azure. Repita a etapa para todas as seções da fatura, a fim de verificar o acesso de todos os departamentos no registro do Contrato Enterprise. Os proprietários da conta que não faziam parte de nenhum departamento obterão permissão em uma seção da fatura chamada **Seção da fatura padrão** . Se acreditar que o acesso de um administrador está ausente, forneça acesso a ele no portal do Azure. Para obter mais informações, confira [Gerenciar funções de cobrança no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- [Introdução à sua nova conta de cobrança](../understand/mca-overview.md)

- [Concluir as tarefas do Contrato Enterprise em sua conta de cobrança para um Contrato de Cliente da Microsoft](mca-enterprise-operations.md)

- [Gerenciar o acesso à sua conta de cobrança](understand-mca-roles.md)