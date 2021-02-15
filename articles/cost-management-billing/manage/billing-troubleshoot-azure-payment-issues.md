---
title: Solucionar problemas de pagamento do Azure
description: Resolvendo um problema ao atualizar a conta das informações de pagamento no portal do Microsoft Azure ou no Centro de Contas.
author: v-miegge
ms.reviewerr: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: jaserano
ms.openlocfilehash: 77ca8e8425326cec31fce345600bdd0174ab5f4e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131846"
---
# <a name="troubleshoot-azure-payment-issues"></a>Solucionar problemas de pagamento do Azure

Você pode receber um erro ou ter problemas ao tentar atualizar a conta das informações de pagamento no portal do Microsoft Azure ou no Centro de Contas do Azure.

Para resolver o problema, selecione o tópico abaixo que mais se assemelha ao erro.

## <a name="my-credit-card-was-declined-when-i-tried-to-sign-up-for-azure"></a>Meu cartão de crédito foi recusado quando tentei me inscrever no Azure

Para solucionar problemas relacionados a um cartão recusado, confira [Solução de problemas de um cartão recusado na inscrição do Azure](troubleshoot-declined-card.md).

## <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Não é possível ver as assinaturas na minha conta para atualizar a forma de pagamento

Você pode estar usando uma ID de email diferente daquela usada nas assinaturas.

Para solucionar esse problema, confira o [Erro de entrada Nenhuma assinatura encontrada no portal do Azure ou no Centro de Contas do Azure](no-subscriptions-found.md).

## <a name="unable-to-use-a-virtual-or-prepaid-credit-or-debit-card-as-a-payment-method"></a>Não é possível usar um cartão de crédito ou débito virtual ou pré-pago como forma de pagamento.

*   Os cartões de crédito virtuais ou pré-pagos não são aceitos como pagamento de assinaturas do Azure.
*   Os cartões de débito não são aceitos como pagamento de assinaturas do Azure.

Para obter mais informações, confira [Solução de problemas de um cartão recusado na inscrição do Azure](troubleshoot-declined-card.md).

## <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Não é possível remover o cartão de crédito de uma forma de pagamento de cobrança salva

Por padrão, não é possível remover um cartão de crédito de uma assinatura ativa.

Se um cartão existente precisar ser excluído, um novo cartão deverá ser adicionado à assinatura para que o meio de pagamento antigo possa ser excluído com êxito ou você deverá cancelar a assinatura. Isso exclui a assinatura permanentemente e remove o cartão.

## <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Não é possível excluir uma forma de pagamento antiga após adicionar uma nova forma de pagamento

O novo meio de pagamento não pode ser associado à assinatura. Para ajudar a associar o meio de pagamento à assinatura, confira [Adicionar, atualizar ou remover um cartão de crédito no Azure](change-credit-card.md).

## <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Não é possível excluir a forma de pagamento devido ao erro *Impossível excluir a forma de pagamento*

Isso ocorre devido a um saldo pendente. Liquide todos os saldos pendentes antes de excluir a forma de pagamento.

## <a name="unable-to-make-payment-for-a-subscription"></a>Não é possível fazer o pagamento de uma assinatura

Se você recebe a mensagem de erro: *O pagamento está atrasado. Há um problema com sua forma de pagamento* ou *Desculpe, não foi possível salvar as informações. Feche o navegador e tente novamente.* , então deve haver um pagamento pendente no cartão, pois ele foi negado pela instituição financeira.

Verifique se o cartão de crédito tem saldo suficiente para efetuar o pagamento. Se não tiver, use outro cartão para efetuar o pagamento ou entre em contato com sua instituição financeira para resolver o problema.

Entre em contato com o seu banco para checar os seguintes problemas:

- As transações internacionais não estão habilitadas.
- O cartão tem um limite de crédito e o saldo deve ser liquidado.
- Um pagamento recorrente está habilitado no cartão.

## <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Não é possível alterar a forma de pagamento devido a problemas no navegador (o navegador não responde, não carrega e assim por diante)

Faça logoff de todas as sessões ativas do Azure e siga as etapas do [artigo Navegar InPrivate no Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) para iniciar uma sessão InPrivate no Microsoft Edge ou no Internet Explorer.

Na sessão particular, siga as etapas em [Como alterar um cartão de crédito](change-credit-card.md) para atualizar ou alterar as informações do cartão de crédito.

Você também pode tentar fazer o seguinte:

- Atualizar seu navegador
- Usar outro navegador
- Excluir os cookies em cache

## <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Minha assinatura ainda está desabilitada, mesmo após atualizar a forma de pagamento.

Isso ocorre devido a saldo pendente. Liquide todos os saldos pendentes antes de excluir a forma de pagamento.

## <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Não é possível alterar a forma de pagamento devido a uma página de resposta de erro XML

Você receberá essa mensagem se estiver usando o [portal do Azure](https://portal.azure.com/) para adicionar um novo cartão de crédito.

Para adicionar detalhes do cartão, entre no portal de Conta do Azure usando o endereço de email da conta do administrador.

## <a name="additional-help-resources"></a>Recursos adicionais de ajuda

Outros artigos de solução de problemas para a Cobrança e as Assinaturas do Azure

- [Cartão recusado](troubleshoot-declined-card.md)
- [Problemas na entrada da assinatura](troubleshoot-sign-in-issue.md)
- [Nenhuma assinatura encontrada](no-subscriptions-found.md)
- [Exibição do custo da empresa desabilitado](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Entrar em contato conosco para obter ajuda

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- [Documentação de cobrança do Azure](../index.yml)