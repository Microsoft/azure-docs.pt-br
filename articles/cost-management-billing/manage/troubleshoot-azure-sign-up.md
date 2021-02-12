---
title: Solucionar problemas ao se inscrever para uma nova conta no portal do Azure ou no Centro de Contas do Azure
description: Solução de problemas ao tentar se inscrever em uma nova conta no Centro de Contas do portal do Microsoft Azure.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: v-miegge
ms.openlocfilehash: 015e6058236190a04df152573621f0e57030d23b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054154"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Solucionar problemas ao se inscrever para uma nova conta no portal do Azure ou no Centro de Contas do Azure

Você pode ter problemas ao tentar se inscrever em uma nova conta no portal do Microsoft Azure ou no Centro de Contas do Azure. Este guia breve orientará você pelo processo de inscrição e discutirá alguns problemas comuns em cada etapa.

> [!NOTE]
> Se você já tiver uma conta existente e estiver procurando orientações para solucionar problemas de credenciais, confira [Solucionar problemas de credenciais da assinatura do Azure](./troubleshoot-sign-in-issue.md).

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a inscrição, verifique o seguinte:

- Se as informações em seu Perfil de Conta do Azure (incluindo o endereço de email de contato, o endereço residencial e o número de telefone) estão corretas.
- Se as informações do cartão de crédito estão corretas.
- Se você ainda não tem uma conta Microsoft com as mesmas informações.

## <a name="guided-walkthrough-of-azure-sign-up"></a>Guia passo a passo para a inscrição do Azure

A experiência de inscrição do Azure consiste em quatro seções:

- Sobre você
- Verificação de identidade por telefone
- Verificação de identidade por cartão
- Contrato

Este tutorial fornece exemplos das informações corretas para se inscrever em uma conta do Azure. Cada seção também contém alguns problemas comuns e como resolvê-los.

## <a name="about-you"></a>Sobre você

![Sobre você](./media/troubleshoot-azure-sign-up/1.png)
 
### <a name="common-issues-and-solutions"></a>Problemas comuns e soluções

#### <a name="you-see-the-message-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Você vê a mensagem "Não é possível prosseguir com a inscrição devido a um problema na sua conta. Entre em contato com o suporte de cobrança"

Para resolver esse erro, siga estas etapas:

