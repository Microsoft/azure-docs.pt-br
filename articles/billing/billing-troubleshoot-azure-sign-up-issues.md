---
title: "Solucionar problemas de inscrição do Azure | Microsoft Docs"
description: "Descreve como solucionar alguns problemas de inscrição no Azure."
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f9d4e580b0d4d7c3ea39c53b1bf4067a59d4d52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-sign-up-issues-for-azure"></a>Solucionar problemas de inscrição do Azure
Se você não conseguir se inscrever no Azure, use as dicas deste artigo para solucionar problemas comuns. Caso tenha um problema com seu cartão de crédito durante a inscrição, consulte [Seu cartão de débito ou crédito é recusado durante a inscrição do Azure](billing-credit-card-fails-during-azure-sign-up.md). Se você tiver uma conta do Azure, mas não conseguir se conectar, consulte [Não consigo me conectar para gerenciar minha assinatura do Azure](billing-cannot-login-subscription.md).

## <a name="error-we-cannot-proceed-with-signup-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Erro "Não é possível prosseguir com a inscrição devido a um problema com sua conta. Entre em contato com o suporte à cobrança". 

Para resolver o problema, siga estas etapas:

1. Entre no [Centro de Contas do Azure](https://account.azure.com/Profile) usando a credencial do administrador da conta. 
2. Clique em **Editar detalhes**.
3. Certifique-se de que todos os campos de endereço estejam preenchidos e válidos. 
4. Ao se inscrever à assinatura do Azure, certifique-se de que o endereço de cobrança inserido durante o registro de cartão de crédito corresponde os registros do banco.

Se você ainda receber o erro, tente se inscrever usando um navegador diferente.

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>A barra de progresso trava na seção "Verificação de identidade por cartão"

Para concluir a verificação de identidade por cartão, cookies de terceiros devem ser permitidos no navegador.

![Capture a tela da seção Verificação de identidade por cartão travada durante a inscrição](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

Use as etapas a seguir para atualizar as configurações de cookies do navegador.

1. Se estiver usando o Chrome, acesse **Configurações** > **Mostrar configurações avançadas** > **Privacidade** > **Configurações de conteúdo**. Desmarque a opção **Bloquear cookies de terceiros e dados do site**.
2. Se estiver usando o Edge, acesse **Configurações** > **Exibir configurações avançadas** > **Cookies**. Selecione **Não bloquear cookies**.
3. Atualize a página de inscrição do Azure e verifique se o problema foi resolvido.
4. Se a atualização não resolver o problema, feche e reinicie o navegador e tente novamente.

## <a name="credit-card-form-doesnt-support-my-billing-address"></a>O formulário de cartão de crédito não dá suporte ao meu endereço para cobrança
Seu endereço para cobrança precisa estar no país selecionado na seção **Sobre você**. Selecione o país correto.

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Nenhuma mensagem de texto ou chamada durante a verificação da conta de inscrição
Embora isso normalmente seja muito mais rápido, poderá levar até quatro minutos para que o código de verificação seja entregue. O número de telefone inserido para verificação não é armazenado como um número de contato da conta.

Estas são algumas outras dicas:
* Um número de telefone VOIP não pode ser usado para o processo de verificação do telefone.
* Verifique novamente o número de telefone inserido, incluindo o código do país selecionado no menu suspenso.
* Se o telefone não receber mensagens de texto (SMS), tente a opção **Ligar para mim**.
* Verifique se o telefone pode receber chamadas ou mensagens SMS de um número dos Estados Unidos.

Quando você receber a mensagem de texto ou a chamada telefônica, insira o código recebido na caixa de texto.

## <a name="credit-card-declined-or-not-accepted"></a>Cartão de crédito recusado ou não aceito
Cartões de crédito ou débito pré-pago ou virtuais não são aceitos como pagamentos de assinaturas do Azure. Para ver o que mais pode fazer com que seu cartão seja recusado, consulte [Seu cartão de débito ou crédito é recusado durante a inscrição do Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="free-trial-is-not-available"></a>"A Avaliação Gratuita não está disponível"
Você já usou uma assinatura do Azure anteriormente? O Contrato de Termos de Uso do Azure limita a ativação gratuita da avaliação para usuários que sejam novos no Azure. Se você já teve qualquer outro tipo de assinatura do Azure, não poderá ativar uma avaliação gratuita. Considere inscrever-se em uma [assinatura Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/).

## <a name="i-saw-a-charge-on-my-free-trial-account"></a>Observei um encargo em minha conta de Avaliação Gratuita
Você poderá ver uma pequena verificação em espera em sua conta de cartão de crédito após a inscrição, que é removida no prazo de até 3 a 5 dias. Se estiver preocupado com o gerenciamento de custos, leia mais sobre como [evitar custos inesperados](https://docs.microsoft.com/azure/billing/billing-getting-started).

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Não consigo ativar um plano de benefícios do Azure, por exemplo, MSDN, BizSpark, BizSparkPlus ou MPN
Verifique se você está usando as credenciais de conexão corretas. Em seguida, verifique o programa de benefícios para garantir que você está qualificado. 

* MSDN
  * Verifique o status de qualificação na [página da sua conta MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  * Se não conseguir verificar seu status, contate o [Atendimento ao Cliente das Assinaturas do MSDN](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* BizSpark
  * Entre no [portal do BizSpark](https://www.microsoft.com/bizspark/default.aspx#start-two) e verifique o status de qualificação para BizSpark e BizSpark Plus.
  * Se você não conseguir confirmar seu status, [obtenha ajuda nos fóruns do BizSpark](http://aka.ms/bzforums).
* MPN
  * Entre no [portal do MPN](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) e verifique o status da sua qualificação. Se tiver as [Competências de Plataforma de Nuvem](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)adequadas, você poderá estar qualificado para receber outros benefícios.
  * Se não conseguir verificar seu status, contate o [suporte do MPN](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx).

## <a name="cant-activate-new-azure-in-open-subscription"></a>Não consigo ativar uma nova assinatura do Azure via Open
Para criar uma assinatura do Azure via Open, você deve ter uma chave OSA (Ativação de Serviço Online) válida com pelo menos um token do Azure via Open associado a ela. Se você não tiver uma chave OSA, contate um dos Parceiros da Microsoft listados no [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
