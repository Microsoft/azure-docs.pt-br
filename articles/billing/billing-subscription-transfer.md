---
title: Transferir a propriedade de cobrança da assinatura do Azure para outra conta | Microsoft Docs
description: Descreve como transferir a propriedade da cobrança de uma assinatura do Azure para outra conta e algumas perguntas frequentes (FAQ) sobre o processo
keywords: transferir a assinatura de transferência de assinatura do azure, azure, mover assinatura do azure para outra conta, do azure Alterar proprietário da assinatura, transferir assinatura do azure para cobrança da transferência de outra conta, do azure
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d41e32065e3de37eb8f01ab1b836040e7f57b12
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657911"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Transferir a propriedade de cobrança de uma assinatura do Azure para outra conta

Talvez você queira transferir a propriedade de cobrança da sua assinatura do Azure se você deixa sua organização, ou se você deseja que sua assinatura será cobrado para outra conta. Transferir a propriedade para outra conta fornece os administradores da nova permissão de conta para executar tarefas de cobrança, como alterar o método de pagamento, Exibir encargos e cancelar a assinatura.

Se você quiser manter a propriedade de cobrança, mas a alteração do tipo de sua assinatura, consulte [alterar sua assinatura do Azure para outra oferta](billing-how-to-switch-azure-offer.md). Se você quiser controlar quem pode gerenciar recursos na assinatura, consulte [funções internas para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Se você for um cliente do Enterprise Agreement (EA), os administradores de empresa podem transferir a propriedade da cobrança de suas assinaturas entre contas. Para obter mais informações, consulte [transferir a propriedade de cobrança de assinaturas do Enterprise Agreement (EA)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Transferir a propriedade de cobrança de uma assinatura do Azure

1. Entrar para o [portal do Azure](https://portal.azure.com) como administrador da conta de cobrança que tem a assinatura que você deseja transferir. Para saber se você for um administrador, consulte [perguntas frequentes](#faq).

1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Selecione **assinaturas** do painel esquerdo. Dependendo do seu acesso, você precisará selecionar um escopo de cobrança e, em seguida, selecione **inscrições** ou **assinaturas do Azure**.

1. Selecione **transferir a propriedade de cobrança** para a assinatura que você deseja transferir. 

   ![Selecione a assinatura para transferir](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Insira o endereço de email de um usuário que seja um administrador de cobrança da conta que será o novo proprietário da assinatura e, em seguida, selecione **enviar solicitação de transferência**.

    > [!IMPORTANT]
    >
    > Se você transferir a propriedade de cobrança da sua assinatura para uma conta de usuário no AD do Azure de outro locatário, todos os [controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md) atribuições para gerenciar recursos na assinatura serão removidas permanentemente. Somente o novo proprietário terá acesso para gerenciar recursos na assinatura. Para obter mais informações, consulte [transferir a assinatura a um usuário em outro locatário do Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md).
  
    ![Enviar a página de transferência](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. O usuário recebe um email com instruções para revisar a solicitação de transferência.

   ![Email de transferência de assinatura para o destinatário](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Para aprovar a solicitação de transferência, o usuário seleciona o link no email e segue as instruções. O usuário precisa selecionar um método de pagamento que será usado para pagar pela assinatura. Além disso, se o usuário não tiver uma conta do Azure, eles teria se inscrever para uma nova conta. 

   ![Primeira página da Web de transferência de assinatura](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Segunda página da Web de transferência de assinatura](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Segunda página da Web de transferência de assinatura](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Sucesso! Agora a assinatura será transferida.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Transferência de assinatura para uma conta em outro locatário do Azure AD

Um locatário do Azure Active Directory (AD) é criado quando você se inscrever para o Azure. O locatário representa sua conta. Você pode usar o locatário para gerenciar o acesso a suas assinaturas e recursos.

Quando você cria uma nova assinatura, ele é hospedado no locatário do Azure AD da sua conta. Se você deseja fornecer a que outros usuários acessem a sua assinatura ou recursos, você precisa para convidá-los ao seu locatário. Isso ajuda a controlar o acesso a suas assinaturas e recursos.

Quando você transfere a propriedade de cobrança da sua assinatura a uma conta no outro locatário do AD do Azure, a assinatura é movida para o locatário da nova conta. Todos os usuários, grupos ou entidades de serviço que tinham [(RBAC) de acesso baseado em função](../role-based-access-control/overview.md) perderão o acesso para gerenciar recursos na assinatura. Somente o usuário na nova conta que aceitar sua solicitação de transferência terá acesso para gerenciar os recursos. Para fornecer acesso aos usuários que originalmente tinham acesso, o novo proprietário teria [adicioná-los manualmente à assinatura](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Transferindo o Visual Studio, Microsoft Partner Network (MPN) e assinaturas de desenvolvimento/teste pago conforme o uso

As assinaturas do Visual Studio e o Microsoft Partner Network tem recorrente crédito mensal do Azure associado a eles. Quando você transfere essas assinaturas, seu crédito não está disponível na conta de cobrança de destino. A assinatura usa o crédito na conta de cobrança de destino. Por exemplo, se Pedro transfere uma assinatura do Visual Studio Enterprise para a conta em 9 de setembro de Alice e Jane aceita a transferência. Depois que a transferência for concluída, a assinatura é iniciado usando a crédito na conta de Jane. O crédito redefinirá todos os meses no dia 9. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Transferir a propriedade de cobrança de assinaturas do Enterprise Agreement (EA)

O administrador da empresa pode transferir a propriedade de assinaturas entre contas dentro de um registro. Para obter mais informações, consulte [transferir propriedade da conta](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) no portal do EA.

<a id="CSP"></a>

## <a name="next-steps-after-accepting-billing-ownership"></a>Próximas etapas depois de aceitar a propriedade da cobrança

Se você aceitou a propriedade da cobrança de uma assinatura do Azure, recomendamos que você examine as próximas etapas:

1. Examine e atualize o Administrador de Serviços, os Coadministradores e outras funções do RBAC. Para obter mais informações, confira [Adicionar ou alterar os administradores de assinatura do Azure](billing-add-change-azure-subscription-administrator.md) e [Gerenciar o acesso usando o RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).
1. Atualize as credenciais associadas aos serviços dessa assinatura, incluindo:
   1. Certificados de gerenciamento que concedem ao usuário direitos de administrador aos recursos de assinatura. Para saber mais, confira [Criar e carregar um certificado de gerenciamento do Azure](../cloud-services/cloud-services-certs-create.md)
   1. Teclas de acesso para serviços como Armazenamento. Para saber mais, consulte [Sobre as contas de Armazenamento do Azure](../storage/common/storage-create-storage-account.md)
   1. Credenciais de Acesso Remoto para serviços como Máquinas Virtuais do Azure.
1. Se estiver trabalhando com um parceiro, considere a atualização da ID do parceiro nessa assinatura. Você pode atualizar a ID do parceiro no [Portal do Azure](https://portal.azure.com). Para obter mais informações, consulte [vincular uma ID de parceiro a suas contas do Azure](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

Transferência de assinatura no portal do Azure está disponível para os tipos de assinatura listados abaixo. Atualmente, a transferência não há suporte para [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) ou [do Azure no Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) assinaturas. Para uma solução alternativa, consulte [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../azure-resource-manager/resource-group-move-resources.md). Para transferir outras assinaturas, como [patrocínio](https://azure.microsoft.com/offers/ms-azr-0036p/) ou planos de suporte, [entre em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Assinantes do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [No portal de EA](#EA).

\*\* Suporte apenas para contas que são criadas durante a inscrição para cima no site do Azure. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Perguntas frequentes (FAQ para os remetentes)

Essas perguntas frequentes se aplicam a usuários, o que estiver transferindo a propriedade da cobrança de uma assinatura do Azure para outra conta.

### <a name="whoisaa"></a> Quem é o administrador de cobrança de uma conta?

Um administrador de cobrança é uma pessoa que tenha permissão para gerenciar a cobrança para uma conta. Eles estão autorizados a acessar a cobrança na [portal do Azure](https://portal.azure.com) e realizar diversas tarefas de cobrança, como criar assinaturas, exibição e pagar faturas ou atualizar métodos de pagamento.

Para identificar para o qual você seja um administrador de cobrança de assinaturas, use as seguintes etapas:

1. Visite o [página de cobrança no portal do Azure do gerenciamento de custos +](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Selecione **assinaturas** do painel esquerdo. Dependendo do seu acesso, você precisará selecionar um escopo de cobrança e, em seguida, selecione **inscrições** ou **assinaturas do Azure**
1. A página de assinaturas lista todas as assinaturas para o qual você é um administrador de cobrança.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Tudo é transferido? Incluindo grupos de recursos, VMs, discos e outros serviços em execução?

Todos os seus recursos, como VMs, discos e sites são transferidos para a nova conta. No entanto, se você transferir assinatura a uma conta no AD do Azure de outro locatário, qualquer [funções de administrador](billing-add-change-azure-subscription-administrator.md) e [controle de acesso baseado em função (RBAC)](../role-based-access-control/role-assignments-portal.md) atribuições na assinatura [não transferência](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Além disso, [registros de aplicativo](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) e outros serviços específicos de locatário não são transferidas, juntamente com a assinatura.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Posso transferir a propriedade a uma conta em outro país?
Infelizmente, cruzada país transferências não podem ser executadas no portal do Azure. Para transferir sua assinatura em países [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Eu sou um administrador em duas contas. Para transferir uma assinatura de uma das minhas contas para outra?
Sim, você pode transferir a assinatura entre suas contas. Suas contas conceitualmente são consideradas contas de dois usuários diferentes para que você pode usar as etapas acima para transferir as assinaturas entre suas contas.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Uma transferência de assinatura resulta em qualquer tempo de inatividade do serviço?

Se você transferir uma assinatura a um usuário no mesmo locatário do AD do Azure, não há nenhum impacto sobre os recursos em execução na assinatura.  No entanto, se você transferir a assinatura para um usuário em outro locatário, todos os usuários, grupos e entidades de serviço que tinham [(RBAC) de acesso baseado em função](../role-based-access-control/overview.md) perderão o acesso para gerenciar recursos na assinatura. 

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>O destinatário tem acesso ao histórico de cobrança e de uso?

As únicas informações disponíveis para o destinatário serão o custo do mês passado para a sua assinatura. O restante do histórico de cobrança e uso não é transferida com a assinatura

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Como faço para migrar dados e serviços da minha assinatura do Azure para uma nova assinatura?

Se não for possível transferir a propriedade da assinatura, migre os recursos manualmente. Consulte [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Se eu transferir uma assinatura do Visual Studio ou Microsoft Partner Network, meu crédito são realizadas com a assinatura na nova conta?

Não, seu crédito não está disponível na nova conta. O usuário que aceita a solicitação de transferência precisa ter uma licença do Visual Studio para aceitar a solicitação de transferência. A assinatura usa o crédito do Visual Studio que está disponível na conta do usuário. Para obter mais informações, consulte [transferindo Visual Studio, Microsoft Partner Network (MPN) e desenvolvimento/teste pago conforme o uso de assinaturas](#transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions)


## <a name="frequently-asked-questions-faq-for-recipients"></a>Perguntas frequentes (FAQ para os destinatários)

Essas perguntas frequentes se aplicam aos usuários, que estão assumindo a propriedade de uma assinatura do Azure com outra conta de cobrança.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Se eu assumir a propriedade de uma assinatura com outra conta de cobrança, fazer os usuários em que a conta continuarão a ter acesso aos meus recursos?

Se a assinatura é transferida para uma conta no mesmo locatário do AD do Azure, todos os usuários, grupos e entidades de serviço que tinham [(RBAC) de acesso baseado em função](../role-based-access-control/overview.md) para gerenciar recursos na assinatura retenham seu acesso. Para exibir os usuários que têm acesso RBAC à assinatura, use as seguintes etapas:

1. Visite o [página de assinatura no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a assinatura que você deseja verificar e, em seguida, selecione **controle de acesso (IAM)** do painel esquerdo.
1. Selecione **atribuições de função** da parte superior da página. A página de atribuições de função lista todos os usuários que têm acesso RBAC na assinatura.

Se a assinatura é transferida para uma conta no outro locatário do AD do Azure, todos os usuários, grupos e entidades de serviço que tinham [(RBAC) de acesso baseado em função](../role-based-access-control/overview.md) perderão o acesso para gerenciar recursos na assinatura. No entanto, mesmo se eles não tiverem acesso RBAC mais, ele ainda terá acesso à assinatura por meio de alguns mecanismos de segurança, incluindo:

* Certificados de gerenciamento que concedem ao usuário direitos de administrador aos recursos de assinatura. Para saber mais, confira [Criar e carregar um certificado de gerenciamento do Azure](../cloud-services/cloud-services-certs-create.md).
* Teclas de acesso para serviços como Armazenamento. Para saber mais, confira [Sobre as contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).
* Credenciais de Acesso Remoto para serviços como Máquinas Virtuais do Azure.

Se o destinatário precisar restringir, acesso a seus recursos, eles devem considerar a atualização dos segredos associados ao serviço. A maioria dos recursos pode ser atualizada usando as seguintes etapas:

  1. Entre no [Portal do Azure](https://portal.azure.com).
  2. No menu Hub, selecione **Todos os recursos**.
  3. Selecione o recurso.
  4. Na página de recursos, clique em **configurações**. Aqui você pode exibir e atualizar os segredos existentes.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Se eu assumir a propriedade da cobrança de uma assinatura no meio do ciclo de cobrança, preciso pagar por todo o ciclo de cobrança?

Sua conta é responsável pelo pagamento por qualquer uso que será relatado a partir do momento da transferência em diante. Pode haver algum uso que ocorreu antes da transferência, mas foi relatado posteriormente. O uso é incluído na fatura da sua conta.

### <a name="can-i-use-a-different-payment-method"></a>Posso usar um método de pagamento diferente?

Sim. Ao aceitar a solicitação de transferência, você pode selecionar um método de pagamento existente que esteja vinculado à sua conta ou adicionar um novo método de pagamento.

## <a name="troubleshooting"></a>Solução de problemas

### <a id="no-button"></a> Por que não vejo o botão “Transferir Assinatura”?

Infelizmente, a transferência de assinatura de autoatendimento não está disponível para sua conta de cobrança. Atualmente, não damos suporte a transferência de propriedade de cobrança da conta do Enterprise Agreement (EA) no portal do Azure. Além disso, as contas de contrato de cliente da Microsoft que são criadas durante o trabalho por meio de vendas da Microsoft não oferecem suporte a transferir a propriedade de cobrança de assinaturas. 

### <a id="no-button"></a> Por que minha assinatura do tipo de transferência de suporte? 

Infelizmente, nem todos os tipos de assinaturas dão suporte a transferência de propriedade de cobrança. Para exibir a lista de tipos de assinatura que dá suporte a transferências, consulte [suporte para tipos de assinatura](#supported-subscription-types)

### <a id="no-button"></a> Por que estou recebendo um erro de acesso negado ao tentar transferir a propriedade de cobrança de uma assinatura? 

Você verá esse erro se você está tentando transferir uma assinatura do plano do Microsoft Azure e você não tiver a permissão necessária. Para transferir uma assinatura de plano do Microsoft Azure, você precisa ser um proprietário ou colaborador na seção de nota fiscal para o qual a assinatura é cobrada. Para obter mais informações, consulte [gerenciar assinaturas para a seção de nota fiscal](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

- Examine e atualize o Administrador de Serviços, os Coadministradores e outras funções do RBAC. Para obter mais informações, confira [Adicionar ou alterar os administradores de assinatura do Azure](billing-add-change-azure-subscription-administrator.md) e [Gerenciar o acesso usando o RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md).