1.  Entre no [Centro de Contas do Azure](https://account.azure.com/Profile) usando a credencial do administrador da conta.
1.  Selecione em **Editar detalhes**.
1.  Verifique se todos os campos de endereço estão preenchidos e são válidos.
1.  Ao se inscrever para a assinatura do Azure, verifique se o endereço para cobrança inserido durante o registro do cartão de crédito corresponde os dados do registro bancário.

Se você continuar recebendo a mensagem, tente se inscrever usando um navegador diferente.

E quanto à navegação InPrivate?

#### <a name="free-trial-is-not-available"></a>A avaliação gratuita não está disponível

Você já usou uma assinatura do Azure anteriormente? O Contrato de Termos de Uso do Azure limita a ativação gratuita da avaliação para usuários que sejam novos no Azure. Se você já teve qualquer outro tipo de assinatura do Azure, não poderá ativar uma avaliação gratuita. Considere inscrever-se em uma [assinatura Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/).

#### <a name="you-see-the-message-you-are-not-eligible-for-an-azure-subscription"></a>Você verá a mensagem "Você não está qualificado para uma assinatura do Azure"

Para resolver esse problema, verifique se os seguintes itens:

- Se as informações que você forneceu em seu perfil de conta do Azure (incluindo o endereço de email de contato, o endereço residencial e o número de telefone) estão corretas.
- Se as informações do cartão de crédito estão corretas.
- Se você ainda não tem uma conta Microsoft que usa as mesmas informações.

#### <a name="you-see-the-message-your-current-account-type-is-not-supported"></a>Você verá a mensagem "Não há suporte para seu tipo de conta atual"

Esse problema poderá ocorrer se a conta estiver registrada em um [diretório não gerenciado do Azure AD](../../active-directory/enterprise-users/directory-self-service-signup.md) e se não estiver no diretório do Azure AD da sua organização.
Para resolver esse problema, inscreva-se na conta do Azure usando outra conta ou assuma o controle do diretório do AD não gerenciado. Para obter mais informações, confira [Controlar um diretório não gerenciado como administrador no Azure Active Directory](../../active-directory/enterprise-users/domains-admin-takeover.md).

## <a name="identity-verification-by-phone"></a>Verificação de identidade por telefone

![Verificação de identidade por telefone](./media/troubleshoot-azure-sign-up/2.png)
 
Ao receber a mensagem de texto ou a chamada telefônica, insira o código recebido na caixa de texto.

### <a name="common-issues-and-solutions"></a>Problemas comuns e soluções

#### <a name="no-verification-text-message-or-phone-call"></a>Nenhuma mensagem de texto de verificação ou chamada telefônica

Embora o processo de verificação de inscrição costume ser rápido, pode levar até quatro minutos para que o código de verificação seja entregue.

Estas são algumas outras dicas:

- Você pode usar qualquer número de telefone para verificação, desde que ele cumpra os requisitos. O número de telefone inserido para verificação não é armazenado como um número de contato da conta.
  - Um número de telefone VoIP (Voz sobre IP) não pode ser usado para o processo de verificação por telefone.
  - Verifique se o telefone pode receber chamadas ou mensagens SMS de um número de telefone localizado nos Estados Unidos.
- Confira o número de telefone inserido, incluindo o código do país selecionado no menu suspenso.
- Se o telefone não receber mensagens de texto (SMS), tente a opção **Ligar para mim**.

## <a name="identity-verification-by-card"></a>Verificação de identidade por cartão

![Verificação de identidade por cartão](./media/troubleshoot-azure-sign-up/3.png)
 
### <a name="common-issues-and-solutions"></a>Problemas comuns e soluções

#### <a name="credit-card-declined-or-not-accepted"></a>Cartão de crédito recusado ou não aceito

Cartões de crédito ou débito pré-pagos ou virtuais não são aceitos como forma de pagamento para as assinaturas do Azure. Para ver o que mais pode fazer com que o cartão seja recusado, confira [Solucionar problemas de um cartão recusado na inscrição do Azure](./troubleshoot-declined-card.md).

#### <a name="credit-card-form-doesnt-support-my-billing-address"></a>O formulário de cartão de crédito não dá suporte ao meu endereço para cobrança

Seu endereço para cobrança precisa estar no país selecionado na seção **Sobre você**. Verifique se você selecionou o país correto.

#### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>A barra de progresso trava na seção de verificação de identidade por cartão

Para concluir a verificação de identidade por cartão, cookies de terceiros devem ser permitidos no navegador.

Use as etapas a seguir para atualizar as configurações de cookies do navegador.

1. Atualize as configurações de cookie.
   - Se você estiver usando **Chrome**:
     - Selecione **Configurações** > **Mostrar configurações avançadas** > **Privacidade** > **Configurações de conteúdo**. Desmarque a opção **Bloquear cookies e dados de site de terceiros**.

   - Se você estiver usando o **Microsoft Edge**:
     - Selecione **Configurações** > **Exibir configurações avançadas** > **Cookies** > **Não bloquear cookies**.

1. Atualize a página de inscrição do Azure e verifique se o problema foi resolvido.
1. Se a atualização não resolver o problema, saia, reinicie o navegador e tente novamente.

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Observei um encargo em minha conta de avaliação gratuita

Você poderá ver uma pequena retenção temporária para verificação na sua conta de cartão de crédito após a inscrição. Essa retenção é removida dentro de três a cinco dias. Se estiver preocupado com o gerenciamento de custos, leia mais sobre como [Analisar encargos inesperados](../understand/analyze-unexpected-charges.md).

## <a name="agreement"></a>Contrato

Conclua o contrato.

## <a name="other-issues"></a>Outros problemas

### <a name="cant-activate-azure-benefit-plan-like-visual-studio-bizspark-bizsparkplus-or-mpn"></a>Não consigo ativar um plano de benefícios do Azure como o Visual Studio, o BizSpark, o BizSparkPlus ou o MPN

Verifique se você está usando as credenciais de conexão corretas. Em seguida, verifique o programa de benefícios e se você está qualificado.
- Visual Studio
  - Verifique seu status de qualificação na [página da conta do Visual Studio](https://my.visualstudio.com/Benefits).
  - Se não puder verificar seu status, entre em contato com o [Suporte da Assinatura do Visual Studio](https://visualstudio.microsoft.com/subscriptions/support/).
- Microsoft para Startups
  - Entre no [portal do Microsoft para Startups](https://startups.microsoft.com/#start-two) para verificar seu status de qualificação para o Microsoft para Startups.
  - Se não conseguir verificar seu status, você pode obter ajuda nos [fóruns do Microsoft para Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Entre no [portal do MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) para verificar seu status de qualificação. Se tiver as [Competências de Plataforma de Nuvem](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)adequadas, você poderá estar qualificado para receber outros benefícios.
  - Se não conseguir verificar seu status, entre em contato com o [suporte do MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Não consigo ativar uma nova assinatura do Azure via Open

Para criar uma assinatura do Azure via Open, você precisa ter uma chave OSA (Ativação de Serviço Online) válida com pelo menos um token do Azure via Open associado a ela. Se você não tiver uma chave OSA, entre em contato com um dos Parceiros da Microsoft listados no [Microsoft Pinpoint](https://pinpoint.microsoft.com/).

## <a name="additional-help-resources"></a>Recursos adicionais de ajuda

Outros artigos de solução de problemas para a Cobrança e as Assinaturas do Azure

- [Cartão recusado](./troubleshoot-declined-card.md)
- [Problemas na entrada da assinatura](./troubleshoot-sign-in-issue.md)
- [Nenhuma assinatura encontrada](./no-subscriptions-found.md)
- [Exibição do custo da empresa desabilitado](./enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Entrar em contato conosco para obter ajuda

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="find-out-more-about-azure-cost-management"></a>Descubra mais sobre o Gerenciamento de Custos do Azure

- [Documentação de Cobrança e Gerenciamento de Custos do Azure](../index.yml)