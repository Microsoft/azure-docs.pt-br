---
title: Configurar sua conta de cobrança para um contrato de cliente da Microsoft - Azure
description: Saiba como configurar sua conta de cobrança para um contrato de cliente da Microsoft.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 947bfe85d94a5d11eeb54bd6b24c4c515af024d4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490689"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Configurar sua conta de cobrança para um contrato de cliente da Microsoft

Se o registro do Enterprise Agreement tiver expirado ou prestes a expirar, você pode assinar um contrato de cliente da Microsoft para renovar seu registro. Este artigo descreve as alterações em sua cobrança existente após a configuração e o orienta através da instalação de sua nova conta de cobrança. A renovação inclui as seguintes etapas:

1. Aceite o novo contrato ao cliente Microsoft. Trabalhar com seu representante de campo da Microsoft para entender os detalhes e aceitar o novo contrato.
2. Configure a nova conta de cobrança que é criada para o novo contrato ao cliente Microsoft.

Para configurar a conta de cobrança, transição a cobrança de assinaturas do Azure do seu registro do Enterprise Agreement para a nova conta. A configuração não afeta os serviços do Azure que são executados em suas assinaturas. No entanto, ele muda a maneira que você gerenciará a cobrança para suas assinaturas.

- Em vez do [portal de EA](https://ea.azure.com), você gerenciará seus serviços do Azure e a cobrança, no [portal do Azure](https://portal.azure.com).
- Você receberá uma fatura mensal digital para os encargos. Você pode exibir e analisar a nota fiscal no gerenciamento de custos do Azure + página cobrança.
- Em vez de departamentos e conta em seu registro de Enterprise Agreement, você usará a estrutura e escopos a partir da nova conta de cobrança para gerenciar e organizar sua cobrança.

Antes de iniciar a instalação, é recomendável que você faça o seguinte:

- **Entender sua nova conta de cobrança**
  - Simplifica a sua nova conta de cobrança para sua organização. [Obtenha uma visão geral da sua nova conta de cobrança](billing-mca-overview.md)
- **Verifique se o acesso para concluir a instalação**
  - Somente os usuários com determinadas permissões administrativas podem concluir a instalação. Verifique se você tem o [acesso necessário para concluir a configuração](#access-required-to-complete-the-setup).
- **Entender as alterações na sua hierarquia de cobrança**
  - Você nova conta de cobrança é organizados de forma diferente que o seu registro de Enterprise Agreement. [Entender as alterações para a hierarquia na nova conta de cobrança](#understand-changes-to-your-billing-hierarchy).
- **Entender as alterações para o acesso dos administradores de cobrança**
  - Os administradores da sua inscrição de Enterprise Agreement obtém acesso para os escopos na nova conta de cobrança. [Entender as alterações para o acesso](#changes-to-billing-administrator-access).
- **Exibir recursos do Enterprise Agreement que são substituídos pela nova conta**
  - Recursos de exibição do registro de contrato Enterprise que são substituídos pelos recursos na nova conta.
- **Exibir as respostas às perguntas mais comuns**
  - Modo de exibição [informações adicionais](#additional-information) para saber mais sobre a instalação.

## <a name="access-required-to-complete-the-setup"></a>Acesso necessário para concluir a instalação

Para concluir a instalação, você precisa ter o seguinte acesso:

- Proprietário do perfil de cobrança que foi criado quando o contrato de cliente da Microsoft foi assinado. Para saber mais sobre perfis de cobrança, consulte [entender a cobrança perfis](billing-mca-overview.md#billing-profiles).

- Administrador corporativo no registro que é renovado.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Se você não tiver um administrador corporativo no registro

Você pode solicitar que os administradores de empresa do registro para concluir a configuração de sua conta de cobrança.

1. Entrar no portal do Azure usando o link no email que foi enviado a você quando conectado o contrato do cliente Microsoft.

2. Se outra pessoa em sua organização o contrato assinado ou você não tiver o email, entre usando o link a seguir. Substitua **enrollmentNumber** com o número de registro do seu contrato enterprise que foi renovado.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Selecione os administradores de empresa que você deseja enviar a solicitação.

   ![Captura de tela que mostra convidando-os administradores de empresa](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. Selecione **solicitação de envio**.

   Os administradores receberão um email com instruções para concluir a instalação.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Se você não tiver um proprietário do perfil de cobrança

O usuário em sua organização, quem assinou o contrato de cliente da Microsoft é adicionado como o proprietário do perfil de cobrança. Solicite o usuário para adicioná-lo como um proprietário para que você possa concluir a instalação.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>Entender as alterações na sua hierarquia de cobrança

Sua nova conta de cobrança simplifica a cobrança para sua organização, fornecendo a você aprimorada de cobrança e recursos de gerenciamento de custos. O diagrama a seguir explica como a cobrança é organizada na nova conta de cobrança.

![Imagem de ea-mca-post-transição-hierarquia](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. Você pode usar a conta de cobrança para gerenciar a cobrança para o contrato de cliente da Microsoft. Para saber mais sobre a conta de cobrança, consulte [entender a conta de cobrança](billing-mca-overview.md#your-billing-account).
2. Você pode usar o perfil de cobrança para gerenciar a cobrança para sua organização, semelhante ao seu registro de Enterprise Agreement. Os administradores de empresa se tornarem proprietários de perfil de cobrança. Para saber mais sobre perfis de cobrança, consulte [entender a cobrança perfis](billing-mca-overview.md#billing-profiles).
3. Você pode usar uma seção de nota fiscal para organizar seus custos com base em suas necessidades, semelhantes aos departamentos em seu registro de Enterprise Agreement. Departamento se torna a seções de nota fiscal e administradores de departamento se tornarem proprietários das seções respectivo da nota fiscal. Para saber mais sobre as seções de fatura, consulte [entender as seções de nota fiscal](billing-mca-overview.md#invoice-sections).
4. As contas que foram criadas no seu contrato Enterprise não têm suporte na nova conta de cobrança. Assinaturas da conta pertencem à seção respectivos fatura para seus departamentos. Os proprietários de conta podem criar e gerenciar assinaturas para suas seções de nota fiscal.

## <a name="changes-to-billing-administrator-access"></a>Alterações para acesso de administrador de cobrança

Dependendo do seu acesso, os administradores de cobrança no seu registro do Enterprise Agreement obtém acesso a escopos a nova conta de cobrança. A tabela a seguir explica a alteração no acesso durante a instalação:

| Função existente | Função de transição de postagem |
| --- | --- |
| **Administrador corporativo (leitura apenas = nenhuma)** | **-A cobrança proprietário do perfil** </br> Gerenciar tudo sobre o perfil de cobrança </br> - **Proprietário de seção de nota fiscal em todas as seções de nota fiscal** </br> Gerenciar tudo sobre as seções de nota fiscal |
| **Administrador corporativo (leitura apenas = Sim)** | **-Leitor de perfis de cobrança** </br> Exibição somente leitura e de tudo na conta de cobrança</br>**-Leitor de seção fatura na seção de todas as faturas**</br> Exibição somente leitura e de tudo o que na seção de nota fiscal|
| **Administrador de departamento (leitura apenas = nenhuma)** |**-Proprietário de seção de fatura na seção de nota fiscal criada para seus respectivos departamentos** </br>Gerenciar todos os itens na seção de nota fiscal|
| **Administrador de departamento (leitura apenas = Sim)**|**-Leitor de seção fatura na seção de nota fiscal criada para seus respectivos departamentos**</br> Exibição somente leitura de tudo o que na seção de nota fiscal|
| **Proprietário da conta** | **– Criador da assinatura do azure na seção de nota fiscal criada para seus respectivos departamentos** </br>  Criar assinaturas do Azure para sua seção de nota fiscal|

Um locatário do Azure Active Directory é selecionado para a nova conta de cobrança ao assinar o contrato do cliente Microsoft. Se um locatário não existir para a sua organização, é criado um novo locatário. O locatário representa a sua organização no Azure Active Directory. Os administradores de locatários global em sua organização usam o locatário para gerenciar o acesso de aplicativos e dados em sua organização.

Sua nova conta dá suporte apenas a usuários do locatário que foi selecionado ao assinar o contrato do cliente Microsoft. Se os usuários com permissões administrativas no seu contrato Enterprise são parte do locatário, ele obterá acesso para a nova conta de cobrança durante a instalação. Se eles não fazem parte do locatário, eles não será capazes de acessar a nova conta de cobrança, a menos que você convidá-los.

Ao convidar usuários, eles são adicionados ao locatário como usuários convidados e obtém acesso à conta de cobrança. Para convidar usuários, o acesso de convidado deve ser ativado para o locatário. Para obter mais informações, consulte [controlar o acesso de convidado no Azure Active Directory](https://docs.microsoft.com/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Se o acesso de convidado é desativado, entre em contato com os administradores globais do seu locatário para ativá-lo. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>Exibir recursos substituídos

Recursos do Enterprise Agreement seguintes são substituídos por novos recursos na conta de cobrança para um contrato de cliente da Microsoft.

### <a name="enterprise-agreement-accounts"></a>Contas de Enterprise Agreement

Não há suporte para as contas que foram criadas em seu registro do Enterprise Agreement na nova conta de cobrança. Assinaturas da conta pertencem à seção de nota fiscal criada para seus respectivos departamentos. Os proprietários de conta se tornam os criadores de assinatura do Azure e podem criar e gerenciar assinaturas para suas seções de nota fiscal.

### <a name="notification-contacts"></a>Contatos de notificação

Contatos de notificação são enviados comunicações por email sobre o contrato Enterprise do Azure. Eles não têm suporte na nova conta de cobrança. Emails de créditos do Azure e notas fiscais são enviados para os usuários que têm acesso a perfis de cobrança na sua conta de cobrança.

### <a name="spending-quotas"></a>Cotas de gastos

As cotas de gastos que foram definidas para os departamentos de TI da sua inscrição de Enterprise Agreement são substituídas com orçamentos na nova conta de cobrança. Um orçamento é criado para cada cota de gastos definido em departamentos em seu registro. Para obter mais informações sobre os orçamentos, consulte [criar e gerenciar orçamentos Azure](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Centros de custos

Centro de custo que foram definidos nas assinaturas do Azure em seu registro de contrato Enterprise são transmitidas na nova conta de cobrança. No entanto, os centros de custo para departamentos e contas de Enterprise Agreement não são suportados.

## <a name="additional-information"></a>Informações adicionais

As seções a seguir fornecem informações adicionais sobre como configurar sua conta de cobrança.

### <a name="no-service-downtime"></a>Não há tempo de inatividade do serviço

Os serviços do Azure na sua assinatura continuam sendo executados sem qualquer interrupção. Apenas fazemos a transição do relacionamento de cobrança para suas assinaturas do Azure. Não haverá impacto nos recursos existentes, nos grupos de recursos ou nos grupos de gerenciamento.

### <a name="user-access-to-azure-resources"></a>Acesso do usuário aos recursos do Azure

Acesso aos recursos do Azure que foi definido usando o Azure RBAC (controle de acesso baseado em função) não é afetado durante a transição.

### <a name="azure-reservations"></a>Reservas do Azure

Reservas do Azure em seu registro de Enterprise Agreement é movida para a sua nova conta de cobrança. Durante a transição, não haverá alterações nos descontos de reserva que estão sendo aplicados às suas assinaturas.

### <a name="azure-marketplace-products"></a>Produtos do Azure Marketplace

Todos os produtos do Azure Marketplace em seu registro de contrato Enterprise são movidos juntamente com as assinaturas. Haverá quaisquer alterações para o acesso de serviço dos produtos do Marketplace durante a transição.

### <a name="support-plan"></a>Plano de suporte

Benefícios de suporte não são transferidas como parte da transição. Compre um novo plano de suporte para obter os benefícios de assinaturas do Azure na sua nova conta de cobrança.

### <a name="past-charges-and-balance"></a>Após os encargos e saldo

Saldo de cobranças e créditos antes da transição pode ser exibido em seu registro de Enterprise Agreement por meio do portal do Azure. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Quando a instalação deve ser concluída?

Conclua a instalação de sua conta de cobrança antes de expira o registro do Enterprise Agreement. Se seu registro expirar, os serviços em suas assinaturas do Azure serão ainda continuar funcionando sem interrupções. No entanto, você será cobrado a taxas de varejo para os serviços.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Alterações para o registro do Enterprise Agreement após a instalação

Assinaturas do Azure que são criadas para o registro do Enterprise Agreement, após a transição pode ser movida para a nova conta de cobrança. Para obter mais informações, consulte [obter propriedade de assinaturas do Azure de outros usuários de cobrança](billing-mca-request-billing-ownership.md). Para mover as reservas do Azure que são adquiridos após a transição [entre em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Você também pode fornecer aos usuários acesso à conta de cobrança após a transição. Para obter mais informações, consulte [gerenciar funções de cobrança no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Reverter a transição

A transição não pode ser revertida. Depois que a cobrança de suas assinaturas do Azure é a transição para a nova conta de cobrança, não é possível revertê-lo novamente ao seu registro de Enterprise Agreement.

### <a name="closing-your-browser-during-setup"></a>Fechar o navegador durante a instalação

Antes de clicar no **inicie transição**, você pode fechar o navegador. Você pode voltar para a instalação usando o link que você recebeu no email e inicie a transição. Se você fechar o navegador após a transição é iniciada, a transição será continuar funcionando. Volte para a página de status de transição para monitorar o status mais recente da sua transição. Você receberá um email quando a transição é concluída.

## <a name="complete-the-setup-in-the-azure-portal"></a>Concluir a configuração no portal do Azure

Para concluir a instalação, você precisa de acesso para a nova conta de cobrança e o registro do Enterprise Agreement. Para obter mais informações, consulte [acesso necessário para concluir o conjunto de backup de sua conta de cobrança](#access-required-to-complete-the-setup).

1. Entrar no portal do Azure usando o link no email que foi enviado a você quando conectado o contrato do cliente Microsoft.

2. Se outra pessoa em sua organização o contrato assinado ou você não tiver o email, entre usando o link a seguir. Substitua **enrollmentNumber** com o número de registro do seu contrato Enterprise que foi renovado.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Selecione **inicie transição** na última etapa da instalação. Depois de selecionar a transição de início:

    ![Captura de tela que mostra o Assistente de instalação](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - Uma hierarquia de cobrança correspondente à sua hierarquia do Enterprise Agreement é criada na nova conta de cobrança. Para obter mais informações, consulte [entender as alterações na sua hierarquia de cobrança](#understand-changes-to-your-billing-hierarchy).
    - Os administradores desde o seu registro de Enterprise Agreement recebem acesso para a nova conta de cobrança para que eles continuam a gerenciar a cobrança para sua organização.
    - A cobrança de suas assinaturas do Azure é transferida para a nova conta. **Haverá algum impacto nos serviços do Azure durante essa transição. Vai continuariam em execução sem qualquer interrupção**.
    - Se você tiver o Azure reservas, elas são movidas para sua nova conta de cobrança com os mesmos descontos e termo. O desconto de reserva continuarão a ser aplicado durante a transição.

4. Você pode monitorar o status da transição na **transição status** página.

   ![Captura de tela que mostra o status de transição](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-set-up"></a>Validar a configuração de conta de cobrança

 Valide o seguinte para garantir que sua nova conta de cobrança está configurada corretamente:

### <a name="azure-subscriptions"></a>Assinaturas do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **custo de gerenciamento + cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Selecione o perfil de cobrança. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança. Na conta de cobrança, selecione **perfis de cobrança** e, em seguida, o perfil de cobrança.

4. Selecione **as assinaturas do Azure** do lado esquerdo.

   ![Captura de tela que mostra a lista de assinaturas](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

Assinaturas do Azure que são transferidas da sua inscrição de Enterprise Agreement para a nova conta de cobrança são exibidas na página de assinaturas do Azure. Se você acreditar que qualquer assinatura está ausente, fazer a transição de cobrança da assinatura manualmente no portal do Azure. Para obter mais informações, consulte [obter propriedade de assinaturas do Azure de outros usuários de cobrança](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Reservas do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **custo de gerenciamento + cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Selecione uma seção de nota fiscal. Dependendo de seu acesso, você precisa selecionar uma conta de cobrança ou de um perfil de cobrança.  Do perfil de cobrança ou conta de cobrança, selecione **seções de nota fiscal** e, em seguida, uma seção de nota fiscal.

    ![Captura de tela que mostra a lista de transição de postagem de seção de nota fiscal](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Selecione **todos os produtos** do lado esquerdo.

5. Pesquise **reservado**.

    ![Captura de tela que mostra a lista de transição de postagem de assinaturas](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Reservas do Azure que são movidas do seu registro do Enterprise Agreement para a nova conta de cobrança são exibidas na página todos os produtos. Repita as etapas para todas as seções de nota fiscal verificar se todas as reservas do Azure são movidas do seu registro do Enterprise Agreement. Se você acredita que qualquer reserva do Azure estiver ausente, [entre em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para mover a reserva para a nova conta de cobrança.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Acesso de administradores de empresa no perfil de cobrança

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **custo de gerenciamento + cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Selecione o perfil de cobrança que criou para seu registro. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança.  Na conta de cobrança, selecione **perfis de cobrança** e, em seguida, o perfil de cobrança.

4. Selecione **controle de acesso (IAM)** do lado esquerdo.

   ![Captura de tela que mostra o acesso de transição de postagem de administradores enterprise](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

Os administradores de empresa são listados como proprietários de perfil ao mesmo tempo a empresa que os administradores com permissões somente leitura são listados como leitores de perfil de cobrança de cobrança. Se você acredita que o acesso para os administradores de empresa está ausente, você possa lhe fornecer acesso no portal do Azure. Para obter mais informações, consulte [gerenciar funções de cobrança no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Acesso de administradores de empresa, administradores de departamento e proprietários de conta em seções de nota fiscal

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **custo de gerenciamento + cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. Selecione uma seção de nota fiscal. Seções de nota fiscal têm o mesmo nome que seus respectivos departamentos em registros do contrato Enterprise. Dependendo do seu acesso, você precisa selecionar um perfil de cobrança ou de uma conta de cobrança. O perfil de cobrança ou a conta de cobrança, selecione **seções de nota fiscal** e, em seguida, selecione uma seção de nota fiscal.

   ![Captura de tela que mostra a lista de transição de postagem de seção de nota fiscal](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Selecione **controle de acesso (IAM)** do lado esquerdo.

    ![Captura de tela que mostra o acesso do departamento e a conta de administradores de acessar a transição de postagem](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

Os administradores de empresa e administradores de departamento são listados como proprietários da seção de nota fiscal ou leitores de seção de nota fiscal, enquanto os proprietários de conta do departamento são listados como criadores de assinatura do Azure. Repita a etapa para todas as seções de nota fiscal verificar o acesso a todos os departamentos em seu registro de Enterprise Agreement. Os proprietários de conta que não eram parte de qualquer departamento receberá permissão em uma seção de nota fiscal denominada **seção de fatura padrão**. Se você acredita que o acesso para os administradores está ausente, você possa lhe fornecer acesso no portal do Azure. Para obter mais informações, consulte [gerenciar funções de cobrança no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- [Comece com sua nova conta de cobrança](billing-mca-overview.md)

- [Concluir tarefas de Enterprise Agreement em sua conta de cobrança para um contrato de cliente da Microsoft](billing-mca-enterprise-operations.md)

- [Gerenciar o acesso à sua conta de cobrança](billing-understand-mca-roles.md)
