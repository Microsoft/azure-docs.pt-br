---
title: Alterar a oferta de assinatura do Azure
description: Saiba como alterar sua assinatura do Azure e mudar para outra oferta.
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: banders
ms.openlocfilehash: dd8040effc5972d86e620793e437f5b185e12603
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685435"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Alterar sua assinatura do Azure para outra oferta

Como cliente com uma [assinatura paga conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/), você pode mudar sua assinatura do Azure para outra oferta no portal do Azure. Por exemplo, você pode usar esse recurso para aproveitar os [créditos mensais para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

**Deseja apenas atualizar da avaliação gratuita?** Confira [Atualizar sua assinatura](upgrade-azure-subscription.md).

## <a name="whats-supported"></a>O que tem suporte:

Mude de uma assinatura paga conforme o uso para:

- [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> Para obter outras alterações da oferta, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="switch-subscription-offer"></a>Alternar oferta de assinatura

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procure **Assinaturas** e selecione sua assinatura paga conforme o uso.
1. Na parte superior da página, escolha **Alterar Oferta**. A opção só estará disponível se você tiver uma assinatura paga conforme o uso e tiver concluído seu primeiro período de cobrança.  
    :::image type="content" source="./media/switch-azure-offer/switch-offer.png" alt-text="Texto Alt da Imagem que mostra detalhes da assinatura com o optionTEXT Alterar Oferta" lightbox="./media/switch-azure-offer/switch-offer.png" :::
1. Selecione a oferta desejada na lista de ofertas para as quais sua assinatura pode ser alterada. Essa lista varia com base nas associações em que sua conta está associada. Se nada estiver disponível, verifique a [lista de ofertas disponíveis para as quais você pode alternar](#whats-supported) e confirme se você tem as associações corretas. Em seguida, selecione **Avançar**.
    :::image type="content" source="./media/switch-azure-offer/select-offer.png" alt-text="Selecione uma oferta para a qual você deseja alternar" lightbox="./media/switch-azure-offer/select-offer.png" :::
    Dependendo da oferta para qual esteja trocando, é provável que você veja uma observação sobre o impacto da troca. Examine a lista atentamente e siga as instruções antes de continuar. Talvez você também precise confirmar seu número de telefone.
1. Depois de examinar as observações ou confirmar seu número de telefone, selecione **oferta de comutador**.
1. Sua assinatura foi trocada pela nova oferta.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
As seções a seguir respondem às perguntas mais frequentes.

### <a name="what-is-an-azure-offer"></a>O que é uma oferta do Azure?

Uma oferta do Azure é o *tipo* de assinatura do Azure que você tem. Por exemplo, [uma assinatura com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/), o [Azure via Open](https://azure.microsoft.com/offers/ms-azr-0111p/) e o [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) são todas ofertas do Azure. Cada oferta apresenta diferentes [termos](https://azure.microsoft.com/support/legal/offer-details/) e alguns têm vantagens especiais. A oferta da sua assinatura é mostrada na página de detalhes da assinatura.

:::image type="content" source="./media/switch-azure-offer/subscription-details.png" alt-text="Página de detalhes da assinatura mostrando o tipo de oferta" lightbox="./media/switch-azure-offer/subscription-details.png" :::

### <a name="why-dont-i-see-the-button"></a>Por que não vejo o botão?

Talvez você não veja a opção **Alterar Oferta** se:

* Você não tem uma [assinatura com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). No momento, apenas as assinaturas com taxas pagas conforme o uso podem ser convertidas para outra oferta.
  * Se você tiver uma [avaliação gratuita](https://azure.microsoft.com/free/), saiba como [atualizar para o Pagamento Conforme o Uso](upgrade-azure-subscription.md).
  * Para alternar a oferta de uma assinatura diferente, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Você ainda está em seu primeiro período de cobrança. Você precisa aguardar até o término do primeiro período de cobrança para mudar ofertas.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Por que vejo “Não há ofertas disponíveis em seu país ou região no momento”?

* Você pode não estar qualificado para quaisquer alterações de oferta. Verifique a [lista de ofertas disponíveis para as quais você pode mudar](#whats-supported) e certifique-se de que você ativou os benefícios corretos com o Visual Studio ou o Bizspark.
* Algumas ofertas podem não estar disponíveis em todos os países/todas as regiões.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>O que a troca das ofertas do Azure oferece ao meu serviço e à cobrança?

Estes são os detalhes do que acontece quando você altera as ofertas do Azure.

#### <a name="no-service-downtime"></a>Não há tempo de inatividade do serviço

Não há nenhum tempo de inatividade do serviço para os usuários associados à assinatura. No entanto, a nova oferta poderá ter restrições. Por exemplo, algumas ofertas proíbem o uso de produção, portanto, seria necessário mover os recursos de produção para outra assinatura.

#### <a name="quota-increases-are-reset"></a>Os aumentos de cota são redefinidos

Quando você muda de oferta, qualquer [aumento de cota ou de limite acima do limite padrão](../../azure-portal/supportability/resource-manager-core-quotas-request.md) é redefinido. Não há nenhum tempo de inatividade do serviço, mesmo que você tenha mais recursos além do limite padrão. Por exemplo, você estiver usando 200 núcleos em sua assinatura, mudar de oferta redefinirá sua cota de núcleos de volta para o valor padrão de 20 núcleos. As VMs que usam os 200 núcleos não são afetadas e continuarão em execução. Se você não fizer outra solicitação de aumento de cota, no entanto, você não poderá provisionar nenhum núcleo adicional.

#### <a name="billing"></a>Cobrança

No dia da alteração, uma fatura é gerada para todos os encargos pendentes. Em seguida, sua assinatura é cobrada pelos termos de preços da nova oferta. O vencimento da cobrança da assinatura será alterado para a data da alteração da oferta. Os dados de uso e de cobrança anteriores à alteração da oferta não são mantidos, portanto, recomendamos que você baixe uma cópia antes da troca.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-provider-csp-or-enterprise-agreement-ea"></a>Posso migrar de uma assinatura com taxas pagas conforme o uso para o CSP (Provedor de Soluções na Nuvem) ou o EA (Contrato Enterprise)?

* Para migrar para o CSP, confira [Transferir assinaturas do Azure entre assinantes e CSPs](transfer-subscriptions-subscribers-csp.md).
* Para migrar para o EA, peça ao Administrador de Registro para adicionar sua conta no EA. Siga as instruções no email de convite para que as suas assinaturas sejam movidas para o registro do EA.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Posso migrar dados e serviços para uma nova assinatura?

* Você pode migrar os recursos diretamente para a nova assinatura. Confira [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Para transferir a propriedade de uma assinatura do Azure e todo o conteúdo para outra pessoa, confira [Transferring ownership of an Azure subscription](billing-subscription-transfer.md) (Transferindo a propriedade de uma assinatura do Azure)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md)
